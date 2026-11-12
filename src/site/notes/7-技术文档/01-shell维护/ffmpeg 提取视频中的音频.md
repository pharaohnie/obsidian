---
{"dg-publish":true,"permalink":"/7/01-shell/ffmpeg/","tags":["ffmpeg"],"dg-note-properties":{"tags":["ffmpeg"]}}
---



```bash
find . -type f \( -iname "*.mp4" -o -iname "*.mov" -o -iname "*.webm" \) -print0 | while IFS= read -r -d '' file; do ffmpeg -y -i "$file" -c:a libmp3lame -b:a 128k -q:a 0 -map a "${file%.*}.mp3"; done
```

