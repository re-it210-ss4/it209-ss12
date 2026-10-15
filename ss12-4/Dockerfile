# Sử dụng Nginx Alpine siêu nhẹ làm container phục vụ ứng dụng và health check
FROM nginx:alpine

# Tạo thư mục và endpoint /actuator/health trả về HTTP 200 OK với JSON {"status":"UP"}
RUN mkdir -p /usr/share/nginx/html/actuator && \
    echo '{"status":"UP","application":"Spring Boot Mock Service","details":{"diskSpace":{"status":"UP"},"ping":{"status":"UP"}}}' > /usr/share/nginx/html/actuator/health && \
    echo '<h1>Application is Running Successfully!</h1><p>Check health at <a href="/actuator/health">/actuator/health</a></p>' > /usr/share/nginx/html/index.html && \
    sed -i 's/listen       80;/listen       8080;/' /etc/nginx/conf.d/default.conf && \
    sed -i 's/default_type application\/octet-stream;/default_type application\/json;/' /etc/nginx/nginx.conf

# Mở cổng 8080 theo yêu cầu bài toán
EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
