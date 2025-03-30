# Server Side
# CDN 
## What is CDN?
-  CDN (Content Delivery Network) là một mạng lưới máy chủ phân tán trên toàn cầu giúp cung cấp nội dung (như trang web, hình ảnh, video, file CSS/JS) đến người dùng một cách nhanh chóng và hiệu quả hơn. CDN giúp giảm độ trễ, tăng tốc độ tải trang và giảm tải cho máy chủ gốc.
-  The popularity of CDN services continues to grow, and today the majority of web traffic is served through CDNs, including traffic from major sites like Facebook, Netflix, and Amazon.

## Benefits

- Improving website load times
- Reducing bandwidth costs
- Increasing content availability and redundancy: Nếu một máy chủ gặp sự cố, CDN sẽ tự động chuyển hướng đến máy chủ khác
- Cải thiện SEO – Trang web nhanh hơn có lợi cho thứ hạng trên Google.
- Improving website security: Chống DDoS, bảo vệ dữ liệu người dùng

## How does a CDN work?
1. Lưu trữ bản sao nội dung (Caching Content):
- Khi một trang web sử dụng CDN, nội dung tĩnh như hình ảnh, video, file CSS, JavaScript được sao chép và lưu trữ trên các máy chủ CDN đặt tại nhiều địa điểm khác nhau trên thế giới (gọi là PoP - Points of Presence).
2. Phân phối nội dung dựa trên vị trí người dùng:
- Khi người dùng truy cập vào trang web, CDN sẽ định tuyến yêu cầu đến máy chủ gần nhất thay vì máy chủ gốc, giúp giảm thời gian tải dữ liệu.
3. Giảm tải cho máy chủ gốc:
- Nhờ CDN, lượng request đến máy chủ chính giảm đáng kể, giúp giảm băng thông và tối ưu hiệu suất.
4. Bảo mật và ổn định:
- CDN có thể giúp bảo vệ trang web khỏi các cuộc tấn công DDoS bằng cách hấp thụ lượng truy cập lớn trước khi nó đến máy chủ gốc.

## Nhà cung cấp CDN phổ biến
- Cloudflare, Amazon CloudFront, Google Cloud CDN, Microsoft Azure CDN

# Client Side Caching
## Client-Side Caching là gì?
- Client-Side Caching là kỹ thuật lưu trữ dữ liệu tạm thời trên thiết bị của người dùng (trình duyệt, bộ nhớ cache, hoặc local storage) để tăng tốc độ tải trang và giảm số lần yêu cầu đến máy chủ.
## Cách hoạt động của Client-Side Caching
1. Lần tải đầu tiên
- Khi người dùng truy cập trang web, trình duyệt gửi yêu cầu đến máy chủ để tải các tài nguyên như HTML, CSS, JavaScript, hình ảnh.

- Máy chủ phản hồi với dữ liệu kèm theo các tiêu đề HTTP quy định cách trình duyệt lưu trữ cache.
2. Lần tải sau
- Khi người dùng truy cập lại, trình duyệt kiểm tra cache trước khi yêu cầu tài nguyên từ máy chủ.

- Nếu tài nguyên còn hợp lệ, trình duyệt sử dụng cache thay vì tải lại từ server, giúp trang web tải nhanh hơn.

- Nếu tài nguyên đã hết hạn hoặc thay đổi, trình duyệt sẽ gửi yêu cầu mới đến máy chủ.

## Các loại Client-Side Caching phổ biến
1. Browser Cache (Bộ nhớ cache của trình duyệt)
- Trình duyệt lưu trữ các tài nguyên tĩnh (CSS, JS, hình ảnh) để tránh tải lại nhiều lần.
- Được kiểm soát bằng các tiêu đề HTTP như Cache-Control, Expires, ETag.
2. Service Worker Cache
- Một kỹ thuật trong Progressive Web Apps (PWA), cho phép ứng dụng web hoạt động ngay cả khi offline bằng cách lưu trữ dữ liệu cục bộ.

- Dùng API Cache Storage để lưu tài nguyên.
3. Local Storage và Session Storage
- Local Storage: Lưu trữ dữ liệu dưới dạng key-value trên trình duyệt mà không hết hạn (trừ khi bị xóa thủ công).
- Session Storage: Lưu trữ tạm thời và bị xóa khi đóng trình duyệt.
4. IndexedDB
- Một cơ sở dữ liệu NoSQL tích hợp trong trình duyệt, giúp lưu trữ lượng lớn dữ liệu có cấu trúc như JSON.
- Thích hợp cho các ứng dụng web phức tạp.