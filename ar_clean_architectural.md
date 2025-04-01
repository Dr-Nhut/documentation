# Dependency
- La cac phu thuoc giua cac component, library hoac giua cac class
- Cac component cang it phu thuoc phan mem cang de bao tri
- Trong Clean Architectural, su phu thuoc chi la 1 chieu
## Concrete class
- La mot class cu the co the tao ra doi tuong
- Khi co qua nhieu chi tiet de tao nen mot concrete class, tao ra mot doi tuong concrete class dan den viec tao ra them nhieu doi tuong khac
- Giai phap: Su dung interface nhu mot trung gian giua class su dung va concrete class, class su dung chi quan tam den cac phuong thuc can thiet ma khong can quan tam trien khai cu the trong concrete class
- Luc nay, concrete class phu thuoc nguoc lai interface goi la Dependency Inversion

# Domain
- The domain is the real-world context in which you're attempting to solve a proplem using software
- Domain đại diện cho lõi logic nghiệp vụ (business logic) của ứng dụng, tức là phần quan trọng nhất chứa các quy tắc, quy trình và thực thể (entities) phản ánh vấn đề thực tế mà ứng dụng giải quyết
- Domain trong Clean Architecture là "trái tim" của hệ thống, nơi chứa đựng giá trị cốt lõi của ứng dụng, được thiết kế để bền vững và không bị ảnh hưởng bởi các yếu tố kỹ thuật bên ngoài.
# Trien khai
- Chia phan mem ra lam 3 nhom:
    - Domain bao gom Entity va Use Case (tim hieu them voi sach OOAD, bao gom cac quy tac, quy trinh lam viec) phu thuoc vao Entity
    - Abstraction: la cac interface dong vai tro trung gian, tranh de cach use case phu thuoc truc tiep vao implementation
    - Concrete: Bao gom implementation (lien quan den truy xuat database, call API...) va no phu thuoc vao external service (example: MySQL), day la cac thanh phan thuong xuyen thay doi. Vi du: thay doi phuong thuc luu tru

# Infrastructure
- Trien khai Frameworks & Drivers + Controllers & Gateways & Presenters