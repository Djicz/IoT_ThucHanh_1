========================================================================
BÁO CÁO THỰC HÀNH BUỔI 1: LẬP TRÌNH PYTHON VỚI GIAO THỨC MQTT
========================================================================

1. CẤU TRÚC DỰ ÁN:
IOT-ThucHanh_1/
├── Bai_1/
│   ├── publisher_bai1.py          # Phát thông điệp gồm Họ tên, MSV
│   └── subscriber_bai1.py         # Nhận thông điệp và in kèm thời gian nhận
├── Bai_2/
│   ├── sensor_publisher_bai2.py   # Mô phỏng cảm biến gửi JSON định kỳ 3 giây
│   └── monitor_subscriber_bai2.py # Nhận dữ liệu và kiểm tra ngưỡng cảnh báo
├── Bai_3/
│   ├── device_bai3.py             # Thiết bị đèn nhận lệnh ON/OFF và báo trạng thái
│   └── controller_bai3.py         # App điều khiển người dùng nhập lệnh và xem trạng thái
├── README.md                      # Báo cáo tổng hợp chi tiết (Markdown)
└── README.txt                     # Hướng dẫn tóm tắt tổng hợp (Plain Text)

2. MÔI TRƯỜNG & BROKER SỬ DỤNG:
- Cài đặt thư viện: pip install paho-mqtt
- Tên Broker: Local Eclipse Mosquitto Broker (hoặc Public Broker)
- Host / IP: localhost
- Cổng (Port): 1883
- Giao thức: MQTT over TCP

3. DANH SÁCH CÁC BÀI THỰC HÀNH & TOPIC:
- Bai_1/
  + publisher_bai1.py: Gửi thông điệp chứa Họ tên & MSV
  + subscriber_bai1.py: Lắng nghe thông điệp và in kèm thời gian nhận
  + Topic: iot/lab/message
- Bai_2/
  + sensor_publisher_bai2.py: Mô phỏng cảm biến gửi JSON định kỳ 3 giây
  + monitor_subscriber_bai2.py: Giám sát và đưa ra cảnh báo ngưỡng (T > 35°C, H < 40%)
  + Topic: iot/lab/sensor01/data
- Bai_3/
  + device_bai3.py: Thiết bị đèn thông minh nhận lệnh và báo trạng thái
  + controller_bai3.py: Ứng dụng điều khiển nhập lệnh (ON/OFF/EXIT)
  + Topics: iot/lab/light01/cmd (lệnh) & iot/lab/light01/status (trạng thái)

4. CÁCH CHẠY TỪNG BÀI:
(Mở 2 cửa sổ Terminal độc lập cho mỗi bài)

* BÀI 1:
  - Terminal 1: python Bai_1/subscriber_bai1.py
  - Terminal 2: python Bai_1/publisher_bai1.py

* BÀI 2:
  - Terminal 1: python Bai_2/monitor_subscriber_bai2.py
  - Terminal 2: python Bai_2/sensor_publisher_bai2.py

* BÀI 3:
  - Terminal 1: python Bai_3/device_bai3.py
  - Terminal 2: python Bai_3/controller_bai3.py
    (Nhập lệnh ON / OFF / EXIT trên bàn phím)

5. KẾT QUẢ ĐẠT ĐƯỢC:
- Bài 1: Gửi và nhận thông điệp chứa Họ tên, MSV thành công trên topic 'iot/lab/message'.
- Bài 2: Gửi dữ liệu cảm biến định kỳ 3s (JSON), Subscriber nhận và cảnh báo đúng ngưỡng nhiệt độ > 35°C và độ ẩm < 40%.
- Bài 3: Giao tiếp 2 chiều ổn định giữa Controller và Device, điều khiển ON/OFF và nhận phản hồi trạng thái JSON tức thời.
========================================================================
