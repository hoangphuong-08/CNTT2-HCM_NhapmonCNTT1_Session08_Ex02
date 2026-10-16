- ***Định nghĩa về Input ( đầu vào )*** : Là các thông tin thô, tài liệu gốc, hoặc đề bài ban đầu.
- ***Định nghĩa về Output( đầu ra )*** : Là sản phẩm sau khi đã được xử lý và trình bày lại theo đúng chuẩn giao diện chuyên nghiệp.
# Hướng Dẫn Cài Đặt Hệ Thống Cảm Biến Độ Ẩm "Lan Farm"
Tài liệu này hướng dẫn kỹ thuật viên cấu hình phần cứng và phần mềm cho hệ thống giám sát độ ẩm chuyên dụng dành cho vườn lan.
## So sánh thiết bị cảm biến
| Tiêu chí kỹ thuật | Cảm biến cũ (DHT11) | Cảm biến mới (DHT22 / AM2302) |
| :--- | :--- | :--- |
| **Độ chính xác độ ẩm** | $\pm$5% RH | $\pm$2% - 5% RH |
| **Dải đo nhiệt độ** | 0°C đến 50°C | -40°C đến 80°C |
| **Tần suất lấy mẫu** | 1 Hz (1 lần/giây) | 0.5 Hz (2 giây/lần) |
| **Ứng dụng khuyên dùng** | Giám sát cơ bản | Tự động hóa nhà kính cao cấp |

### Các bước cài đặt
\*Terminal trên Raspberry Pi và chạy lần lượt các câu lệnh sau để chuẩn bị thư viện điều khiển:

1. Cập nhật hệ thống: `sudo apt update && sudo apt upgrade -y`
2. Cài đặt thư viện Python hỗ trợ: `pip install Adafruit-DHT`
3. Khai báo biến chân cắm cảm biến trong file cấu hình: `sensor_pin = 4`
#### Mã Nguồn Điều Khiển Máy Bơm (`pump_control.py`)
Đoạn mã Python dưới đây đọc dữ liệu từ cảm biến DHT22 kết nối qua chân GPIO và tự động kích hoạt máy bơm khi độ ẩm xuống thấp hơn ngưỡng tiêu chuẩn cho vườn lan:

```python
import Adafruit_DHT
import time

# Cấu hình loại cảm biến và chân GPIO kết nối
SENSOR = Adafruit_DHT.DHT22
PIN = 4
HUMIDITY_THRESHOLD = 70.0  # Ngưỡng kích hoạt máy bơm (%)

def monitor_orchid_farm():
    print("Đang khởi động hệ thống giám sát độ ẩm Lan Farm...")
    try:
        while True:
            humidity, temperature = Adafruit_DHT.read_retry(SENSOR, PIN)
            
            if humidity is not None and temperature is not None:
                print(f"Nhiệt độ: {temperature:.1f}°C | Độ ẩm: {humidity:.1f}%")
                
                if humidity < HUMIDITY_THRESHOLD:
                    print("Cảnh báo: Độ ẩm thấp! Đang bật máy bơm tưới...")
                    # Thêm logic kích hoạt Relay điều khiển máy bơm ở đây
                else:
                    print("Trạng thái ổn định. Máy bơm ở chế độ chờ.")
            else:
                print("Lỗi: Không thể đọc dữ liệu từ cảm biến. Kiểm tra lại kết nối!")
                
            time.sleep(5)
            
    except KeyboardInterrupt:
        print("Đã dừng hệ thống thủ công.")

