---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/ffmpeg 提取视频中的音频/","tags":["ffmpeg"]}
---



```bash
find . -type f \( -iname "*.mp4" -o -iname "*.mov" -o -iname "*.webm" \) -print0 | while IFS= read -r -d '' file; do ffmpeg -y -i "$file" -c:a libmp3lame -b:a 128k -q:a 0 -map a "${file%.*}.mp3"; done
```

