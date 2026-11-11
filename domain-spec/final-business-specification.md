# TÀI LIỆU MÔ TẢ NGHIỆP VỤ FINAL (VISUS × KNIA B2B)
**Dự án:** Hệ thống Phân tích Tai nạn Giao thông & Quản lý Bồi thường Tự động (Visus AI B2B)  
**Phiên bản:** Final 1.0 (Đã hợp nhất Bản gốc Visus Web & Bản đổi mới B2B Flow)  
**Ngày phê duyệt:** 05/11/2026  

---

## 1. TỔNG QUAN HỆ THỐNG VÀ PHẠM VI (SCOPE & CONTEXT)

Hệ thống **Visus AI B2B** là nền tảng số hỗ trợ giám định viên bảo hiểm (Claims Adjuster) tự động hóa quy trình phân tích video tai nạn giao thông, xác định biểu đồ quy chuẩn lỗi **KNIA (Korea Non-life Insurance Association)**, hỗ trợ đàm phán phân chia tỷ lệ lỗi và số hóa toàn bộ vòng đời hồ sơ bồi thường.

### 1.1. Kết quả giải quyết xung đột (Conflict Resolution Matrix)
* **Quy trình tổng thể:** Giữ trọn vẹn luồng B2B (tải video, AI đề xuất chart, thẩm định bằng chứng, xuất PDF v1) làm tiền đề bắt buộc; sau đó tích hợp liền mạch luồng Visus downstream (Thương lượng tỷ lệ lỗi, Thông báo khách hàng, Đóng hồ sơ, Lịch sử hoạt động).
* **Quy tắc phân quyền chỉnh sửa dữ liệu:** 
  - Trong quá trình lập báo cáo: Dữ liệu sự kiện AI trích xuất, ảnh bằng chứng, bảng đối chiếu điều kiện KNIA và Tỷ lệ lỗi cơ sở được **khóa bất biến (Read-only)**. Giám định viên **chỉ được phép chỉnh sửa ô "Nội dung diễn giải của nhân viên"**.
  - Ngoại lệ phục hồi: Chỉ khi AI gặp lỗi dừng giữa chừng (`undetermined` do thiếu điểm va chạm / chất lượng video), giám định viên mới được phép dùng AI Copilot Chat để bổ sung mốc thời gian va chạm nhằm chạy phân tích lại.
* **Cơ chế đa phiên bản (Versioning):** Báo cáo đã xuất (`v1`) là bất biến. Khi đổi sang biểu đồ khác, hệ thống lưu vết `v1` và sinh bản mới (`v2 [Hiện hành]`), tự động đặt lại giá trị thương lượng theo biểu đồ mới.

---

## 2. QUY TRÌNH NGHIỆP VỤ ĐẦU - CUỐI (5 GIAI ĐOẠN)

```
[Giai đoạn 1] Tải video & AI Phân tích sự kiện 
     │
     ▼
[Giai đoạn 2] Xếp hạng Chart KNIA ──► Chọn Chart ──► Soạn Diễn giải ──► Xuất Báo cáo PDF v1 (MỐC CHỐT)
     │
     ▼
[Giai đoạn 3] Thương lượng Tỷ lệ lỗi với Bảo hiểm đối phương (Mở khóa sau GĐ 2)
     │
     ▼
[Giai đoạn 4] Soạn thảo & Gửi thông báo cho Khách hàng (Đính kèm Link/PDF)
     │
     ▼
[Giai đoạn 5] Kết thúc Vụ việc (Đóng hồ sơ / Lưu trữ có quyền mở lại)
```

---

## 3. DANH MỤC 11 QUY TẮC NGHIỆP VỤ BẤT BIẾN (BUSINESS RULES)

* **`BR-01` (Kiểm định tệp đầu vào):** Chỉ chấp nhận định dạng `.mp4`, `.mov`, `.avi`, `.mkv`. Thời lượng tối đa 180 giây (3 phút), dung lượng không vượt quá 200MB.
* **`BR-02` (Tách biệt Độ phù hợp và Tỷ lệ lỗi cơ sở):**
  - **Mức phù hợp (%)**: Mức độ trùng khớp giữa tình huống video với mô tả chuẩn KNIA (Cao ≥70%, Trung bình 40–69%, Thấp <40%).
  - **Tỷ lệ lỗi cơ sở (A : B)**: Tỷ lệ phân định trách nhiệm pháp lý mặc định theo biểu đồ (ví dụ: Xe A 80% - Xe B 20%). Hai chỉ số này hoàn toàn độc lập.
* **`BR-03` (Nguyên tắc Chọn Chart & Cơ chế Sinh Bằng chứng Động - Evidence Generation):**
  - Khi nhân viên bấm chọn một chart (hoặc gửi mã chart trong chat): **AI sẽ dựa vào tiêu chí của chính chart đó để trích xuất và sinh ra bộ chứng cứ (Evidence Pack) ủng hộ riêng cho chart được chọn**.
  - Mỗi mục bằng chứng bắt buộc gồm 4 thuộc tính:
    1. **Mô tả hành vi/sự kiện chứng minh:** Nêu rõ hành vi ủng hộ điều kiện của chart (VD: Xe A đi thẳng qua giao lộ lúc đèn vàng nhấp nháy, Xe B rẽ trái cắt đầu).
    2. **Mốc thời gian hiển thị trong video (Timestamp / Time range):** Hiển thị chính xác (VD: `00:02.1` - `00:03.4`); khi click vào sẽ tua video ngay tới khung hình đó.
    3. **Tỷ lệ tự tin của bằng chứng (Confidence Score %):** Mức độ chắc chắn của AI về sự kiện đó (VD: 94%, 89%).
    4. **Hình ảnh trích xuất (Frame thumbnail) & Điều kiện KNIA tương ứng**.
  - *Xác nhận tạo báo cáo*: Là mốc pháp lý chính thức chốt biểu đồ và bộ bằng chứng đã chọn cho vụ việc, phát hành PDF `v1` và mở khóa màn hình Thương lượng.
* **`BR-04` (Khóa chỉnh sửa báo cáo):** Trước khi xác nhận, giám định viên chỉ được chỉnh sửa văn bản trong mục "Nội dung diễn giải của nhân viên". Toàn bộ dữ liệu AI (mã chart, ảnh frame, mốc giây, tỷ lệ cơ sở) đều ở trạng thái chỉ đọc.
* **`BR-05` (Bất biến phiên bản & Tạo lại báo cáo):** Bản PDF `v1` không thể chỉnh sửa trực tiếp. Để đổi chart, nhân viên chat với AI để chọn lại chart và xuất bản `v2`. Bản `v2` trở thành bản hiện hành, bản `v1` lưu trữ lịch sử để tra cứu và tải lại.
* **`BR-06` (Quy tắc tính toán Thương lượng lỗi):** Tỷ lệ lỗi sau thương lượng = Tỷ lệ lỗi cơ sở ± Các yếu tố điều chỉnh đã đạt trạng thái **`Đã đồng ý (합의)`**. Các yếu tố `Đang thương lượng` hoặc `Không áp dụng` tuyệt đối không làm thay đổi tỷ lệ lỗi.
* **`BR-07` (Cứu hộ phân tích `undetermined`):** Khi AI không thể kết luận (dừng tại bước nhận diện va chạm), hệ thống giữ nguyên các mốc sự kiện trước đó, hiển thị lý do dừng và mở quyền cho giám định viên nhập mốc va chạm qua chat để AI phân tích tiếp.
* **`BR-08` (Giới hạn hiển thị biểu đồ ứng viên):** Hiển thị tối đa 5 biểu đồ có độ phù hợp cao nhất (ngưỡng tối thiểu ≥20%). Nếu không có biểu đồ nào đạt ≥20%, hệ thống chuyển sang trạng thái cảnh báo phân tích thất bại.
* **`BR-09` (Cảnh báo đặt lại thương lượng khi đổi biểu đồ):** Nếu đã thương lượng ở `v1` mà người dùng đổi chart tạo `v2`, hệ thống bắt buộc hiển thị cảnh báo: Toàn bộ các yếu tố điều chỉnh sẽ được hoàn tác về tỷ lệ cơ sở của biểu đồ mới, lịch sử đàm phán cũ được lưu kèm `v1`.
* **`BR-10` (Thông tin định danh hồ sơ):** Mã hồ sơ (sinh tự động), Tên khách hàng, Số hợp đồng, Biển số xe, Ngày tai nạn, Tóm tắt hiện trường. Ngoại trừ video là bắt buộc, các trường thông tin khác cho phép để trống và bổ sung sau.
* **`BR-11` (Quy chế Đóng hồ sơ):** Hồ sơ chuyển trạng thái `Đã kết thúc` sẽ khóa toàn bộ thao tác chỉnh sửa. Chỉ khi thực hiện hành động `Hủy kết thúc vụ việc` (có ghi vết kiểm toán trong Activity Log) thì hồ sơ mới mở lại quyền tạo báo cáo/thương lượng.

---

## 4. CHI TIẾT NGHIỆP VỤ TỪNG GIAI ĐOẠN

### 4.1. Giai đoạn 1: Tiếp nhận & Phân tích Video
1. **Đầu vào:** Giám định viên kéo thả file video vào màn hình Tải lên hoặc trực tiếp vào khung AI Copilot Chat.
2. **Tiến trình AI trích xuất (4 bước):**
   - Bước 1: Tiền xử lý & nhận diện làn đường / phương tiện.
   - Bước 2: Trích xuất chuỗi sự kiện thời gian (timeline sự kiện).
   - Bước 3: Xác định điểm va chạm (impact point & collision grid).
   - Bước 4: So khớp tập luật KNIA và xếp hạng các biểu đồ tiềm năng.
3. **Xử lý ngoại lệ (`undetermined`):** Nếu AI dừng ở Bước 3 (do camera rung, góc khuất), hệ thống hiển thị badge cảnh báo màu vàng, hiển thị timeline các giây đã nhận diện và mở luồng chat: *"AI cần xác định mốc va chạm. Bạn có thể cung cấp giây va chạm không?"*

### 4.2. Giai đoạn 2: Lựa chọn Biểu đồ KNIA & Sinh Bằng chứng Động (Dynamic Evidence Generation)
1. **Trực quan hóa so sánh biểu đồ:** Hiển thị tối đa 5 card biểu đồ, mỗi card gồm:
   - Mã biểu đồ (VD: `차31`, `차32`, `차252`).
   - Tên tình huống tai nạn (Tiếng Hàn & Tiếng Việt).
   - Thanh đối trọng tỷ lệ lỗi cơ sở (Fault Balance Bar).
   - Mức độ tin cậy/khớp luật (High / Medium / Low).
2. **Cơ chế Sinh Bằng chứng theo Chart được chọn:** Khi nhân viên chọn 1 biểu đồ:
   - AI lập tức truy vấn và hiển thị **danh mục các bằng chứng ủng hộ trực tiếp cho chart đó** (chứ không dùng một danh sách sự kiện tĩnh chung chung).
   - Mỗi bằng chứng hiển thị rõ:
     * **Mốc thời gian (Timestamp):** Thời điểm bắt đầu - kết thúc sự kiện trong video (nhấp vào sẽ đồng bộ nhảy video tới đúng frame).
     * **Độ tự tin của bằng chứng (% Confidence):** Ví dụ 94%, 88%, 79%.
     * **Điều kiện pháp lý KNIA được bảo vệ:** Ví dụ: "Vị trí làn đường", "Quyền ưu tiên giao lộ", "Thời điểm chuyển hướng".
   - Bảng đối chiếu 4 điều kiện của Biểu đồ KNIA: Điểm va chạm, Hướng di chuyển, Tín hiệu đèn, Hành vi vi phạm.
   - Khung cảnh báo điểm chưa chắc chắn (Uncertainties).
3. **Soạn thảo và Ban hành:**
   - Giám định viên kiểm tra và bổ sung ghi chú nghiệp vụ tại ô `Nội dung diễn giải của nhân viên`.
   - Bấm `Xác nhận tạo báo cáo`: Hệ thống tạo báo cáo số hóa PDF `v1` (lưu trữ biểu đồ và toàn bộ chứng cứ ủng hộ đã thẩm định), đóng dấu kiểm định điện tử, chuyển trạng thái vụ việc sang `Chờ thương lượng`.

### 4.3. Giai đoạn 3: Thương lượng Tỷ lệ Lỗi (Fault Negotiation)
1. **Thanh cân bằng lỗi tương tác (Interactive Fault Balance Bar):**
   - Thanh trượt 100% hiển thị trực quan tỷ lệ Xe Khách Hàng (Màu xanh) và Xe Đối Phương (Màu cam).
2. **Danh mục yếu tố điều chỉnh KNIA (Modification Factors):**
   - Tăng/giảm tốc độ, không bật xi-nhan, lỗi quan sát, phương tiện ưu tiên...
   - Mỗi yếu tố có 3 trạng thái: `Không áp dụng`, `Đang thương lượng`, `Đã đồng ý (+/- X%)`.
3. **AI Đàm phán (Copilot Suggestion):**
   - Cung cấp gợi ý lập luận pháp lý và trích dẫn tiền lệ của KNIA cho từng yếu tố đang tranh chấp.
   - Khi hai bên thống nhất, hệ thống cho phép xuất bản **Báo cáo cập nhật sau thương lượng (PDF Update)**.

### 4.4. Giai đoạn 4: Thông báo Khách hàng (Customer Notification)
1. **Soạn thảo thông báo:** Hệ thống tự động điền mẫu thông báo giải trình tai nạn dựa trên kết quả thương lượng cuối cùng.
2. **Kênh phát hành:** Hỗ trợ tạo liên kết tra cứu bảo mật (Secure Web View) gửi qua SMS/KakaoTalk kèm tệp đính kèm PDF.
3. **Phê duyệt:** Giám định viên ký duyệt điện tử trước khi gửi cho khách hàng.

### 4.5. Giai đoạn 5: Kết thúc Vụ việc & Quản trị Hồ sơ
1. **Đóng hồ sơ:** Yêu cầu chọn lý do kết thúc (`Đã thỏa thuận bồi thường`, `Chuyển Hội đồng phân xử KNIA`, `Chuyển Tòa án giải quyết`).
2. **Nhật ký kiểm toán (Activity Audit Log):** Lưu trữ 100% dòng thời gian: ai thực hiện, thời gian, sự kiện (tải video, chọn chart, sửa diễn giải, xuất v1, đổi sang v2, cập nhật thương lượng, đóng/mở hồ sơ).

---

## 5. CẤU TRÚC BÁO CÁO GIÁM ĐỊNH TAI NẠN (CHUẨN 5 PHẦN)

Một bản báo cáo PDF hợp lệ gồm đúng 5 phân đoạn:
1. **Phần 1 - Thông tin vụ việc & Video:** Mã hồ sơ, biển số, thời gian, thông số video (fps, thời lượng).
2. **Phần 2 - Biểu đồ KNIA & Tỷ lệ lỗi cơ sở:** Mã hiệu biểu đồ, hình vẽ sơ đồ va chạm, tỷ lệ lỗi ban đầu.
3. **Phần 3 - Bằng chứng trích xuất:** Chuỗi frame hình ảnh mốc thời gian, bảng đối chiếu 4 điều kiện.
4. **Phần 4 - Các điểm chưa chắc chắn:** Đánh giá độ rủi ro, điểm mù camera, điều kiện thời tiết hạn chế tầm nhìn.
5. **Phần 5 - Diễn giải của Giám định viên:** Nhận định chuyên môn cá nhân của giám định viên phụ trách hồ sơ.

---
*Tài liệu này là cơ sở duy nhất để đối chiếu nghiệm thu (Acceptance Criteria) cho bản mẫu giao diện người dùng và hệ thống vận hành.*

