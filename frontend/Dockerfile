# Sử dụng máy chủ web Nginx
FROM nginx:alpine

# Xóa file mặc định của Nginx
RUN rm -rf /usr/share/nginx/html/*

# Copy toàn bộ code Frontend (index.html, css...) vào thư mục gốc của Nginx
COPY . /usr/share/nginx/html

# Mở cổng 80 (cổng web tiêu chuẩn)
EXPOSE 80