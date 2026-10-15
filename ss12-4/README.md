# Hướng dẫn hoàn thành Bài 4: Tự động triển khai ứng dụng lên VPS qua SSH

## 1. Cấu hình GitHub Repository Secrets
Truy cập vào repository trên GitHub:
`Settings` -> `Secrets and variables` -> `Actions` -> Chọn **New repository secret** và thêm 3 secrets sau:

| Secret Name | Giá trị mẫu / Mô tả |
| :--- | :--- |
| `SSH_HOST` | Địa chỉ IP Public của VPS (ví dụ: `103.176.xxx.xxx` hoặc `192.168.1.100`) |
| `SSH_USER` | Tên người dùng SSH trên VPS (ví dụ: `root` hoặc `ubuntu`) |
| `SSH_KEY` | Toàn bộ nội dung Private Key SSH (`~/.ssh/id_rsa` hoặc `~/.ssh/id_ed25519`) bao gồm cả phần `-----BEGIN ... PRIVATE KEY-----` và `-----END ... PRIVATE KEY-----` |

> **Lưu ý trên VPS:**
> 1. Đảm bảo Public Key tương ứng đã được thêm vào file `~/.ssh/authorized_keys` trên VPS.
> 2. Đảm bảo VPS đã cài đặt Docker và user SSH có quyền chạy `docker` (`sudo usermod -aG docker $USER`).
> 3. Cổng 8080 và 22 trên VPS đã được mở trong Firewall / Security Group.

---

## 2. Quy trình Pipeline (.github/workflows/deploy-vps.yml)
Pipeline gồm 2 jobs chạy tuần tự:
1. **`build-image`**:
   - Tự động checkout mã nguồn và chuẩn hóa tên Docker Image thành chữ thường theo chuẩn OCI/Docker.
   - Đăng nhập vào GitHub Container Registry (`ghcr.io`).
   - Tự động build Docker Image từ `Dockerfile` (hỗ trợ endpoint `/actuator/health` trả về `HTTP 200 OK`) và push lên `ghcr.io`.
2. **`deploy-vps`** (chạy sau `build-image` qua khai báo `needs: build-image`):
   - Sử dụng action `appleboy/ssh-action@v1.0.3` để SSH vào VPS.
   - Đăng nhập GHCR trên VPS bằng `GITHUB_TOKEN`.
   - Thực thi kéo image mới nhất: `docker pull ghcr.io/<username>/app:latest`.
   - Dừng và xóa container cũ nếu tồn tại, khởi chạy lại container:
     ```bash
     docker stop app || true && docker rm app || true && docker run -d --name app -p 8080:8080 $IMAGE
     ```

---

## 3. Kiểm tra và Nghiệm thu
1. Commit và push code lên nhánh `main`:
   ```bash
   git add .
   git commit -m "feat: complete bai 4 deploy to vps with build-image and deploy-vps jobs"
   git push origin main
   ```
2. Vào tab **Actions** trên GitHub:
   - Theo dõi log của job `build-image` (xanh - thành công).
   - Theo dõi log của job `deploy-vps` (xanh - SSH và Docker commands thực thi thành công). Chụp ảnh màn hình log này để nộp bài.
3. Mở trình duyệt truy cập:
   ```
   http://<VPS_IP>:8080/actuator/health
   ```
   Xác nhận trả về:
   ```json
   {"status":"UP","application":"Spring Boot Mock Service","details":{"diskSpace":{"status":"UP"},"ping":{"status":"UP"}}}
   ```
   (HTTP status 200 OK).
