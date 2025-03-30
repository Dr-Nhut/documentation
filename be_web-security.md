# MD5
## What is MD5?
- MD5 (Message Digest Algorithm 5) là một thuật toán băm (hash function) tạo ra một giá trị băm có độ dài cố định là 128-bit (16 byte) từ một chuỗi dữ liệu đầu vào bất kỳ.
## Cách hoạt động của MD5
- MD5 thực hiện một loạt các phép toán trên dữ liệu đầu vào để tạo ra giá trị băm duy nhất. Quá trình này bao gồm:
    - Chia dữ liệu thành các khối 512-bit
    - Thêm bit đệm để làm cho độ dài dữ liệu chia hết cho 512
    - Chia thành các khối nhỏ hơn và xử lý qua các vòng lặp biến đổi
    - Xuất ra kết quả cuối cùng là một giá trị băm 128-bit

## Ứng dụng của MD5
- Kiểm tra tính toàn vẹn của dữ liệu (checksum)
- Lưu trữ mật khẩu (hashing password)
- Tạo chữ ký số
## Nhược điểm và vấn đề bảo mật
- MD5 hiện nay không còn an toàn vì:
    - Dễ bị tấn công va chạm (collision attack): Hai dữ liệu khác nhau có thể tạo ra cùng một mã băm MD5.
    - Dễ bị tấn công brute-force và rainbow table: Do không có cơ chế bảo vệ chống lại dò tìm mật khẩu.

# SHA Family
## SHA là gì?
- SHA (Secure Hash Algorithm) là một họ thuật toán băm mật mã được phát triển bởi Viện Tiêu chuẩn và Công nghệ Quốc gia Hoa Kỳ (NIST) cùng với Cơ quan An ninh Quốc gia Hoa Kỳ (NSA). SHA được sử dụng rộng rãi để đảm bảo tính toàn vẹn của dữ liệu và bảo mật thông tin.
## Các phiên bản chính của SHA
- SHA-1 (160 bit): Không an toàn, Đã bị phá vỡ bởi tấn công va chạm (collision attack).
SHA-2: (224, 256, 384, 512 bit)	An toàn (có giới hạn), bao gồm SHA-224, SHA-256, SHA-384, SHA-512.
SHA-3: (224, 256, 384, 512 bit)	An toàn nhất, dựa trên Keccak, khác biệt so với SHA-1 và SHA-2.

## Ứng dụng của SHA
- Bảo mật mật khẩu (kết hợp với salt & pepper).
- Chứng thực dữ liệu (chữ ký số, SSL/TLS).
- Tiền mã hóa (cryptocurrency) (Bitcoin dùng SHA-256).
- Kiểm tra tính toàn vẹn của tệp tin (checksum).

# Scrypt
## What is Scrypt
- Scrypt là một thuật toán dẫn xuất khóa (key derivation function - KDF) được thiết kế để bảo mật mật khẩu và kháng lại các cuộc tấn công brute-force và tấn công bằng GPU/ASIC. Nó được tạo ra bởi Colin Percival vào năm 2009 và được sử dụng trong nhiều ứng dụng bảo mật, đặc biệt là tiền mã hóa.
## Đặc điểm nổi bật của Scrypt
- Cần nhiều tài nguyên bộ nhớ (Memory-hard):
    - Không giống như MD5 hay SHA-256 chỉ yêu cầu CPU, Scrypt yêu cầu cả CPU lẫn bộ nhớ RAM.
    - Điều này làm cho việc tấn công bằng phần cứng chuyên dụng (ASIC/GPU) trở nên kém hiệu quả và tốn kém hơn.
- Có thể điều chỉnh độ khó (Configurable parameters):
    - N: Số vòng lặp tính toán (tăng giá trị này sẽ làm tăng độ khó).
    - r: Kích thước khối dữ liệu.
    - p: Số luồng xử lý song song.
    - Salt: Chuỗi ngẫu nhiên thêm vào để tránh tấn công rainbow table.
- An toàn hơn so với các thuật toán băm thông thường
    - Bảo vệ chống tấn công brute-force tốt hơn so với SHA-256 hoặc MD5.
    - Thích hợp để lưu trữ mật khẩu an toàn.

## Ứng dụng của Scrypt
✅ Lưu trữ mật khẩu một cách an toàn
✅ Tiền mã hóa (cryptocurrency): Được sử dụng trong Litecoin, Dogecoin thay vì SHA-256 như Bitcoin.
✅ Mã hóa khóa cá nhân: Bảo vệ các dữ liệu nhạy cảm như ví tiền điện tử.

# Bcrypt
## What is Bcrypt
- Bcrypt là một thuật toán băm mật khẩu (password hashing algorithm) được thiết kế để lưu trữ mật khẩu một cách an toàn. Nó được phát triển vào năm 1999 bởi Niels Provos và David Mazières, dựa trên thuật toán mã hóa Blowfish.

## Đặc điểm nổi bật của Bcrypt
- Có yếu tố "salt" giúp chống tấn công Rainbow Table
    - Bcrypt tự động tạo một chuỗi salt ngẫu nhiên trước khi băm mật khẩu, giúp mỗi lần băm cho ra kết quả khác nhau ngay cả khi cùng một mật khẩu được băm nhiều lần.
- Có thể điều chỉnh độ khó (Cost factor)
    - Tham số work factor (cost factor) giúp tăng thời gian tính toán khi băm mật khẩu.
    - Điều này làm cho các cuộc tấn công brute-force trở nên chậm hơn theo thời gian khi phần cứng mạnh lên.
- Chống lại tấn công GPU/ASIC
    - Bcrypt sử dụng nhiều vòng tính toán lặp lại, làm cho nó khó bị tăng tốc trên GPU hoặc ASIC hơn so với MD5 hay SHA-256.

## Cách hoạt động của Bcrypt
1️⃣ Tạo salt ngẫu nhiên
2️⃣ Ghép mật khẩu với salt
3️⃣ Áp dụng thuật toán Blowfish với nhiều vòng lặp (cost factor)
4️⃣ Xuất ra chuỗi hash dạng Base64

## Ứng dụng của Bcrypt
✅ Lưu trữ mật khẩu an toàn (các hệ thống web, ứng dụng)
✅ Bảo vệ thông tin nhạy cảm (API keys, token)

## Nhược điểm của Bcrypt
❌ Chậm hơn so với MD5 và SHA-256 (nhưng đây cũng là ưu điểm để chống tấn công).
❌ Không tối ưu bằng Argon2 (Argon2 có khả năng chống tấn công GPU tốt hơn).

# Compare
| Thuật toán  | Bộ nhớ cần dùng | Chống brute-force | Khả năng ASIC/GPU hóa | Ứng dụng chính             |
|------------|---------------|------------------|--------------------|--------------------------|
| **MD5**    | Thấp          | Kém              | Dễ dàng            | Hash dữ liệu (cũ)        |
| **SHA-256**| Thấp          | Trung bình       | Rất dễ dàng        | Tiền mã hóa (Bitcoin)    |
| **Bcrypt** | Trung bình    | Tốt              | Khó                | Lưu trữ mật khẩu         |
| **Scrypt** | Cao           | Rất tốt          | Rất khó            | Lưu mật khẩu, tiền mã hóa |
| **Argon2** | Cao nhất      | Tốt nhất         | Khó nhất           | Lưu mật khẩu (mạnh nhất)  |
