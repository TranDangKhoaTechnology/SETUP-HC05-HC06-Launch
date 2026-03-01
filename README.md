# SETUP-HC05-HC06-Launch

Bộ công cụ cấu hình **Bluetooth UART HC-05 / HC-06** trên Windows bằng file `.exe` (không cần cài Python).

Repo này chứa **bản chạy (binary)**:
- `hc-setup-gui.exe` — giao diện đồ hoạ (GUI) để cấu hình + pairing.
- `hc-setup-wizard.exe` — dạng wizard chạy trong console (nhẹ hơn, phù hợp khi remote/terminal).

> Nếu bạn cần source code: repo này chỉ là “launcher/binary”.

---

## 1) Yêu cầu

- Windows 10/11 (x64).
- 1 USB-to-UART (CH340 / CP210x / FTDI…) để nối module HC-05/HC-06 vào máy tính.
- Driver USB-UART đã cài (nếu cắm vào mà không thấy COM port thì hãy cài driver tương ứng).

### Lưu ý phần cứng (quan trọng)
- HC-05/HC-06 thường chạy logic UART mức **3.3V**.
- Nếu USB-UART của bạn xuất TX mức 5V, nên dùng **chia áp** vào chân RX của module (để an toàn).

---

## 2) Sơ đồ nối dây cơ bản

| USB-UART | HC-05/HC-06 |
|---|---|
| TXD | RXD |
| RXD | TXD |
| GND | GND |
| 5V (hoặc 3.3V tuỳ board) | VCC |

> Luôn nối **GND chung**.  
> TX/RX phải **nối chéo**.

---

## 3) Vào chế độ AT (rất hay bị sai)

### HC-05
HC-05 có thể cần đưa vào AT mode bằng chân **KEY/EN** (tuỳ loại board ZS-040/JY-MCU).
- Cách phổ biến: **giữ nút/KEY** khi cấp nguồn để vào AT mode.
- Một số board: cần kéo **KEY = HIGH (3.3V)** trước khi cấp nguồn.

Dấu hiệu thường gặp:
- Nháy LED chậm / theo nhịp khác so với chế độ data.

### HC-06
HC-06 thường **không có master mode**, AT mode thường đơn giản hơn nhưng phụ thuộc firmware.
Nếu app không detect được, hãy thử:
- đúng baud,
- đúng line ending (CR/LF),
- đảm bảo module không đang kết nối Bluetooth với thiết bị khác.

---

## 4) Cách dùng nhanh (khuyến nghị dùng GUI)

### 4.1 Chạy bản GUI
1. Tải repo về / hoặc tải riêng `hc-setup-gui.exe`.
2. Double-click `hc-setup-gui.exe`.
3. Chọn đúng **COM port** (ví dụ COM3/COM4…).
4. Bấm **Detect** (hoặc chức năng tương đương) để app tự nhận dạng HC-05/HC-06.
5. Chọn profile cấu hình:
   - Đặt tên (NAME)
   - PIN (PSWD)
   - Baud UART
   - Role (Master/Slave) *— tuỳ module*
6. Bấm **Run / Apply** để ghi cấu hình.
7. (Tuỳ nhu cầu) dùng phần **Pair/Bind/Link** để ghép 2 module (thường dùng cho chế độ Master–Slave).

**Khi cấu hình xong:** tháo nguồn/cắm lại module để chạy ở chế độ data bình thường.

---

### 4.2 Chạy bản Wizard (console)
1. Double-click `hc-setup-wizard.exe`.
2. Làm theo menu hướng dẫn:
   - Chọn COM
   - Detect module
   - Chọn chế độ cấu hình
   - Thực thi và xem log

Bản wizard phù hợp nếu máy yếu, hoặc bạn muốn log gọn trong console.

---

## 5) Kịch bản thường dùng

### A) Đổi tên + PIN + baud (1 module)
- Vào AT mode đúng cách (đặc biệt HC-05).
- Detect → Set NAME / PSWD / UART → Apply.

### B) Ghép 2 module (Master ↔ Slave) – thường dùng với HC-05
- Module 1 đặt **Slave**, module 2 đặt **Master**.
- Lấy địa chỉ (ADDR) của Slave.
- Trên Master: bind/link tới Slave theo quy trình trong app.
- Nếu LINK fail nhưng BIND OK: thử cấp nguồn lại cả 2 module rồi test.

> Lưu ý: HC-06 thường chỉ làm Slave, nên nếu bạn cần Master–Slave “chủ động kết nối”, ưu tiên dùng HC-05.

---

## 6) Troubleshooting

### Không thấy COM port
- Cài driver CH340/CP210x/FTDI tương ứng.
- Đổi cáp USB hoặc đổi cổng USB.
- Kiểm tra Device Manager (Ports / COM & LPT).

### Detect không ra / không nhận lệnh AT
- Chưa vào đúng **AT mode** (HC-05 hay gặp nhất).
- Sai baud: thử các mức phổ biến (9600 / 38400 / 57600 / 115200).
  - Nhiều HC-05 dùng **38400** cho AT mode (tuỳ firmware/board).
- Sai line ending: một số firmware cần `\r\n` (CRLF).
- Module đang kết nối Bluetooth với thiết bị khác → ngắt kết nối trước.

### Open port failed / Permission denied
- Bạn đang mở COM bằng app khác (Arduino Serial Monitor, PuTTY, …) → tắt hết.
- Rút USB-UART và cắm lại.

### Chữ bị lỗi / cấu hình xong không chạy
- Nhầm baud giữa AT mode và data mode.
- Nhầm TX/RX hoặc thiếu GND chung.
- Nguồn yếu (module reset liên tục) → dùng nguồn ổn định.

---

## 7) Bảo mật & lưu ý
- Đổi PIN mặc định (thường là 1234/0000) nếu dùng trong môi trường thực tế.
- Không cấp 5V trực tiếp vào chân RX nếu module không có mạch chuyển mức.

---

## 8) License
MIT License — xem file `LICENSE`.

---

## 9) Gợi ý phát hành (khuyến nghị)
Repo hiện chưa có mục Releases. Bạn có thể tạo GitHub Release và đính kèm `.exe` để người dùng tải dễ hơn.
