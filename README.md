# BÁO CÁO THỰC HÀNH BUỔI 1: LẬP TRÌNH PYTHON VỚI GIAO THỨC MQTT

## 1. Cấu trúc thư mục dự án

```text
IOT-ThucHanh_1/
│
├── Bai_1/
│   ├── publisher_bai1.py          # Phát thông điệp gồm Họ tên, MSV
│   └── subscriber_bai1.py         # Nhận thông điệp và in kèm thời gian nhận
│
├── Bai_2/
│   ├── sensor_publisher_bai2.py   # Mô phỏng cảm biến gửi JSON định kỳ 3 giây
│   └── monitor_subscriber_bai2.py # Nhận dữ liệu và kiểm tra ngưỡng cảnh báo
│
├── Bai_3/
│   ├── device_bai3.py             # Thiết bị đèn nhận lệnh ON/OFF và báo trạng thái
│   └── controller_bai3.py         # App điều khiển người dùng nhập lệnh và xem trạng thái
│
├── README.md                      # Báo cáo tổng hợp toàn bộ bài thực hành (Markdown)
└── README.txt                     # Hướng dẫn tóm tắt tổng hợp (Plain Text)
```

---

## 2. Môi trường & Cấu hình MQTT Broker

### 2.1 Cài đặt thư viện
Yêu cầu Python 3.8+ và thư viện `paho-mqtt`:
```bash
pip install paho-mqtt
```

### 2.2 Cấu hình Broker
- **Loại Broker:** Local Eclipse Mosquitto Broker (hoặc Public Broker như `broker.emqx.io` / `broker.hivemq.com`)
- **Host / IP:** `localhost`
- **Port:** `1883`
- **Giao thức:** MQTT over TCP

---

## 3. Chi tiết từng bài thực hành

---

### 🔹 BÀI 1: Ứng dụng gửi và nhận thông điệp MQTT cơ bản

#### 1. Mô tả & Mục tiêu
- Xây dựng 2 ứng dụng Python độc lập:
  - **Publisher (`publisher_bai1.py`):** Đóng gói thông điệp Họ tên, Mã sinh viên và lời chào gửi lên Broker.
  - **Subscriber (`subscriber_bai1.py`):** Lắng nghe liên tục, tiếp nhận thông điệp và in ra màn hình kèm thời gian nhận thực tế.
- **Topic sử dụng:** `iot/lab/message`

#### 2. Cách chạy:
Mở 2 cửa sổ Terminal:
- **Terminal 1 (Chạy trước):**
  ```bash
  python Bai_1/subscriber_bai1.py
  ```
- **Terminal 2 (Gửi tin nhắn):**
  ```bash
  python Bai_1/publisher_bai1.py
  ```

#### 3. Kết quả thực thi thực tế:
- **Tại Terminal Publisher:**
  ```text
  Dang ket noi toi MQTT Broker...
  [*] Ket noi thanh cong toi Broker: localhost:1883

  --- DANG GUI MESSAGE ---
  Topic  : iot/lab/message
  Payload: Xin chao tu client Python MQTT - B23DCCN169 - Le Huy Duc
  [+] Da gui message thanh cong!
  [*] Da ngat ket noi.
  ```

- **Tại Terminal Subscriber:**
  ```text
  Dang ket noi toi MQTT Broker...
  [*] Ket noi thanh cong toi Broker: localhost:1883
  [*] Dang lang nghe tren topic: 'iot/lab/message'...
  [*] Nhan Ctrl+C de dung chuong trinh.
  ========================================

  Nhan duoc message:
  Topic  : iot/lab/message
  Payload: Xin chao tu client Python MQTT - B23DCCN169 - Le Huy Duc
  Time   : 10:15:20
  ----------------------------------------
  ```

---

### 🔹 BÀI 2: Mô phỏng cảm biến nhiệt độ & độ ẩm

#### 1. Mô tả & Mục tiêu
- **Sensor Publisher (`sensor_publisher_bai2.py`):** Mô phỏng thiết bị đo môi trường, sinh dữ liệu ngẫu nhiên (nhiệt độ 20–40°C, độ ẩm 30–90%) và tự động publish định kỳ **mỗi 3 giây** dưới dạng chuỗi JSON.
- **Monitoring Subscriber (`monitor_subscriber_bai2.py`):** Nhận bản tin, parse dữ liệu JSON, in ra các thông số và đưa ra cảnh báo:
  - Cảnh báo nhiệt độ cao khi $T > 35^\circ\text{C}$.
  - Cảnh báo độ ẩm thấp khi $H < 40\%$.
- **Topic sử dụng:** `iot/lab/sensor01/data`
- **Định dạng Payload (JSON):**
  ```json
  {
    "device_id": "sensor01",
    "temperature": 36.8,
    "humidity": 37.4
  }
  ```

#### 2. Cách chạy:
Mở 2 cửa sổ Terminal:
- **Terminal 1 (Subscriber):**
  ```bash
  python Bai_2/monitor_subscriber_bai2.py
  ```
- **Terminal 2 (Publisher):**
  ```bash
  python Bai_2/sensor_publisher_bai2.py
  ```

#### 3. Kết quả thực thi thực tế:
- **Terminal Sensor Publisher:**
  ```text
  Dang ket noi toi MQTT Broker...
  [*] Ket noi thanh cong toi Broker: localhost:1883
  [*] Bat dau gui du lieu cam bien dinh ky moi 3s len topic 'iot/lab/sensor01/data'...
  [*] Nhan Ctrl+C de dung chuong trinh.
  ========================================
  [1] Gui du lieu: {"device_id": "sensor01", "temperature": 28.5, "humidity": 65.2}
  [2] Gui du lieu: {"device_id": "sensor01", "temperature": 36.8, "humidity": 37.4}
  ```

- **Terminal Monitoring Subscriber:**
  ```text
  -----------------------------------
  Device: sensor01
  Temperature: 28.5 C
  Humidity: 65.2 %
  -----------------------------------

  -----------------------------------
  Device: sensor01
  Temperature: 36.8 C
  Humidity: 37.4 %
  >>> CANH BAO: Nhiet do cao <<<
  >>> CANH BAO: Do am thap <<<
  -----------------------------------
  ```

---

### 🔹 BÀI 3: Mô phỏng hệ thống điều khiển đèn thông minh (Giao tiếp 2 chiều)

#### 1. Mô tả & Mục tiêu
- Xây dựng mô hình tương tác 2 chiều Client-Device:
  - **Thiết bị đèn (`device_bai3.py`):** Subscribe topic nhận lệnh `iot/lab/light01/cmd`. Khi nhận được lệnh `ON` hoặc `OFF`, cập nhật trạng thái và publish trạng thái mới về topic `iot/lab/light01/status`.
  - **Bộ điều khiển (`controller_bai3.py`):** Cho phép người dùng nhập lệnh (`ON`, `OFF`, `EXIT`) từ bàn phím, gửi lệnh đi và lắng nghe phản hồi xác nhận từ thiết bị.
- **Topics sử dụng:**
  - Topic nhận lệnh: `iot/lab/light01/cmd`
  - Topic báo trạng thái: `iot/lab/light01/status`

#### 2. Cách chạy:
Mở 2 cửa sổ Terminal:
- **Terminal 1 (Khởi động thiết bị):**
  ```bash
  python Bai_3/device_bai3.py
  ```
- **Terminal 2 (Giao diện điều khiển):**
  ```bash
  python Bai_3/controller_bai3.py
  ```

#### 3. Kết quả thực thi thực tế:
- **Terminal Controller:**
  ```text
  --- HE THONG DIEU KHIEN DEN THONG MINH ---
  Cac lenh hop le: ON, OFF, EXIT

  Nhap lenh: ON
  [>] Da gui lenh ON toi light01
  [<] Trang thai nhan duoc tu [iot/lab/light01/status]:
      {"device_id": "light01", "status": "ON"}
  -----------------------------------

  Nhap lenh: OFF
  [>] Da gui lenh OFF toi light01
  [<] Trang thai nhan duoc tu [iot/lab/light01/status]:
      {"device_id": "light01", "status": "OFF"}
  -----------------------------------

  Nhap lenh: EXIT
  [*] Dang thoat chuong trinh...
  ```

- **Terminal Device:**
  ```text
  [*] Thiet bi 'light01' da ket noi toi Broker: localhost:1883
  [*] Dang lang nghe lenh tren topic: 'iot/lab/light01/cmd'
  =============================================
  [+] Da phan hoi trang thai: {"device_id": "light01", "status": "OFF"}

  [!] Nhan duoc lenh: 'ON' tu topic 'iot/lab/light01/cmd'
  [*] Chuyen trang thai den thanh: [ON]
  [+] Da phan hoi trang thai: {"device_id": "light01", "status": "ON"}

  [!] Nhan duoc lenh: 'OFF' tu topic 'iot/lab/light01/cmd'
  [*] Chuyen trang thai den thanh: [OFF]
  [+] Da phan hoi trang thai: {"device_id": "light01", "status": "OFF"}
  ```

---

## 4. Tổng kết & Đánh giá
| Tiêu chí | Bài 1 | Bài 2 | Bài 3 |
| :--- | :--- | :--- | :--- |
| **Mô hình MQTT** | 1-Way (Pub ➔ Sub) | 1-Way Periodic (Pub ➔ Sub) | 2-Way (Pub/Sub 🔁 Sub/Pub) |
| **Định dạng dữ liệu** | Chuỗi ký tự (Plain Text) | Chuỗi JSON (`device_id`, `temp`, `humidity`) | Lệnh Text (`ON`/`OFF`) & JSON trạng thái |
| **Tần suất / Kích hoạt** | Gửi 1 lần khi kích hoạt | Gửi tự động lặp lại 3s/lần | Gửi theo sự kiện người dùng nhập phím |
| **Tính năng mở rộng** | Hiển thị thời gian thực | Cảnh báo ngưỡng nhiệt độ/độ ẩm | Kiểm soát lệnh sai, quản lý trạng thái thiết bị |
| **Kết quả kiểm thử** | Đạt 100% yêu cầu | Đạt 100% yêu cầu | Đạt 100% yêu cầu |
