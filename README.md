# Demo: Triển khai ứng dụng NodeJS + MySQL bằng Docker Compose

Repo này sẽ demo cách setup file docker-compose như thế nào để có thể triển khai ứng dụng một cách dễ dàng.

>Source code tham khảo từ Repo: https://github.com/First-Cloud-Journey/000004-EC2.git

Sau đây, chúng ta sẽ tiến hành cài đặt ứng dụng từ cách thủ công đến cách dùng Docker để xem những điểm khác nhau và sự tiện lợi mà Docker mang lại.

Trước khi bắt đầu, giới thiệu qua cấu trúc ứng dụng một chút:
- Đây là ứng dụng website chạy bằng NodeJS.
- Kết nối với database MySQL

Nhiệm vụ phải làm:
- Cài đặt database MySQL, tạo user/password, bảng và insert dữ liệu dummy trong file <code>/my-app/db/user-dummy-data.sql</code> vào database.
- Triển khai ứng dụng:
    - Cài đặt NodeJS, npm.
    - Cài đặt thư viện phụ thuộc từ npm
    - Khởi chạy server


## I. Cách cài đặt thủ công trên máy local

### 1. Tạo database MySQL:

Làm theo hướng dẫn sau để biết cách cài đặt: https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04

Sau khi setup xong thì truy cập vào database paste code SQL từ file ***user-dummy-data.sql*** vào cmd để hoàn tất.

>Nhận xét: Setup khá công phu và phức tạp

### 2. Triển khai ứng dụng:

Làm theo hướng dẫn sau để cài đặt: https://000006.awsstudygroup.com/vi/2-prerequiste/2.9-deployapp/

>Lưu ý đổi thông tin DB_HOST -> 'localhost' vì db bây giờ đang được host ở máy local chứ không phải trên EC2.

## II. Triển khai bằng Docker

Việc triển khai bằng Docker sẽ mang lại những lợi ích sau:
- Dễ dàng triển khai ở bất cứ đâu chỉ cần có Docker.
- Không làm nhiễu tài nguyên hiện có ở máy: vì khi cài và xoá nó tạo riêng container để quản lý, dễ dàng dọn dẹp.
- CHUYÊN NGHIỆP ^^!

### 1. Tạo Dockerfile cho ứng dụng

Từ những gì đã làm để setup ứng dụng thủ công, chúng ta sẽ chuyển chúng vào file Dockerfile để tự động quá trình này mà không cần phải gõ từng lệnh nữa.

Trước tiên, thay đổi cấu hình chạy nodemon trong file package.json trước. Lý do là vì khi chạy *docker compose up* thì ứng dụng sẽ không kịp nhận IP của database nên cần thêm <code>--exitcrash</code> để ứng dụng tự động tắt để start lại cho đến khi kết nối được vào database.
><code>nodemon --exitcrash app.js</code>

Dockerfile như sau:

```shell
# Sử dụng hình ảnh chính thống của Node.js
FROM node:14

# Tạo thư mục làm việc và đặt nó làm thư mục làm việc mặc định
WORKDIR /app

# Sao chép toàn bộ file trong folder my-app vào folder app ở container
COPY . .

# Cài đặt các phụ thuộc của ứng dụng
# RUN npm install #Lệnh này để install những thư viện trong file package.json
# Lệnh này để cài đặt trực tiếp những thư viện (cài cách này sẽ cài version mới nhất của các thư viện)
RUN npm install express dotenv express-handlebars body-parser mysql2
RUN npm install --save-dev nodemon
```

Việc chuẩn bị môi trường cho ứng dụng đã xong, bây giờ tạo file docker-compose để tạo ra các service và thông số khác có liên quan.

Xem file ***docker-compose.yml*** để hiểu rõ hơn.