# Sử dụng môi trường Node.js siêu nhẹ
FROM node:18-alpine

# Tạo thư mục làm việc trong container
WORKDIR /app

# Copy file dữ liệu vào container
COPY db.json .

# Cài đặt thư viện json-server để chạy mock API
RUN npm install -g json-server

# Mở cổng 3000
EXPOSE 3000

# Lệnh khởi động server
CMD ["json-server", "--watch", "db.json", "--port", "3000", "--host", "0.0.0.0"]