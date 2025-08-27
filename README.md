# MPEGDASH

สร้าง MPEG DASH Streaming Server แบบง่าย ๆ
โดยจะเป็นการใช้ไฟล์ mp4 วนลูป Stream ออกไป โดยเราจะใช้ โปรแกรม 2 ตัวในการทำงานนี้คือ ffmpeg สำหรับการเปลี่ยน Codec file และใช้ในการทำ สร้าง MPEG DASH Streaming 
และ ใช้ python ในการ run เป็น web Server (ทำได้ทั้ง Windows และ Linux)

1. เตรียมไฟล์ h.265
หากเป็นไฟล์ ที่ codec เป็นแบบ h.264 ต้องแปลง Codec ของไฟล์ ก่อน
  ffmpeg -i bbb1080.mp4 -c:v libx265 -vf "scale=1280:720" -crf 28 -preset fast -c:a aac -ac 2 -b:a 128k -movflags +faststart bbb720h265.mp4

นำไฟล์ไปวางบนเครื่องที่จะเป็น Server

2. ในเครื่องที่จะเป็น Server 
  - สร้าง folder /var/www/html   เพื่อใช้เป็น root ให้กับ Web Server
  -  จาก /var/www/html Run As Web Server ด้วยคำสั่ง    python3 -m http.server 8080  (8080 หรือ 80 สำหรับ Port ปกติของเว็บ)

3. จาก Folder ที่เก็บ ไฟล์ Video Run คำสั่งสำรับสร้าง Stream 
(กรณี ไฟล์ mp4 code เป็น h.264 ต้องทำการ Codec ใหม่ (-c:v libx265)) : ffmpeg -re -stream_loop -1 -i bbb720h265.mp4 -c:v libx265 -crf 28 -preset fast -c:a aac -ac 2 -b:a 128k -f dash -window_size 5 -extra_window_size 5 -remove_at_exit 1 /var/www/html/manifest.mpd

(กรณี ไฟล์ mp4 code เป็น h.265 อยู่แล้ว)  ffmpeg -re -stream_loop -1 -i bbb720h265.mp4 -c:v copy -c:a copy -f dash -window_size 5 -extra_window_size 5 -remove_at_exit 1 /var/www/html/manifest.mpd

การเล่นไฟล์
1. ใช้ vle open network stream : http://radiostl2.mcot.net/manifest.mpd  หรือ
2. ใช้ ffplay -stats http://radiostl2.mcot.net/manifest.mpd
