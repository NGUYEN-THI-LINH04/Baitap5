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

Mở Node-RED

Trên trình duyệt Windows mở: http://192.168.44.134:1880

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/6fc79d29-9bde-4f2d-a352-22f360b25173" />

Import flow

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/ecac070b-885c-4fd3-b25d-11721ec0926f" />

- LƯU LỊCH SỬ VÀO INFLUXDB

- Mở InfluxDB

Trình duyệt mở: http://192.168.44.134:8086

<img width="941" height="1056" alt="image" src="https://github.com/user-attachments/assets/4760e2e9-a73f-4b39-80b2-a547621b3dff" />

Tạo tài khoản

<img width="941" height="1066" alt="image" src="https://github.com/user-attachments/assets/340e83bc-854b-45ef-8fd4-e88f0672f5af" />

CÀI NODE INFLUXDB CHO NODE-RED

<img width="494" height="539" alt="image" src="https://github.com/user-attachments/assets/89018283-c538-48ba-8ca4-198be777dc6d" />

Double click node influxdb

Điền cấu hình

<img width="941" height="939" alt="Screenshot 2026-06-04 160941" src="https://github.com/user-attachments/assets/72f715e5-377b-4959-b0f0-af0108bfa816" />

<img width="691" height="924" alt="image" src="https://github.com/user-attachments/assets/ef9b8847-2011-4095-93cf-fcdcc2908301" />

- Node-Red lúc đầu để xem dữ liệu vào InfluxDB chưa

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f2d0c9e6-8532-4ab0-8273-4558a85b2423" />

- Test InfluxDB có dữ liệu chưa

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/5a5d4966-5100-4cad-9689-dd3d7553fc0d" />

- GRAFANA VẼ BIỂU ĐỒ

- Mở Grafana

   + Mở: http://192.168.44.134:3000

<img width="941" height="1055" alt="image" src="https://github.com/user-attachments/assets/42e58f2a-a8a6-4217-ad45-a73a193a5127" />

Cấu hình datasource

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/abe9b566-0770-4276-a2ff-e1df15fdd9bb" />

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/92c16eaf-9807-4d09-8298-51370df87536" />

- TẠO DASHBOARD BIỂU ĐỒ REALTIME

- Tạo query nhiệt độ

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/93c90793-afc0-4f0d-9bc2-aef98ab5c9b5" />

- Thêm độ ẩm

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/faee6a40-18c9-442a-93ca-a566b831ca0e" />

- Thêm áp suất

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e2f6f064-be03-4761-bbc1-a8679def0a36" />

- Tạo cảnh báo nhiệt độ cao

- Đặt tên cảnh báo: High Temperature Alert

  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/61d8b02f-cb2e-4c35-a161-789cb23a2813" />

- Kết quả

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/b1e28758-8ae6-43d5-9125-1e543c00c3d6" />

- TẠO FRONTEND WEB
  + mở: cd frontend
   + tạo file: nano index.html

<img width="941" height="1065" alt="image" src="https://github.com/user-attachments/assets/434596f9-49c1-4f7c-abbc-3f651d937c2b" />

- tạo nginx config
  +  Mở: cd nginx
  + Tạo file: nano default.conf

<img width="941" height="483" alt="image" src="https://github.com/user-attachments/assets/625240a4-7acf-40cb-b861-3a4458e4b538" />

Mở: http://192.168.44.134:8080

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fc538eac-845a-49f4-b36f-d0b3d106175e" />


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8c1c733a-47eb-44d8-82f8-b9a8701cfd7c" />

