# Bài 3: Tách biệt luồng CI/CD đa môi trường (Staging & Production) với GitHub Environments

## Mục tiêu
- Phân tầng luồng triển khai liên tục (CD) thành 2 môi trường chuyên biệt: **Staging** (Kiểm thử tiền phát hành) và **Production** (Môi trường vận hành thực tế).
- Ứng dụng tính năng GitHub Environments và Environment Protection Rules để thiết lập cổng kiểm duyệt phê duyệt thủ công (Manual Approval Gate).
- Đảm bảo mã nguồn chỉ được đẩy lên Production sau khi đã deploy và kiểm thử thành công trên Staging.

---

## 1. Cấu trúc Dự án
```text
ss12/b3/
├── .github/
│   └── workflows/
│       └── cd-multi-env.yml    # Pipeline triển khai đa môi trường
├── gradle/wrapper/
├── src/
│   └── main/java/com/example/demo/DemoApplication.java
├── build.gradle
├── settings.gradle
├── gradlew
└── README.md
```

---

## 2. Kiến trúc Luồng Triển khai (Pipeline DAG)
```text
[ Job: build ]
       │
       ▼
[ Job: deploy-staging ] (Tự động deploy khi có commit mới)
       │
       ▼ (Yêu cầu: Nhánh 'main' & Phê duyệt thủ công)
[ Job: deploy-production ]
```

---

## 3. Cấu hình GitHub Environments
1. Truy cập **Settings** -> **Environments** trong repository GitHub:
   - Tạo môi trường `staging`: Không yêu cầu reviewer, cho phép deploy từ nhánh `develop` và `main`.
   - Tạo môi trường `production`:
     - Bật tùy chọn **Required reviewers** (chọn Tech Lead / DevOps Admin).
     - Giới hạn nhánh được deploy: Chỉ cho phép `main` (Deployment branches: Selected branches -> `main`).
2. Biến môi trường (`vars`) và Secrets được cấu hình riêng biệt cho từng môi trường để tránh nhầm lẫn thông số database hay API key.
