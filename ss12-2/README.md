# Bài 2: Tự động hóa đóng gói và phát hành Docker Image khi gắn Git Tag

## Mục tiêu
- Thiết lập quy trình Release tự động kích hoạt mỗi khi lập trình viên gắn nhãn phiên bản (`git tag v*.*.*`).
- Biên dịch ứng dụng, tạo Docker Image có gắn nhãn tag cụ thể và tag `latest`.
- Đẩy Docker Image lên kho lưu trữ GitHub Container Registry (`ghcr.io`).

---

## 1. Cấu trúc Dự án
```text
ss12/b2/
├── .github/
│   └── workflows/
│       └── release.yml         # Pipeline phát hành Docker image theo Git Tag
├── gradle/wrapper/
├── src/
│   └── main/java/com/example/demo/DemoApplication.java
├── Dockerfile
├── build.gradle
├── settings.gradle
├── gradlew
└── README.md
```

---

## 2. Quy trình Kích hoạt Workflow qua Git Tag

```bash
# 1. Tạo tag phiên bản ngữ nghĩa (Semantic Versioning)
git tag -a v1.0.0 -m "Release version 1.0.0"

# 2. Đẩy tag lên GitHub
git push origin v1.0.0
```

Khi sự kiện đẩy tag được ghi nhận, GitHub Actions sẽ:
1. Trích xuất biến `${GITHUB_REF_NAME}` thành `v1.0.0`.
2. Đăng nhập vào GHCR với quyền hạn tự cấp của `${{ secrets.GITHUB_TOKEN }}`.
3. Tạo 2 thẻ image:
   - `ghcr.io/catim07/spring-app:v1.0.0`
   - `ghcr.io/catim07/spring-app:latest`

---

## 3. Kết luận
Quy trình giúp loại bỏ thao tác thủ công, đảm bảo mọi phiên bản phát hành đều tương ứng chính xác 1-1 với một mốc commit/tag trong lịch sử Git.
