# Business Rules — Visus × KNIA B2B Prototype

1. **`BR-01` (Quy tắc kiểm tra video đầu vào):** Chỉ chấp nhận 1 file video cho mỗi lần tải lên với định dạng `.mp4`, `.mov`, `.avi`, `.mkv`, thời lượng tối đa `180 giây`, dung lượng tối đa `200MB`.
2. **`BR-02` (Tách biệt Độ phù hợp / Độ tin cậy và Tỷ lệ lỗi):** Mức phù hợp của từng chart KNIA (`%` kèm nhãn mức độ) phản ánh mức độ khớp giữa sự kiện trong video và điều kiện của chart KNIA, hoàn toàn độc lập với cặp **Tỷ lệ lỗi cơ sở** (`Khách hàng : Đối phương`, ví dụ `100 : 0` hoặc `80 : 20`).
3. **`BR-03` (Nguyên tắc Chọn Chart & Cơ chế Sinh Bằng chứng Động - Evidence Generation):**
   - **Khi nhân viên Chọn Chart** (bấm vào card hoặc nhắn mã/tên chart cho Agent): AI sẽ căn cứ vào cấu trúc điều kiện của chính chart đó để **sinh ra/trích xuất bộ bằng chứng ủng hộ riêng cho chart đã chọn**. Mỗi bằng chứng hiển thị rõ: (1) Mô tả hành vi ủng hộ điều kiện của chart, (2) Mốc thời gian xuất hiện trong video (`mm:ss.s`), (3) Tỷ lệ tự tin của bằng chứng (`% Confidence`), (4) Khung hình trích xuất và điều kiện KNIA tương ứng.
   - **Xác nhận Tạo Báo cáo** mới chính thức chốt biểu đồ và bộ bằng chứng ủng hộ đã chọn cho vụ việc, sinh phiên bản Báo cáo PDF (`v1`) và mở khóa giai đoạn **Thương lượng tỷ lệ lỗi**.
4. **`BR-04` (Quy tắc khóa chỉnh sửa khi lập báo cáo):** Trước khi bấm `Xác nhận tạo báo cáo`, nhân viên **chỉ được sửa mục "Nội dung diễn giải của nhân viên"**. Mã chart, sự kiện AI nhận diện, ảnh/frame bằng chứng, bảng đối chiếu điều kiện KNIA và tỷ lệ lỗi cơ sở đều bị khóa chỉnh sửa trực tiếp trên báo cáo.
5. **`BR-05` (Quy tắc bất biến phiên bản báo cáo & Tạo lại báo cáo qua Agent):**
   - Một phiên bản báo cáo đã tạo (`v1`) là bất biến (không sửa trực tiếp mã chart trên `v1`).
   - Để đổi sang chart khác sau khi đã tạo báo cáo, nhân viên phải nhắn lại với AI Agent trong chat để chọn chart khác, kiểm tra bằng chứng/diễn giải và xác nhận tạo **phiên bản báo cáo mới (`v2`)**.
   - Phiên bản mới nhất (`v2`) trở thành **Báo cáo & Biểu đồ hiện hành** của vụ việc (cập nhật lại các yếu tố điều chỉnh ở màn Thương lượng), trong khi mọi phiên bản cũ (`v1`) vẫn được giữ nguyên để xem và tải lại PDF.
6. **`BR-06` (Quy tắc tính tỷ lệ lỗi trong Thương lượng):** Chỉ những yếu tố tăng/giảm lỗi được nhân viên đặt trạng thái **`Đã đồng ý (합의)`** mới được cộng/trừ vào Tỷ lệ lỗi cơ sở để ra Tỷ lệ lỗi sau thương lượng; các mục `Không áp dụng` hoặc `Đang thương lượng` không làm thay đổi tỷ lệ lỗi.
7. **`BR-07` (Quy tắc gỡ lỗi khi AI dừng phân tích `undetermined`):** Khi AI dừng giữa chừng (như dừng ở bước `impact_grid`), hệ thống giữ lại các sự kiện đã phân tích xong trước điểm dừng và mở quyền cho nhân viên hỏi AI nguyên nhân hoặc cung cấp bổ sung thời điểm/sự kiện va chạm trong chat để chạy phân tích lại.

8. **`BR-08` (Hiển thị chart ứng viên):** Tối đa 5 chart, xếp theo % phù hợp giảm dần; nhãn Cao ≥70% / Trung bình 40–69% / Thấp <40%; ẩn chart <20%; không chart nào ≥20% → trạng thái dừng/lỗi.
9. **`BR-09` (Đổi chart sau khi đã thương lượng):** Cảnh báo trước khi tạo phiên bản mới; mục thương lượng cũ lưu lịch sử gắn với phiên bản cũ; phiên bản mới bắt đầu thương lượng lại, tỷ lệ lỗi về tỷ lệ cơ sở của chart mới.
10. **`BR-10` (Thông tin hồ sơ):** Mã hồ sơ (tự sinh), Tên khách hàng, Số hợp đồng, Biển số xe, Ngày tai nạn, Tóm tắt hiện trường, Tên video; chỉ video bắt buộc.
11. **`BR-11` (Vụ việc đã kết thúc):** Chỉ xem/tải PDF; mở lại bằng `Hủy trạng thái kết thúc` (ghi Lịch sử hoạt động) mới được tạo báo cáo mới/đổi chart.
