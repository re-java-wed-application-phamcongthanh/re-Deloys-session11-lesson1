# Bài 1: Khởi Tạo Workflow Chạy Unit Test Tự Động Cho Java Spring Boot Với Gradle

## 🎯 1. Mục Tiêu
- Khởi tạo tệp kịch bản **GitHub Actions Workflow** cho dự án Java Spring Boot sử dụng Gradle Wrapper.
- Thiết lập môi trường chạy tự động trên GitHub Actions runner (`ubuntu-latest`) với **Java JDK 17 (Eclipse Temurin)**.
- Tự động hóa quá trình cấp quyền (`chmod +x gradlew`) và thực thi toàn bộ bài kiểm thử JUnit 5 (`./gradlew test`) mỗi khi có sự kiện `push` lên nhánh `main`.

---

## 📁 2. Cấu Trúc Thư Mục Dự Án
```text
bai1/
├── .github/
│   └── workflows/
│       └── ci.yml               # Kịch bản CI tự động hóa chạy Unit Test
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar   # Gradle Wrapper thực thi
│       └── gradle-wrapper.properties
├── src/
│   ├── main/java/com/example/demo/
│   │   └── DemoApplication.java      # Lớp khởi chạy Spring Boot
│   └── test/java/com/example/demo/
│       └── DemoApplicationTests.java  # Lớp kiểm thử JUnit 5
├── .gitignore                   # Loại trừ build artifacts
├── build.gradle                 # Cấu hình dự án & thư viện phụ thuộc
├── gradlew                      # Script thực thi Gradle trên Linux/macOS
├── gradlew.bat                  # Script thực thi Gradle trên Windows
├── settings.gradle              # Khai báo tên dự án
└── README.md                    # Tài liệu báo cáo & hướng dẫn kiểm tra
```

---

## ⚙️ 3. Chi Tiết Kịch Bản Workflow (`.github/workflows/ci.yml`)

```yaml
name: Java Spring Boot CI (Gradle)

on:
  push:
    branches: [ "main" ]

jobs:
  test:
    name: Run Unit Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v5

      - name: Thiết lập môi trường Java JDK 17
        uses: actions/setup-java@v5
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Cấp quyền cho Gradle Wrapper
        run: chmod +x gradlew

      - name: Thực thi JUnit Test với Gradle
        run: ./gradlew test
```

### Giải Thích Các Thành Phần:
1. **Trigger (`on: push: branches: [ "main" ]`)**:
   - Workflow sẽ tự động được kích hoạt mỗi khi lập trình viên thực hiện lệnh `git push` trực tiếp hoặc khi một Pull Request được merge vào nhánh `main`.
2. **Runner (`runs-on: ubuntu-latest`)**:
   - Sử dụng máy ảo Ubuntu phiên bản mới nhất do GitHub cung cấp trên đám mây, sạch sẽ và độc lập.
3. **Step 1 - Checkout mã nguồn (`actions/checkout@v5`)**:
   - Kéo toàn bộ mã nguồn của commit hiện tại về không gian làm việc `$GITHUB_WORKSPACE` của runner.
4. **Step 2 - Thiết lập môi trường Java (`actions/setup-java@v5`)**:
   - Tải về và cài đặt máy ảo Java JDK 17 với bản phân phối **Eclipse Temurin** (chuẩn Adoptium được khuyến nghị cho môi trường doanh nghiệp), đồng thời tự động cấu hình biến môi trường `JAVA_HOME` và bổ sung vào `PATH`.
5. **Step 3 - Cấp quyền cho Gradle Wrapper (`chmod +x gradlew`)**:
   - Đảm bảo tệp nhị phân shell script `gradlew` có quyền thực thi (`execute`) trên hệ điều hành Linux runner.
6. **Step 4 - Thực thi kiểm thử (`./gradlew test`)**:
   - Kích hoạt tác vụ `test` của Gradle để biên dịch mã nguồn test và chạy toàn bộ các ca kiểm thử JUnit 5 trong thư mục `src/test/java/`.

---

## 🧪 4. Mã Nguồn Kiểm Thử (`DemoApplicationTests.java`)

```java
package com.example.demo;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

@SpringBootTest
class DemoApplicationTests {

    @Test
    @DisplayName("Kiểm tra Spring Application Context load thành công")
    void contextLoads() {
        assertTrue(true, "Application context must load successfully");
    }

    @Test
    @DisplayName("Unit Test mẫu kiểm tra logic tính toán")
    void sampleUnitTest() {
        int expected = 42;
        int actual = 20 + 22;
        assertEquals(expected, actual, "Math calculation should match expected value");
    }
}
```

---

## ✅ 5. Quy Trình Kiểm Tra Trạng Thái Tích Xanh (Success) Trên GitHub

1. **Đẩy mã nguồn lên repository**:
   ```bash
   git add .
   git commit -m "feat: setup Java Spring Boot CI workflow with Gradle and JUnit 5"
   git push origin main
   ```
2. **Truy cập tab Actions trên GitHub Repository**:
   - Mở trình duyệt và truy cập:  
     `https://github.com/re-java-wed-application-phamcongthanh/re-Deloys-session11-lesson1/actions`
3. **Xác nhận kết quả thực thi**:
   - Workflow có tên **"Java Spring Boot CI (Gradle)"** sẽ tự động được kích hoạt.
   - Nhấp vào workflow run mới nhất để xem chi tiết Job **"Run Unit Tests"**.
   - Khi tất cả các bước (Checkout, Setup Java, Chmod Gradle, Run Gradle test) hoàn thành thành công, trạng thái sẽ hiển thị **dấu tích xanh (Success / Passing)**.
