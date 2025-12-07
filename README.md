# 📘 **TheKhiem7 CloudHub – Project Documentation**

## 🌐 Giới thiệu

**TheKhiem7 CloudHub** là một **mini cloud platform** được xây dựng để vận hành nhiều backend service trên một hạ tầng duy nhất.
Dự án kết hợp **Cloudflare**, **Traefik**, **Docker**, và **Portainer** để tạo ra một framework linh hoạt, dễ mở rộng và có chi phí vận hành tối ưu.

CloudHub được thiết kế như một **multi-service hosting platform**, nơi mỗi dịch vụ có domain riêng, routing rõ ràng, và được quản lý tập trung thông qua giao diện trực quan.

---

## 🎯 Mục tiêu Dự án

### TheKhiem7 CloudHub hướng đến:

* Hỗ trợ **nhiều backend ASP.NET / microservices** chạy chung trên một EC2.
* Cung cấp **domain riêng** cho từng dịch vụ, dạng:

  ```
  <project>.duykhiem.id.vn
  ```
* Tối ưu chi phí bằng cách sử dụng **EC2 Spot Instance**.
* Chuẩn hóa mô hình kiến trúc theo phong cách PaaS (như Railway / Vercel).
* Đảm bảo dịch vụ hoạt động **ổn định và lâu dài**.
* Cho phép **dễ dàng thêm dịch vụ mới** mà không phá vỡ kiến trúc.

---

## 🧱 Thành phần Chính

### 1. **AWS EC2 (Spot Instance)**

* Máy chủ duy nhất chạy toàn bộ CloudHub.
* Đóng vai trò compute layer cho toàn bộ container.
* Chạy Docker + Traefik + Portainer + các service backend.

---

### 2. **Docker Containers**

Mỗi service trong hệ sinh thái được đóng gói thành container riêng:

| Service                    | Mục đích                                                     |
| -------------------------- | ------------------------------------------------------------ |
| **FengShuiKoi Consulting** | Nền tảng tư vấn/consulting theo logic nghiệp vụ riêng.       |
| **ClaimRequestSystem**     | Quản lý yêu cầu (claim) của người dùng/doanh nghiệp.         |
| **VitaFlow**               | Dịch vụ liên quan sức khỏe, wellness, hoặc workflow cá nhân. |
| **EzyFix**                 | Backend cho nền tảng sửa chữa/bảo trì đồ gia dụng.           |
| **EvoCare**                | Dịch vụ chăm sóc, bảo hành, đặt lịch…                        |
| **Portainer**              | Công cụ quản lý container & stack qua giao diện web.         |

Tất cả đều chạy độc lập nhưng chia sẻ chung hạ tầng CloudHub.

---

### 3. **Traefik – Reverse Proxy & Router**

Traefik là trái tim điều hướng nội bộ của CloudHub:

* Định tuyến request đến đúng service qua domain:

  ```
  ezyfix.duykhiem.id.vn → EzyFix service
  vitaflow.duykhiem.id.vn → VitaFlow service
  claim.duykhiem.id.vn → ClaimRequestSystem
  ...
  ```
* Tự động phát hiện container mới (Docker Provider).
* Middleware để chỉnh sửa header, bảo mật, throttling, v.v.
* Giảm thiểu việc mở nhiều port công khai trên EC2 — tất cả giao tiếp qua cổng duy nhất.

---

### 4. **Cloudflare – Public Gateway**

Cloudflare là mặt tiền Internet của CloudHub:

* DNS cho domain `duykhiem.id.vn`
* HTTPS miễn phí (Full / Full Strict)
* Proxy, WAF, performance
* Che giấu IP EC2, tăng tính bảo mật
* Route domain đến EC2 mà không cần tạo port phức tạp

**Mỗi service sẽ có domain:**

```
fengshuikoi.duykhiem.id.vn
claim.duykhiem.id.vn
vitaflow.duykhiem.id.vn
ezyfix.duykhiem.id.vn
evocare.duykhiem.id.vn
portainer.duykhiem.id.vn   (internal management)
```

---

### 5. **Portainer – Service Orchestration UI**

* Cho phép quản lý tất cả container và Docker Compose stack.
* Không cần SSH vào server cho các tác vụ đơn giản.
* Hữu ích khi vận hành lâu dài:

  * Restart service
  * Update image
  * Theo dõi log
  * Quản lý volume và network

---

## 🌐 Kiến trúc tổng quan (Concept)

```
Client
   │
   ▼
Cloudflare (DNS + Proxy + SSL)
   │
   ▼
AWS EC2 (Spot)
   │
   ▼
Traefik Reverse Proxy
 ┌───────────┬───────────┬───────────┬───────────┐
 ▼           ▼           ▼           ▼           ▼
FengShui   ClaimReq   VitaFlow    EzyFix      EvoCare
:8080       :8080       :8080       :8080       :8080
(Container) (Container) (Container) (Container) (Container)

Portainer (UI quản lý toàn bộ)
```

**Key idea:**
Traefik xử lý traffic, Docker vận hành service, Cloudflare đứng trước EC2, và Portainer quản lý toàn bộ môi trường phía sau.

---

## 💡 Giá trị cốt lõi của TheKhiem7 CloudHub

### ■ Tối ưu chi phí

Một EC2 Spot giá thấp → vận hành nhiều dịch vụ cùng lúc.

### ■ Mở rộng dễ dàng

Thêm dịch vụ mới = thêm 1 container + 1 domain.

### ■ Tách biệt logic cho từng service

Không ảnh hưởng lẫn nhau, dễ bảo trì.

### ■ Dễ quản lý

Portainer cung cấp UI thay vì phải dùng SSH.

### ■ Bảo mật tốt

Cloudflare che giấu IP thật, lọc traffic, SSL miễn phí.

### ■ Chuẩn hóa kiến trúc

Tương tự cách các nền tảng cloud chuyên nghiệp vận hành microservices.

---

## 📌 Phạm vi dự án

* Triển khai hạ tầng chung CloudHub
* Định nghĩa routing & domain cho từng service
* Tạo môi trường vận hành lâu dài
* Không tập trung vào triển khai chi tiết (Dockerfile, CI/CD, script…) trong tài liệu này
* Là nền tảng shared cho nhiều project khác nhau của TheKhiem7

---

## 🏁 Kết luận

**TheKhiem7 CloudHub** không chỉ là một server EC2 —
đây là **một nền tảng mini-cloud tự chủ**, hỗ trợ nhiều dịch vụ backend hoạt động ổn định, rõ ràng, có domain riêng, an toàn và linh hoạt.

CloudHub là giải pháp:

* tiết kiệm chi phí,
* hiện đại về kiến trúc,
* dễ quản lý và mở rộng,
* phù hợp để vận hành lâu dài nhiều sản phẩm cá nhân hoặc học thuật.