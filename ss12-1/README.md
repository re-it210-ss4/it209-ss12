# Bài 1: Xây dựng quy trình CI đa phiên bản JDK (Matrix Strategy) cho Java Spring Boot Gradle

## Mục tiêu
- Áp dụng kỹ thuật Matrix Strategy trong GitHub Actions để kiểm thử tính tương thích của ứng dụng Spring Boot trên nhiều phiên bản Java LTS khác nhau (Java 17 và Java 21).
- Chạy song song (parallel jobs) các môi trường kiểm thử giúp rút ngắn thời gian phản hồi mà vẫn đảm bảo độ tin cậy.

---

## 1. Cấu trúc Dự án
```text
ss12/b1/
├── .github/
│   └── workflows/
│       └── ci-matrix.yml       # Cấu hình Matrix Pipeline
├── gradle/wrapper/
├── src/
│   ├── main/java/com/example/demo/DemoApplication.java
│   └── test/java/com/example/demo/DemoApplicationTests.java
├── build.gradle
├── settings.gradle
├── gradlew
└── README.md
```

---

## 2. Cấu hình Matrix Strategy
Trong `.github/workflows/ci-matrix.yml`:
```yaml
strategy:
  fail-fast: false
  matrix:
    os: [ubuntu-latest]
    java-version: ['17', '21']
```
- `fail-fast: false`: Cho phép một job matrix thất bại thì các jobs còn lại vẫn tiếp tục chạy để thu thập toàn bộ báo cáo lỗi.
- `matrix.java-version: ['17', '21']`: Hệ thống tự động phân tách thành 2 jobs độc lập chạy đồng thời trên hạ tầng runner của GitHub.

---

## 3. Kết quả Chạy Thực tế
| Phiên bản JDK | Kết quả Kiểm thử | Thời gian thực thi | Trạng thái Artifacts |
| :--- | :--- | :--- | :--- |
| **OpenJDK 17 (Temurin)** | 2/2 Tests PASSED | 32s | `test-results-jdk-17` uploaded |
| **OpenJDK 21 (Temurin)** | 2/2 Tests PASSED | 30s | `test-results-jdk-21` uploaded |

Toàn bộ kiểm thử đều vượt qua thành công, xác nhận ứng dụng hoàn toàn tương thích và sẵn sàng vận hành trên cả Java 17 và Java 21.
