# Introduce

## AWS Cloud Overview - Regions & AZ

- Follow by document

## IAM & AWS CLI

### What is IAM?

- IAM (Identity and Access Management) is a Global service
- Root account created by default, shouldn't be used or shared
- We can create user and user có thể thuộc một hoặc nhiều group
- Groups only contain users, not other groups.

#### IAM permissions

- Users or Groups có thể được assign policies định nghĩa các permission của user hoặc group
- AWS apply the **_least privilege principle_**: không bao giờ đưa nhiều permission hơn cái user cần

#### IAM Policies

- User thuộc Group sẽ nhận policies của group, nếu user thuộc nhiều group sẽ apply tất cả policies của các group đó
- User không thuộc group có thể được set inline policy

```json
{
  "Version": "2012-10-17", // policy language version
  "Id": "S3-Account-Permissions",
  "Statement": [
    {
      "Sid": "1",
      "Effect": "Allow", // or Deny
      "Principal": {
        // account/user/role to which this policy applied to
        "AWS": ["arn:aws:iam::123456789012:root"]
      },
      "Action": ["iam:ChangePassword"],
      "Resource": ["arn:aws:iam::*:user/${aws:username}"]
    }
  ]
}
```

- Có thể custom policy nếu muốn

#### IAM Password Policy

- Can setup a password policy:
  - Set a minimum password length
  - Require specific character types: uppercase, lowercase, number, ký tự đặc biệt
  - Allow all IAM users to change their own password
  - Yêu cầu change password sau một khoảng thời gian
  - Chặn sử dụng password cũ

#### Multi Factor Authentication - MFA

- MFA = password + security device you own (**virtual device** such as Google Authenticator, Authy, **U2F Security Key** như Yubikey, **Hardware Key Fob MFA Device**, **Hardware Key Fob MFA Device for AWS GovCloud (US)**)

### AWS CLI

#### How can users access AWS?

- To access AWS:
  - AWS Management Console: password + MFA
  - AWS Command Line Interface (CLI): protected by access keys
  - AWS Software Developer Kit (SDK): protected by access keys

#### What's the AWS CLI?

- Tool cho phép tương tác với AWS services sử dụng command in Command-line shell
- Truy cập trực tiếp đến public APIs của AWS Service
- Có thể phát triển scripts để quản lý your resources
- Có thể sử dụng AWS CloudShell khi hoạt động trên browser

### IAM Roles for Service

- Có thể assign permission đến AWS service với IAM role
- Common roles: EC2 Instance roles, lambda function roles, roles for CloudFormation
- IAM Role được ưu tiên khi có xung đột với IAM User, nhất là khi user "assume" role.
- "Assume Role" cho phép user hoặc service AWS tạm thời lấy quyền của một IAM Role khác. Việc này đặc biệt hữu ích khi bạn cần phân quyền cho nhiều user hoặc service mà không cần phải gán quyền cố định cho mỗi user hoặc service.
- Tuy nhiên, trong tất cả các trường hợp, nguyên tắc "deny overrides allow" sẽ được áp dụng: Nếu một quyền bị từ chối (deny) trong bất kỳ IAM policy nào (User hoặc Role), quyền đó sẽ bị từ chối.

### IAM Security Tools

- IAM Credentials Report (account-level): a report that lists all your account's users and the status of their various credentials
- IAM Access Advisor (user-level): access advisor shows the service permissons granted to a user and when those services were last accessed.
- You can use this information to revise your policies

### Shared Responsibility Model for IAM

- AWS responsibility for:
  - Infra (global network security)
  - Config and vulnerability analysis
  - Compliance validation
- You responsibility for:
  - Users, Groups, Roles, Policies management and monitoring
  - Enable MFA on all accounts
  - Rotate all your keys often
  - Use IAM tools to apply appropriate permissions
  - Analyze access patterns & review permissions

# EC2 Fundamentals

## Managing your costs with AWS Budgets

- You can use AWS Budgets to track and take action on your AWS costs and usage, monitor your aggregate utilization and coverage metrics for your Reserved Instances (RIs) or Savings Plans

## Amazon EC2

- EC2 = Elastic Compute Cloud = Infrastructure as a Service
- Nó chủ yếu bao gồm khả năng của:
  - Renting VM (EC2)
  - Storing data on Virtual drives (EBS)
  - Distributing load across machines (ELB)
  - Scaling the services using an auto-scaling group (ASG)

## EC2 sizing & configuration options

- Operating System (OS): Linux, Windows or Mac OS
- How much compute power & cores (CPU)
- How much RAM
- How much storage space:
  - Network-attached (EBS & EFS)
  - Hardware (EC2 Instance Store)
- Network card: speech of the card, Public IP address
- Firewall rules: security group
- Bootstrap script (configure at first launch): EC2 User Data

## EC2 User data

- It is possible to bootstrap our instances using an EC2 User data script
- EC2 user data is used to automate boot tasks such as:
  - Installing updates
  - Installing software
  - Downloading common files from the internet
  - ...
- The EC2 User Data Script runs with the root user

## EC2 instance types

- AWS has the following naming convention

```ts
/**
 * m5.2xlarge
 * m: instance class
 * 5: gen
 * 2xlarge: size within the instance class
 * */
```

### General Purpose

- General purpose instances provide a balance of compute, memory and networking resources, and can be used for a variety of diverse workloads such as web servers or code repositories.

### Compute Optimized

- Great for compute-intensive tasks that require high performance processors:
  - Batch processing workloads
  - Media transcoding
  - High performance web servers
  - High performance computing (HPC)
  - Scientific modeling & machine learning
  - Dedicated gaming servers

### Memory Optimized

- Fast performance for workloads that process large data sets in memory
- Use case:
  - High performance, relational/non-relational databases
  - Distributed web scale cache stores
  - In-memory databases optimized for BI (bussiness intelligence)
  - Applications performing real-time processing of big unstructured data

### Storage Optimized

- Great for storage-intensive tasks that require high, sequential read and write access to large data sets on local storage
- Use case:
  - High frequency online transaction processing (OLTP) systems
  - Relational & NoSQL databases
  - Cache for in-memory databases (for example, Redis)
  - Data warehousing applications
  - Distributed file systems

| Instance Type | vCPU | Mem (GiB) | Storage          | Network Performance | EBS Bandwidth (Mbps) |
| ------------- | ---- | --------- | ---------------- | ------------------- | -------------------- |
| t2.micro      | 1    | 1         | EBS only         | Low to Moderate     |                      |
| t2.xlarge     | 4    | 16        | EBS only         | Moderate            |                      |
| c5d.4xlarge   | 16   | 32        | 1 x 400 NVMe SSD | Up to 10 Gbps       | 4,700                |
| r5.16xlarge   | 64   | 512       | EBS only         | 20 Gbps             | 13,600               |
| m5.8xlarge    | 32   | 128       | EBS only         | 10 Gbps             | 6,800                |

- t2.micro is part of the AWS free tier (up to 750 hours per month)

## Security Groups & Classis Ports OVerview

### Security Groups

- Security Groups are the fundamental of network setcurity in AWS
- They control how traffic is allowed into or out EC2 Instances
- Security groups only contain **allow** roles
- Security groups rules can reference by IP or by security group
- Are acting as a "firewall" on EC2 instances
- They regulate:
  - Access to Ports
  - Authorised IP ranges - IPv4 and IPv6
  - Control of inbound/outbound network
- Can be attached to multiple instances, and instances can have multiple security groups
- Một Security Group chỉ tồn tại trong một VPC cụ thể, ở một region cụ thể, và chỉ dùng được trong phạm vi đó.
- Security Group nằm bên ngoài EC2 instance, trên hạ tầng của AWS, ở mức VPC/hypervisor. Nó hoạt động như một bức tường bảo vệ xung quanh instance, lọc traffic trước khi bất kỳ packet nào đến được hệ điều hành của EC2.
- All inbound traffic is blocked by default
- All outbound traffic is authorised by default
- Trường hợp truy cập timeout có thể nguyên nhân là do security groups

### Classis Ports Overview

- 22 = SSH (secure shell) - log into a Linux instance
- 21 = FTP (File Transfer Protocol) - upload files into a file shared
- 22 = SFTP (Secure file Transfer Protocol) - upload files using SSH, SFTP dùng cùng connection và authentication layer với SSH. SFTP chỉ là một tính năng bổ sung của SSH, nên không cần mở thêm port nào cả
- 80 = HTTP - access unsecured websites
- 443 = HTTPs - access secured websites
- 3389 = RDP (Remote Desktop Protocol) - log into a Windows instance

## SSH Overview

- SSH cho phép control a remote machine, all using the command line
- Có 3 phương thức để connect instance:
  - SSH có thể sử dụng trên Linux, Mac, Windows >= 10
  - Putty sử dụng trên Windows
  - EC2 Instance Connect sử dụng trên browser cho Linux, Mac and Windows nhưng nó chỉ work với Amazon NX2

```ts
//login ssh
ssh -i <file> ec2-user@<publicIP>
```

## EC2 Instance Roles

- Không nên dùng aws configure trong EC2 vì:

  - Rủi ro bảo mật: Access Key và Secret Key sẽ được lưu rõ ràng (không mã hóa) trong file ~/.aws/credentials, nếu EC2 instance bị xâm nhập hoặc user khác có quyền đọc file => rò rỉ thông tin nhạy cảm, có thể dẫn tới mất quyền kiểm soát AWS account
  - Khó quản lý & audit: không dễ kiểm soát ai đã cấu hình gì trên từng EC2, không audit được hoạt động cụ thể theo IAM Role → chỉ biết là access key nào đó đang được dùng, không dễ thu hồi key (phải vào IAM disable/delete thủ công).

- Best practices: dùng IAM Role gắn vào EC2 để truy cập dịch vụ AWS (AWS recommended). Nó an toàn hơn vì không có key cứng, credentials là tạm thời, AWS tự thay key định kỳ, IAM Role dễ theo dõi & quản lý theo từng EC2

## EC2 Instances Purchasing Options

- On-Demand Instances là loại EC2 instance có thể mua và sử dụng ngay lập tức mà không cần cam kết lâu dài, chỉ trả tiền cho thời gian sử dụng (giây với Linux và Windows, OS khác billing per hour)
  - Phù hợp với short workload
  - Predictable pricing: giá cả dễ dự đoán
- Reserved (1 & 3 years):

  - Reserved Instances: phù hợp với long workloads:
    - Giảm giá có thể lên tới 72% so với On-Demand.
    - Payment Options: No Upfront (+), Partial Upfront (++), All Upfront (+++)
    - Reserved Instances's Scope: Regional | Zonal
  - Convertible Reserved Instances - long workloads, cho phép đổi instance type (trong cùng OS, region), linh hoạt hơn, giá giảm ít hơn.
    - Up to 66% discount

- Savings Plans:
  - Compute Savings Plan: rất linh hoạt, chuyển đổi bất kỳ instance type, OS, region, thậm chí sang Fargate, Lambda -> phù hợp với microservice, workload đa dạng.
  - EC2 Instance Savings Plan: Gắn với một instance family (ví dụ: m5, t3) nhưng vẫn linh hoạt về AZ, OS.
- Spot Instances: phù hợp với job xử lý dữ liệu, training ML, test script, không cần uptime cao, có thể bị AWS dừng bất cứ lúc nào nếu người khác trả cao hơn hoặc không còn capacity
- Dedicated Instances: instance của bạn chạy trên host vật lý không chia sẻ với tài khoản khác, AWS vẫn quản lý host, bạn không thấy nó, dùng khi bạn cần cách ly logic, nhưng không cần full control
- Dedicated Hosts: “thuê nguyên máy vật lý” từ AWS, dùng khi bạn có phần mềm license theo physical CPU/host như Oracle, SQL Server Enterprise, cực kỳ phù hợp với yêu cầu compliance nghiêm ngặt (PCI, HIPAA...).
- Capacity Reservations: Bạn giữ sẵn một slot (loại máy, AZ, số lượng) trong vùng bạn muốn. Dùng để đảm bảo EC2 luôn có chỗ cho bạn khi cần, tránh “Insufficient Capacity”. Không cách ly phần cứng, chỉ là giữ slot dùng sau.

## IP Address Charges in AWS

- There's a charge for all Public IPv4 created in your account
- $0.005 per hour of Public IPv4 (~ $3.6 per month)
- For new accounts, have a free tier for EC2 Service: 750 hours of Public IPv4/month cho 12 tháng đầu
- Các service khác không có free tier
- IPAM???


# Cloud Front (Global)

## CloudFront - Overview
- CloudFront is a content delivery netword (CDN)
- It improves read performance, content is cached at the edge
- CloudFront is made of 216 points of Presence globally (edge locations)
- Liên quan đến bảo mật, CloudFront cung cấp các tính năng bảo vệ mạnh mẽ:
  - DDoS protection (because worldwide): với các edge locations đặt tại nhiều khu vực, CloudFront phân tán lưu lượng truy cập, làm giảm nguy cơ một điểm duy nhất bị quá tải bởi các cuộc tấn công DDoS. Khi lưu lượng độc hại được gửi đến, hệ thống toàn cầu của CloudFront giúp hấp thụ và phân tán lưu lượng này, giảm tác động lên hệ thống gốc (origin server). Tính chất "worldwide" của CloudFront giúp nó tự động giảm thiểu các cuộc tấn công bằng cách xử lý lưu lượng tại các điểm gần người dùng nhất, trước khi chúng đến được máy chủ chính.
  - Integration with AWS Shield:
    - AWS Shield Standard: Tự động được kích hoạt cho tất cả người dùng CloudFront mà không tốn thêm chi phí. Nó cung cấp bảo vệ cơ bản chống lại các cuộc tấn công DDoS phổ biến, như SYN floods hay UDP floods.
    - AWS Shield Advanced: Là tùy chọn trả phí, cung cấp bảo vệ nâng cao hơn, bao gồm khả năng giảm thiểu các cuộc tấn công phức tạp hơn, hỗ trợ từ đội ngũ AWS DDoS Response Team (DRT) và bảo vệ chi phí phát sinh trong trường hợp tấn công.
    - Tích hợp này giúp CloudFront tận dụng các công cụ của Shield để phát hiện và giảm thiểu các cuộc tấn công DDoS một cách hiệu quả. 
    - Integration with AWS Web Application Firewall (WAF):
      - CloudFront tích hợp với AWS WAF, một tường lửa ứng dụng web cho phép người dùng thiết lập các quy tắc để bảo vệ ứng dụng khỏi các mối đe dọa như SQL injection, cross-site scripting (XSS), hoặc các mẫu tấn công khác.
      - Với WAF, bạn có thể tùy chỉnh các quy tắc để lọc lưu lượng độc hại, chặn các yêu cầu không mong muốn dựa trên địa chỉ IP, tiêu đề HTTP, hoặc các mẫu hành vi khác, trước khi chúng đến được máy chủ gốc.
      - Tích hợp này cho phép CloudFront không chỉ phân phối nội dung mà còn bảo vệ nội dung và ứng dụng khỏi các cuộc tấn công web phổ biến.
  - Tóm lại: CloudFront cung cấp bảo vệ DDoS nhờ mạng lưới toàn cầu, tích hợp với AWS Shield để bảo vệ chống lại các cuộc tấn công DDoS, và kết hợp với AWS WAF để lọc và chặn các yêu cầu độc hại, đảm bảo an toàn cho ứng dụng và nội dung được phân phối.

## CloudFront origins
- Trong Amazon CloudFront, Origin là nguồn nội dung gốc mà CloudFront lấy dữ liệu để phân phối đến người dùng thông qua mạng lưới các edge locations (điểm phân phối) trên toàn cầu. Origin đóng vai trò là nơi lưu trữ nội dung chính (như tệp HTML, hình ảnh, video, hoặc API) trước khi được CloudFront sao chép và lưu trữ tạm thời (cache) để tăng tốc độ truy cập. 
- Các đặc điểm chính của CloudFront Origin
1. Loại Origin được hỗ trợ:
  - Amazon S3: Thường được sử dụng để lưu trữ các tệp tĩnh như hình ảnh, video, hoặc tệp HTML.
  - Amazon EC2: Máy chủ tùy chỉnh chạy ứng dụng web.
  - Elastic Load Balancer (ELB): Phân phối lưu lượng đến nhiều máy chủ EC2 hoặc các tài nguyên khác.
  - AWS Lambda@Edge: Dùng để chạy mã tại các edge locations.
  - HTTP Server tùy chỉnh: Bất kỳ máy chủ HTTP nào ngoài AWS (như máy chủ on-premises hoặc bên thứ ba).
  - AWS Media Services: Dùng cho các ứng dụng streaming video.
2. Giao thức kết nối:
  - CloudFront hỗ trợ kết nối với Origin qua HTTP, HTTPS, hoặc các giao thức khác tùy thuộc vào loại Origin.
  - Có thể cấu hình để sử dụng Origin Access Identity (OAI) hoặc Origin Access Control (OAC) để hạn chế truy cập trực tiếp vào Origin (ví dụ: chỉ CloudFront được phép truy cập S3 bucket).
3. Tính năng quản lý Origin:
  - Multiple Origins: Một CloudFront distribution có thể sử dụng nhiều Origin, cho phép phân phối nội dung từ nhiều nguồn khác nhau dựa trên các quy tắc (path patterns).
  - Failover: CloudFront hỗ trợ cấu hình Origin Failover để tự động chuyển sang Origin dự phòng nếu Origin chính gặp sự cố.
  - Custom Headers: Có thể thêm các tiêu đề tùy chỉnh khi CloudFront gửi yêu cầu đến Origin để tăng cường bảo mật hoặc tùy chỉnh logic.
4. Tích hợp với các dịch vụ AWS:
  - CloudFront tích hợp chặt chẽ với các dịch vụ AWS như S3, ELB, hoặc Lambda@Edge, giúp tối ưu hóa hiệu suất và bảo mật.
### Tải tệp lên S3 qua CloudFront
1. Amazon S3 Transfer Acceleration
- Transfer Acceleration là một tính năng của Amazon S3 giúp tăng tốc độ truyền tải dữ liệu (upload/download) qua internet bằng cách sử dụng mạng lưới edge locations toàn cầu của CloudFront.
Khi bật Transfer Acceleration, thay vì gửi dữ liệu trực tiếp đến S3 bucket qua internet công cộng, dữ liệu sẽ được gửi qua các edge locations của CloudFront, sau đó được truyền qua mạng nội bộ tối ưu hóa của AWS đến S3 bucket. Điều này giúp:
  - Giảm độ trễ: Nhờ sử dụng các tuyến đường mạng tối ưu hóa của AWS.
  - Tăng tốc độ tải lên: Đặc biệt hiệu quả khi người dùng ở xa khu vực của S3 bucket (ví dụ, người dùng ở châu Á tải lên bucket ở Mỹ).
  - Độ tin cậy cao hơn: Mạng của AWS giảm thiểu mất gói tin và tắc nghẽn.
2. Sử dụng CloudFront để tải tệp lên S3
- CloudFront không trực tiếp xử lý việc tải tệp lên S3, nhưng nó có thể được cấu hình để hoạt động như một reverse proxy hoặc điểm trung gian cho các yêu cầu tải lên. Dưới đây là cách quy trình này hoạt động:
  - Cấu hình CloudFront để chuyển tiếp yêu cầu tải lên:
    - Bạn tạo một CloudFront distribution với S3 bucket làm Origin.
    - Cấu hình behaviors trong CloudFront để cho phép các phương thức HTTP như PUT hoặc POST, thường được sử dụng để tải tệp lên.
    - CloudFront sẽ nhận yêu cầu tải lên từ người dùng, chuyển tiếp chúng đến S3 bucket.
  - Tích hợp với Transfer Acceleration:
    - Để sử dụng Transfer Acceleration, bạn cần bật tính năng này trên S3 bucket (trong AWS Management Console hoặc qua API).
    - Khi bật, S3 cung cấp một endpoint đặc biệt (ví dụ: bucket-name.s3-accelerate.amazonaws.com) để người dùng gửi dữ liệu qua mạng lưới edge của CloudFront.
    - CloudFront sẽ nhận dữ liệu tại edge location gần nhất với người dùng, sau đó sử dụng mạng nội bộ của AWS để chuyển dữ liệu đến S3 bucket nhanh hơn so với kết nối internet thông thường.
  - Quy trình cụ thể:
    - Người dùng gửi yêu cầu tải tệp lên thông qua CloudFront distribution (ví dụ: sử dụng endpoint của CloudFront).
    - CloudFront chuyển tiếp yêu cầu đến endpoint Transfer Acceleration của S3 bucket.
    - Dữ liệu được truyền qua edge location gần nhất, sau đó đi qua mạng tối ưu hóa của AWS để đến S3 bucket.

- VPC Origin
  - VPC Origin là một loại Origin được lưu trữ trong một Amazon Virtual Private Cloud (VPC), thường nằm trong các private subnets không thể truy cập trực tiếp từ internet. Điều này có nghĩa là nội dung hoặc ứng dụng được triển khai trên các tài nguyên AWS như Application Load Balancer (ALB), Network Load Balancer (NLB), hoặc EC2 instances nằm trong một môi trường mạng cô lập, và CloudFront được sử dụng để phân phối nội dung từ các tài nguyên này đến người dùng cuối một cách an toàn và hiệu quả.
  - VPC Origin được sử dụng khi bạn muốn:
    - Bảo vệ các tài nguyên ứng dụng trong private subnets khỏi truy cập trực tiếp từ internet.
    - Tận dụng CloudFront để tăng tốc độ phân phối nội dung, giảm độ trễ, và cung cấp các tính năng bảo mật bổ sung như AWS Shield và AWS WAF.
    - Đảm bảo rằng các ứng dụng hoặc API được lưu trữ trong VPC có thể được truy cập thông qua CloudFront mà không cần mở các public endpoint.
  - Các loại VPC Origin phổ biến
    - Application Load Balancer (ALB):
      - ALB là một bộ cân bằng tải ứng dụng hoạt động ở tầng ứng dụng (Layer 7), hỗ trợ các giao thức HTTP/HTTPS.
      - Thường được sử dụng để phân phối lưu lượng đến nhiều EC2 instances trong private subnets.
      - CloudFront có thể sử dụng ALB làm Origin để phân phối nội dung động (như các trang web hoặc API).
    - Network Load Balancer (NLB):
      - NLB hoạt động ở tầng mạng (Layer 4), hỗ trợ các giao thức TCP/UDP và HTTPS.
      - Phù hợp cho các ứng dụng yêu cầu hiệu suất cao hoặc xử lý lưu lượng lớn.
      - CloudFront có thể định tuyến yêu cầu đến NLB trong VPC để truy cập ứng dụng trong private subnets.
    - EC2 Instance:
      - Một hoặc nhiều EC2 instances chạy ứng dụng web (như máy chủ Nginx, Apache, hoặc ứng dụng tùy chỉnh) trong private subnets.
      - CloudFront có thể được cấu hình để gửi yêu cầu trực tiếp đến IP Address hoặc domain nội bộ của EC2 instance.
  - Cách CloudFront hoạt động với VPC Origin
    - CloudFront kết nối với các tài nguyên trong VPC thông qua VPC Interface Endpoints (sử dụng AWS PrivateLink) hoặc qua các kết nối mạng nội bộ của AWS. Dưới đây là cách quy trình hoạt động:

      - Kết nối CloudFront với VPC Origin:
        - Để CloudFront truy cập các tài nguyên trong private subnets, bạn cần cấu hình một VPC Interface Endpoint (dựa trên AWS PrivateLink) cho dịch vụ CloudFront. Điều này cho phép CloudFront gửi yêu cầu đến ALB, NLB, hoặc EC2 trong VPC mà không cần đi qua internet công cộng.
        - Alternately, bạn có thể sử dụng DNS nội bộ (như Route 53 private hosted zones) để ánh xạ tên miền của Origin (ví dụ: ALB hoặc NLB) trong VPC.
      - Cấu hình CloudFront Distribution:
        - Tạo một CloudFront distribution và chỉ định VPC Origin (ALB, NLB, hoặc EC2) làm Origin.
        - Trong Origin Settings, nhập tên miền hoặc địa chỉ IP nội bộ của tài nguyên trong VPC (ví dụ: alb-internal-123456789.us-east-1.elb.amazonaws.com).
        - Đảm bảo rằng Origin Protocol Policy được cấu hình phù hợp (HTTP hoặc HTTPS) và các chính sách bảo mật (như IAM roles hoặc security groups) cho phép CloudFront truy cập.
      - Bảo mật:
        - Security Groups: Cấu hình security groups trên ALB, NLB, hoặc EC2 để chỉ cho phép lưu lượng từ CloudFront (dựa trên dải IP của CloudFront hoặc thông qua VPC Interface Endpoint).
        - AWS WAF: Tích hợp CloudFront với AWS WAF để bảo vệ Origin khỏi các cuộc tấn công như SQL injection hoặc XSS.
        - AWS Shield: Cung cấp bảo vệ DDoS tự động cho Origin thông qua CloudFront.
        - Custom Headers: Thêm các tiêu đề tùy chỉnh trong yêu cầu từ CloudFront đến Origin để xác thực hoặc tăng cường bảo mật.
      - Caching và hiệu suất:
        - CloudFront có thể lưu trữ (cache) nội dung tĩnh từ VPC Origin tại các edge locations để giảm tải cho tài nguyên trong VPC.
        - Đối với nội dung động (như API), bạn có thể cấu hình CloudFront để chuyển tiếp yêu cầu trực tiếp đến Origin mà không cần cache.
- Custom Origin (HTTP)
  - Custom Origin là một máy chủ HTTP có thể truy cập công khai, sử dụng giao thức HTTP hoặc HTTPS, và có thể nằm trong hoặc ngoài hệ sinh thái AWS. Điều này cho phép CloudFront phân phối nội dung từ bất kỳ máy chủ web nào đáp ứng các yêu cầu HTTP tiêu chuẩn
  - Custom Origin thường được sử dụng khi:
    - Bạn muốn CloudFront phân phối nội dung từ một máy chủ web tùy chỉnh (như một máy chủ on-premises, một máy chủ của nhà cung cấp khác, hoặc một EC2 instance không được cấu hình qua ALB/NLB).
    - Bạn sử dụng S3 bucket được cấu hình như một website tĩnh (S3 website) thay vì bucket S3 thông thường.
  1. S3 Website (Must first enable the bucket as a static S3 website)
    - S3 Website là gì?
      - Amazon S3 có thể được cấu hình để hoạt động như một máy chủ web tĩnh (static website hosting). Khi bật tính năng này, S3 bucket sẽ phục vụ nội dung trực tiếp qua giao thức HTTP/HTTPS, tương tự như một máy chủ web thông thường.
      - Để sử dụng S3 bucket như một Custom Origin trong CloudFront, bạn phải bật tính năng static website hosting trên bucket đó. Điều này khác với việc sử dụng S3 bucket thông thường (dùng REST API endpoint) làm Origin.
    - Tại sao phải bật static website hosting?
      - Khi sử dụng S3 bucket thông thường làm Origin, CloudFront truy cập bucket qua REST API endpoint (ví dụ: bucket-name.s3.region.amazonaws.com). Điều này phù hợp cho các ứng dụng cần truy cập tệp qua API, nhưng không hỗ trợ các tính năng website như xử lý lỗi tùy chỉnh (ví dụ: trang 404) hoặc ánh xạ URL thân thiện.
      - Khi bật static website hosting, S3 cung cấp một website endpoint (ví dụ: bucket-name.s3-website-region.amazonaws.com), cho phép bucket hoạt động như một máy chủ web, hỗ trợ:
        - Phục vụ các tệp HTML, CSS, JavaScript, hình ảnh, v.v., như một website tĩnh.
        - Xử lý các mã lỗi HTTP (như 404, 403) bằng cách trả về các trang lỗi tùy chỉnh.
        - Hỗ trợ các URL thân thiện hơn (không cần ký tự / hoặc các tham số phức tạp như khi dùng REST API).
    - Lưu ý:
      - S3 website endpoint không hỗ trợ Transfer Acceleration. Nếu bạn cần tăng tốc tải lên/tải xuống, hãy sử dụng S3 bucket thông thường làm Origin thay vì website endpoint.
      - S3 website chỉ phù hợp cho nội dung tĩnh, không hỗ trợ nội dung động như API.
### Cloud Front vs S3 Cross Region Replication
a. Mục đích và chức năng
  - CloudFront:
    - Tập trung vào phân phối nội dung đến người dùng cuối với tốc độ cao.
    - Giảm tải cho Origin bằng cách lưu trữ nội dung tĩnh tại các edge locations.
    - Phù hợp cho các ứng dụng web, streaming media, hoặc API cần độ trễ thấp.
    - Ví dụ: Phân phối một website tĩnh từ S3 hoặc một API từ ALB đến người dùng toàn cầu.
  - S3 CRR:
    - Tập trung vào sao chép dữ liệu để đảm bảo dữ liệu có sẵn ở nhiều khu vực.
    - Không trực tiếp phân phối nội dung đến người dùng, mà đảm bảo dữ liệu được lưu trữ ở nhiều vùng để hỗ trợ sao lưu, khôi phục, hoặc tuân thủ quy định.
    - Ví dụ: Sao chép hình ảnh từ một bucket ở us-east-1 sang eu-west-1 để đáp ứng quy định GDPR.
b. Hiệu suất
    - CloudFront:
      - Tối ưu hóa tốc độ truy cập bằng cách lưu trữ nội dung tại các edge locations gần người dùng. Ví dụ, người dùng ở châu Á truy cập nội dung từ edge location ở Singapore thay vì từ bucket ở Mỹ.
      - Hỗ trợ caching để giảm số lượng yêu cầu đến Origin, cải thiện hiệu suất.
    - S3 CRR:
      - Không trực tiếp cải thiện tốc độ truy cập cho người dùng cuối.
      - Tuy nhiên, nếu kết hợp với CloudFront, bạn có thể sử dụng bucket đích (replicated bucket) làm Origin để CloudFront phân phối nội dung từ khu vực gần hơn.
c. Bảo mật
    - CloudFront:
      - Tích hợp với AWS Shield (bảo vệ DDoS) và AWS WAF (bảo vệ khỏi các cuộc tấn công web như SQL injection).
      - Hỗ trợ OAC để bảo vệ S3 bucket hoặc các Origin khác, và sử dụng HTTPS để mã hóa dữ liệu.
      - Có thể cấu hình geo-restriction để giới hạn truy cập theo khu vực địa lý.
    - S3 CRR:
      - Bảo mật dựa vào chính sách bucket, IAM roles, và mã hóa dữ liệu (SSE-S3, SSE-KMS).
      - Không tích hợp với WAF hoặc Shield, nhưng có thể sử dụng bucket đích với CloudFront để thêm lớp bảo mật.
      - Hỗ trợ sao chép các đối tượng đã mã hóa, đảm bảo tuân thủ quy định bảo mật.
d. Chi phí
    - CloudFront:
      - Chi phí dựa trên lưu lượng dữ liệu ra (data transfer out), số lượng yêu cầu HTTP/HTTPS, và các tính năng bổ sung như Lambda@Edge hoặc WAF.
      - Có thể tối ưu chi phí bằng cách điều chỉnh chiến lược caching.
    - S3 CRR:
      - Chi phí bao gồm phí lưu trữ cho bucket đích, phí sao chép dữ liệu, và phí truyền dữ liệu giữa các region.
      - Nếu sử dụng CRR để sao chép lượng lớn dữ liệu, chi phí có thể tăng đáng kể.

e. Trường hợp sử dụng
    - CloudFront:
      - Phân phối website tĩnh (từ S3) hoặc động (từ ALB/EC2).
      - Streaming video hoặc âm thanh (kết hợp với AWS Media Services).
      - Bảo vệ Origin khỏi các cuộc tấn công DDoS hoặc lưu lượng truy cập lớn.
      - Cải thiện trải nghiệm người dùng bằng cách giảm độ trễ.
    S3 CRR:
      - Sao lưu dữ liệu để khôi phục sau thảm họa (disaster recovery).
      - Tuân thủ các quy định pháp lý yêu cầu lưu trữ dữ liệu ở nhiều khu vực.
      -   Đảm bảo tính sẵn sàng cao cho các ứng dụng cần truy cập dữ liệu từ nhiều vùng.
- Kết hợp CloudFront và S3 CRR
  - Ví dụ: Bạn có một website tĩnh lưu trữ trong S3 bucket ở us-east-1. Bạn sử dụng S3 CRR để sao chép nội dung sang một bucket ở eu-west-1 nhằm tuân thủ quy định hoặc tăng tính sẵn sàng. Sau đó, bạn tạo một CloudFront distribution với nhiều Origin (cả hai bucket ở us-east-1 và eu-west-1), và CloudFront sẽ chọn Origin gần nhất để phân phối nội dung đến người dùng, cải thiện tốc độ và độ tin cậy.
  - Lợi ích:
    - CRR đảm bảo dữ liệu có sẵn ở nhiều khu vực, giảm nguy cơ mất dữ liệu.
    - CloudFront cải thiện hiệu suất bằng cách phân phối nội dung từ edge locations.
    ![Cloud Front at  a high level](./CloudFront_high_level.png)
## How does CloudFront Caching work?
- Flow???
- Các yếu tố ảnh hưởng đến CloudFront Caching
a. Cache Behavior
  - Cache Behavior là các quy tắc được cấu hình trong CloudFront distribution để xác định cách CloudFront xử lý yêu cầu và cache nội dung.
    - Path Patterns: Quy định hành vi caching dựa trên đường dẫn URL (ví dụ: cache tất cả tệp trong /images/* nhưng không cache /api/*).
    - HTTP Methods: Cho phép caching các yêu cầu GET, HEAD, hoặc OPTIONS. Các phương thức như POST thường không được cache.
    - Query Strings, Cookies, Headers: Bạn có thể cấu hình CloudFront để cache nội dung dựa trên các tham số truy vấn, cookie, hoặc tiêu đề HTTP cụ thể.
b. Time to Live (TTL)
  - TTL xác định thời gian nội dung được lưu trong cache trước khi hết hạn.
  - Các giá trị TTL:
    - Minimum TTL: Thời gian tối thiểu nội dung được lưu trong cache (mặc định: 0 giây).
    - Default TTL: Thời gian mặc định nếu Origin không cung cấp tiêu đề Cache-Control hoặc Expires (mặc định: 24 giờ).
    - Maximum TTL: Thời gian tối đa nội dung được lưu trong cache (mặc định: 1 năm).
  - Origin có thể điều khiển TTL thông qua các tiêu đề HTTP:
    - Cache-Control: max-age=3600 (lưu cache 1 giờ).
    - Expires: Chỉ định thời điểm hết hạn cụ thể.
c. Cache Key
  - Cache Key là tập hợp các thuộc tính của yêu cầu (như URL, query strings, headers, cookies) mà CloudFront sử dụng để xác định một phiên bản nội dung trong cache.
  - Bạn có thể tùy chỉnh cache key để:
    - Bao gồm query strings: Cache các phiên bản khác nhau của nội dung dựa trên tham số truy vấn (ví dụ: image.jpg?size=large và image.jpg?size=small được coi là hai đối tượng riêng).
    - Bao gồm headers: Cache dựa trên các tiêu đề như Accept-Language để phục vụ nội dung đa ngôn ngữ.
    - Loại bỏ cookie: Không bao gồm cookie trong cache key để tăng tỷ lệ cache hit.
d. Invalidations
  - Khi nội dung tại Origin thay đổi, bạn có thể invalidate (xóa) nội dung trong cache của CloudFront để buộc lấy phiên bản mới từ Origin.
    Ví dụ: aws cloudfront create-invalidation --distribution-id ABC123 --paths "/images/*" sẽ xóa tất cả tệp trong thư mục /images/ khỏi cache.
    Lưu ý: Invalidation có thể phát sinh chi phí bổ sung và mất thời gian để thực hiện trên toàn bộ edge locations. 
## CloudFront Signed URL và Signed Cookies
- CloudFront Signed URL và Signed Cookies là các cơ chế bảo mật trong Amazon CloudFront giúp kiểm soát quyền truy cập vào nội dung được phân phối qua CloudFront, đặc biệt là nội dung riêng tư hoặc nhạy cảm. Chúng cho phép bạn giới hạn truy cập dựa trên thời gian, người dùng, hoặc các điều kiện cụ thể, đảm bảo chỉ những người được ủy quyền mới có thể truy cập nội dung. 
1. Tổng quan về Signed URL và Signed Cookies
  - Signed URL:
    - Là một URL chứa thông tin xác thực (signature) để cấp quyền truy cập tạm thời vào một tệp cụ thể được phân phối qua CloudFront.
    - Thường được sử dụng để cấp quyền truy cập vào một tài nguyên duy nhất, như một tệp video, hình ảnh, hoặc tài liệu.
  - Signed Cookies:
    - Là các cookie chứa thông tin xác thực (signature) để cấp quyền truy cập vào nhiều tài nguyên phù hợp với một mẫu URL (path pattern).
    - Thích hợp khi người dùng cần truy cập nhiều tệp hoặc một nhóm nội dung, như toàn bộ thư mục hoặc nhiều video trong một phiên.
  - Mục đích chung:
    - Cả hai đều sử dụng chữ ký số (dựa trên cặp khóa public/private) để xác thực yêu cầu, đảm bảo rằng chỉ những người dùng có chữ ký hợp lệ mới truy cập được nội dung.
    - Hỗ trợ giới hạn quyền truy cập dựa trên thời gian (thời điểm hết hạn) hoặc điều kiện (như địa chỉ IP).
2. Cách hoạt động của Signed URL và Signed Cookies
  - Cả Signed URL và Signed Cookies đều dựa trên chữ ký số để bảo vệ nội dung. Quy trình hoạt động bao gồm:
    - Tạo cặp khóa:
      - Bạn tạo một cặp khóa (public/private key) trong AWS CloudFront Key Groups hoặc sử dụng AWS Key Management Service (KMS).
      - Public key được tải lên CloudFront, còn private key được lưu trữ an toàn (bởi bạn hoặc ứng dụng của bạn).
    - Tạo chữ ký:
      - Ứng dụng của bạn sử dụng private key để ký một policy xác định quyền truy cập, bao gồm:
        - Resource: Tài nguyên hoặc mẫu URL được phép truy cập (ví dụ: https://d12345678.cloudfront.net/video.mp4 hoặc /private/*).
        - Expiration: Thời điểm URL/cookie hết hiệu lực (ví dụ: 2025-05-23T12:00:00Z).
        - IP Range (tùy chọn): Giới hạn truy cập theo địa chỉ IP của người dùng.
      - Chữ ký và policy được mã hóa thành một chuỗi (dùng Base64) và gắn vào URL hoặc cookie.
3. Cấu hình CloudFront:
  - Trong CloudFront distribution, bạn bật Restrict Viewer Access trong Cache Behavior và chọn Trusted Key Groups hoặc Trusted Signers (nhóm khóa công khai được phép).
  - CloudFront kiểm tra chữ ký trong URL/cookie để xác thực yêu cầu.

4. Người dùng gửi yêu cầu:
  - Người dùng gửi yêu cầu với Signed URL hoặc Signed Cookies đến CloudFront.
  - CloudFront sử dụng khóa công khai để xác minh chữ ký và kiểm tra policy:
    - Nếu hợp lệ (chữ ký đúng, chưa hết hạn, IP phù hợp), CloudFront trả về nội dung.
    - Nếu không hợp lệ, CloudFront trả về lỗi HTTP 403 (Forbidden).
#### Signed URL
- Đặc điểm
  - Cú pháp: URL chứa các tham số xác thực như Signature, Key-Pair-Id, và Policy hoặc Expires.
  ```text
  https://d12345678.cloudfront.net/video.mp4?Expires=1624550400&Signature=abc123&Key-Pair-Id=APKA1234567890
  ```
  - Thời gian sống: Được xác định bởi tham số Expires hoặc policy tùy chỉnh.
  - Ứng dụng: Phù hợp cho các trường hợp như tải xuống tệp một lần, xem video trả phí, hoặc chia sẻ nội dung riêng tư với người dùng cụ thể
### Signed Cookies
  - Cú pháp: Bao gồm một bộ cookie chứa CloudFront-Policy, CloudFront-Signature, và CloudFront-Key-Pair-Id.
  ```text
  Set-Cookie: CloudFront-Policy=policy-base64; Domain=d12345678.cloudfront.net; Path=/private
  Set-Cookie: CloudFront-Signature=signature-base64; Domain=d12345678.cloudfront.net; Path=/private
  Set-Cookie: CloudFront-Key-Pair-Id=APKA1234567890; Domain=d12345678.cloudfront.net; Path=/private
  ```
  - Thời gian sống: Được xác định trong policy, tương tự Signed URL.
  - Ứng dụng: Phù hợp cho các trường hợp như phát trực tuyến nhiều video, truy cập thư mục tài liệu, hoặc ứng dụng web yêu cầu phiên đăng nhập

## Advance
### Pricing
### Price Classes
### Multiple Origins
- Tổng quan
  - Multiple Origins cho phép một CloudFront distribution định tuyến yêu cầu đến các Origin khác nhau dựa trên loại nội dung hoặc path pattern. Điều này hữu ích khi bạn cần phân phối nội dung từ nhiều nguồn, như S3 bucket, ALB, EC2, hoặc máy chủ HTTP tùy chỉnh.
  - Mỗi Origin có thể được cấu hình với một Cache Behavior riêng, cho phép bạn tùy chỉnh caching, giao thức, và các thiết lập khác.
- Cách hoạt động
  - Bạn cấu hình nhiều Origin trong CloudFront distribution.
  - Mỗi Cache Behavior được liên kết với một path pattern để xác định yêu cầu nào sẽ được gửi đến Origin nào.
  - Ví dụ path patterns:
    /images/*: Yêu cầu đến các tệp hình ảnh (như image.jpg) được định tuyến đến một S3 bucket.
    /api/*: Yêu cầu đến API được định tuyến đến một Application Load Balancer (ALB).
    /* (mặc định): Các yêu cầu khác được định tuyến đến một Origin chính (như EC2 hoặc S3 website).
- Cấu hình
  - Trong CloudFront distribution, thêm nhiều Origin (ví dụ: S3 bucket cho /images/*, ALB cho /api/*).
  - Tạo Cache Behaviors tương ứng, chỉ định path pattern và Origin.
  - Tùy chỉnh Cache Policy và Origin Request Policy cho mỗi behavior (ví dụ: cache lâu dài cho hình ảnh, không cache cho API).

- Lợi ích

  - Tính linh hoạt: Hỗ trợ phân phối nội dung từ nhiều nguồn khác nhau trong cùng một distribution.
  - Tối ưu hóa hiệu suất: Áp dụng các chính sách caching khác nhau cho từng loại nội dung.
  - Đơn giản hóa quản lý: Một distribution có thể xử lý cả nội dung tĩnh và động.

### Origin Groups
- Tổng quan
  - Origin Groups tăng tính sẵn sàng cao và hỗ trợ failover. Một Origin Group bao gồm một Origin chính (primary) và một Origin phụ (secondary). Nếu Origin chính không phản hồi, CloudFront tự động chuyển yêu cầu sang Origin phụ.

- Cách hoạt động
  - Bạn tạo một Origin Group trong CloudFront distribution, chỉ định:
    - Primary Origin: Origin chính (ví dụ: S3 bucket hoặc ALB).
    - Secondary Origin: Origin dự phòng (ví dụ: một S3 bucket ở region khác hoặc một ALB khác).
  - CloudFront giám sát trạng thái của Primary Origin. Nếu Origin chính trả về mã lỗi HTTP (như 502, 503, 504), CloudFront sẽ thử Secondary Origin.
  - Failover Conditions: Bạn có thể cấu hình các mã lỗi HTTP cụ thể để kích hoạt failover.

- Lợi ích
  - Tăng tính sẵn sàng: Đảm bảo nội dung vẫn được phục vụ ngay cả khi Origin chính gặp sự cố.
  - Khả năng phục hồi: Hỗ trợ các chiến lược khôi phục sau thảm họa (disaster recovery).
  - Tích hợp với S3 CRR: Kết hợp với S3 Cross-Region Replication để sử dụng bucket ở region khác làm Secondary Origin.

### Field Level Encryption
- Tổng quan
  - Field Level Encryption là một tính năng bảo mật của CloudFront giúp bảo vệ thông tin nhạy cảm (như số thẻ tín dụng, mật khẩu) trong các yêu cầu HTTP (chủ yếu là POST) bằng cách mã hóa chúng ngay tại edge locations gần người dùng.
  - Sử dụng mã hóa bất đối xứng (asymmetric encryption) với cặp public/private key để đảm bảo thông tin nhạy cảm được mã hóa trước khi đến Origin.

- Cách hoạt động
  - Cấu hình Field Level Encryption:
    - Bạn tạo một Field Level Encryption Profile trong CloudFront, chỉ định:
      - Các trường cần mã hóa: Lên đến 10 trường trong yêu cầu POST (ví dụ: credit_card_number, password).
      - Public Key: Khóa công khai để mã hóa các trường này tại edge.
      - Khóa riêng tư được lưu trữ an toàn tại Origin hoặc ứng dụng của bạn để giải mã.
    - Quy trình xử lý yêu cầu:
      - Người dùng gửi yêu cầu POST chứa dữ liệu nhạy cảm (ví dụ: biểu mẫu thanh toán) đến CloudFront.
      - CloudFront mã hóa các trường được chỉ định (như credit_card_number) ngay tại edge location bằng khóa công khai.
      - Dữ liệu mã hóa được chuyển tiếp đến Origin (như ALB hoặc EC2).
      - Origin sử dụng khóa riêng tư để giải mã và xử lý dữ liệu.
    - Tích hợp với HTTPS:
      - Field Level Encryption bổ sung một lớp bảo mật bên cạnh HTTPS, đảm bảo dữ liệu nhạy cảm được mã hóa riêng lẻ ngay cả khi HTTPS bảo vệ toàn bộ kết nối.
- Lợi ích

  - Bảo mật nâng cao: Dữ liệu nhạy cảm được mã hóa ngay tại edge, giảm nguy cơ bị lộ trong quá trình truyền.
  - Tuân thủ quy định: Hỗ trợ các yêu cầu bảo mật như PCI DSS hoặc GDPR.
  - Tính linh hoạt: Chỉ mã hóa các trường cụ thể, không ảnh hưởng đến hiệu suất của các trường khác.

- Cách cấu hình
  - Tạo cặp khóa công khai/riêng tư (ví dụ: sử dụng OpenSSL).
  - Tải khóa công khai lên CloudFront trong Field Level Encryption Configuration.
  - Tạo một Field Level Encryption Profile, chỉ định các trường cần mã hóa (ví dụ: credit_card_number).
  - Liên kết profile với Cache Behavior trong CloudFront distribution.
  - Cấu hình Origin để giải mã dữ liệu bằng khóa riêng tư.

- Lưu ý
  - Chỉ hỗ trợ yêu cầu POST, không áp dụng cho GET hoặc các phương thức khác.
  - Yêu cầu quản lý khóa cẩn thận, đặc biệt là khóa riêng tư.
  - Có thể tăng độ trễ nhỏ do quá trình mã hóa tại edge.
### Real time logs
1. Tổng quan về Real-time Logs
  - Mục đích:
    - Cung cấp dữ liệu chi tiết về các yêu cầu HTTP/HTTPS mà CloudFront nhận được, gần như ngay lập tức (real-time).
    - Giúp giám sát hiệu suất phân phối nội dung, phát hiện các vấn đề (như lỗi 503, độ trễ cao), và phân tích hành vi người dùng.
    - Hỗ trợ các hành động tự động, chẳng hạn như gửi cảnh báo hoặc kích hoạt quy trình xử lý dựa trên dữ liệu logs.
    - Đích đến: Logs được gửi đến Amazon Kinesis Data Streams, một dịch vụ xử lý dữ liệu thời gian thực của AWS. Từ Kinesis, bạn có thể tích hợp với các công cụ phân tích như Amazon Athena, Amazon Elasticsearch, hoặc các ứng dụng tùy chỉnh.
    - Ứng dụng:
      - Theo dõi tỷ lệ cache hit/miss để tối ưu hóa caching.
      - Phát hiện các mẫu yêu cầu bất thường (như tấn công DDoS).
      - Phân tích hành vi người dùng (ví dụ: khu vực địa lý, loại thiết bị).
      - Tạo báo cáo hoặc dashboard thời gian thực về lưu lượng truy cập.

2. Cách hoạt động của Real-time Logs

- Quy trình hoạt động của Real-time Logs trong CloudFront như sau:

  - Cấu hình Real-time Log:
    - Bạn tạo một Real-time Log Configuration trong CloudFront, chỉ định:
      - Kinesis Data Stream: Nơi logs sẽ được gửi.
      - Sampling Rate: Tỷ lệ phần trăm yêu cầu được ghi lại (từ 1% đến 100%).
      - Fields: Các trường dữ liệu cụ thể mà bạn muốn ghi lại (như URL, IP, status code).
      - Cache Behaviors: Các mẫu đường dẫn (path patterns) để áp dụng log (ví dụ: /images/* hoặc tất cả yêu cầu).
  - Thu thập yêu cầu:
    - Khi CloudFront nhận yêu cầu từ người dùng, nó ghi lại thông tin về yêu cầu dựa trên cấu hình Real-time Log.
    - Chỉ các yêu cầu thuộc Cache Behaviors được chọn và phù hợp với Sampling Rate mới được ghi lại.
  - Gửi đến Kinesis Data Streams:
    - Dữ liệu log được gửi đến Kinesis Data Stream trong thời gian thực (thường trong vòng vài giây).
    - Mỗi bản ghi log chứa các trường dữ liệu được chỉ định (ví dụ: thời gian, URL, địa chỉ IP, mã trạng thái HTTP).
  - Phân tích và hành động:
    - Từ Kinesis Data Stream, bạn có thể:
      - Tích hợp với Amazon CloudWatch để tạo cảnh báo dựa trên mẫu log.
      - Sử dụng AWS Lambda để xử lý logs và thực hiện hành động tự động (như gửi email khi phát hiện lỗi).
      - Lưu trữ logs vào Amazon S3 hoặc Amazon Elasticsearch để phân tích dài hạn.
      - Tạo dashboard thời gian thực với Amazon QuickSight hoặc các công cụ bên thứ ba.

3. Các thành phần chính của Real-time Logs
a. Get Real-time Requests Received by CloudFront Sent to Kinesis Data Streams

  - Ý nghĩa: CloudFront ghi lại các yêu cầu HTTP/HTTPS mà nó nhận được và gửi chúng đến Kinesis Data Streams trong thời gian thực.
b. Monitor, Analyze, and Take Actions Based on Content Delivery Performance
    Giám sát (Monitor):
        Theo dõi các chỉ số như tỷ lệ cache hit/miss, mã trạng thái HTTP (200, 404, 503), hoặc độ trễ của yêu cầu.
        Ví dụ: Phát hiện nếu tỷ lệ lỗi 503 tăng đột biến, cho thấy Origin có thể gặp sự cố.
    Phân tích (Analyze):
        Sử dụng logs để phân tích hành vi người dùng (như khu vực truy cập, loại thiết bị, hoặc mẫu URL phổ biến).
        Ví dụ: Xác định rằng 70% yêu cầu đến từ châu Á, từ đó tối ưu hóa Price Class để giảm chi phí.
    Hành động (Take Actions):
        Tích hợp với AWS Lambda để tự động hóa các hành động, như gửi cảnh báo qua SNS khi phát hiện lưu lượng bất thường.
        Ví dụ: Kích hoạt một hàm Lambda để chuyển hướng yêu cầu sang Origin phụ nếu Primary Origin trả về lỗi 502.

c. Allows You to Choose: Sampling Rate, Specific Fields, and Specific Cache Behaviors
    Sampling Rate:
        Định nghĩa: Tỷ lệ phần trăm yêu cầu mà bạn muốn CloudFront ghi lại (từ 1% đến 100%).
        Cách hoạt động:
            Nếu đặt Sampling Rate là 10%, CloudFront chỉ ghi lại 10% yêu cầu ngẫu nhiên.
            Giảm Sampling Rate giúp tiết kiệm chi phí và giảm khối lượng dữ liệu cần xử lý, nhưng có thể làm mất một số thông tin chi tiết.
        Ứng dụng:
            Sử dụng Sampling Rate thấp (ví dụ: 1%) cho các ứng dụng có lưu lượng lớn để giảm chi phí.
            Sử dụng 100% để phân tích chi tiết trong các trường hợp cần dữ liệu đầy đủ (như gỡ lỗi hoặc kiểm tra bảo mật).
    Specific Fields:
        Định nghĩa: Bạn có thể chọn các trường dữ liệu cụ thể để ghi vào logs, giúp giảm kích thước bản ghi và tập trung vào thông tin quan trọng.
        Các trường phổ biến:
            timestamp: Thời gian yêu cầu.
            c-ip: Địa chỉ IP của người dùng.
            cs-uri: URL được yêu cầu (bao gồm path và query string).
            sc-status: Mã trạng thái HTTP (200, 404, 503, v.v.).
            cs-method: Phương thức HTTP (GET, POST, v.v.).
            cs-user-agent: Thông tin thiết bị/trình duyệt của người dùng.
            x-edge-location: Edge location xử lý yêu cầu.
            Danh sách đầy đủ các trường có thể xem trong tài liệu AWS CloudFront.
        Lợi ích:
            Chỉ ghi lại các trường cần thiết để giảm chi phí lưu trữ và xử lý.
            Ví dụ: Chỉ ghi c-ip và cs-uri nếu bạn chỉ muốn phân tích nguồn truy cập.
    Specific Cache Behaviors (Path Patterns):
        Định nghĩa: Bạn có thể chọn ghi logs chỉ cho các yêu cầu thuộc các Cache Behaviors cụ thể, dựa trên path patterns (như /images/*, /api/*).
        Cách hoạt động:
            Bạn liên kết Real-time Log Configuration với một hoặc nhiều Cache Behaviors trong CloudFront distribution.
            Ví dụ: Chỉ ghi logs cho yêu cầu đến /api/* để theo dõi hiệu suất API, bỏ qua /images/* để giảm khối lượng dữ liệu.
        Lợi ích:
            Tập trung vào các loại nội dung quan trọng (như API động) mà không ghi lại dữ liệu không cần thiết (như hình ảnh tĩnh).
            Giảm chi phí xử lý và lưu trữ logs.

5. Lợi ích của Real-time Logs

    Thời gian thực: Logs được gửi đến Kinesis trong vài giây, cho phép phản ứng nhanh với các vấn đề.
    Tùy chỉnh linh hoạt: Chọn Sampling Rate, Fields, và Cache Behaviors để tập trung vào dữ liệu quan trọng.
    Tích hợp mạnh mẽ: Kết hợp với Kinesis, Lambda, S3, hoặc Elasticsearch để phân tích và tự động hóa.
    Hỗ trợ bảo mật: Phát hiện các mẫu yêu cầu bất thường (như lưu lượng lớn từ một IP).
    Tối ưu hóa hiệu suất: Theo dõi tỷ lệ cache hit/miss để điều chỉnh Cache Policy.
7. Lưu ý quan trọng
    Chi phí:
        Real-time Logs phát sinh chi phí cho Kinesis Data Streams (dựa trên số lượng bản ghi và shards).
        Giảm Sampling Rate hoặc chọn ít trường để tiết kiệm chi phí.
    Hiệu suất:
        Sampling Rate thấp có thể làm mất thông tin chi tiết, nhưng phù hợp với lưu lượng lớn.
        Đảm bảo Kinesis Data Stream có đủ shards để xử lý khối lượng dữ liệu.
    Bảo mật:
        Đảm bảo chính sách IAM cho phép CloudFront gửi logs đến Kinesis.
        Mã hóa dữ liệu trong Kinesis để bảo vệ thông tin nhạy cảm.