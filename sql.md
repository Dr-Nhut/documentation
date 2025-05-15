# Learn the basic

## What Are Relational Databases?

- A relational database is a collection of data points with pre-defined relationships between them.
- The relational model organizes data into tables — with each row representing an individual record and each column consisting of attributes that contain values. This tabular database structure makes it easy to establish relationships between data points so that the information can be accessed in a variety of different ways without reorganizing the data itself.
- Key features include:
  - Use of SQL (Structured Query Language) for querying and managing data
  - Support for ACID transactions (Atomicity, Consistency, Isolation, Durability)
  - Enforcement of data integrity through constraints (e.g., primary keys, foreign keys)
  - Ability to establish relationships between tables, enabling complex queries and data retrieval
  - Scalability and support for multi-user environments

## RDBMS Benefits and Limitations

- Benefits
  - Structured Data: RDBMS allows data storage in a structured way, using rows and columns in tables -> ensuring efficient and flexible usage.
  - ACID Properties: ACID stands for Atomicity, Consistency, Isolation, and Durability. These properties ensure reliable and safe data manipulation in a RDBMS, making it suitable for mission-critical applications
  - Normalization: RDBMS supports data normalization, a process that organizes data in a way that reduces data redundancy and improves data integrity.
  - Scalability: RDBMSs generally provide good scalability options, allowing for the addition of more storage or computational resources as the data and workload grow.
  - Data Integrity: RDBMS provides mechanisms like constraints, primary keys, and foreign keys to enforce data integrity and consistency, ensuring that the data is accurate and reliable.
  - Security: RDBMSs offer various security features such as user authentication, access control, and data encryption to protect sensitive data.
- Limitations
  - Complexity: Setting up and managing an RDBMS can be complex, especially for large applications.
  - Cost: RDBMSs can be expensive, both in terms of licensing fees and the computational and storage resources they require.
  - Fixed Schema: RDBMS follows a rigid schema for data organization, which means any changes to the schema can be time-consuming and complicated.
  - Handling of Unstructured Data: RDBMSs are not suitable for handling unstructured data like multimedia files, social media posts, and sensor data, as their relational structure is optimized for structured data
  - Horizontal Scalability: RDBMSs are not as easily horizontally scalable as NoSQL databases. Scaling horizontally, which involves adding more machines to the system, can be challenging in terms of cost and complexity.

# Basic SQL Syntax

## Data Types
### Numberic
- Đối với integer, display width chỉ ảnh hưởng đến cách số được hiển thị, nếu có dùng ZEROFILL
- ZEROFILL không còn được khuyến khích, LPAD() hoặc lưu dữ liệu dạng chuỗi (CHAR, VARCHAR) nếu dữ liệu mang tính định danh.
```sql
CREATE TABLE test (
    id INT(5) ZEROFILL
);
INSERT INTO test (id) VALUES (42); --value: 00042
SELECT * FROM test;
```

| Kiểu dữ liệu        | Ý nghĩa của M           | Ý nghĩa của D             | Đặc điểm chính                                               | Dùng khi nào                                 |
|---------------------|--------------------------|----------------------------|---------------------------------------------------------------|----------------------------------------------|
| `INT(M)`            | Độ rộng hiển thị (display width), không ảnh hưởng đến giá trị lưu trữ | —                          | Chỉ ảnh hưởng khi dùng với `ZEROFILL` <br> (Bị loại bỏ từ MySQL 8.0) | Khi muốn hiển thị số có padding 0 (trước 8.0)|
| `DECIMAL(M, D)`     | Tổng số chữ số (precision) | Số chữ số sau dấu thập phân (scale) | Độ chính xác tuyệt đối, dùng số thập phân cố định           | Dùng cho tiền tệ, tính toán tài chính        |
| `FLOAT(M, D)`       | Tổng số chữ số (đề xuất độ chính xác) | Số chữ số sau dấu thập phân (scale) | Số thực dấu phẩy động, có thể có sai số nhỏ                | Khi cần hiệu năng, không yêu cầu tuyệt đối   |
| `DOUBLE(M, D)`      | Như `FLOAT`, nhưng độ chính xác cao hơn | Như trên                  | Cũng là số thực, chính xác hơn `FLOAT`, chiếm nhiều bộ nhớ hơn | Khi cần xử lý số thực có giá trị lớn hơn     |

- Các kiểu dữ liệu số như: TINYINT, SMALLINT, INT, BIGINT, FLOAT, DOUBLE, DECIMAL, v.v. đều có thể thêm thuộc tính UNSIGNED (Không dấu) hoặc SIGNED (Có dấu).
- UNSIGNED khong con duoc khuyen khich, thay vao do CHECK là một ràng buộc (constraint) dùng để giới hạn giá trị có thể được lưu trong một cột, đảm bảo dữ liệu thỏa mãn một điều kiện nào đó.
```sql
ALTER TABLE employees
ADD CHECK (age <= 65);
```
- SERIAL is an alias for BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE. 
- BIT(n) là kiểu dữ liệu lưu trữ chuỗi nhị phân có độ dài đúng bằng n bit (1 - 64). Đây là kiểu dữ liệu được tối ưu hóa để lưu trữ thông tin dạng bit-level, không phải là boolean (mặc dù thường dùng để giả lập TRUE/FALSE).
- TINYINT là kiểu số nguyên nhỏ nhất trong MySQL. Dùng để lưu trữ các số nguyên trong phạm vi giới hạn, với kích thước 1 byte (8 bits: -128 -> 127 | UNSIGNED: 0 -> 255).
- SMALLINT là kiểu dữ liệu số nguyên có kích thước 2 byte (16 bit)
- MEDIUMINT là kiểu dữ liệu số nguyên có kích thước 3 byte (24 bit)
- INT là kiểu dữ liệu số nguyên có kích thước 4 byte (32 bit)
- BIGINT là kiểu dữ liệu số nguyên có kích thước 8 byte (64 bit)
- DECIMAL (hoặc DEC) dùng để lưu trữ các số thực chính xác cao. Lưu trữ số thập phân với độ chính xác cao và không làm mất mát số học.
- FLOAT (4 bytes) lưu trữ số thực với độ chính xác hạn chế , (Khoảng 7 chữ số chính xác), ví dụ như các phép tính khoa học hoặc khi bạn không cần sự chính xác tuyệt đối.
- Out-of-Range và Overflow
  - **Out-of-Range**: Khi giá trị vượt quá phạm vi cho phép của kiểu dữ liệu.
    - Ví dụ: Lưu `10,000` vào `TINYINT` (phạm vi từ -128 đến 127).
  
  - **Overflow**: Khi kết quả phép toán vượt quá phạm vi kiểu dữ liệu.
    - Ví dụ: Thực hiện phép cộng với `INT` và kết quả vượt quá phạm vi.

- Xử lý Out-of-Range và Overflow trong MySQL

  1. **Chế độ `STRICT_TRANS_TABLES` và `STRICT_ALL_TABLES`**

  - Khi **`STRICT` mode** bật, MySQL báo lỗi khi có **Out-of-Range** hoặc **Overflow**.
  
  2. **Chế độ `STRICT` và non-`STRICT`**

  - **`STRICT` mode**: Báo lỗi khi có dữ liệu sai phạm.
  - **Non-`STRICT` mode**: MySQL sẽ tự động điều chỉnh hoặc cắt ngắn giá trị vượt phạm vi.
  3. Sử dụng CAST để ép kiểu  
  4. Sử dụng IFNULL, COALESCE để xử lý giá trị NULL
### Date and Time
- 📅 So sánh các kiểu dữ liệu thời gian trong MySQL

| Kiểu dữ liệu | Mô tả ngắn | Phạm vi giá trị | Định dạng lưu trữ mặc định | Hỗ trợ phần giây (`fsp`) | Ghi chú |
|--------------|------------|------------------|-----------------------------|---------------------------|--------|
| **DATE**     | Ngày       | `'1000-01-01'` đến `'9999-12-31'` | `YYYY-MM-DD`               | ❌ Không                 | Dùng để lưu ngày, không có giờ |
| **TIME**     | Giờ        | `'-838:59:59.000000'` đến `'838:59:59.000000'` | `HH:MM:SS[.fraction]`      | ✅ Có (`TIME(fsp)`)     | Cho cả giá trị âm và dương |
| **TIMESTAMP**| Dấu thời gian (UTC) | `'1970-01-01 00:00:01.000000' UTC` đến `'2038-01-19 03:14:07.999999' UTC` | `YYYY-MM-DD HH:MM:SS[.fraction]` | ✅ Có (`TIMESTAMP(fsp)`) | Bị ảnh hưởng bởi múi giờ |
| **YEAR**     | Năm        | `1901` đến `2155` (hoặc `0000`) | `YYYY`                    | ❌ Không                 | Lưu năm dạng 4 chữ số |

---

- **TIMESTAMP**:
  - Lưu trữ theo UTC, khi đọc sẽ tự động chuyển theo `time_zone` của session.
  - Hữu ích trong các hệ thống phân tán, đa múi giờ.

- **TIME**:
  - Có thể dùng để lưu thời lượng (duration) như `03:15:45`.
  - Có thể âm – ví dụ: `-02:30:00`.

- **YEAR**:
  - Kiểu lưu trữ rất nhỏ gọn (1 byte), phù hợp để lưu năm sinh, năm sản xuất,...
  - Mặc định là `YEAR(4)`, `YEAR(2)` đã bị loại bỏ từ MySQL 5.7.

- ⏱️ Fractional Seconds trong MySQL: MySQL cho phép sử dụng **phần giây thập phân (fractional seconds)** cho các kiểu dữ liệu: `TIME`, `DATETIME`, `TIMESTAMP`. Độ chính xác tối đa: tối đa **6 chữ số** (microseconds)
- Cách khai báo cột có phần giây thập phân
```sql
--type_name(fsp)
CREATE TABLE events (
    start_time DATETIME(3),    -- chính xác đến milliseconds
    log_time TIMESTAMP(6),     -- chính xác đến microseconds
    duration TIME(2)           -- chính xác đến centiseconds
);
```

- 🔄 Tự động khởi tạo và cập nhật với TIMESTAMP và DATETIME trong MySQL

  - MySQL cho phép cột `TIMESTAMP` và `DATETIME` **tự động nhận giá trị hiện tại** khi insert va update

```sql
CREATE TABLE example (
    id INT PRIMARY KEY,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```
- Convert
## 🧾 Bảng tổng kết chuyển đổi giữa các kiểu DATE/TIME trong MySQL

| Từ kiểu dữ liệu | Sang kiểu dữ liệu | Cách thực hiện                                                                 |
|------------------|-------------------|---------------------------------------------------------------------------------|
| `DATETIME`       | `DATE`            | `DATE(datetime)` hoặc `CAST(datetime AS DATE)`                                  |
| `DATETIME`       | `TIME`            | `TIME(datetime)` hoặc `CAST(datetime AS TIME)`                                  |
| `DATE` + `TIME`  | `DATETIME`        | `CAST(CONCAT(date, ' ', time) AS DATETIME)`                                    |
| Chuỗi (`VARCHAR`) | `DATE` / `DATETIME` | `STR_TO_DATE(string, format)`                                                  |
| `DATE` / `DATETIME` | Chuỗi (`VARCHAR`) | `DATE_FORMAT(date, format)`                                                    |
| Chuỗi `TIME`     | `TIME`            | `CAST('12:30:00' AS TIME)`                                                     |
| Chuỗi `DATE`     | `DATE`            | `CAST('2025-05-04' AS DATE)` hoặc `STR_TO_DATE(...)`                            |
| `YEAR`           | `VARCHAR`         | `CAST(year AS CHAR)` hoặc `CONVERT(year, CHAR)`                                |

---

- 📌 Ghi chú:

  - Hàm `CAST(expr AS type)` và `CONVERT(expr, type)` gần như tương đương nhau.
  - Khi nối chuỗi ngày và giờ: dùng `CONCAT(date, ' ', time)`.
  - Với `STR_TO_DATE()` và `DATE_FORMAT()`, cần định dạng đúng theo cú pháp ngày giờ (ví dụ: `%Y-%m-%d`, `%H:%i:%s`, v.v.).


### String
# 🧵 Các kiểu dữ liệu chuỗi (String Data Types) trong MySQL

| Kiểu dữ liệu | Đặc điểm chính | Dung lượng lưu trữ | Ghi chú |
|--------------|----------------|--------------------|---------|
| `CHAR(n)`    | Chuỗi cố định độ dài | Tối đa 255 ký tự | Tự động **đệm khoảng trắng** bên phải nếu ngắn hơn |
| `VARCHAR(n)` | Chuỗi thay đổi độ dài | Tối đa 65,535 byte (tùy charset) | Có thêm 1–2 byte để lưu độ dài chuỗi |
| `TEXT`       | Văn bản dài | Tối đa 65,535 byte | Không hỗ trợ `INDEX` đầy đủ trừ khi chỉ số hóa một phần |
| `TINYTEXT`   | Văn bản rất ngắn | Tối đa 255 byte | Sử dụng 1 byte cho độ dài |
| `TEXT`       | Văn bản trung bình | Tối đa 65,535 byte | Sử dụng 2 byte cho độ dài |
| `MEDIUMTEXT` | Văn bản dài | Tối đa 16,777,215 byte | Sử dụng 3 byte cho độ dài |
| `LONGTEXT`   | Văn bản cực dài | Tối đa 4,294,967,295 byte (~4GB) | Sử dụng 4 byte cho độ dài |
| `ENUM('a','b',...)` | Danh sách giá trị cố định | Lưu dưới dạng số nguyên | Tiết kiệm dung lượng, chỉ lưu 1 giá trị |
| `SET('a','b',...)`  | Tập hợp giá trị cố định (nhiều giá trị chọn cùng lúc) | Lưu dưới dạng bitmask | Có thể chọn nhiều giá trị đồng thời |

---

- 📌 Ghi chú thêm:

- **`CHAR`** phù hợp khi chuỗi có độ dài cố định (ví dụ: mã quốc gia, mã sản phẩm).
- **`VARCHAR`** phù hợp cho dữ liệu văn bản thông thường có độ dài biến đổi.
- Các kiểu `TEXT` không nên dùng nếu cần **tìm kiếm hoặc sắp xếp thường xuyên** → dùng `VARCHAR` thay thế.
- `ENUM` hữu ích để **hạn chế giá trị nhập vào**, còn `SET` cho phép **nhiều lựa chọn cùng lúc**.

---
- 📚 `CHARACTER SET` và `COLLATE` trong MySQL

  - **`CHARACTER SET`** chỉ định bộ ký tự được sử dụng để lưu trữ và xử lý chuỗi dữ liệu.
  - Một số bộ ký tự phổ biến trong MySQL:
    - `utf8` – Bộ ký tự UTF-8 (hỗ trợ nhiều ngôn ngữ và ký tự đặc biệt)
    - `latin1` – Bộ ký tự Latin-1 (dùng cho các ngôn ngữ phương Tây)
    - `utf8mb4` – Bộ ký tự UTF-8 đầy đủ (hỗ trợ các ký tự Unicode bao gồm emoji)

- **`COLLATE`** chỉ định cách sắp xếp và so sánh chuỗi, dựa trên **`CHARACTER SET`** được chọn.
- `COLLATE` ảnh hưởng đến **so sánh chuỗi (case-sensitivity)** và **sắp xếp thứ tự (ordering)** trong các truy vấn.

-  🖥️ `BINARY` Attribute trong MySQL
  - **`BINARY`** là một thuộc tính có thể được sử dụng để **chỉ định dữ liệu nhị phân** hoặc **so sánh nhị phân**, có nghĩa là MySQL sẽ so sánh chuỗi theo từng byte, phân biệt chữ hoa chữ thường.
  - **`BINARY`** có thể được sử dụng trong **kiểu dữ liệu chuỗi** như `CHAR`, `VARCHAR` hoặc **kiểu dữ liệu nhị phân** như `BLOB` hoặc `VARBINARY`.
  - Khi áp dụng **`BINARY`** vào cột, mọi phép so sánh chuỗi sẽ trở nên **case-sensitive** (phân biệt chữ hoa và chữ thường).
  - Thay vì sử dụng **`BINARY`** (khong con khuyen khich trong 8.4), các ứng dụng nên điều chỉnh để sử dụng **collation kết thúc với `_bin`** để thực hiện so sánh nhị phân.
# Tổng Quan Các Kiểu Dữ Liệu BINARY, VARBINARY, BLOB, và TEXT trong MySQL

| Kiểu Dữ Liệu   | Đặc Điểm                                     | Độ Dài Tối Đa      | Mục Đích Sử Dụng                                         | Cú Pháp Ví Dụ                                    |
|----------------|----------------------------------------------|--------------------|----------------------------------------------------------|--------------------------------------------------|
| **BINARY(N)**  | Lưu trữ dữ liệu nhị phân với độ dài cố định. | Tối đa 65,535 byte | Dữ liệu nhị phân cố định, không thay đổi độ dài.          | `BINARY(16)`                                      |
| **VARBINARY(N)** | Lưu trữ dữ liệu nhị phân với độ dài thay đổi. | Tối đa 65,535 byte | Dữ liệu nhị phân thay đổi, có thể ngắn hơn độ dài tối đa. | `VARBINARY(255)`                                  |
| **BLOB**       | Lưu trữ các đối tượng nhị phân lớn (Binary Large Object). | Tối đa 65,535 byte | Dữ liệu nhị phân lớn như hình ảnh, âm thanh, video.       | `BLOB`                                            |
| **TEXT**       | Lưu trữ chuỗi văn bản dài.                   | Tối đa 65,535 ký tự| Lưu trữ văn bản dài.                                      | `TEXT`                                            |

---
### Spatial Type
### Json
- Cho phép lưu trữ dữ liệu dưới dạng JSON trực tiếp trong db, giúp việc xử lý dữ liệu phức tạp trở nên đơn giản và trực quan. Kiểu dữ liệu này được hỗ trợ natively trong MySQL, cung cấp các công cụ để lưu trữ, truy vấn và xử lý các đối tượng JSON.
- Điểm Mạnh:
  - Lưu trữ dữ liệu không cấu trúc: JSON rất linh hoạt trong việc lưu trữ dữ liệu không theo cấu trúc cố định
  - Hỗ trợ truy vấn JSON: MySQL cung cấp các hàm và toán tử đặc biệt để truy vấn và thao tác với dữ liệu JSON như JSON_EXTRACT(), JSON_UNQUOTE(), JSON_SET(), JSON_ARRAY(), JSON_OBJECT() để truy vấn và sửa đổi dữ liệu JSON.
  - Tương thích với ứng dụng web: JSON là một định dạng phổ biến trong các ứng dụng web, đặc biệt là trong giao tiếp API.
- Điểm Yếu:
  - Hiệu suất: Truy vấn và thao tác trên dữ liệu JSON có thể kém hiệu quả hơn so với các loại dữ liệu truyền thống như INT, VARCHAR, hoặc TEXT. Các truy vấn phức tạp hoặc xử lý trên các trường JSON có thể làm giảm hiệu suất nếu dữ liệu không được tối ưu hóa.
  -Không hỗ trợ toàn bộ tính năng của mô hình quan hệ: Một số tính năng như constraints hoặc các phép toán liên kết (JOINs) có thể không dễ dàng thực hiện trên dữ liệu JSON.
  - Thiếu khả năng lập chỉ mục: Truy vấn dữ liệu JSON thường không thể sử dụng chỉ mục một cách hiệu quả
  - Không thể dễ dàng thực hiện các phép toán tổng hợp

# Data Definition Language (DDL)

# Data Manipulation Language (DML)

# Aggregate Queries

# Data Constraints

# SQL JOIN Queries

# Sub Queries

## Nested Subqueries

## Correlated Subqueries

## Different type

### Scalar

# Advanced functions

## String Functions

```sql
CONCAT(expression1, expression2, expression3,...)
--Example
SELECT CONCAT(Address, " ", PostalCode, " ", City) AS Address
FROM Customers;

LENGTH(string)
--Example
SELECT LENGTH(CustomerName) AS LengthOfName
FROM Customers;

SUBSTRING(string, start, length)
--Or
SUBSTRING(string FROM start FOR length)
--Example
SELECT SUBSTRING(CustomerName, 2, 5) AS ExtractString
FROM Customers;


REPLACE(string, from_string, new_string)
--Example
SELECT REPLACE("XYZ FGH XYZ", "X", "M");

UPPER(text)
--Example
SELECT UPPER(CustomerName) AS UppercaseCustomerName
FROM Customers;

LOWER(text)
--Example
SELECT LOWER(CustomerName) AS LowercaseCustomerName
FROM Customers;
```

## Numeric functions

```sql
FLOOR(number)
--Example
SELECT FLOOR(25);

ABS(number)
--Example
SELECT ABS(-243.5);

MOD(x, y)
or
x MOD y
or
x % y
--Example
SELECT 18 MOD 4;

ROUND(number, decimals)
--Example
SELECT ROUND(135.375, 2);

CEILING(number)
--Example
SELECT CEILING(25);
```

## Date and Time

```sql
DATE(expression)
--Example
SELECT DATE("2017-06-15 09:34:21");

TIME(expression)
--Example
SELECT TIME("2017-08-15 19:30:10");


TIMESTAMP(expression, time)
-- returns a datetime value based on a date or datetime value.
--Example
SELECT TIMESTAMP("2017-07-23");

DATEDIFF(date1, date2)
-- Return the number of days between two date values
SELECT DATEDIFF("2017-06-25", "2017-06-15");
```

## Conditional

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END;
---Example
SELECT OrderID, Quantity,
CASE
    WHEN Quantity > 30 THEN "The quantity is greater than 30"
    WHEN Quantity = 30 THEN "The quantity is 30"
    ELSE "The quantity is under 30"
END
FROM OrderDetails;

-- The NULLIF() function compares two expressions and returns NULL if they are equal. Otherwise, the first expression is returned
NULLIF(expr1, expr2)
--Example
SELECT NULLIF(25, "Hello");


-- The COALESCE() function returns the first non-null value in a list.
COALESCE(val1, val2, ...., val_n)
--Example
SELECT COALESCE(NULL, 1, 2, 'W3Schools.com');
```

# Views

# Indexs

# Trasactions

## What is a trasaction?

- Transactions là một tập hợp các thao tác db được thực hiện như một đơn vị công việc duy nhất, đảm bảo tính toàn vẹn và nhất quán của dữ liệu.

## Đặc điểm

- Atomicity (Tính nguyên tử): Đảm bảo rằng tất cả các thao tác trong một transaction được thực hiện hoàn toàn hoặc không thực hiện gì cả. Nếu một phần của transaction thất bại, toàn bộ transaction sẽ bị hủy bỏ (rollback).
- Consistency (Tính nhất quán): transaction đưa db từ một trạng thái hợp lệ sang một trạng thái hợp lệ khác, tuân thủ các ràng buộc, quy tắc và định nghĩa dữ liệu.
- Isolation (Tính cô lập): Các transaction được thực hiện độc lập với nhau. Một transaction đang thực hiện sẽ không bị ảnh hưởng bởi các transaction khác cho đến khi nó hoàn tất.
- Durability (Tính bền vững): Đảm bảo rằng khi một transaction đã được xác nhận (committed), các thay đổi sẽ được lưu trữ vĩnh viễn, ngay cả khi hệ thống gặp sự cố.

## Syntax

```sql
--START TRANSACTION hoặc BEGIN: Bắt đầu một giao dịch mới.
START TRANSACTION;
--COMMIT: Xác nhận giao dịch, lưu tất cả các thay đổi vào cơ sở dữ liệu
COMMIT;
--ROLLBACK: Hủy bỏ giao dịch, hoàn tác tất cả các thay đổi đã thực hiện trong giao dịch
ROLLBACK;
--SAVEPOINT: Đặt một điểm lưu trữ tạm thời trong giao dịch để có thể quay lại nếu cần.
SAVEPOINT savepoint_name;
--ROLLBACK TO SAVEPOINT: Hoàn tác các thay đổi trở về một savepoint cụ thể
ROLLBACK TO savepoint_name;
--SET TRANSACTION: Thiết lập các thuộc tính cho giao dịch, chẳng hạn như mức độ cô lập (isolation level).
SET TRANSACTION READ ONLY;
```

## Các mức độ cô lập

- READ UNCOMMITTED: Cho phép một transaction đọc dữ liệu chưa được xác nhận (dirty read). Mức này ít được sử dụng vì có nguy cơ cao về dữ liệu không nhất quán.
- READ COMMITTED: Chỉ cho phép đọc dữ liệu đã được xác nhận. Ngăn chặn dirty read nhưng có thể xảy ra non-repeatable read.
- REPEATABLE READ: Đảm bảo rằng các lần đọc trong cùng một transaction trả về kết quả giống nhau, trừ khi chính transaction đó thay đổi dữ liệu. Đây là mức mặc định trong InnoDB.
- SERIALIZABLE: Cao nhất, đảm bảo các giao dịch được thực hiện tuần tự hoàn toàn, loại bỏ mọi vấn đề về đồng thời nhưng có thể ảnh hưởng đến hiệu suất.

## Note

- Hiệu suất: Giao dịch dài hoặc phức tạp có thể làm giảm hiệu suất do khóa dữ liệu (locking) và tăng nguy cơ xung đột.
- Khóa (Locks): InnoDB sử dụng cơ chế khóa để quản lý đồng thời. Các giao dịch có thể gây ra deadlock nếu không được thiết kế cẩn thận.
- Tự động commit: Theo mặc định, MySQL bật chế độ autocommit, nghĩa là mỗi câu lệnh SQL được coi như một giao dịch riêng lẻ và tự động commit. Để sử dụng giao dịch thủ công, bạn cần tắt autocommit hoặc sử dụng START TRANSACTION
- Thời gian giao dịch: Giữ giao dịch ngắn gọn để tránh giữ khóa lâu, giảm nguy cơ xung đột và cải thiện hiệu suất

# Data Integrity and Security

## Data Integrity (Tính toàn vẹn dữ liệu)

- Tính toàn vẹn dữ liệu đảm bảo rằng dữ liệu trong db là chính xác, nhất quán, và đáng tin cậy trong suốt vòng đời của nó.

### Phân loại

- Entity Integrity (Tính toàn vẹn thực thể)
  - Đảm bảo mỗi bản ghi trong bảng được xác định duy nhất, thường thông qua khóa chính (Primary Key).
  - Trong MySQL, khóa chính không được phép chứa giá trị NULL và phải là duy nhất.
- Domain Integrity (Tính toàn vẹn miền)
  - Đảm bảo các giá trị trong một cột thuộc về một tập hợp giá trị hợp lệ (kiểu dữ liệu, phạm vi, định dạng).
  - MySQL sử dụng các kiểu dữ liệu (như INT, VARCHAR, DATE) và các ràng buộc như NOT NULL, CHECK để thực thi.
- Referential Integrity (Tính toàn vẹn tham chiếu)
  - Đảm bảo mối quan hệ giữa các bảng được duy trì thông qua khóa ngoại (Foreign Key).
  - Khóa ngoại phải tham chiếu đến một giá trị hợp lệ trong bảng cha hoặc là NULL.
  - MySQL hỗ trợ các hành động như ON DELETE CASCADE hoặc ON UPDATE RESTRICT để duy trì tính toàn vẹn.
- User-Defined Integrity
  - Các quy tắc hoặc logic kinh doanh được áp dụng thông qua trigger, stored procedure, hoặc ứng dụng.

### Cơ chế đảm bảo Data Integrity trong MySQL

- Constraints (Ràng buộc): PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK.
- Transactions
- Triggers: trigger tự động thực hiện các hành động khi dữ liệu được thêm, sửa, hoặc xóa để duy trì các quy tắc toàn vẹn.
- Data Validation trong ứng dụng

## Data Security (Bảo mật dữ liệu)

- Bảo mật dữ liệu liên quan đến việc bảo vệ dữ liệu khỏi truy cập trái phép, sửa đổi, mất mát, hoặc rò rỉ. Trong MySQL, bảo mật dữ liệu được triển khai thông qua nhiều lớp kiểm soát và công cụ.

### Các khía cạnh của Data Security

- Authentication (Xác thực): Đảm bảo rằng chỉ những người dùng được phép mới có thể truy cập vào cơ sở dữ liệu. MySQL sử dụng hệ thống tài khoản người dùng với tên người dùng và mật khẩu.

```sql
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'secure_password';
```

- Authorization (Phân quyền): Quy định quyền truy cập cụ thể cho từng người dùng hoặc vai trò (role). MySQL cung cấp các quyền như SELECT, INSERT, UPDATE, DELETE, CREATE, v.v.

```sql
GRANT SELECT, INSERT ON mydb.* TO 'app_user'@'localhost';
REVOKE DELETE ON mydb.* FROM 'app_user'@'localhost';
```

- Encryption (Mã hóa):

  - At Rest: MySQL hỗ trợ mã hóa dữ liệu lưu trữ trên đĩa bằng cách sử dụng plugin như InnoDB tablespace encryption.

  ```sql
  CREATE TABLE sensitive_data (
    id INT,
    data VARCHAR(100)
  ) ENCRYPTION='Y';
  ```

  - In Transit: MySQL hỗ trợ SSL/TLS để mã hóa dữ liệu truyền giữa client và server.
    sql

  ```sql
  GRANT USAGE ON *.* TO 'app_user'@'localhost' REQUIRE SSL;
  ```

- Access control: MySQL cho phép giới hạn truy cập dựa trên địa chỉ IP hoặc hostname.

```sql
CREATE USER 'remote_user'@'192.168.1.%' IDENTIFIED BY 'password';
```

- Auditing and Logging: Theo dõi các hoạt động trong db để phát hiện hành vi đáng ngờ. MySQL Enterprise cung cấp plugin Audit Log, hoặc bạn có thể sử dụng general_log và slow_query_log để ghi lại các truy vấn.

```sql
SET GLOBAL general_log = 'ON';
```

- Data Masking and Anonymization: MySQL Enterprise cung cấp tính năng che giấu dữ liệu (data masking) để bảo vệ thông tin nhạy cảm như số thẻ tín dụng hoặc email.

- Backup and Recovery: Đảm bảo dữ liệu có thể được khôi phục trong trường hợp mất mát hoặc hỏng hóc. MySQL hỗ trợ các công cụ như mysqldump hoặc MySQL Enterprise Backup để sao lưu dữ liệu.

```bash
mysqldump -u root -p mydb > backup.sql
```

### Các mối đe dọa và biện pháp bảo vệ

- SQL Injection
- Privilege Escalation
  - Người dùng có quyền thấp cố gắng truy cập dữ liệu hoặc chức năng không được phép.
  - Biện pháp: Áp dụng nguyên tắc least privilege (quyền tối thiểu cần thiết).
- Data Leakage
  - Dữ liệu nhạy cảm bị lộ do cấu hình sai hoặc truy cập trái phép.
  - Biện pháp: Mã hóa dữ liệu, kiểm tra cấu hình MySQL, và sử dụng tường lửa
- Brute Force Attacks:
  - Kẻ tấn công cố gắng đoán mật khẩu người dùng.
  - Biện pháp: Sử dụng mật khẩu mạnh, giới hạn số lần thử đăng nhập, và bật xác thực hai yếu tố (nếu có).

# Performance Optimization

## Query Analysis Techniques

### Sử dụng Explain

```sql
EXPLAIN SELECT * FROM orders WHERE customer_id = 123;
--output
--id: Thứ tự thực thi.
--select_type: Loại truy vấn (SIMPLE, SUBQUERY, JOIN, v.v.).
--table: Bảng được truy cập.
--type: Loại truy cập (const, ref, range, index, ALL, v.v.). ALL thường chỉ ra quét toàn bảng, không tối ưu.
--possible_keys: Các chỉ mục có thể sử dụng.
--key: Chỉ mục thực sự được sử dụng.
--rows: Số hàng dự kiến được quét.
--Extra: Thông tin bổ sung, ví dụ: “Using temporary” hoặc “Using filesort” (có thể chỉ ra vấn đề hiệu suất).
```

### EXPLAIN ANALYZE (MySQL 8.0 trở lên)

- Là phiên bản mở rộng của EXPLAIN, cung cấp thông tin chi tiết hơn về kế hoạch thực thi thực tế, bao gồm thời gian xử lý và số hàng thực sự được quét.
- Phân tích thời gian thực thi của từng bước trong truy vấn, xác định các bước tốn kém nhất.

```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE customer_id = 123;
--output
--Cây thực thi (execution tree) với thời gian xử lý từng bước.
--Số hàng thực tế so với ước tính.
--Thông tin về vòng lặp (loop) hoặc thao tác lặp lại.
```

### Performance Schema

- Performance Schema là một công cụ tích hợp trong MySQL để giám sát hiệu suất hệ thống và truy vấn ở cấp độ chi tiết
- Theo dõi thời gian thực thi truy vấn.
- Xác định các truy vấn chậm hoặc tiêu tốn tài nguyên.
- Phân tích khóa (locks), chờ đợi (waits), và sự kiện hệ thống.
- Phù hợp cho môi trường sản xuất để giám sát liên tục.

```sql
SELECT EVENT_NAME, COUNT_STAR, SUM_TIMER_WAIT
FROM performance_schema.events_statements_summary_by_digest
WHERE SUM_TIMER_WAIT > 0
ORDER BY SUM_TIMER_WAIT DESC
LIMIT 10;

--output
-- Các truy vấn được nhóm theo “digest” (bản tóm tắt truy vấn).
-- Thời gian thực thi tổng cộng, số lần thực thi, và các số liệu khác
```

### Slow Query Log

- Ghi lại các truy vấn vượt quá ngưỡng thời gian thực thi được cấu hình hoặc không sử dụng chỉ mục.
- Xác định các truy vấn chậm trong hệ thống.
- Phân tích lịch sử truy vấn để tối ưu hóa

```sql
--config
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1; -- Ghi lại truy vấn chạy > 1 giây
SET GLOBAL slow_query_log_file = '/var/log/mysql/slow.log';

--Phân tích: Sử dụng công cụ như mysqldumpslow hoặc pt-query-digest (từ Percona Toolkit) để đọc log.
mysqldumpslow /var/log/mysql/slow.log
```

### General Query Log

- Ghi lại tất cả các truy vấn được gửi đến máy chủ MySQL
- Hữu ích trong giai đoạn phát triển hoặc debug để theo dõi toàn bộ hoạt động truy vấn.
- Lưu ý: Không nên bật trong môi trường sản xuất vì có thể gây ảnh hưởng nghiêm trọng đến hiệu suất.

```sql
SET GLOBAL general_log = 'ON';
SET GLOBAL general_log_file = '/var/log/mysql/general.log';
```

### Index Analysis

- Phân tích việc sử dụng chỉ mục (indexes) để đảm bảo truy vấn tận dụng chúng một cách hiệu quả.
- Kiểm tra các cột thường xuyên xuất hiện trong WHERE, JOIN, GROUP BY, hoặc ORDER BY.
  Xác định chỉ mục không cần thiết hoặc trùng lặp.
- Công cụ:
  - EXPLAIN để xem chỉ mục được sử dụng.
  - SHOW INDEX FROM table_name để liệt kê chỉ mục.
  - INFORMATION_SCHEMA.STATISTICS để phân tích thống kê chỉ mục

### Query Rewriting

### Optimizer Trace

- Cung cấp thông tin chi tiết về cách trình tối ưu hóa của MySQL chọn kế hoạch thực thi.
- Phân tích các quyết định phức tạp của trình tối ưu hóa, đặc biệt với các truy vấn có nhiều JOIN hoặc điều kiện.

```sql
SET optimizer_trace="enabled=on";
SELECT * FROM orders WHERE customer_id = 123;
SELECT * FROM information_schema.optimizer_trace;
SET optimizer_trace="enabled=off";
```

- Ưu điểm: Hữu ích khi cần debug các quyết định tối ưu hóa bất thường.

## Các vấn đề phổ biến và cách khắc phục

- Full Table Scan:
  - Nguyên nhân: Thiếu chỉ mục hoặc điều kiện WHERE không tối ưu.
  - Khắc phục: Thêm chỉ mục hoặc viết lại điều kiện WHERE.
- Filesort hoặc Using Temporary:
  - Nguyên nhân: Sử dụng ORDER BY, GROUP BY trên các cột không có chỉ mục.
  - Khắc phục: Tạo composite index hoặc giảm số lượng cột trong SELECT.
- Deadlocks hoặc Locking Issues:
  - Nguyên nhân: Nhiều giao dịch truy cập cùng dữ liệu với thứ tự khác nhau.
  - Khắc phục: Sử dụng SELECT ... FOR UPDATE cẩn thận, tối ưu hóa thứ tự truy cập bảng, hoặc giảm thời gian giao dịch.
- Subquery không hiệu quả:
  - Nguyên nhân: Subquery lặp lại nhiều lần hoặc không sử dụng chỉ mục.
  - Khắc phục: Thay bằng JOIN hoặc tạo bảng tạm.

# Avanced SQL

## Window functions

- Window Functions thực hiện tính toán trên một tập hợp các hàng được xác định bởi một window, nhưng không làm giảm số lượng hàng đầu ra như các hàm tổng hợp (SUM, AVG, v.v.) khi dùng với GROUP BY. Mỗi hàng vẫn giữ nguyên trong kết quả, nhưng được bổ sung giá trị tính toán dựa trên cửa sổ tương ứng.
- Window: Là một tập hợp các hàng liên quan đến hàng hiện tại, được xác định bởi mệnh đề OVER. Cửa sổ có thể được tùy chỉnh theo phạm vi (range) hoặc số hàng (rows), và có thể được sắp xếp hoặc phân vùng.

### Syntax

```sql
function_name(arguments) OVER (
    [PARTITION BY column_list]
    [ORDER BY column_list]
    [ROWS or RANGE frame_specification]
)
```

### Phân loại

1. Ranking Functions

- ROW_NUMBER(): Gán một số thứ tự duy nhất cho mỗi hàng trong cửa sổ.
- RANK(): Gán thứ hạng, nhưng các hàng có giá trị bằng nhau nhận cùng thứ hạng, và thứ hạng tiếp theo bị bỏ qua (ví dụ: 1, 1, 3).
- DENSE_RANK(): Tương tự RANK(), nhưng không bỏ qua thứ hạng tiếp theo (ví dụ: 1, 1, 2).
- NTILE(n): Chia các hàng trong cửa sổ thành n nhóm đều nhau.

2. Aggregate Functions

- Các hàm tổng hợp thông thường (SUM, AVG, COUNT, MAX, MIN, v.v.) có thể được sử dụng như Window Functions khi kết hợp với OVER.

3. Value Functions

- Dùng để truy cập giá trị từ các hàng khác trong cửa sổ mà không cần JOIN
  - LAG(): Lấy giá trị của cột từ hàng trước đó trong cửa sổ.
  - LEAD(): Lấy giá trị của cột từ hàng tiếp theo trong cửa sổ.
  - FIRST_VALUE(): Lấy giá trị đầu tiên trong cửa sổ.
  - LAST_VALUE(): Lấy giá trị cuối cùng trong cửa sổ.
  - NTH_VALUE(): Lấy giá trị thứ n trong cửa sổ.

### Lợi ích

- Linh hoạt: Cho phép thực hiện các phép tính phức tạp mà không cần subquery hoặc JOIN.
- Hiệu quả: Giảm số lượng truy vấn và cải thiện hiệu suất so với cách tiếp cận truyền thống.
- Đọc hiểu dễ dàng: Cú pháp rõ ràng, dễ bảo trì hơn các truy vấn phức tạp với nhiều subquery.
- Hỗ trợ phân tích dữ liệu: Rất hữu ích trong báo cáo, phân tích xu hướng, và xếp hạng.

### Hạn chế

- Window Functions chỉ khả dụng trong MySQL 8.0 trở lên.
- Hiệu suất: Với tập dữ liệu lớn, Window Functions có thể tốn tài nguyên nếu cửa sổ không được tối ưu hóa (ví dụ: thiếu chỉ mục cho ORDER BY hoặc PARTITION BY).
- Cần chỉ mục: Để cải thiện hiệu suất, đảm bảo các cột trong PARTITION BY hoặc ORDER BY có chỉ mục phù hợp.
- Khung cửa sổ (frame): Một số hàm như SUM hoặc AVG yêu cầu khung cửa sổ rõ ràng (ROWS hoặc RANGE) để tính toán chính xác, đặc biệt với LAST_VALUE().
- Không thể lồng ghép: Window Functions không thể được sử dụng trực tiếp trong các hàm khác hoặc trong WHERE, GROUP BY, HAVING. Cần dùng CTE hoặc subquery để xử lý.

## Recursive Queries

### What is Recursive Queries?

- Là một loại truy vấn cho phép một CTE tham chiếu chính nó, lặp lại quá trình xử lý cho đến khi không còn dữ liệu mới được tạo ra.
- Thường được sử dụng để duyệt qua các cấu trúc dữ liệu phân cấp, nơi một bản ghi có thể liên kết với một bản ghi khác thông qua mối quan hệ cha-con (parent-child).

### Syntax:

```sql
WITH RECURSIVE cte_name (column_list) AS (
    -- Base case: Truy vấn khởi đầu
    -- Bước 1: Thực thi Base Case
    SELECT column_list
    FROM table_name
    WHERE condition
    -- Kết nối với phần đệ quy
    UNION [ALL]
    -- Recursive case: Truy vấn lặp lại
    -- Bước 2: Thực thi Recursive Case
    SELECT column_list
    FROM table_name
    INNER JOIN cte_name ON condition
    -- Bước 3: Lặp lại
)
-- Truy vấn chính sử dụng CTE
-- Bước 4: Truy vấn kết quả
SELECT * FROM cte_name;
```

## Dynamic SQL

### Dynamic SQL là gì?

- Trong MySQL, Dynamic SQL thường được thực hiện thông qua các Prepared Statements (câu lệnh chuẩn bị) hoặc trong các Stored Procedure, Function, hoặc Trigger.

### Syntax

```sql
SET @table_name = 'employees';
SET @sql = CONCAT('SELECT * FROM ', @table_name, ' WHERE department_id = ?');
SET @dept_id = 10;

PREPARE stmt FROM @sql;
EXECUTE stmt USING @dept_id;
DEALLOCATE PREPARE stmt;
```

### Lợi ích

- Linh hoạt: Cho phép tạo truy vấn dựa trên điều kiện runtime, phù hợp với các ứng dụng có yêu cầu thay đổi.
- Tái sử dụng: Giảm số lượng mã SQL cần viết khi xử lý các trường hợp tương tự.
- Tích hợp metadata: Kết hợp với INFORMATION_SCHEMA để truy vấn cấu trúc cơ sở dữ liệu (bảng, cột, chỉ mục) và tạo truy vấn động.
- Tăng khả năng mở rộng: Hỗ trợ các ứng dụng cần xử lý dữ liệu phức tạp hoặc không cố định.

### Hạn chế

- Không hỗ trợ tất cả các câu lệnh: Một số câu lệnh như CREATE DATABASE hoặc SET không thể sử dụng trực tiếp trong Prepared Statements. Cần xử lý trong Stored Procedure hoặc mã ứng dụng.
- Hiệu suất: Dynamic SQL có thể chậm hơn truy vấn tĩnh do cần biên dịch câu lệnh tại runtime.
- Khó debug: Lỗi trong chuỗi SQL động khó phát hiện hơn so với truy vấn tĩnh.
- Giới hạn độ dài chuỗi: Biến @sql bị giới hạn bởi max_allowed_packet (mặc định 4MB trong MySQL).
- Nguy cơ SQL Injection

# Relationship
## What is MySQL Replication?
- MySQL replication is a process where data from one MySQL database (the master) is copied to one or more MySQL databases (the replicas), enabling data redundancy, scalability, and high availability. 
- Replication allows real-time or near-real-time synchronization of data between a master and replicas.
- Changes (inserts, updates, deletes) on the master are propagated to replicas.
- Replicas are typically read-only, while the master handles writes

## Types of Replication
### Asynchronous Replication
  - The master writes changes to its binary log, and replicas fetch and apply these changes independently.
  - Slight delay (lag) may occur between master and replicas.
  - Default and most common type.

### Semi-Synchronous Replication:

  - The master waits for at least one replica to acknowledge receipt of the changes before committing.
  - Reduces lag but may slow down the master.

### Group Replication

  - A cluster-based replication where multiple servers act as peers, supporting multi-master or single-primary setups.
  - Ensures high availability and fault tolerance using group consensus.

### Galera Cluster:

  - Synchronous multi-master replication using a third-party solution (Galera).
  - All nodes are writable, with near-synchronous data consistency.

## Key Components

  - Binary Log: A file on the master that records all changes (DML, DDL).
  - Relay Log: A temporary log on the replica that stores events fetched from the master’s binary log.
  - Replication Threads:
    - I/O Thread: On the replica, connects to the master and retrieves binary log events.
    - SQL Thread: On the replica, applies the events from the relay log to the database.
  - Master Info and Relay Log Info: Metadata tracking replication progress.

## How Replication Works

  - The master logs changes to its binary log.
  - The replica’s I/O thread copies events from the master’s binary log to its relay log.
  - The replica’s SQL thread reads and executes events from the relay log, updating the replica’s database.

## Common Use Cases

  - Read Scalability: Distribute read queries across replicas to reduce load on the master.
  - High Availability: Use replicas as failover servers if the master fails.
  - Backups: Perform backups on replicas to avoid locking the master.
  - Data Analytics: Run heavy analytical queries on replicas without impacting the master.
  - Geographic Distribution: Place replicas in different regions for low-latency access.

## Setting Up Replication

  - Prerequisites:
    - Enable binary logging on the master (log_bin in my.cnf).
    - Assign a unique server_id to each server.
    - Create a replication user on the master with appropriate privileges.
  - Basic Steps:
    1. Configure the master
    ```sql
    [mysqld]
    log_bin = mysql-bin
    server_id = 1
    ```
    2. Grant replication privileges
    ```sql
    GRANT REPLICATION SLAVE ON *.* TO 'repl_user'@'replica_host' IDENTIFIED BY 'password';
    ```
    3. Dump the master’s database
    ```bash
    mysqldump --single-transaction --master-data=2 -u root -p > master_dump.sql
    ```
    4. Configure the replica
    ```sql
    [mysqld]
    server_id = 2
    ```
    5. Import the dump on the replica and start replication
    ```sql
    CHANGE MASTER TO
      MASTER_HOST = '172.20.0.2',
      MASTER_PORT = 3306,
      MASTER_USER = 'root',
      MASTER_PASSWORD = 'root',
      MASTER_LOG_FILE = 'binlog.000002',
      MASTER_LOG_POS = 157,
      MASTER_CONNECT_RETRY=60,
      GET_MASTER_PUBLIC_KEY=1;
    START SLAVE;
    ```
## Replication Formats
- Statement-Based Replication (SBR):
  - Replicates SQL statements.
  - Lightweight but may cause inconsistencies with non-deterministic queries.

- Row-Based Replication (RBR):
  - Replicates actual data changes (rows).
  - More reliable but generates larger logs.

- Mixed Replication:
  - Combines SBR and RBR based on query type.

## Monitoring and Troubleshooting
- Check Replication Status
```sql
SHOW SLAVE STATUS\G
```

## Best Practices
- Use GTID (Global Transaction Identifiers) for easier replication management.
- Regularly monitor replication lag and errors.
- Ensure replicas have similar or better hardware than the master.
- Secure replication traffic with SSL/TLS.
- Test failover scenarios to ensure high availability.

## Limitations

- Asynchronous replication may lead to data inconsistency during lag.
- Replicas are read-only by default (except in multi-master setups).
-  Large datasets or high write loads can strain replication.


# Explain 
## Explain output column
1. id
  - Ý nghĩa: Thứ tự hoặc cấp độ của truy vấn con trong SQL.
  - Giá trị:
    - Nếu chỉ có một SELECT đơn giản → id = 1
    - Nếu có subquery, derived tables → id tăng dần, cao hơn là truy vấn cấp sâu hơn (xử lý trước).
  - Lưu ý: Giá trị id cao hơn được xử lý trước (giống như thứ tự thi hành).
2. select_type
  - Ý nghĩa: Loại của truy vấn.
  - Một số giá trị phổ biến:

    - SIMPLE: Truy vấn không có subquery hay UNION.

    - PRIMARY: Truy vấn ngoài cùng (truy vấn chính).

    - SUBQUERY: Truy vấn con trong SELECT, WHERE, v.v.

    - DERIVED: Truy vấn con trong FROM (tạo thành bảng tạm).

    - UNION: Một phần của UNION.

    - DEPENDENT SUBQUERY: Subquery phụ thuộc vào truy vấn cha.
3. table

  - Ý nghĩa: Tên bảng hoặc alias mà dòng EXPLAIN đang mô tả.
4. partitions
  - Ý nghĩa: Nếu bảng được phân vùng (PARTITIONED), cột này cho biết phân vùng nào được quét.
5. type
  - Ý nghĩa: Loại join (cũng là mức độ hiệu quả của truy vấn).
  - Sắp xếp từ tốt đến kém:
    const, eq_ref, ref, range, index, ALL

  - Giải thích một số giá trị:

    - ALL: Quét toàn bộ bảng (tệ nhất).
    - index: Quét toàn bộ chỉ mục.
    - range: Dùng chỉ mục với phạm vi (BETWEEN, <, >, v.v.)
    - ref: Dùng chỉ mục với giá trị không duy nhất (non-unique key).
    - eq_ref: Dùng chỉ mục với giá trị duy nhất (PRIMARY/UNIQUE).
    - const: Bảng có đúng 1 dòng khớp, MySQL xử lý như hằng số.
6. possible_keys
  - Ý nghĩa: Các chỉ mục (index) có thể được dùng để tối ưu truy vấn.
7. key
  - Ý nghĩa: Chỉ mục thực sự được chọn để dùng trong truy vấn.
  - Nếu NULL: Không dùng chỉ mục nào (tức là quét toàn bảng).
8. key_len
  - Ý nghĩa: Độ dài của chỉ mục được dùng (tính bằng byte).
  - Dùng để đánh giá hiệu quả sử dụng chỉ mục — càng nhỏ càng tốt (tránh dư thừa).
9. ref
  - Ý nghĩa: Cột hoặc hằng số được so sánh với chỉ mục.
  - Ví dụ: const, func, col_name, v.v.
10. rows
  - Ý nghĩa: Số dòng ước tính mà MySQL sẽ cần đọc để trả kết quả.
  - Giá trị này càng nhỏ càng tốt (tránh truy xuất dư thừa)
11. filtered
  - Ý nghĩa: Phần trăm dòng dự kiến sẽ thỏa mãn điều kiện WHERE sau khi lọc.

  - Ví dụ: filtered = 20.00 nghĩa là 20% dòng được giữ lại sau khi áp điều kiện.

12. extra
  - Ý nghĩa: Thông tin bổ sung. Có thể chứa các cảnh báo hiệu năng.
  - Một số giá trị cần chú ý:

    - Using where: Có sử dụng điều kiện WHERE để lọc dòng.

    - Using index: Dữ liệu được đọc hoàn toàn từ chỉ mục (rất tốt).

    - Using temporary: Dùng bảng tạm — có thể giảm hiệu suất.

    - Using filesort: Cần sắp xếp ngoài bộ chỉ mục — nên tối ưu.

    - Impossible WHERE: Điều kiện không bao giờ đúng.

    - Using join buffer: Không dùng index để join — kém hiệu quả.
  
## Cost
MySQL đánh giá chi phí của các kế hoạch truy vấn dựa trên:

  - I/O cost: chi phí truy xuất dữ liệu (disk/index scan)

  - CPU cost: chi phí xử lý logic (so sánh, join, sort, etc.)

  - Cost cho join, sort, lọc, temporary table, buffer...

## Optimize
- Tóm tắt luồng tối ưu sau EXPLAIN ANALYZE

  - Mục tiêu: Giảm estimated cost, số dòng rows, tránh Using temporary, Using filesort, FULL TABLE SCAN, hoặc JOIN BUFFER

1. TỐI ƯU INDEX
- Khi nào dùng?
  - key = NULL

  - type = ALL hoặc index

  - Extra = Using filesort, Using temporary

- Cách làm:

- Tạo chỉ mục phù hợp với:

    - Cột trong WHERE

    - Cột dùng để JOIN

    - Cột ORDER BY, GROUP BY
  
2. TỐI ƯU JOIN
- Khi nào cần?

  -EXPLAIN ANALYZE cho thấy:

    - Nested loop join với nhiều vòng lặp

    - JOIN BUFFER đang dùng vì không có chỉ mục

        type = ALL ở bảng thứ hai

- Cách làm:

  - Tạo chỉ mục ở cột JOIN

  - Đảm bảo thứ tự bảng hợp lý (bảng lọc ít dòng trước)

  - Tránh LEFT JOIN nếu không cần thiết (dễ làm MySQL không tối ưu được)

  - Nếu join phức tạp → dùng STRAIGHT_JOIN để ép thứ tự
3. TỐI ƯU WHERE, LIMIT, EXISTS
- Khi nào cần?
  - filtered rất thấp (lọc nhiều dòng nhưng lấy ít)
  - rows quá lớn, điều kiện WHERE kém hiệu quả

- Cách làm:
  - Viết lại WHERE rõ ràng để kích hoạt index

  - Tránh dùng hàm trên cột trong WHERE (YEAR(date_col), v.v.)
  - Dùng EXISTS thay vì IN nếu subquery trả về nhiều dòng

4. TRÁNH Using filesort và Using temporary
- Khi nào cần?

- Extra hiển thị "Using filesort" hoặc "Using temporary" trong EXPLAIN ANALYZE

- Cách làm:

  - Tạo index hỗ trợ ORDER BY/GROUP BY
  - Sắp xếp dữ liệu ở ứng dụng nếu cần thiết
  - Dùng LIMIT kết hợp với ORDER BY có index
5. DÙNG COVERING INDEX (Using index)
- Khi nào cần?

  - Truy vấn đơn giản, chỉ lấy cột đã có trong index

- Cách làm:
  - Tạo index gồm tất cả các cột cần SELECT
  - MySQL không cần truy xuất bảng gốc (giảm I/O)
6. TRÁNH SUBQUERY PHỨC TẠP / DERIVED TABLE
- Khi nào cần?
  - select_type = DERIVED, SUBQUERY, DEPENDENT SUBQUERY
  - Nhiều dòng EXPLAIN có id tăng dần
- Cách làm:
  - Dùng JOIN thay vì subquery nếu được
  - Materialize bảng trung gian thành bảng thật nếu cần cache
7. DÙNG FORCE INDEX (nếu cần ép MySQL dùng index)

8. PHÂN TÍCH actual rows vs estimated rows (EXPLAIN ANALYZE)
  - Nếu actual rows lệch quá xa estimated rows → cần:
    - Update statistics
    - ANALYZE TABLE để cập nhật phân phối dữ liệu
## Phuong phap
  - Quy trinh hoat dong
  ```
  SQL statement -> Check syntax -> Check logic -> Check cache -> Hard Phase (phan tich va xay dung chien luot thuc thi) / Soft Phase -> Execute
  ```

  - SQL se so sanh cac cau lenh bang string de xem da co cau lenh thuc thi truoc do khong