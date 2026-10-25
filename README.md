# 📺 T-Display S3 MJPEG Player

Trình phát video MJPEG trên board **LilyGO T-Display S3** sử dụng màn hình ST7789 song song 8-bit, đọc file `.mjpeg` từ LittleFS (flash SPIFFS partition) và giải mã bằng thư viện JPEGDEC, hiển thị qua LovyanGFX

---

## ✨ Tính năng

- 🎬 **Phát video MJPEG** từ file `/video1.mjpeg` trong LittleFS
- 🖼️ **Sprite đệm trong PSRAM** (320×170, RGB565) → giảm xé hình, tăng mượt
- 🎯 **Giới hạn FPS** mục tiêu **24 FPS** (cấu hình qua `TARGET_FPS`)
- 💡 **Upload video qua webserver** bằng nút nhấn
- 🔁 **Nút restart** (GPIO0 – nút BOOT) → phát lại video từ đầu
- 📊 **Log Serial** FPS + dung lượng RAM/PSRAM mỗi 2 giây
- 🛡️ **Fallback PSRAM → Internal RAM** nếu PSRAM không khả dụng

---

## 🧩 Phần cứng

| Thành phần | Thông số |
|---|---|
| Board | LilyGO T-Display S3 (ESP32-S3) |
| Màn hình | ST7789 170×320, bus song song 8-bit |
| Nút nhấn | `GPIO0` (BOOT – restart) |

## 📁 Cấu trúc project

```
.
├── T_Display_S3_MJPEG.ino # Sketch chính
└── partitions.csv         # Bảng phân vùng flash

```

### Phân vùng flash (`partitions.csv`)

| Name | Type | SubType | Offset | Size |
|---|---|---|---|---|
| nvs | data | nvs | 0x9000 | 0x6000 |
| phy_init | data | phy | 0xF000 | 0x1000 |
| factory (app) | app | factory | 0x10000 | **3 MB** |
| spiffs (LittleFS) | data | spiffs | 0x310000 | **~12.9 MB** |

**Nhớ chuyển partition scheme thành custom nhé**

> ⚠️ Phân vùng `spiffs` được mount thành **LittleFS**. Toàn bộ video cần nằm trong ~12.9 MB này.

---

## 📚 Thư viện phụ thuộc

Cài trong **Arduino IDE** hoặc **PlatformIO**:

| Thư viện | Mục đích |
|---|---|
| [LovyanGFX] (https://github.com/lovyan03/LovyanGFX) | Driver màn hình + sprite |
| [JPEGDEC] (https://github.com/bitbank2/JPEGDEC) | Giải mã JPEG (có hỗ trợ ESP32-S3) |

**Board package:** `esp32` by Espressif (khuyến nghị ≥ 2.0.14, hỗ trợ S3 + PSRAM).

---

## ⚙️ Cấu hình

Các hằng số có thể tinh chỉnh ở đầu file sketch:

```cpp
#define VIDEO_FILE "/video1.mjpeg"  // Đường dẫn file
#define TARGET_FPS 24               // FPS mục tiêu
#define MAX_BUFFER_SIZE (64 * 1024) // Kích thước 1 frame tối đa
#define READ_CHUNK_SIZE 4096        // Kích thước mỗi lần đọc file
#define FS_PARTITION_LABEL "spiffs" // Label phân vùng LittleFS
```

---

## 🚀 Cách sử dụng

### 1. Chuẩn bị file video

Chuyển video bất kỳ sang MJPEG bằng **Tools kèm theo**
Tên video là: **video1.mjpeg**

### 2. Nạp file vào LittleFS

Ấn rst xong đè boot để vào webserver **Không cũng lúc**

---

## 🎮 Thao tác nút

| Nút | Hành động | Kết quả |
|---|---|---|
| **GPIO0** | Nhấn | Phát lại video từ đầu |

---

<div align="center">

**Made by Thoai for the ESP32 community**

</div>