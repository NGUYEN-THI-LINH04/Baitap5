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
## LÝ THUYẾT

## 1. Docker là gì?

Docker là một nền tảng mã nguồn mở giúp đóng gói, triển khai và chạy ứng dụng trong các môi trường độc lập gọi là **container**.

Container chứa đầy đủ các thành phần cần thiết để ứng dụng hoạt động như:

* Source code
* Runtime
* Libraries (thư viện)
* Dependencies
* Biến môi trường

Nhờ đó, ứng dụng có thể chạy giống nhau trên mọi môi trường khác nhau như:

* Máy cá nhân
* Máy chủ nội bộ
* Cloud server
* Máy không có Internet

### Ví dụ

Nếu một ứng dụng Node.js chạy tốt trên laptop cá nhân bằng Docker thì khi chuyển sang máy chủ khác, ứng dụng vẫn chạy ổn định mà không bị lỗi khác phiên bản thư viện hay hệ điều hành.

### Các thành phần chính của Docker

* **Docker Engine**: môi trường chạy Docker
* **Docker Image**: khuôn mẫu chứa ứng dụng và thư viện
* **Docker Container**: phiên bản đang chạy của image
* **Docker Hub**: kho chứa image trực tuyến

Ví dụ chạy nginx:

```bash
docker run nginx
```

Lệnh trên sẽ:

1. Tải image nginx (nếu chưa có)
2. Tạo container
3. Chạy web server nginx

---

## 2. Các keyword trong docker-compose.yml

File `docker-compose.yml` dùng để mô tả và quản lý nhiều container cùng lúc.

### 2.1 version

Xác định phiên bản cấu hình Docker Compose.

Ví dụ:

```yaml
version: '3.8'
```

**Ý nghĩa:**
Giúp Docker hiểu cú pháp compose đang sử dụng.

---

### 2.2 services

Dùng để khai báo các service (ứng dụng/container).

Ví dụ:

```yaml
services:
  web:
    image: nginx
```

**Ý nghĩa:**
Mỗi service tương ứng với một container.

---

### 2.3 image

Xác định image được dùng để tạo container.

Ví dụ:

```yaml
image: nginx
```

**Ý nghĩa:**
Sử dụng image nginx có sẵn.

---

### 2.4 build

Dùng để build image từ Dockerfile.

Ví dụ:

```yaml
build: .
```

Hoặc:

```yaml
build:
  context: .
  dockerfile: Dockerfile
```

**Ý nghĩa:**
Docker sẽ tự build image từ source code.

---

### 2.5 container_name

Đặt tên cho container.

Ví dụ:

```yaml
container_name: my-nginx
```

**Ý nghĩa:**
Dễ quản lý container hơn.

---

### 2.6 ports

Mapping cổng máy thật với container.

Ví dụ:

```yaml
ports:
  - "8080:80"
```

**Ý nghĩa:**
Port `8080` trên máy thật sẽ ánh xạ tới port `80` trong container.

Truy cập:

```text
localhost:8080
```

để vào nginx.

---

### 2.7 volumes

Lưu trữ dữ liệu hoặc đồng bộ dữ liệu.

Ví dụ:

```yaml
volumes:
  - ./html:/usr/share/nginx/html
```

**Ý nghĩa:**
Đồng bộ thư mục local với container.

Ví dụ lưu database:

```yaml
volumes:
  - mysql_data:/var/lib/mysql
```

---

### 2.8 environment

Khai báo biến môi trường.

Ví dụ:

```yaml
environment:
  MYSQL_ROOT_PASSWORD: 123456
```

**Ý nghĩa:**
Truyền cấu hình vào container.

---

### 2.9 depends_on

Khai báo container phụ thuộc.

Ví dụ:

```yaml
depends_on:
  - mysql
```

**Ý nghĩa:**
Container web sẽ chạy sau mysql.

---

### 2.10 restart

Tự khởi động lại container.

Ví dụ:

```yaml
restart: always
```

**Ý nghĩa:**
Container sẽ tự chạy lại khi bị lỗi hoặc máy khởi động lại.

---

### 2.11 networks

Kết nối các container trong cùng mạng.

Ví dụ:

```yaml
networks:
  - app-network
```

Khai báo network:

```yaml
networks:
  app-network:
```

**Ý nghĩa:**
Cho phép container giao tiếp với nhau.

Ví dụ: Node-RED kết nối InfluxDB bằng tên service.

---

### 2.12 command

Ghi đè lệnh chạy mặc định.

Ví dụ:

```yaml
command: npm start
```

**Ý nghĩa:**
Chạy ứng dụng bằng lệnh tùy chỉnh.

---

### Ví dụ docker-compose.yml hoàn chỉnh

```yaml
version: '3.8'

services:
  nginx:
    image: nginx
    container_name: my-nginx
    ports:
      - "8080:80"
    restart: always

  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

---

## 3. Ưu điểm khi triển khai ứng dụng bằng Docker

### 3.1 Dễ triển khai

Không cần cài thủ công:

* Thư viện
* Runtime
* Dependency

Chỉ cần chạy container là ứng dụng hoạt động.

### 3.2 Đồng nhất môi trường

Ứng dụng chạy giống nhau trên mọi máy.

Tránh lỗi:

> "Chạy trên máy em được nhưng sang máy khác lỗi"

### 3.3 Dễ mở rộng

Có thể chạy nhiều container cùng lúc.

Ví dụ:

```bash
docker compose up --scale web=3
```

### 3.4 Tiết kiệm tài nguyên

Docker nhẹ hơn máy ảo:

* Khởi động nhanh
* Tốn ít RAM
* Không cần cài hệ điều hành riêng

### 3.5 Dễ backup và restore

Có thể export và khôi phục container nhanh chóng.

### 3.6 Quản lý nhiều service dễ dàng

Ví dụ hệ thống gồm:

* Node-RED
* InfluxDB
* Grafana
* Nginx

Tất cả quản lý bằng:

```text
docker-compose.yml
```

---

## 4. Triển khai app Docker lên máy chủ KHÔNG có Internet

### Bước 1: Tạo và test app trên laptop cá nhân

Chạy ứng dụng:

```bash
docker compose up -d
```

Kiểm tra:

* App hoạt động bình thường
* Không lỗi container
* Database hoạt động ổn định

---

### Bước 2: Kiểm tra image đang sử dụng

```bash
docker images
```

Ví dụ các image:

* nginx
* mysql
* node-red
* influxdb
* grafana

---

### Bước 3: Export image ra file

```bash
docker save -o app-images.tar nginx mysql grafana influxdb node-red
```

**Ý nghĩa:**
Gộp toàn bộ image thành file `.tar`.

---

### Bước 4: Copy source code và file cấu hình

Copy các file:

```text
docker-compose.yml
.env
source code
volume backup
```

---

### Bước 5: Chép sang máy chủ thật

Có thể dùng:

* USB
* Ổ cứng ngoài
* LAN nội bộ

Copy:

```text
app-images.tar
docker-compose.yml
source code
```

sang máy chủ.

---

### Bước 6: Cài Docker trên máy chủ

Cài:

* Docker Engine
* Docker Compose

Không cần Internet nếu đã có image.

---

### Bước 7: Import image

```bash
docker load -i app-images.tar
```

Kiểm tra:

```bash
docker images
```

---

### Bước 8: Khởi chạy ứng dụng

```bash
cd my-project
docker compose up -d
```

---

### Bước 9: Kiểm tra hoạt động

Kiểm tra container:

```bash
docker ps
```

Xem log:

```bash
docker logs ten-container
```

Kiểm tra web:

```text
http://IP-may-chu:PORT
```

---

## Kết luận

Docker giúp triển khai ứng dụng nhanh, đồng nhất và dễ quản lý. Việc đóng gói ứng dụng bằng container giúp tránh lỗi khác môi trường và hỗ trợ triển khai ngay cả trên máy chủ không có Internet thông qua export/import Docker image.


## THỰC HÀNH

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

- ALERT DỮ LIỆU BẤT THƯỜNG

- Tạo 2 rule alert
  + Alert HIGH

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/61d8b02f-cb2e-4c35-a161-789cb23a2813" />

- Kết quả

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/b1e28758-8ae6-43d5-9125-1e543c00c3d6" />

  + TẠO ALERT LOW

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3cdf65df-d9fb-4cf9-8bc7-9e04353e6d17" />

- NODE-RED + TELEGRAM BOT ALERT

- Tạo bot

  <img width="828" height="1792" alt="image" src="https://github.com/user-attachments/assets/194857d1-820c-480c-b4c6-38423efdef2b" />

- Add bot vào group

<img width="828" height="1792" alt="image" src="https://github.com/user-attachments/assets/3fb09559-0523-4ff5-a650-04d6baf53897" />

- Cài node Telegram trong Node-RED

<img width="627" height="911" alt="image" src="https://github.com/user-attachments/assets/a253df79-12eb-430c-b4e7-e147993654fd" />

- Kéo node telegram sender
  ``` text
  telegram sender
  ```
Cấu hình bot

<img width="950" height="966" alt="Screenshot 2026-06-04 224938" src="https://github.com/user-attachments/assets/11e1c28c-21a1-4c9c-b21d-ea55a4e69e06" />

- Thêm node Switch
+ Ở bên trái Node-RED tìm: switch

- Cấu hình switch

<img width="635" height="913" alt="image" src="https://github.com/user-attachments/assets/d6ef1129-907d-4fe6-ba5d-b9629b48e8ba" />

- Thêm  function node

  <img width="821" height="930" alt="image" src="https://github.com/user-attachments/assets/49420e23-7b9e-42c7-9516-9a6fc718e438" />

- Đây là hình Node-Red hoàn chỉnh

<img width="941" height="529" alt="image" src="https://github.com/user-attachments/assets/008158d0-0ffa-43ab-ad71-4822570392ef" />

- Kết quả khi nó báo về bot

<img width="828" height="1792" alt="image" src="https://github.com/user-attachments/assets/bb9837ab-72a3-45cf-8fcb-9c54b361dd35" />

<img width="828" height="1792" alt="image" src="https://github.com/user-attachments/assets/439a1766-ff48-4bd5-9cfc-32cd93533726" />

Một số container lớn như Grafana, Node-RED không export được do giới hạn dung lượng máy ảo Ubuntu (20GB), hệ thống báo:

No space left on device

<img width="941" height="96" alt="image" src="https://github.com/user-attachments/assets/8dd22c36-d1fc-461c-9ed9-c24b511fc5ac" />

- xuất tất cả các container ra file nén.
 
 <img width="941" height="380" alt="image" src="https://github.com/user-attachments/assets/dcab9040-5385-4077-a708-9ee2d0f218c7" />

- xoá mọi container đang chạy
  
  + chạy lệnh này 
``` text
docker compose down
```
<img width="1085" height="368" alt="image" src="https://github.com/user-attachments/assets/b77cbdff-db4a-4b41-8fbe-ca9b8f2a26a6" />

- load lại các container  từ file nén để khôi phục các container đã xoá

- docker load -i flaskapi.tar

- docker load -i influxdb.tar

- docker load -i nginx.tar

MariaDB:

- gunzip mariadb.tar.gz

- docker load -i mariadb.tar
  
<img width="909" height="233" alt="image" src="https://github.com/user-attachments/assets/e3e07404-2e4d-4147-b33b-860debe9914d" />

<img width="941" height="271" alt="image" src="https://github.com/user-attachments/assets/48f87b5f-b01f-47f7-a014-5308112226d9" />

- Khởi động lại hệ thống
``` text
docker compose up -d
```
Kiểm tra:
``` text
docker ps
```
<img width="952" height="778" alt="image" src="https://github.com/user-attachments/assets/9b5fd115-8097-4b3c-b321-33bd1a2cea5e" />




