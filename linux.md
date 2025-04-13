# Chapter 1: Introduction to Linux Families
## Three major Linux distribution falimies
### What is distro?
- Distro là viết tắt của "distribution", nghĩa là bản phân phối của hệ điều hành Linux, được đóng gói từ Linux kernel cùng với các công cụ và phần mềm khác để tạo thành một hệ điều hành hoàn chỉnh.
- Mỗi distro có triết lý riêng, mục tiêu riêng, có thể dành cho desktop, server, bảo mật, embedded system, v.v.
- Linux không giống như Windows hay macOS có một phiên bản duy nhất. Thay vào đó, Linux là một nhân hệ điều hành (kernel), và nhiều tổ chức, cộng đồng hoặc công ty sẽ xây dựng các bản phân phối riêng dựa trên kernel này — đó chính là các distro.
1. Red Hat Enterprise Linux (RHEL)
- Các distro thuộc họ này
    - Red Hat Enterprise Linux (RHEL)

    - CentOS (giờ đã chuyển thành CentOS Stream): là một bản sao gần như giống hệt RHEL, là bản phân phối miễn phí.
    - CentOS Stream: không còn là "clone", mà là bản rolling preview của RHEL, nghĩa là cập nhật sớm hơn một chút so với RHEL chính thức.

    - Fedora: là một bản phân phối Linux hiện đại, cập nhật liên tục, được dùng làm nơi kiểm thử (testing) những công nghệ mới nhất. Nếu mọi thứ hoạt động tốt ở Fedora, thì Red Hat sẽ chọn lọc và đưa những phần ổn định vào bản doanh nghiệp RHEL.

    - Rocky Linux, AlmaLinux (thay thế CentOS)

- Quản lý package:
    - Sử dụng RPM (Red Hat Package Manager)

    - Công cụ cài đặt: yum (trước đây), dnf (hiện nay, hiện đại hơn)
- Đặc điểm: 
    - Ổn định, phù hợp cho server, doanh nghiệp.
    - Cấu hình thường nằm ở /etc/sysconfig.
    - Hệ thống khởi động: systemd
- Key Facts about the Red Hat Families
| Kiến trúc vi xử lý      | Dùng ở đâu?                      | Linux hỗ trợ?         | Ghi chú                                 |
|-----------------|----------------------------------|------------------------|------------------------------------------|
| **x86_64**       | PC, laptop, server               | ✅ Rất tốt             | Mặc định cho hầu hết các distro          |
| **ARM (aarch64)**| Smartphone, IoT, server mới      | ✅ Rất mạnh            | Được hỗ trợ tốt bởi cả RHEL và Ubuntu   |
| **Itanium (IA-64)**| Server HP cũ                  | ❌ Đã bị bỏ            | Intel ngừng phát triển, không phổ biến   |
| **PowerPC (ppc64le)**| Server IBM, siêu máy tính | ✅ (hạn chế)           | Dành cho hệ thống lớn và HPC            |

```
Upstream là gì?

Trong thế giới phần mềm mã nguồn mở, upstream nghĩa là nơi xuất phát hoặc nguồn gốc ban đầu của phần mềm. 
Ví dụ: Khi Fedora phát triển và thử nghiệm một phần mềm hoặc tính năng mới, sau đó, các bản ổn định của tính năng này sẽ được đưa xuống (downstream) vào các bản distro ổn định hơn như Red Hat Enterprise Linux (RHEL).
```

2. SUSE Family
- Các distro:

    - SUSE Linux Enterprise Server (SLES): is upstream for open SUSE

    - openSUSE (miễn phí, cộng đồng)
- Quản lý package:

    - Cũng dùng RPM, nhưng không dùng yum hay dnf

    - Dùng công cụ riêng: zypper

    - Có trung tâm điều khiển cấu hình mạnh mẽ: YaST (Yet another Setup Tool)
- Đặc điểm:

    - Dễ quản lý với giao diện đồ họa YaST

    - Cấu hình rất hệ thống hóa, phù hợp cả server lẫn desktop

    - SUSE rất phổ biến trong ngành bán lẻ vì nó nhẹ, ổn định, và dễ triển khai trên các hệ thống POS, kiosk, hoặc chuỗi cửa hàng, đặc biệt tại châu Âu và các thị trường cần tùy chỉnh cao.
3. Debian Family
- Debian Family is upstream for Ubuntu, and Ubuntu upstream for Linux Mint and others
- Ubuntu được sử dụng rộng rãi trong cloud deployment vì nó hỗ trợ tốt với các nền tảng như AWS, Azure, Google Cloud, rất thân thiện với người dùng và tối ưu hóa cho các công nghệ DevOps, containerization.
- AWS EC2: Ubuntu là hệ điều hành được sử dụng phổ biến nhất cho các máy chủ EC2.
- Docker & Kubernetes: Ubuntu rất mạnh mẽ với các công cụ container hóa như Docker và Kubernetes, làm nền tảng cho nhiều ứng dụng cloud-native.
- Debian Family: Dựa trên GNOME (là một trong những desktop environment phổ biến nhất trên Linux) nhưng có sự khác biệt về mặt giao diện
- Các distro: Debian, Ubuntu (và các biến thể như Kubuntu, Xubuntu, Linux Mint), Raspberry Pi OS

- Quản lý package:

    - Dùng hệ thống DEB (Debian package)

    - Công cụ cài đặt: apt, apt-get, dpkg

- Đặc điểm:

    - Dễ dùng, cộng đồng đông đảo

    - Cấu hình trực quan, dễ học với người mới

    - Phổ biến cho cả máy chủ và máy cá nhân

# Chapter 2: Linux Philosophy and Concepts

## Linux Philosophy
- Linux là một hệ điều hành mã nguồn mở, được phát triển dựa trên triết lý Unix, nổi tiếng với tính linh hoạt, ổn định và khả năng tùy chỉnh.
1. Everything is a file
- Không chỉ văn bản hay hình ảnh, mà cả ổ đĩa, thiết bị, socket, process, config... đều được biểu diễn như file. Điều này giúp thao tác với hệ thống đơn giản, nhất quán.
- Example: /dev/sda, /proc/cpuinfo, /etc/passwd đều là “file”.

2. Do one thing and do it well
- Các command-line tool của Linux thường rất nhỏ gọn. Mỗi lệnh chỉ tập trung làm một việc duy nhất (ví dụ: grep, cat, ls, wc...), nhưng bạn có thể kết hợp chúng bằng pipeline để tạo ra công cụ mạnh mẽ.
- 📍 Ví dụ: cat file.txt | grep "apple" | wc -l → đếm số dòng chứa từ “apple”.

3. Small is beautiful
- Linux ưa chuộng phần mềm nhẹ, dễ cấu hình, dễ kiểm soát hơn là phần mềm “nặng nề” nhiều tính năng không cần thiết.

4. Everything is configurable
- Hệ thống Linux ưu tiên cấu hình bằng file văn bản (plain text). Điều này giúp dễ dàng quản lý, tự động hóa, sao lưu, chia sẻ và sửa lỗi.

- Ví dụ: File cấu hình của SSH là /etc/ssh/sshd_config, bạn có thể sửa trực tiếp bằng nano hoặc vim.

5 Make each program a filter
- Linux có khả năng kết hợp nhiều chương trình nhỏ lại bằng pipe (|).
- Examplee: ps aux | grep apache | awk '{print $2}' | xargs kill
→ tìm và kill các tiến trình Apache.
s
## 📚 Linux Concepts 

| Khái niệm                | Ý nghĩa                                                                 |
|--------------------------|------------------------------------------------------------------------|
| **Kernel**               | Lõi hệ điều hành, quản lý phần cứng, process, memory...                |
| **Shell**                | Giao diện dòng lệnh giữa người dùng và hệ thống (ví dụ: Bash)         |
| **File system hierarchy**| Cấu trúc thư mục chuẩn như `/etc`, `/var`, `/home`, `/usr`, `/bin`... |
| **Permission & Ownership**| Phân quyền người dùng, nhóm, và quyền đọc/ghi/thực thi                |
| **Process management**   | Quản lý tiến trình, foreground/background, PID, signals...             |
| **Package management**   | Cài đặt và quản lý phần mềm qua hệ thống gói (`apt`, `yum`, `dnf`...) |
| **Daemon & Service**     | Các chương trình chạy nền như `sshd`, `cron`, `httpd`...              |
| **Log system**           | Theo dõi hoạt động hệ thống tại `/var/log/`                            |
| **Boot loader**           | là một phần mềm nhỏ chạy đầu tiên khi máy tính khởi động. Nó có nhiệm vụ tải hệ điều hành Linux vào bộ nhớ (RAM) và chuyển quyền điều khiển cho kernel để tiếp tục quá trình boot. Example: GRUB (sử dụng phổ biến nhất), Syslinux, rEFInd                          |
