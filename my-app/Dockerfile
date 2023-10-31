# Sử dụng hình ảnh chính thống của Node.js
FROM node:14

# Tạo thư mục làm việc và đặt nó làm thư mục làm việc mặc định
WORKDIR /app

# Sao chép tệp package.json và package-lock.json vào thư mục làm việc
# COPY package*.json .
COPY . .

# Cài đặt các phụ thuộc của ứng dụng
# RUN npm install
RUN npm install express dotenv express-handlebars body-parser mysql2
RUN npm install --save-dev nodemon