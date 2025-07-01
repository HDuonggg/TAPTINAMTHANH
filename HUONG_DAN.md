# Hướng Dẫn Sử Dụng Hệ Thống Gửi File Âm Thanh An Toàn

## 📋 Mô tả dự án

Đây là một hệ thống mô phỏng gửi file âm thanh an toàn qua mạng không ổn định với các tính năng bảo mật tiên tiến:

### 🔐 Các tính năng bảo mật:
- **Mã hóa Triple DES**: Mỗi đoạn file được mã hóa bằng thuật toán Triple DES với SessionKey
- **Hash SHA-512**: Kiểm tra toàn vẹn dữ liệu bằng thuật toán SHA-512
- **Ký số RSA 2048-bit**: Xác thực nguồn gốc và tính toàn vẹn bằng chữ ký số RSA
- **Handshake an toàn**: Quy trình bắt tay an toàn giữa client và server
- **Xử lý lỗi**: Gửi ACK/NACK để xác nhận thành công hoặc thất bại

### 🔄 Quy trình hoạt động:
1. **Handshake**: Client gửi "Hello!" → Server trả "Ready!"
2. **Gửi metadata**: Thông tin file + chữ ký số
3. **Trao đổi khóa**: SessionKey được mã hóa bằng RSA
4. **Gửi dữ liệu**: 3 gói tin (iv, cipher, hash, sig)
5. **Xác nhận**: Server kiểm tra và trả ACK/NACK

## 🚀 Cài đặt và chạy

### 1. Cài đặt thư viện
```bash
pip install -r requirements.txt
```

### 2. Chạy server
```bash
python server.py
```

### 3. Chạy client (trong terminal khác)
```bash
python client.py recording.mp3
```

### 4. Chạy demo tự động
```bash
python demo.py
```

## 📁 Cấu trúc dự án

```
AMNHAC/
├── client.py          # Chương trình client (người gửi)
├── server.py          # Chương trình server (người nhận)
├── crypto_utils.py    # Các hàm tiện ích mã hóa
├── file_utils.py      # Các hàm xử lý file
├── network_utils.py   # Các hàm giao tiếp mạng
├── demo.py           # File demo tự động
├── requirements.txt   # Thư viện cần thiết
├── README.md         # Hướng dẫn tổng quan
└── HUONG_DAN.md     # Hướng dẫn chi tiết (file này)
```

## 🔧 Các module chính

### 1. crypto_utils.py
- **Triple DES**: Mã hóa/giải mã dữ liệu
- **RSA 2048-bit**: Tạo khóa, ký số, xác thực
- **SHA-512**: Tạo hash kiểm tra toàn vẹn
- **Base64**: Mã hóa dữ liệu để truyền qua socket

### 2. file_utils.py
- **Chia file**: Chia file thành 3 đoạn nhỏ
- **Ghép file**: Kết hợp các đoạn thành file hoàn chỉnh
- **Metadata**: Tạo và quản lý thông tin file
- **Kiểm tra**: Xác định loại file âm thanh

### 3. network_utils.py
- **Socket**: Giao tiếp mạng TCP
- **Handshake**: Quy trình bắt tay an toàn
- **Packet**: Gửi/nhận dữ liệu có cấu trúc
- **ACK/NACK**: Xác nhận thành công/thất bại

## 📊 Quy trình chi tiết

### Bước 1: Handshake
```
Client → "Hello!" → Server
Client ← "Ready!" ← Server
```

### Bước 2: Gửi metadata
```
Client tạo metadata:
- Tên file
- Kích thước
- Thời gian
- Session ID
- Số chunk

Client ký số metadata bằng RSA
Client gửi metadata + chữ ký số
```

### Bước 3: Trao đổi SessionKey
```
Client tạo SessionKey ngẫu nhiên
Client mã hóa SessionKey bằng RSA (public key của server)
Client gửi SessionKey đã mã hóa
Server giải mã SessionKey bằng private key
```

### Bước 4: Gửi dữ liệu
```
Với mỗi chunk:
1. Mã hóa chunk bằng Triple DES + SessionKey
2. Tạo hash SHA-512 của chunk gốc
3. Ký số hash bằng RSA
4. Gửi: IV + Ciphertext + Hash + Signature
```

### Bước 5: Xử lý và xác nhận
```
Server nhận từng chunk:
1. Xác thực chữ ký số
2. Giải mã bằng Triple DES
3. Kiểm tra hash SHA-512
4. Ghép các chunk lại

Nếu thành công: Gửi ACK
Nếu thất bại: Gửi NACK
```

## 🛠️ Tùy chỉnh

### Thay đổi số chunk
```python
# Trong file_utils.py, hàm split_file
chunks = self.file_utils.split_file(file_path, 5)  # Thay đổi từ 3 thành 5
```

### Thay đổi cổng
```bash
python server.py --port 8080
python client.py recording.mp3 --port 8080
```

### Thay đổi địa chỉ
```bash
python server.py --host 0.0.0.0
python client.py recording.mp3 --host 192.168.1.100
```

## 🔍 Debug và xử lý lỗi

### Lỗi thường gặp:
1. **Port đã được sử dụng**: Thay đổi cổng hoặc đóng ứng dụng khác
2. **File không tồn tại**: Kiểm tra đường dẫn file
3. **Lỗi mã hóa**: Kiểm tra thư viện cryptography
4. **Lỗi kết nối**: Kiểm tra firewall và địa chỉ IP

### Debug mode:
```python
# Thêm vào đầu file để debug
import logging
logging.basicConfig(level=logging.DEBUG)
```

## 📈 Mở rộng dự án

### Có thể thêm:
1. **Giao diện đồ họa**: Tạo GUI bằng tkinter hoặc PyQt
2. **Nén dữ liệu**: Sử dụng zlib hoặc lzma
3. **Mã hóa end-to-end**: Trao đổi khóa Diffie-Hellman
4. **Hỗ trợ nhiều file**: Gửi nhiều file cùng lúc
5. **Resume transfer**: Tiếp tục gửi khi bị gián đoạn
6. **Progress bar**: Hiển thị tiến trình gửi/nhận

### Tối ưu hóa:
1. **Buffer size**: Điều chỉnh kích thước buffer
2. **Threading**: Xử lý đa luồng cho nhiều client
3. **Compression**: Nén dữ liệu trước khi mã hóa
4. **Caching**: Cache khóa để tăng tốc độ

## 📚 Tài liệu tham khảo

- [Cryptography Python](https://cryptography.io/)
- [Python Socket Programming](https://docs.python.org/3/library/socket.html)
- [Triple DES](https://en.wikipedia.org/wiki/Triple_DES)
- [RSA Cryptography](https://en.wikipedia.org/wiki/RSA_(cryptosystem))
- [SHA-512](https://en.wikipedia.org/wiki/SHA-2)

## 🤝 Đóng góp

Nếu bạn muốn đóng góp vào dự án:
1. Fork repository
2. Tạo branch mới
3. Commit thay đổi
4. Push và tạo Pull Request

## 📄 License

Dự án này được phát hành dưới MIT License. 