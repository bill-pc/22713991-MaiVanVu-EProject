
## 🧩 Hệ thống Quản lý Bán hàng Microservices (EProject-Phase-1)

Dự án này mô phỏng một hệ thống thương mại điện tử cơ bản được phát triển theo **kiến trúc Microservices**, sử dụng **Node.js**, **Express**, **MongoDB**, và được **đóng gói bằng Docker**.
Mục tiêu của hệ thống là quản lý người dùng, sản phẩm và đơn hàng một cách tách biệt, đồng thời đảm bảo khả năng mở rộng linh hoạt.

---

## ⚙️ Kiến trúc tổng quan

Hệ thống được chia thành nhiều **dịch vụ (service)** độc lập. Mỗi service chịu trách nhiệm cho một phần nghiệp vụ riêng, giúp việc phát triển và triển khai trở nên dễ dàng hơn.

### Các thành phần chính:

* **API Gateway**:
  Là cổng giao tiếp duy nhất giữa client và hệ thống. Nó tiếp nhận tất cả các yêu cầu và định tuyến chúng đến các service tương ứng.

* **Authentication Service**:
  Đảm nhận chức năng **đăng ký**, **đăng nhập** và **xác thực người dùng** bằng **JWT (JSON Web Token)**.

* **Product Service**:
  Quản lý dữ liệu sản phẩm như tên, giá, tồn kho. Cho phép thực hiện các thao tác CRUD (CREATE, READ, UPDATE, DELETE).

* **Order Service**:
  Phụ trách xử lý đơn hàng: tạo đơn, lưu thông tin đặt hàng, và thông báo cập nhật tồn kho cho Product Service.

---

## 🔄 Cơ chế giao tiếp giữa các service

* **Giao tiếp đồng bộ (Synchronous)**:
  Các request từ client sẽ được gửi tới **API Gateway**, sau đó Gateway chuyển tiếp đến các service thông qua **HTTP RESTful API**.

* **Giao tiếp bất đồng bộ (Asynchronous)**:
  Các service trao đổi thông tin nội bộ bằng **RabbitMQ**.
  Ví dụ: khi một đơn hàng được tạo, Order Service sẽ gửi thông điệp tới Product Service để giảm số lượng hàng tồn.

---

## 🗄️ Cơ sở dữ liệu

Dự án tuân thủ nguyên tắc **“Database per Service”** — tức là **mỗi service có một cơ sở dữ liệu MongoDB riêng biệt**, giúp đảm bảo tính độc lập, dễ bảo trì và giảm thiểu rủi ro khi mở rộng.

---

## 🧰 Công nghệ sử dụng

| Thành phần           | Công nghệ              |
| -------------------- | ---------------------- |
| **Backend**          | Node.js, Express.js    |
| **Database**         | MongoDB (Mongoose ODM) |
| **Message Broker**   | RabbitMQ (amqplib)     |
| **Containerization** | Docker, Docker Compose |
| **Testing**          | Mocha, Chai, Chai-HTTP |

---

## 🗂️ Cấu trúc thư mục

```
.
├── api-gateway/          # Cấu hình API Gateway
├── auth/                 # Authentication Service
├── order/                # Order Service
├── product/              # Product Service
├── .env                  # Thông tin môi trường (RabbitMQ)
├── .gitignore            # Loại trừ các file/thư mục không cần đẩy lên Git
├── docker-compose.yml    # File cấu hình container
└── README.md             # Tài liệu hướng dẫn dự án
```

---

## 🚀 Hướng dẫn cài đặt và khởi chạy

### 🧩 Yêu cầu môi trường

Trước khi bắt đầu, hãy đảm bảo bạn đã cài đặt:

* [Docker Desktop](https://www.docker.com/products/docker-desktop/)
* [Docker Compose](https://docs.docker.com/compose/)
* [Git](https://git-scm.com/)

---

### ⚙️ Các bước thực hiện

#### 1️⃣ Clone dự án về máy:

```bash
git clone https://github.com/bill-pc/22713991-MaiVanVu-EProject.git
cd EProject-Phase-1
```

#### 2️⃣ Tạo các file `.env` cần thiết

Hệ thống sử dụng nhiều file `.env` để lưu biến môi trường.

#### 3️⃣ Xây dựng và chạy hệ thống với Docker Compose

```bash
docker-compose up --build -d
```

Tùy chọn:

* `--build`: xây dựng lại image.
* `-d`: chạy container ở chế độ nền.

---

#### 4️⃣ Kiểm tra container đang chạy

```bash
docker ps
```

Nếu thành công, bạn sẽ thấy khoảng **8 container** hoạt động (3 service + 3 DB + RabbitMQ + Gateway).

---

## 🧠 Cách sử dụng API

Tất cả các request sẽ đi qua **API Gateway** tại địa chỉ:

```
http://localhost:3003
```

| Chức năng              | Phương thức | Endpoint         | Yêu cầu token |
| ---------------------- | ----------- | ---------------- | ------------- |
| **Auth**               |             |                  |               |
| Đăng ký                | `POST`      | `/auth/register` | ❌ Không cần   |
| Đăng nhập              | `POST`      | `/auth/login`    | ❌ Không cần   |
| **Products**           |             |                  |               |
| Lấy danh sách sản phẩm | `GET`       | `/products`      | ✅ Có          |
| Thêm sản phẩm mới      | `POST`      | `/products`      | ✅ Có          |
| **Orders**             |             |                  |               |
| Tạo đơn hàng           | `POST`      | `/buy`        | ✅ Có          |
| Xem danh sách đơn hàng | `GET`       | `/buy`        | ✅ Có          |

---

## 🧪 Chạy test

Để test một service cụ thể (ví dụ `auth`):

```bash
cd auth
npm test
```

Lưu ý: các container phụ thuộc cần được bật trước khi chạy test.

---

## 📘 Ghi chú

* Dự án được thiết kế theo hướng mô-đun, dễ mở rộng thêm service mới.
* Có thể triển khai trên cloud (AWS, GCP, hoặc Azure) với cấu trúc microservices tương tự.
* Sử dụng JWT giúp xác thực an toàn, còn RabbitMQ giúp các service giao tiếp linh hoạt, giảm độ phụ thuộc.

---
Sinh viên thực hiện: Mai Văn Vũ
MSSV: 22713991
Lớp: DHHTTT18B
Trường: Đại học Công nghiệp TP. Hồ Chí Minh (IUH)
Dự án: EProject Phase 1 – Hệ thống Quản lý Bán hàng Microservices
GV hướng dẫn: Huỳnh Nam
