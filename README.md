# AMS B2B — Hệ Thống Phân Tích Tai Nạn & Xác Định Tỷ Lệ Lỗi Bồi Thường

Dự án B2B mô phỏng luồng nghiệp vụ giải quyết bồi thường tai nạn giao thông giữa công ty bảo hiểm, hệ thống AI, đối phương và khách hàng.

---

## Cấu trúc thư mục

Repository đã được tách biệt rõ ràng giữa **Prototype** và **Domain Spec**:

```
AMS-b2b/
├── index.html                      # Portal điều hướng trung tâm
├── prototype/                      # ⚡ Ứng dụng Prototype thực tế
│   ├── index.html                  # Workbench ứng dụng đầy đủ (Video, KNIA Chart, Bằng chứng, Thương lượng, Báo cáo)
│   └── swimlane-editor/            # Ứng dụng Swimlane Editor (Vite + React Flow đã build)
│       ├── index.html
│       └── assets/
├── domain-spec/                    # 📘 Tài liệu Đặc tả Nghiệp vụ & Domain
│   ├── index.html                  # Tài liệu Handoff tương tác (nhúng trực tiếp Swimlane Editor)
│   ├── business-rules.md           # Quy tắc nghiệp vụ tính lỗi và hiệu chỉnh
│   ├── confirmed-requirements.md   # Yêu cầu nghiệp vụ đã thống nhất
│   ├── domain-overview-notes.md    # Ghi chú tổng quan domain
│   └── final-business-specification.md # Đặc tả bàn giao chi tiết
├── README.md
└── .gitignore
```

---

## Hướng dẫn sử dụng

1. Mở file `index.html` tại thư mục gốc bằng bất kỳ trình duyệt nào (hoặc chạy local server như `python3 -m http.server 8088`).
2. Nhấp vào:
   - **Prototype Workbench** (`./prototype/index.html`): Để trải nghiệm thao tác đầy đủ 6 bước nghiệp vụ.
   - **Domain Specification** (`./domain-spec/index.html`): Để đọc đặc tả chi tiết và tương tác với sơ đồ Swimlane 3 bên.
