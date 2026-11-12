# LAB 3 – Mạch giám sát và cảnh báo người chạy (ESP32 + FreeRTOS)

Bài Lab 3 môn IoT: thiết kế mạch giám sát và cảnh báo người chạy bộ bằng **ESP32** và cảm biến **BMP180**, chạy trên nền **FreeRTOS**. Mạch đo nhiệt độ, áp suất khí quyển, tính độ cao so với mực nước biển, gửi dữ liệu qua **MQTT** lên giao diện web và cảnh báo bằng LED + còi khi có nguy cơ.

## Nội dung dự án
## Tải xuống dự án

- **Tải toàn bộ dự án (file .zip):** [Tải xuống](https://github.com/Quang-Dung-t/LAB_3_RUNNER/archive/refs/heads/main.zip)

| Thành phần | Liên kết |
|---|---|
| Báo cáo | [bao_cao_lab3_QD.docx](docs/bao_cao_lab3_QD.docx) |
| Video mô phỏng | [Xem video](https://quang-dung-t.github.io/LAB_3_RUNNER/demo/video_mo_phong.mp4) |
| Mô phỏng Wokwi | [Mở trên Wokwi](https://wokwi.com/projects/480228802761296897) |
| Giao diện web | [Mở web](https://quang-dung-t.github.io/LAB_3_RUNNER/web/) |

## Chức năng chính

- Đọc nhiệt độ, áp suất từ BMP180 (I2C), tính độ cao theo công thức khí áp.
- Gửi dữ liệu lên MQTT Broker qua WiFi; web nhận dữ liệu qua MQTT over WebSocket.
- Web hiển thị số liệu và 2 biểu đồ (nhiệt độ, độ cao) theo thời gian thực.
- **Cảnh báo tự động** (LED + còi) khi nhiệt độ vượt **35 °C** hoặc độ cao thay đổi đột ngột.
- **Điều khiển thủ công** LED và còi bằng nút bấm trên web (chế độ Tự động / Thủ công).

## Kiến trúc FreeRTOS

| Task | Core | Ưu tiên | Nhiệm vụ |
|---|---|---|---|
| Task đọc data | 1 | 2 | Đọc BMP180 mỗi 1 giây, tính độ cao, đẩy dữ liệu vào 2 Queue |
| Task cảnh báo | 1 | 3 | So sánh với ngưỡng, điều khiển LED và còi |
| Task truyền data | 0 | 1 | Kết nối WiFi/MQTT, gửi dữ liệu, nhận lệnh từ web |

Các Task trao đổi dữ liệu qua hai hàng đợi: `data Queue` (gửi MQTT) và `data Alert` (cảnh báo).

## Sơ đồ nối chân

| Linh kiện | Chân | ESP32 |
|---|---|---|
| BMP180 | VCC / GND | 3V3 / GND |
| BMP180 | SDA / SCL | GPIO21 / GPIO22 |
| LED đỏ (qua trở 220 Ω) | Anode | GPIO26 |
| Buzzer | (+) | GPIO27 |

## Cách chạy

1. Mở link Wokwi ở trên, bấm **Start Simulation**.
2. Mở giao diện web, chờ trạng thái chuyển sang "Đã kết nối broker".
3. Kéo thanh trượt nhiệt độ / áp suất của BMP180 trong Wokwi để thử cảnh báo; dùng nút trên web để điều khiển thủ công.

## Tác giả

- **Trần Quang Dũng** – MSV 231432196 – Lớp KTDTTHCN2 – K64
- GVHD: ThS. Võ Quang Sơn
- Trường Đại học Giao thông Vận tải – Khoa Điện – Điện tử
