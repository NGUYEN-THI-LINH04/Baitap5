# Baitap5
# BÀI TẬP LỚN
## Môn: phát triển ứng dụng với mã nguồn mở - tee0421
Bt5:
  - lý thuyết: 
    + docker là gì? 
    + các keyword được sử dụng trong docker-compose.yml
      để mô tả 1 service, network, volume,...
      liệt kê + ý nghĩa của từ khoá đó + ví dụ minh hoạ
    + ưu điểm khi triển app sử dụng docker là gì?
    + dùng docker: tạo app, test app OK trên laptop cá nhân
      giờ muốn triển khai app này trên máy chủ thật ko có internet
      thì các bước cần làm là?
  - thực hành áp dụng: APP MONITOR + ALERT DATA REALTIME
    sử dụng docker compose có nhiều serivce 
    và các thành phần cần thiết để tạo thành ứng dụng:
     + nodered liên tục lấy dữ liệu từ nguồn nào đó (chứng khoán, thời tiết, giá vàng,...)
       nguồn thực tế, số liệu luôn động sau thời gian ngắn
     + nodered lưu trữ dữ liệu vào 2 database: mariadb để lưu giá trị tức thời
       lưu lịch sử vào influxdb
     + sử dụng grafana để trực quan hoá dữ liệu: vẽ biểu đồ
     + sử dụng nginx để làm webserver
       chạy 1 trang web html+js+css làm front-end
       js: lấy dữ liệu tức thời trong mariadb qua (ajax | socket) 
           gọi api (api tự build bằng Flask giống bt1)
           api trả về giá trị tức thời trong mariadb
           hiển thị lên web, auto hiển thị số mới khi thay đổi
       sử dụng iframe để gọi grafana
       hiển thị biểu đồ dữ liệu lịch sử của thông số đã lưu
     + QUAN SÁT DỮ LIỆU LỊCH SỬ => GIÁ TRỊ BẤT THƯỜNG
       (VD MIỀN A..B: OK, DƯỚI A: ALERT LOW, TRÊN B: ALERT HIGH)
     + nodered: kết hợp bot Telegram
       khi dữ liệu not OK, thì gửi tin nhắn từ bot => group trên telegram
       group đã add bot vào: (nhóm đã có 2 người), add thêm 1875746636 thành 3 người
       mỗi khi bot gửi dữ liệu vào nhóm: mọi member of group đều nhận đc
       nội dung alert: tường minh, có value gây alert

     xuất tất cả các container ra file nén.
     xoá mọi container đang chạy
     load lại các container  từ file nén để khôi phục các container đã xoá
============================================================================================
# BÀI LÀM
- Tạo thư mục
```text
mkdir ~/bt5-monitor
cd ~/bt5-monitor
```
<img width="941" height="251" alt="image" src="https://github.com/user-attachments/assets/72f74456-b922-474e-829a-fb481351cb83" />

- Tạo cấu trúc thư mục
``` text
mkdir api frontend nginx nodered grafana
```
<img width="941" height="127" alt="image" src="https://github.com/user-attachments/assets/759b87be-d37f-43ac-9333-4b2f64ccaaf2" />

- Tạo file compose
``` text
nano docker-compose.yml
```
<img width="941" height="1030" alt="image" src="https://github.com/user-attachments/assets/115f5a7d-51c3-4dc1-a011-86030a88b5ae" />

- Đi vào thư mục api

- Chạy:
``` text
cd ~/bt5-monitor/api
```

- Tạo file app.py

  + Chạy:
``` text
nano app.py
```
<img width="941" height="1000" alt="image" src="https://github.com/user-attachments/assets/a8c6f4a0-b3f5-4b7a-846c-82127ec2a5a3" />

- Tạo requirements.txt
  
<img width="941" height="446" alt="image" src="https://github.com/user-attachments/assets/3ce25d7e-ad0c-4728-8cb0-3e5df3fa005b" />

- Tạo Dockerfile

<img width="941" height="547" alt="image" src="https://github.com/user-attachments/assets/14ed79fc-1e3b-41a5-b739-4f5bac487363" />

- Kiểm tra đủ file chưa

  + Chạy:
``` text
ls
```
<img width="956" height="234" alt="image" src="https://github.com/user-attachments/assets/88c81a28-5f8c-4882-90a2-38cc6b2c8940" />

- Build và chạy container

  + Chạy lệnh này:
``` text
docker compose up -d --build
docker ps
``` 
<img width="941" height="753" alt="image" src="https://github.com/user-attachments/assets/c3c54995-2569-4417-ad38-7f143ff596a1" />

<img width="970" height="926" alt="image" src="https://github.com/user-attachments/assets/ca482704-2e7b-494c-b903-4a93f58c5d52" />

- Tạo database và table trong MariaDB

Chạy:
``` text
docker exec -it mariadb_monitor mysql -u root -p
```
- Chọn database
- Tạo table weather_data
  
<img width="941" height="498" alt="image" src="https://github.com/user-attachments/assets/dd10756f-7365-4d2f-b23f-0887159f8328" />

Kiểm tra table

Chạy:
``` text
SHOW TABLES;
```

<img width="941" height="537" alt="image" src="https://github.com/user-attachments/assets/22832b0f-f7bc-4a13-9c39-48569cdb5eb9" />

- Test API trong browser

+ http://192.168.44.134:5000

<img width="941" height="368" alt="image" src="https://github.com/user-attachments/assets/d77c5fba-eff2-4a6a-a613-a82ae9d30918" />

- Test API lấy dữ liệu database

Mở: http://192.168.44.134:5000/api/weather

<img width="941" height="454" alt="image" src="https://github.com/user-attachments/assets/7eacf4b8-0f54-42ac-9fe2-df2cd30456df" />

