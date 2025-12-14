# 🛡️ SOC_Project_Elastic_Stack_N8N: Automated SOC Incident Response Workflow (SOAR)
![n8n](https://img.shields.io/badge/n8n-Workflow-orange?style=for-the-badge&logo=n8n)
![Elastic](https://img.shields.io/badge/Elastic-SIEM-blue?style=for-the-badge&logo=elastic)
![Security](https://img.shields.io/badge/Security-Operations-red?style=for-the-badge)


## 📖 Tổng quan (Overview)

Dự án này là một quy trình **SOAR (Security Orchestration, Automation, and Response)** được xây dựng trên **n8n**. Mục tiêu của workflow là tự động hóa quy trình giám sát, làm giàu dữ liệu và gửi cảnh báo từ hệ thống **Elastic SIEM** tới các kênh liên lạc của đội ngũ SOC (Telegram/Email) trong thời gian thực.

Workflow giúp giảm thiểu thời gian phản hồi sự cố (MTTR) và loại bỏ các tác vụ thủ công lặp lại của SOC Analyst Tier 1.

## Kiến trúc Lab
![diagram](https://github.com/grapitycreation/SOC_Project_Elastic_Stack_N8N/blob/main/Architecture.png)



## 🏗️ Kiến trúc luồng dữ liệu của Workflow N8N (Data Flow)

Dưới đây là sơ đồ logic của workflow, mô tả cách dữ liệu di chuyển từ Elastic Alert Queue đến người dùng cuối:

![workflow](https://github.com/grapitycreation/SOC_Project_Elastic_Stack_N8N/blob/main/n8n_Workflow.jpg)

1. **Schedule Trigger**: Kích hoạt workflow định kỳ mỗi 1 phút để quét các cảnh báo mới.
2. **Set Query Timestamp**: Sử dụng biến $meta.lastSuccessfulRunDate để xác định mốc thời gian của lần chạy thành công trước đó. Giúp ngăn chặn việc xử lý trùng lặp các log cũ
3. **Query Alert Queue**: Gọi API vào index n8n-alert-queue trên Elasticsearch để lấy danh sách các Alert ID mới phát sinh.
4. **Split Items**: Tách mảng JSON trả về thành từng item riêng biệt để xử lý song song từng cảnh báo.
5. **Fetch Full Details**: Sử dụng _id từ bước trên để truy vấn ngược lại vào index hệ thống (.internal.alerts-security.*) nhằm lấy toàn bộ ngữ cảnh: Source IP, Process Name, User, GeoIP...
6. **Extract & Format**: Parsing JSON phức tạp để trích xuất các trường quan trọng (rule.name, source.ip, payload) và định dạng lại thành bản tin HTML/Text dễ đọc.
7. **Notifications**: Gửi cảnh báo đa kênh: Telegram (cho phản ứng nhanh) và Email (để lưu trữ và báo cáo).

## 🛠️ Công nghệ sử dụng (Tech Stack)
- **Orchestration**: n8n (Self-hosted)
- **SIEM/Log Management**: Elastic Stack (Elasticsearch, Kibana)
- **Log Ingestion**: Elastic Agent (System & Security Integrations)
- **Communication**: Telegram Bot API, SMTP Email

## 📸 Demo Kết quả
![result](https://github.com/grapitycreation/SOC_Project_Elastic_Stack_N8N/blob/main/images/1.jpg)
![result](https://github.com/grapitycreation/SOC_Project_Elastic_Stack_N8N/blob/main/images/2.jpg)
![result](https://github.com/grapitycreation/SOC_Project_Elastic_Stack_N8N/blob/main/images/3.jpg)

