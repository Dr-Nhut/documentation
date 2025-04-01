# Single Responsibility
- Một class chỉ nên có một nhiệm vụ duy nhất (single responsibility). Mỗi class chỉ nên có một lý do để thay đổi
- Ví dụ, một lớp quản lý sách không nên vừa lưu trữ dữ liệu vừa in sách; in sách nên là trách nhiệm của một lớp khác.

# Open/Closed
- Class nên mở rộng mà không sửa đổi code cũ.
- Khi cần thay đổi hành vi của class, nên mở rộng bằng cách kế thừa hoặc dùng interface thay vì sửa code cũ. 
- ✅ Tốt: Dùng interface PaymentMethod để dễ thêm các phương thức thanh toán mới (Visa, PayPal…).
- ❌ Sai: Sửa trực tiếp class PaymentProcessor mỗi lần có phương thức thanh toán mới.

# Liskov Substitution Principle
- Khi kế thừa, class con không được làm thay đổi hành vi của class cha theo cách gây lỗi.
- ✅ Tốt: Class Bird có phương thức fly(), nhưng Penguin (chim cánh cụt) không thể bay → Tách thành FlyingBird và NonFlyingBird.
- ❌ Sai: Nếu Penguin kế thừa Bird nhưng không thể gọi fly(), chương trình sẽ bị lỗi.
# Interface Segregation Principle
- Không ép một class phải implement những phương thức mà nó không dùng.
- Một interface lớn nên tách nhỏ để tránh việc class phải implement những phương thức không liên quan.
- ✅ Tốt: Worker và Eater thay vì EmployeeInterface
- ❌ Sai: EmployeeInterface yêu cầu tất cả nhân viên phải có work() và eat(), nhưng Robot không cần eat().

# Dependency Inversion Principle
- Module cấp cao không nên phụ thuộc vào module cấp thấp, cả hai nên phụ thuộc vào abstraction.
- Thay vì class A gọi trực tiếp class B, nên dùng interface hoặc abstraction để tách biệt phụ thuộc.
- ✅ Tốt: Database interface để dễ dàng thay đổi giữa MySQL, MongoDB…
- ❌ Sai: Class OrderProcessor gọi trực tiếp MySQLDatabase. Nếu đổi sang MongoDB, phải sửa code.