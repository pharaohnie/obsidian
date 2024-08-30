---
{"dg-publish":true,"permalink":"/技术文档/文本/安装 NEO4J/","tags":["neo4j"]}
---

```bash
apt install gnupg
wget -O - https://debian.neo4j.com/neotechnology.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/neotechnology.gpg
echo 'deb [signed-by=/etc/apt/keyrings/neotechnology.gpg] https://debian.neo4j.com stable latest' | sudo tee -a /etc/apt/sources.list.d/neo4j.list
apt update
apt list -a neo4j
apt-get install neo4j=1:5.23.0
```

```bash
sed -i -e "s/^#server.default_listen_address/server.default_listen_address/g" /etc/neo4j/neo4j.conf
apt install iptables-persistent -y
iptables -A INPUT -p tcp --dport 7474 -s 43.255.200.0/24 -j ACCEPT
iptables -A INPUT -p tcp --dport 7474 -j DROP
iptables-save > /etc/iptables/rules.v4
neo4j-admin dbms set-initial-password 'xxxxx'
service neo4j restart
```


