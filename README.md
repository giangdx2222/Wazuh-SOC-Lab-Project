\# 🛡️ Xây dựng Hệ thống SOC Lab Giám sát An toàn Thông tin với Wazuh SIEM



\## 📝 Tổng quan dự án

\[cite\_start]Dự án này được xây dựng nhằm thiết lập một mô hình phòng Lab SOC (Security Operations Center) thu nhỏ, sử dụng giải pháp \*\*Wazuh SIEM\*\* làm trung tâm quản lý và phân tích log tập trung\[cite: 14]. \[cite\_start]Hệ thống có nhiệm vụ giám sát thời gian thực, phát hiện xâm nhập và phân tích hành vi bất thường trên đa nền tảng hạ tầng\[cite: 14].



\### 🏗️ Kiến trúc mô hình phòng Lab

\* \[cite\_start]\*\*SIEM / Central Manager:\*\* Máy chủ Wazuh Server (Triển khai trên VMware Workstation)\[cite: 14, 20].

\* \[cite\_start]\*\*Windows Endpoint (Giám sát chính):\*\* Máy Host Windows cá nhân (Sử dụng Wazuh Agent kết hợp Microsoft Sysmon để đẩy log sâu)\[cite: 14, 26, 27].

\* \[cite\_start]\*\*Linux Endpoint (Giám sát \& Giả lập tấn công):\*\* Máy ảo Kali Linux (Cài đặt Wazuh Agent giám sát log và thực hiện kiểm thử xâm nhập)\[cite: 14, 23, 30].



\---



\## \[cite\_start]🚀 Giai đoạn 1: Triển khai \& Cấu hình Máy chủ Wazuh SIEM Central 



\### 1. Môi trường triển khai

\* \[cite\_start]Phần mềm ảo hóa: VMware Workstation Pro 

\* \[cite\_start]Nền tảng: Wazuh OVA (Open Virtual Appliance) phiên bản 4.14.6\[cite: 2, 4].

\* \[cite\_start]Cấu hình cấp phát sau tinh chỉnh: 1 vCPU (2 Cores), 4GB RAM, Card mạng chế độ `NAT`\[cite: 10, 60, 110].



\### 2. Kết quả đạt được

\[cite\_start]Hệ thống đã khởi động thành công dịch vụ ngầm, tự động nhận IP nội bộ từ DHCP server của VMware là \*\*`192.168.159.178`\*\*\[cite: 107, 113]. \[cite\_start]Toàn bộ các dịch vụ (Wazuh Indexer, Server, Dashboard) đã vận hành ổn định\[cite: 2, 85].



\*Màn hình Console hệ thống nhận IP nội bộ thành công:\*

!\[Wazuh Console IP](images/1.png)



\*Giao diện đăng nhập hệ thống qua Web (Địa chỉ truy cập: https://192.168.159.178):\*

!\[Wazuh Web Login](images/login.png)



\---



\## \[cite\_start]🛠️ Nhật ký khắc phục lỗi hệ thống (Troubleshooting Log) 



\[cite\_start]Trong quá trình khởi tạo cấu hình file mẫu `.ova` ban đầu trên nền tảng VMware, hệ thống gặp một số xung đột phần cứng thực tế\[cite: 20, 58]. \[cite\_start]Dưới đây là quy trình debug lỗi:



\### ❌ Lỗi 1: Treo và sập CPU khi khởi động (`The CPU has been disabled by the guest operating system`)

\* \[cite\_start]\*\*Nguyên nhân:\*\* Cấu hình lõi phần cứng mặc định của file OVA phân tách không phù hợp kiến trúc CPU ảo (4 Processors x 1 Core) và thiếu tính năng hỗ trợ ảo hóa lồng nhau (Nested Virtualization)\[cite: 58, 59].

\* \[cite\_start]\*\*Giải pháp:\*\* Chỉnh sửa phần cài đặt phần cứng (Settings) máy ảo trên VMware thành `1 Processor` và `2 Cores per processor`\[cite: 60]. \[cite\_start]Đồng thời tích chọn kích hoạt tính năng phần cứng `Virtualize Intel VT-x/EPT or AMD-V/RVI`\[cite: 64].



\### ❌ Lỗi 2: Xung đột ảo hóa hệ điều hành Host (`Virtualized AMD-V/RVI is not supported on this platform`)

\* \*\*Nguyên nhân:\*\* Các tính năng bảo mật bảo vệ lõi (`Memory Integrity`) và nền tảng ảo hóa mặc định của Windows (`Hyper-V`, `Virtual Machine Platform`) giành quyền kiểm soát độc quyền tính năng ảo hóa phần cứng của chip, chặn không cho phần mềm VMware can thiệp lớp ảo hóa lồng nhau.

\* \*\*Giải pháp:\*\*

&#x20;   1. Truy cập `Core Isolation` trên Windows Defender và chuyển `Memory Integrity` sang trạng thái \*\*OFF\*\*.

&#x20;   2. Vào `Turn Windows features on or off`, bỏ tích chọn dịch vụ `Hyper-V`, `Virtual Machine Platform` và `Windows Hypervisor Platform`.

&#x20;   3. Khởi động lại máy tính Host để áp dụng thay đổi phần cứng sạch. Máy ảo sau đó khởi động mượt mà vượt qua logo kiểm tra ban đầu.



\### \[cite\_start]❌ Lỗi 3: Không nhận địa chỉ IP mạng nội bộ (Chỉ nhận Loopback IP `127.0.0.1`) \[cite: 105]

\* \[cite\_start]\*\*Nguyên nhân:\*\* Do card mạng `eth0` của máy ảo đang thiết lập chế độ chưa khớp với dải cấp phát DHCP của hệ thống phần mềm ảo hóa\[cite: 107].

\* \[cite\_start]\*\*Giải pháp:\*\* Thay đổi cấu hình Network Adapter từ `Bridged` sang `NAT`, thực hiện xin lại cấp phát IP động bằng lệnh `sudo dhclient eth0` (hoặc khởi động lại máy ảo)\[cite: 110, 111]. \[cite\_start]Hệ thống nhận dải IP hợp lệ: `192.168.159.178`\[cite: 113].



\---



\## 📅 Lộ trình các bước tiếp theo (Next Steps)

\- \[ ] \[cite\_start]\*\*Giai đoạn 2:\*\* Cấu hình và tích hợp Wazuh Agent lên máy Windows Host + Triển khai cấu hình bộ lọc Microsoft Sysmon log nâng cao\[cite: 26, 27].

\- \[ ] \[cite\_start]\*\*Giai đoạn 3:\*\* Cài đặt Agent giám sát lên máy Kali Linux, thiết lập thu thập log xác thực hệ thống\[cite: 23, 24].

\- \[ ] \[cite\_start]\*\*Giai đoạn 4:\*\* Giả lập các kỹ thuật tấn công (Brute-force, port scanning) và cấu hình Custom Rules trên SIEM để kích hoạt Alert hiển thị Dashboard\[cite: 30, 31].

