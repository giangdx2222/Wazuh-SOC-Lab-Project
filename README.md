# Wazuh-SOC-Lab-Project

\# 🛡️ Xây dựng Hệ thống SOC Lab Giám sát An toàn Thông tin với Wazuh SIEM



\## 📝 Tổng quan dự án

Dự án này được xây dựng nhằm thiết lập một mô hình phòng Lab SOC (Security Operations Center) thu nhỏ, sử dụng giải pháp \*\*Wazuh SIEM\*\* làm trung tâm quản lý và phân tích log tập trung. Hệ thống có nhiệm vụ giám sát thời gian thực, phát hiện xâm nhập và phân tích các hành vi bất thường trên đa nền tảng hạ tầng mạng doanh nghiệp giả lập.



\### 🏗️ Kiến trúc mô hình phòng Lab

\* \*\*SIEM / Central Manager:\*\* Máy chủ Wazuh (triển khai trên hệ điều hành Linux thông qua VMware Workstation).

\* \*\*Windows Endpoint (Giám sát chính):\*\* Máy Host Windows cá nhân (Sử dụng Wazuh Agent kết hợp Microsoft Sysmon để đẩy log sâu).

\* \*\*Linux Endpoint (Giám sát \& Giả lập tấn công):\*\* Máy ảo Kali Linux (Cài đặt Wazuh Agent giám sát log hệ thống và dùng để thực hiện các kỹ thuật tấn công kiểm thử).



\---



\## 🚀 Giai đoạn 1: Triển khai \& Cấu hình Máy chủ Wazuh SIEM Central



\### 1. Môi trường triển khai

\* Phần mềm ảo hóa: VMware Workstation Pro / Player

\* Nền tảng: Wazuh OVA (Open Virtual Appliance) phiên bản mới nhất.

\* Cấu hình cấp phát: 1 vCPU (2 Cores), 4GB RAM, Card mạng thiết lập chế độ `NAT` / `Bridged` để cấp phát IP động nội bộ.



\### 2. Kết quả đạt được

Hệ thống đã khởi động thành công dịch vụ ngầm, nhận IP nội bộ từ hệ thống mạng ảo hóa và kích hoạt toàn bộ các thành phần (Wazuh Indexer, Wazuh Server, Wazuh Dashboard).



\*Giao diện Dashboard quản trị tổng quan sau khi đăng nhập thành công:\*

!\[Wazuh Dashboard](images/intro.png)

!\[Wazuh Dashboard](images/1.png)



\---



\## 🛠️ Nhật ký khắc phục lỗi hệ thống (Troubleshooting Log)



Trong quá trình khởi tạo cấu hình file mẫu `.ova` ban đầu trên nền tảng VMware, hệ thống gặp một số xung đột phần cứng thực tế. Dưới đây là quy trình debug lỗi:



\### ❌ Lỗi 1: Treo và sập CPU khi khởi động (`The CPU has been disabled by the guest operating system`)

\* \*\*Nguyên nhân:\*\* Cấu hình lõi phần cứng mặc định của file OVA phân tách không phù hợp kiến trúc CPU ảo (4 Processors x 1 Core) và thiếu tính năng hỗ trợ ảo hóa lồng nhau (Nested Virtualization).

\* \*\*Giải pháp:\*\* Truy cập vào `Virtual Machine Settings` > Hạ thông số xuống `1 Processor` và tăng lên `2 Cores per processor` (Tổng cộng 2 Cores tập trung). Đồng thời tích chọn kích hoạt tính năng phần cứng `Virtualize Intel VT-x/EPT or AMD-V/RVI`.



\### ❌ Lỗi 2: Xung đột ảo hóa hệ điều hành Host (`Virtualized AMD-V/RVI is not supported on this platform`)

\* \*\*Nguyên nhân:\*\* Các tính năng bảo mật bảo vệ lõi (`Memory Integrity`) và nền tảng ảo hóa mặc định của Windows (`Hyper-V`, `Virtual Machine Platform`) giành quyền kiểm soát độc quyền tính năng ảo hóa phần cứng của chip, chặn không cho phần mềm VMware can thiệp lớp sâu.

\* \*\*Giải pháp:\*\*

&#x20;   1.  Truy cập `Core Isolation` trên Windows Defender và chuyển `Memory Integrity` sang trạng thái \*\*OFF\*\*.

&#x20;   2.  Vào `Turn Windows features on or off`, bỏ tích chọn dịch vụ `Hyper-V`, `Virtual Machine Platform` và `Windows Hypervisor Platform`.

&#x20;   3.  Khởi động lại máy tính Host để áp dụng thay đổi phần cứng sạch. Máy ảo sau đó khởi động mượt mà vượt qua logo kiểm tra ban đầu.



\---



\## 📅 Lộ trình các bước tiếp theo (Next Steps)

\- \[ ] \*\*Giai đoạn 2:\*\* Cấu hình và tích hợp Wazuh Agent lên máy Windows Host + Triển khai cấu hình bộ lọc Microsoft Sysmon log nâng cao.

\- \[ ] \*\*Giai đoạn 3:\*\* Cài đặt Agent giám sát lên máy Kali Linux, thiết lập thu thập log xác thực hệ thống.

\- \[ ] \*\*Giai đoạn 4:\*\* Giả lập các kỹ thuật tấn công tấn công (Brute-force, port scanning, malware) và cấu hình Custom Rules trên SIEM để kích hoạt Alert hiển thị Dashboard.





