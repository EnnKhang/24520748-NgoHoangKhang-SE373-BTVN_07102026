# SE373 - BTVN4: Thiết Kế Agent Sử Dụng Tool & Skill

**Họ và tên:** Ngô Hoàng Khang  
**MSSV:** 24520748  
**Lớp:** SE373  

---

## 1. Tổng quan & Cấu trúc thư mục

Bài làm thực hiện mở rộng khả năng tương tác môi trường tệp cho LangChain Agent thông qua 2 giai đoạn:
1. **Stage 01:** Bổ sung tool `list_files` cho phép Agent duyệt cây thư mục động trong workspace thay vì hardcode đường dẫn.
2. **Stage 02:** Tích hợp Skill `refund-policy` để xử lý quy trình tra cứu chính sách hoàn tiền theo mốc thời gian và định dạng phản hồi chuẩn.

### Cấu trúc bài nộp

```text
.
├── analysis.md
├── stage-01-files/
│   ├── agent.py
│   └── tools/
│       ├── __init__.py
│       └── files.py
│
└── stage-02-skills/
    ├── agent.py
    ├── tools/
    │   ├── __init__.py
    │   └── files.py
    ├── workspace/
    │   ├── data/
    │   │   └── policies/
    │   │       ├── refund-rule-old.md
    │   │       └── refund-rule-new.md
    │   └── skills/
    │       └── refund-policy/
    │           ├── SKILL.md
    │           └── references/
    │               └── answer-template.md
    └── traces/
        ├── 20261007-222930_1b8abca8_turn01_d616e02e.jsonl
        ├── 20261007-223302_1b8abca8_turn02_d199300e.jsonl
        ├── 20261007-223438_1b8abca8_turn03_9c93cef9.jsonl
        └── 20261007-230025_0a026e88_turn01_e06a831a.jsonl