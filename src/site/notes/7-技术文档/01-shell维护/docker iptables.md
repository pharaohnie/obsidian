---
{"dg-publish":true,"permalink":"/7/01-shell/docker-iptables/","tags":["docker","iptables"],"dg-note-properties":{"tags":["docker","iptables"]}}
---




docker启动后会创建一个空的chain叫DOCKER-USER

iptables会在DOCKER-USER 之后执行DOCKER chain里的规则

  

所以如果要封堵端口，【注意，这里的端口应该是容器内的端口，而不是expose的端口】

  

  
```bash
iptables -I DOCKER-USER -i ens18 -p tcp -m tcp --dport 8080 -j DROP
iptables -I DOCKER-USER -i ens18 -p tcp -m tcp --dport 8080 -s 43.255.200.0/24 -j ACCEPT
```



一次性指定多个端口
```bash
iptables -I DOCKER-USER -i ens18 -p tcp -m multiport --dports 3306,18080 -s 43.255.200.0/24 -j ACCEPT
iptables -I DOCKER-USER -i ens18 -p tcp -m multiport --dports 3306,18080 -j DROP
```

或者用ipset


```bash
ipset.china_routes
create china_routes hash:net family inet hashsize 8192 maxelem 65536
add china_routes 43.254.136.0/21
add china_routes 103.97.228.0/23
add china_routes 203.57.70.0/23
add china_routes 116.207.0.0/16
add china_routes 116.213.64.0/18
add china_routes 203.23.85.0/24
ipset restore -f ipset.china_routes

iptables -I DOCKER-USER -i eth0 -p tcp -m set ! --match-set china_routes src -m tcp --dport 1080 -j DROP
```
  

# 1 自动化脚本

```bash
#!/usr/bin/env bash
set -euo pipefail

# 网络接口与端口列表
INTERFACE="ens18"
PORTS="3000,8000"
SUBNETS="43.255.200.83/32"   # 逗号分隔
CHAIN="DOCKER-USER"

# 给本脚本添加的规则打标（用于 view 精确筛选）
COMMENT_TAG="managed-by-iptables-script"

# 解析 SUBNETS -> 数组（去空格）
IFS=',' read -r -a SUBNET_ARR <<< "${SUBNETS}"
for i in "${!SUBNET_ARR[@]}"; do
  SUBNET_ARR[$i]=$(echo "${SUBNET_ARR[$i]}" | xargs)
done

rule_exists_drop() {
  iptables -C "$CHAIN" -i "$INTERFACE" -p tcp -m multiport --dports "$PORTS" \
    -m comment --comment "$COMMENT_TAG" -j DROP 2>/dev/null
}

rule_exists_accept() {
  local subnet="$1"
  iptables -C "$CHAIN" -i "$INTERFACE" -p tcp -m multiport --dports "$PORTS" -s "$subnet" \
    -m comment --comment "$COMMENT_TAG" -j ACCEPT 2>/dev/null
}

rule_exists_return() {
  iptables -C "$CHAIN" -m comment --comment "$COMMENT_TAG" -j RETURN 2>/dev/null
}

delete_managed_rules() {
  for subnet in "${SUBNET_ARR[@]}"; do
    [[ -z "$subnet" ]] && continue
    while iptables -C "$CHAIN" -i "$INTERFACE" -p tcp -m multiport --dports "$PORTS" -s "$subnet" \
      -m comment --comment "$COMMENT_TAG" -j ACCEPT 2>/dev/null; do
      iptables -D "$CHAIN" -i "$INTERFACE" -p tcp -m multiport --dports "$PORTS" -s "$subnet" \
        -m comment --comment "$COMMENT_TAG" -j ACCEPT
    done
  done

  while iptables -C "$CHAIN" -i "$INTERFACE" -p tcp -m multiport --dports "$PORTS" \
    -m comment --comment "$COMMENT_TAG" -j DROP 2>/dev/null; do
    iptables -D "$CHAIN" -i "$INTERFACE" -p tcp -m multiport --dports "$PORTS" \
      -m comment --comment "$COMMENT_TAG" -j DROP
  done

  while rule_exists_return; do
    iptables -D "$CHAIN" -m comment --comment "$COMMENT_TAG" -j RETURN
  done
}

delete_unconditional_returns() {
  while iptables -C "$CHAIN" -j RETURN 2>/dev/null; do
    iptables -D "$CHAIN" -j RETURN
  done
}

ensure_unconditional_return_last() {
  delete_unconditional_returns
  iptables -A "$CHAIN" -j RETURN
}

apply_rules() {
  delete_managed_rules
  delete_unconditional_returns

  for subnet in "${SUBNET_ARR[@]}"; do
    [[ -z "$subnet" ]] && continue
    if rule_exists_accept "$subnet"; then
      echo "ACCEPT 规则已存在（$subnet），跳过添加。"
    else
      iptables -I "$CHAIN" 1 -i "$INTERFACE" -p tcp -m multiport --dports "$PORTS" -s "$subnet" \
        -m comment --comment "$COMMENT_TAG" -j ACCEPT
      echo "已添加 ACCEPT 例外规则（$subnet）。"
    fi
  done

  if rule_exists_drop; then
    echo "DROP 规则已存在，跳过添加。"
  else
    iptables -A "$CHAIN" -i "$INTERFACE" -p tcp -m multiport --dports "$PORTS" \
      -m comment --comment "$COMMENT_TAG" -j DROP
    echo "已添加 DROP 规则。"
  fi

  if ! rule_exists_return; then
    iptables -A "$CHAIN" -m comment --comment "$COMMENT_TAG" -j RETURN
  fi

  ensure_unconditional_return_last
  echo "规则已应用完成。"
}

remove_rules() {
  delete_managed_rules
  ensure_unconditional_return_last
  echo "本脚本添加的规则已删除完成。"
}

# 人性化 view：汇总“哪些源地址可访问哪些端口”
view_rules() {
  echo "==== 本脚本生效策略汇总（链：$CHAIN，接口：$INTERFACE，tag：$COMMENT_TAG）===="

  # 取本脚本规则：只抓带 comment tag 的行
  local rules
  rules="$(iptables -S "$CHAIN" 2>/dev/null | grep -E -- "(--comment[[:space:]]+${COMMENT_TAG}\b|comment[[:space:]]+\"${COMMENT_TAG}\")" || true)"

  if [[ -z "$rules" ]]; then
    echo "未发现带 tag 的规则（可能还没执行 up，或 tag 不一致）。"
    return 0
  fi

  # 解析 ACCEPT / DROP 的来源与端口
  # 说明：我们只汇总本脚本生成的“接口=INTERFACE、tcp、multiport dports=PORTS”的规则
  local allow_list=()
  local drop_ports=""
  while IFS= read -r line; do
    [[ -z "$line" ]] && continue

    # 允许规则：抓 -s
    if [[ "$line" =~ -j[[:space:]]ACCEPT ]]; then
      if [[ "$line" =~ -s[[:space:]]([^[:space:]]+) ]]; then
        allow_list+=("${BASH_REMATCH[1]}")
      else
        allow_list+=("(未指定 -s)")
      fi
    fi

    # 拒绝规则：抓 dports
    if [[ "$line" =~ -j[[:space:]]DROP ]]; then
      if [[ "$line" =~ --dports[[:space:]]([^[:space:]]+) ]]; then
        drop_ports="${BASH_REMATCH[1]}"
      fi
    fi
  done <<< "$rules"

  # 若没抓到 drop_ports，就用默认 PORTS
  [[ -z "$drop_ports" ]] && drop_ports="$PORTS"

  # 允许列表去重（保持出现顺序）
  local uniq_allows=()
  local seen="|"
  local s
  for s in "${allow_list[@]}"; do
    if [[ "$seen" != *"|$s|"* ]]; then
      uniq_allows+=("$s")
      seen+="$s|"
    fi
  done

  echo
  echo "开放端口：TCP ${drop_ports}（入站接口：${INTERFACE}）"
  echo

  if [[ ${#uniq_allows[@]} -gt 0 ]]; then
    echo "✅ 允许访问的源地址/网段："
    for s in "${uniq_allows[@]}"; do
      echo "  - ${s}  ->  TCP ${drop_ports}"
    done
  else
    echo "✅ 允许访问的源地址/网段：无（当前等同于全拒绝）"
  fi

  echo
  echo "⛔ 其他所有来源："
  echo "  - 其他任意源地址  ->  TCP ${drop_ports}（DROP）"

  echo
  echo "---- 规则明细（仅本脚本）----"
  echo "$rules"
}

case "${1:-}" in
  up)   apply_rules ;;
  down) remove_rules ;;
  view) view_rules ;;
  *)
    echo "用法: $0 {up|down|view}"
    exit 1
    ;;
esac

```