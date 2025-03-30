# What is the internet?

- The Internet is a global network of computers connected to each other which communicate through a standardized set of protocols.

# Wires, Cables, and Wi-Fi

- Thông tin trên Internet được truyền từ máy tính này sang máy tính khác dưới dạng bit – đơn vị dữ liệu nhỏ nhất trong máy tính
- Dữ liệu được lưu trữ dưới dạng nhị phân (binary form)
- Ngày nay, chúng ta gửi các bit bằng điện, ánh sáng và sóng vô tuyến
- Dữ liệu có thể được truyền qua nhiều phương tiện vật lý khác nhau:

  - Electricity: qua dây đồng trong cáp Ethernet.
  - Light: qua cáp quang. Cáp quang được thiết kế đặc biệt để phản xạ ánh sáng trong lõi, giúp truyền dữ liệu nhanh và xa mà không bị nhiễu. Tốc độ rất cao, có thể lên đến hàng terabit mỗi giây
  - Wireless: qua mạng không dây như Wi-Fi, 4G, 5G. Sử dụng sóng vô tuyến để gửi dữ liệu từ nơi này sang nơi khác. Phụ thuộc vào khoảng cách, vật cản và chất lượng sóng.

- Bandwidtd:

  - Là dung lượng tối đa mà một thiết bị có thể truyền tải dữ liệu trong một giây
  - Được đo bằng Mbps (megabit per second) hoặc Gbps (gigabit per second).
  - Băng thông càng cao, tốc độ truyền dữ liệu càng nhanh.

- Bit rate

  - Là số bit có thể được gửi trong một khoảng thời gian nhất định, thường tính theo giây (bits per second - bps).
  - Tốc độ cao hơn giúp tải xuống hoặc truyền dữ liệu nhanh hơn.

## IP Addresses and DNS

- ISP (Internet Service Provider) là nhà cung cấp dịch vụ Internet, tức là công ty hoặc tổ chức cung cấp quyền truy cập vào Internet cho cá nhân, doanh nghiệp hoặc tổ chức.
- Protocol (giao thức) là tập hợp các quy tắc và quy ước được định nghĩa để hai hoặc nhiều thiết bị có thể giao tiếp và trao đổi dữ liệu với nhau một cách hiệu quả và chính xác.
- IP (Internet Protocol – Giao thức Internet) là một tập hợp các quy tắc giúp các thiết bị trên mạng giao tiếp và truyền tải dữ liệu với nhau
- Chức năng của IP:

  - Định danh thiết bị: Mỗi thiết bị trên mạng có một địa chỉ IP duy nhất, giúp xác định thiết bị đó trên Internet.

  - Định tuyến dữ liệu: Dữ liệu được chia thành các gói tin (packets) và truyền từ thiết bị này đến thiết bị khác thông qua nhiều mạng trung gian.

  - Kết nối giữa các mạng: IP giúp kết nối nhiều mạng khác nhau thành một hệ thống Internet thống nhất.

- Các loại địa chỉ IP:

  - IPv4 (Internet Protocol version 4): Sử dụng địa chỉ 32-bit, dạng xxx.xxx.xxx.xxx (VD: 192.168.1.1). Chỉ có khoảng 4,3 tỷ địa chỉ, đang dần bị thay thế do thiếu hụt.
  - IPv6 (Internet Protocol version 6): Sử dụng địa chỉ 128-bit, dạng xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx (VD: 2001:db8::ff00:42:8329). Cung cấp số lượng địa chỉ gần như vô hạn.

- Cách hoạt động của IP:

1. Khi bạn gửi dữ liệu (VD: truy cập một trang web), dữ liệu được chia thành các gói tin.

2. Mỗi gói tin chứa địa chỉ IP nguồn (của bạn) và địa chỉ IP đích (máy chủ web).

3. Các gói tin được định tuyến qua nhiều thiết bị mạng (router, switch) để đến đích.

4. Khi đến nơi, dữ liệu được lắp ráp lại và hiển thị trên trình duyệt.

## Domain name system (DNS)

- DNS (Hệ thống phân giải tên miền) là một giao thức giúp chuyển đổi tên miền (domain name) thành địa chỉ IP mà máy tính có thể hiểu và sử dụng để kết nối với các máy chủ trên Internet.
- Cách hoạt động của DNS:

  1. Người dùng nhập tên miền vào trình duyệt (VD: www.example.com).

  2. Trình duyệt gửi yêu cầu đến máy chủ DNS để tìm địa chỉ IP tương ứng.

  3. Máy chủ DNS tìm kiếm và trả về địa chỉ IP (VD: 93.184.216.34).

  4. Trình duyệt sử dụng địa chỉ IP đó để kết nối đến máy chủ đích và tải nội dung website.

- Các loại máy chủ DNS:
- DNS gồm nhiều cấp độ khác nhau, hoạt động theo mô hình phân cấp
  1. Root DNS Servers (Máy chủ gốc): cấp cao nhất, quản lý các miền gốc như .com, .org, .vn.
  2. TLD DNS Servers (Máy chủ tên miền cấp cao - Top Level Domain): quản lý từng miền cao cấp cụ thể, như .com, .net, .vn.
  3. Authoritative DNS Servers (Máy chủ phân giải chính thức): chứa thông tin chính xác về tên miền và địa chỉ IP tương ứng.
  4. Recursive DNS Resolver (Máy chủ phân giải đệ quy):Nhà cung cấp dịch vụ Internet (ISP) hoặc Google DNS, Cloudflare DNS giúp tìm địa chỉ IP nhanh hơn.
- Các loại bản ghi DNS quan trọng

  - A Record: Chuyển tên miền thành địa chỉ IPv4.

  - AAAA Record: Chuyển tên miền thành địa chỉ IPv6.

  - CNAME Record: Chuyển một tên miền thành một tên miền khác.

  - MX Record: Xác định máy chủ email cho tên miền.

  - TXT Record: Chứa thông tin văn bản, dùng để xác thực email, bảo mật (SPF, DKIM, DMARC).

- Tại sao DNS quan trọng?
  ✅ Dễ nhớ hơn: Con người có thể nhớ tên miền dễ hơn là địa chỉ IP số.
  ✅ Tăng tốc độ truy cập: DNS giúp tìm địa chỉ IP nhanh chóng, tối ưu kết nối.
  ✅ Bảo mật hơn: Một số DNS hỗ trợ bảo mật như DNSSEC, DNS over HTTPS (DoH) để mã hóa truy vấn DNS.

## DNS spoofing

- DNS Spoofing (Giả mạo DNS) là một hình thức tấn công an ninh mạng, trong đó kẻ tấn công thay đổi hoặc làm giả dữ liệu DNS để chuyển hướng người dùng đến một trang web giả mạo, thay vì trang web thật mà họ muốn truy cập.

# Packets, Routing, and Reliability

## Packets (Gói tin)

- Packets là đơn vị cơ bản của dữ liệu được truyền qua Internet. Khi bạn gửi một tin nhắn, hình ảnh hoặc video, dữ liệu đó không được gửi liền mạch mà bị chia thành các "gói tin" nhỏ.
- Mỗi gói tin chứa:
  - Dữ liệu thực tế (payload): Phần nội dung bạn muốn gửi.
  - Thông tin điều khiển (header): Địa chỉ nguồn, địa chỉ đích, số thứ tự để ghép lại sau.

## Routing (Định tuyến)

- Routing là quá trình quyết định đường đi cho các gói tin từ điểm xuất phát (máy của bạn) đến điểm đích (máy chủ, thiết bị khác).
- Internet giống như một mạng lưới đường cao tốc, và các router (bộ định tuyến) là những "ngã tư thông minh". Chúng đọc địa chỉ IP trong header của gói tin và chọn đường đi tối ưu dựa trên:
  - Tình trạng mạng (đường nào ít tắc nghẽn).
  - Khoảng cách ngắn nhất hoặc nhanh nhất.
- Gói tin không nhất thiết đi cùng một đường; chúng có thể đến đích theo thứ tự khác nhau hoặc qua các tuyến khác nhau.

## Reliability (Độ tin cậy)

- Internet được thiết kế để đáng tin cậy nhờ giao thức như TCP (Transmission Control Protocol):
  - Kiểm tra lỗi: Nếu gói tin bị mất hoặc hỏng, TCP yêu cầu gửi lại.
  - Sắp xếp thứ tự: Các gói tin đến không theo thứ tự sẽ được sắp xếp lại đúng nhờ số thứ tự trong header.
  - Sắp xếp thứ tự: Các gói tin đến không theo thứ tự sẽ được sắp xếp lại đúng nhờ số thứ tự trong header.
- Ví dụ: Khi tải một trang web, nếu mất vài gói tin, bạn không thấy lỗi ngay vì TCP tự động sửa bằng cách yêu cầu gửi lại.

# HTTP & HTML

## HTTP (HyperText Transfer Protocol)

- HTTP là giao thức truyền tải siêu văn bản, được sử dụng để giao tiếp giữa máy tính của bạn (client) và máy chủ (server) trên Internet.

- HTTPS: Là HTTP với mã hóa (SSL/TLS), bảo mật hơn, thường thấy ở các trang có biểu tượng ổ khóa.

## HTML (HyperText Markup Language)

# Encryption and Public Keys

## Encryption:

- Mục đích: Bảo vệ dữ liệu khi nó di chuyển qua Internet (ví dụ: mật khẩu, thông tin thẻ tín dụng, tin nhắn) để tránh bị hacker chặn và đọc trộm.
- Cách hoạt động trên Internet:
  - Dữ liệu được mã hóa trước khi rời thiết bị của bạn và chỉ được giải mã khi đến đích (máy chủ hoặc người nhận).
  - Giao thức phổ biến nhất là TLS (Transport Layer Security), trước đây gọi là SSL, được dùng trong HTTPS.
- Ví dụ thực tế: Khi bạn nhập mật khẩu trên một trang web (như ngân hàng trực tuyến), TLS mã hóa dữ liệu đó trước khi gửi đến máy chủ. Ai chặn giữa đường chỉ thấy chuỗi ký tự vô nghĩa.
- Cơ chế: Kết hợp mã hóa đối xứng (nhanh, dùng khóa chung) và mã hóa bất đối xứng (an toàn, dùng khóa công khai + riêng tư) để tối ưu tốc độ và bảo mật.

## Public Keys

- Vai trò: Khóa công khai là nền tảng của mã hóa bất đối xứng, giúp thiết lập kết nối an toàn mà không cần chia sẻ khóa bí mật qua mạng.
- Cách hoạt động trên Internet:
  - Mỗi máy chủ (như trang web) có một cặp khóa: khóa công khai (public key) và khóa riêng tư (private key).
  - Trình duyệt của bạn tải khóa công khai từ máy chủ để mã hóa dữ liệu gửi đi, và chỉ máy chủ (với khóa riêng tư) mới giải mã được.
- Quy trình cụ thể trong HTTPS:
  1. Trình duyệt kết nối đến trang we
  2. Máy chủ gửi chứng chỉ số (digital certificate), chứa khóa công khai, được ký bởi một Certificate Authority (CA) đáng tin cậy như Let’s Encrypt.
  3. Trình duyệt kiểm tra chứng chỉ (đảm bảo không giả mạo) và dùng khóa công khai để mã hóa một khóa phiên đối xứng (session key).
  4. Máy chủ giải mã khóa phiên bằng khóa riêng tư, sau đó cả hai bên dùng khóa phiên này để mã hóa/gải mã dữ liệu (nhanh hơn mã hóa bất đối xứng).
- Ví dụ: Khi bạn mua sắm online, khóa công khai của trang web mã hóa thông tin thẻ tín dụng, đảm bảo chỉ máy chủ chính chủ mới đọc được.

## Ứng dụng thực tế:

- HTTPS: Gần như mọi trang web hiện đại (có biểu tượng ổ khóa) dùng khóa công khai và mã hóa TLS.
- VPN: Mã hóa dữ liệu giữa thiết bị và máy chủ VPN, thường dùng khóa công khai để khởi tạo.
- Email bảo mật (PGP): Người gửi dùng khóa công khai của người nhận để mã hóa email.

# Cybersecurity and Crime

## Cybersecurity

- Cybersecurity là tập hợp các biện pháp, công nghệ, và quy trình được thiết kế để bảo vệ hệ thống máy tính, mạng, thiết bị, và dữ liệu khỏi các cuộc tấn công, truy cập trái phép, hoặc hư hại. Trong Internet, nó tập trung vào việc giữ an toàn cho thông tin khi truyền tải qua mạng.
- Vai trò trên Internet
  - Bảo vệ dữ liệu cá nhân (như thông tin ngân hàng, mật khẩu) và dữ liệu doanh nghiệp khỏi bị đánh cắp.
  - Ngăn chặn các mối đe dọa như mã độc (malware), tấn công từ chối dịch vụ (DDoS), hoặc lừa đảo (phishing).
  - Đảm bảo tính toàn vẹn và riêng tư của thông tin khi bạn duyệt web, mua sắm trực tuyến, hoặc giao tiếp qua email.
- Ví dụ thực tế:
  - HTTPS (dùng TLS) mã hóa dữ liệu giữa trình duyệt và máy chủ để ngăn kẻ tấn công đọc trộm.
  - Phần mềm diệt virus phát hiện và chặn mã độc lây lan qua email hoặc tải xuống từ Internet.

## Crime

- Tội phạm mạng (Cybercrime) là các hành vi bất hợp pháp được thực hiện qua Internet hoặc nhắm vào hệ thống máy tính/mạng. Chúng tận dụng tính ẩn danh và quy mô của Internet để gây hại.
- Các loại tội phạm mạng phổ biến:
  - Lừa đảo (Phishing): Gửi email giả mạo để lừa lấy thông tin cá nhân hoặc tiền.
  - Tấn công ransomware: Khóa dữ liệu của nạn nhân và đòi tiền chuộc để mở khóa.
  - Đánh cắp danh tính (Identity Theft): Lấy cắp thông tin cá nhân để giả mạo hoặc sử dụng trái phép.
  - Tin tặc (Hacking): Xâm nhập trái phép vào hệ thống để đánh cắp dữ liệu hoặc gây rối.
  - Gian lận tài chính: Lừa đảo qua giao dịch trực tuyến hoặc đánh cắp thông tin thẻ tín dụng.

## Ứng dụng thực tế

- Cybersecurity bảo vệ:
  - Các trang web dùng chứng chỉ SSL/TLS (HTTPS) để mã hóa dữ liệu.
  - VPN che giấu địa chỉ IP của bạn, tăng cường quyền riêng tư khi duyệt Internet.
- Crime tấn công:
  - Tin tặc dùng mạng botnet (mạng máy tính bị nhiễm) để tấn công DDoS, làm sập trang web.
  - Lừa đảo qua mạng xã hội hoặc quảng cáo giả trên Internet để dụ người dùng tải mã độc.

## Làm thế nào để an toàn trên Internet?

- Đối với cá nhân:
  - Dùng mật khẩu mạnh, bật xác thực hai yếu tố (2FA).
  - Tránh nhấp vào liên kết hoặc tải tệp từ nguồn không rõ.
  - Cập nhật phần mềm thường xuyên để vá lỗ hổng.
- Đối với tổ chức:
  - Triển khai tường lửa (firewall) và hệ thống phát hiện xâm nhập (IDS).
  - Đào tạo nhân viên nhận biết lừa đảo và các mối đe dọa.

# Hosting

## Hosting là gì?

- Hosting là dịch vụ cung cấp không gian lưu trữ trên Internet để lưu các tệp của một trang web và cho phép người dùng truy cập trang web đó qua mạng.
  - Trang web là nội dung bên trong (nội thất).
  - Hosting là không gian vật lý (nhà) để chứa nội dung đó.
  - Tên miền (domain) là "địa chỉ nhà" để người khác tìm đến.

## Cách Hosting hoạt động

### Máy chủ (Server)

- Hosting được cung cấp bởi các máy chủ – những máy tính mạnh mẽ, hoạt động 24/7, được kết nối Internet tốc độ cao.
- Máy chủ lưu trữ tất cả tệp của trang web (code, hình ảnh, v.v.) trong ổ cứng của nó.

### Quy trình hoạt động

- Người dùng yêu cầu
- Máy chủ phản hồi
- Trình duyệt hiển thị

### Các loại Hosting phổ biến

- Shared Hosting: Nhiều trang web chia sẻ tài nguyên trên cùng một máy chủ. Rẻ, phù hợp cho trang nhỏ.
- VPS Hosting (Virtual Private Server): Một máy chủ vật lý được chia thành nhiều máy chủ ảo, cung cấp tài nguyên riêng cho từng người dùng. Phù hợp cho trang vừa và nhỏ.
- Dedicated Hosting: Bạn thuê nguyên một máy chủ vật lý. Đắt, dành cho trang lớn, nhiều truy cập.
- Cloud Hosting: Dùng nhiều máy chủ kết nối qua "đám mây", linh hoạt, dễ mở rộng.

# Browser

- Browser là cầu nối giữa bạn và Internet.
- Cách hoạt động: Lấy dữ liệu từ máy chủ qua HTTP/HTTPS, phân tích và hiển thị thành trang web, với sự hỗ trợ của DNS và các công cụ xử lý nội bộ.
