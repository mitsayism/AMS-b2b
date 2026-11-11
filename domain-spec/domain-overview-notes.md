# Notes nghiệp vụ · Sơ đồ tổng quan xử lý vụ việc

## 1. Mục đích

Sơ đồ mô tả hành trình một vụ tai nạn từ lúc nhân viên mở vụ việc, AI phân tích, chọn chart KNIA, chuẩn bị chứng cứ, thương lượng tỷ lệ lỗi, gửi kết quả cho khách hàng đến khi đóng vụ việc.

Đây là sơ đồ tổng quan; chi tiết giao diện, dữ liệu và quy tắc riêng nằm ở các flow con.

## 2. Đối tượng tham gia

| Đối tượng | Vai trò |
|---|---|
| Nhân viên | Mở vụ việc, kiểm tra kết quả AI, chọn chart, chuẩn bị thương lượng, lập báo cáo và đóng vụ việc. |
| Hệ thống AI | Đọc video, trích xuất keyframe, nhận diện tình tiết, gợi ý chart và chuẩn bị chứng cứ. AI chỉ đề xuất; nhân viên quyết định. |
| Bảo hiểm đối phương | Trao đổi các yếu tố tăng/giảm và xác nhận hoặc không xác nhận tỷ lệ lỗi. |
| Khách hàng | Cung cấp thông tin/video và nhận kết quả sau khi tỷ lệ lỗi đã được chốt. |

## 3. Luồng chính

### 0. Mở vụ việc

- Nhân viên tạo vụ việc, nhập thông tin cơ bản và tải video hiện trường.
- Có thể tải tối đa bốn nguồn video: trước xe khách hàng, sau xe khách hàng, xe đối phương và CCTV.
- Hệ thống kiểm tra file và lưu vụ việc ở trạng thái **Chờ phân tích**.

### 1. Phân tích video

- AI đọc các video đã tải.
- AI phát hiện keyframe, tình tiết, mốc va chạm và thông tin quan sát được.
- Nhân viên xem một video hoặc lưới bốn video, tua đến keyframe và kiểm tra kết quả.
- Kết quả gồm nhận định, thời gian video, keyframe, độ tin tưởng và lý do.
- Nhân viên có thể xác nhận, loại bỏ bằng chứng hoặc yêu cầu phân tích lại.

### 2. Chọn chart KNIA

- AI đề xuất các chart phù hợp với tình huống đã nhận diện.
- Mỗi chart có mã, mô tả, điều kiện áp dụng và tỷ lệ lỗi cơ sở.
- Nhân viên xem chi tiết, so sánh và chọn một chart.
- Chart được chọn làm căn cứ cho bước chứng cứ và thương lượng.

### 3. Chuẩn bị chứng cứ

- AI đối chiếu video với chart đã chọn.
- Hệ thống hiển thị bằng chứng cốt lõi, quyết định tranh chấp tham khảo và án lệ tham khảo.
- Nhân viên xem keyframe, lý do AI, thời gian và nguồn căn cứ.
- Nhân viên có thể loại bỏ bằng chứng không phù hợp.
- Sau khi kiểm tra, nhân viên chuyển sang thương lượng.

### 4. Thương lượng tỷ lệ lỗi

- Nhân viên sử dụng chứng cứ để trao đổi với bảo hiểm đối phương.
- Mỗi yếu tố tăng/giảm có một trạng thái:
  - **Loại trừ:** Không áp dụng.
  - **Đang thương lượng:** Chưa được hai bên xác nhận.
  - **Đã thống nhất:** Được tính vào tỷ lệ cuối.
- Chỉ sử dụng các tỷ lệ có trong recognition criteria book; không tự tạo phần trăm mới.
- Hệ thống cập nhật tỷ lệ dự kiến theo các yếu tố đã thống nhất.
- Khi hai bên đồng ý, nhân viên xác nhận tỷ lệ cuối.

### 5. Báo cáo và thông báo

- Sau khi tỷ lệ được chốt, nhân viên tạo **Báo cáo nội bộ**.
- Báo cáo lưu thông tin vụ việc, chart, chứng cứ, án lệ, quyết định tham khảo và tỷ lệ cuối.
- Nhân viên soạn **Thông báo khách hàng** từ kết quả đã chốt.
- Nhân viên xem trước, duyệt và gửi mô phỏng qua kênh được chọn.
- Báo cáo và thông báo có lịch sử phiên bản riêng.

### 6. Đóng vụ việc

- Nhân viên đóng vụ việc sau khi hoàn tất xử lý.
- Hệ thống lưu tỷ lệ cuối, báo cáo, thông báo và nhật ký hoạt động.
- Vụ việc chuyển sang **Đã kết thúc**.

## 4. Ngoại lệ và nhánh xử lý

### Thiếu hoặc lỗi video

- Nếu thiếu video, hệ thống cho phép tải bổ sung hoặc thay thế video.
- Nếu vẫn tiếp tục không có đủ video, nhân viên phải xác nhận tiếp tục; kết quả cần ghi rõ phạm vi dữ liệu thiếu.
- Thay video làm mất hiệu lực kết quả AI liên quan và yêu cầu chạy lại phân tích.

### AI dừng hoặc phân tích thất bại

- Vụ việc chuyển về **Chờ phân tích**.
- Hiển thị lý do lỗi và nút **Phân tích lại**.
- Kết quả chưa hoàn tất không được dùng để chọn chart hoặc thương lượng.

### Không có chart phù hợp

- Nhân viên mở thư viện chart, tìm kiếm và xem dữ liệu gốc.
- Nếu vẫn không có chart phù hợp, vụ việc dừng ở **Chờ chọn chart** và cần xử lý nghiệp vụ ngoài prototype.

### Bằng chứng bị loại

- Bằng chứng bị loại không được dùng trong reasoning, báo cáo hoặc thương lượng.
- Có thể khôi phục nếu nhân viên chưa đóng vụ việc.

### Hai bên không thống nhất

- Các yếu tố chưa thống nhất giữ trạng thái **Đang thương lượng**.
- Không được gửi thông báo kết quả cuối cho khách hàng.
- Vụ việc tiếp tục trao đổi hoặc chuyển sang Hội đồng phân xử theo quy trình nghiệp vụ.

### Thay đổi chart sau thương lượng

- Chart mới tạo lại tỷ lệ cơ sở và bộ chứng cứ liên quan.
- Các kết quả thương lượng cũ được giữ trong lịch sử, nhưng trạng thái hiện tại chuyển về cần xem xét lại.
- Nhân viên phải xác nhận lại các yếu tố trước khi tạo báo cáo mới.

### Mở lại vụ việc đã kết thúc

- Nhân viên mở lại vụ việc từ danh sách.
- Hệ thống giữ toàn bộ lịch sử cũ và cho phép xem lại dữ liệu từng bước.
- Nếu chạy lại hoặc chỉnh sửa, hệ thống tạo phiên bản mới; dữ liệu cũ không bị ghi đè.

## 5. Quy tắc trạng thái

```text
Chờ phân tích
  → Đang phân tích
  → Chờ chọn chart
  → Đang thương lượng
  → Đã thống nhất
  → Đã kết thúc
```

Các nhánh quay lại:

```text
Đang phân tích ── lỗi/dừng ──→ Chờ phân tích
Đang thương lượng ── đổi chart ──→ Chờ chọn chart
Đã kết thúc ── mở lại ──→ trạng thái trước khi đóng
```

## 6. Ghi chú cho thiết kế

- Swimlane thể hiện trách nhiệm của từng đối tượng; mũi tên liền là luồng chính, mũi tên đứt là trao đổi hoặc quay lại.
- Nút chuyển bước chỉ khả dụng khi điều kiện bước hiện tại hoàn tất.
- AI phải được trình bày là bên hỗ trợ, không phải người phê duyệt cuối.
- Các trạng thái lỗi, thiếu dữ liệu và cần duyệt lại phải nhìn thấy ngay trên danh sách vụ việc.
- Báo cáo nội bộ và thông báo khách hàng dùng chung kết quả tỷ lệ đã chốt nhưng là hai đầu ra khác nhau.

## 7. Ghi chú cho phát triển

- Mỗi bước cần lưu trạng thái, thời điểm, người thao tác và phiên bản dữ liệu.
- Kết quả AI, chart, chứng cứ, thương lượng, báo cáo và thông báo cần có lịch sử thay đổi.
- Không ghi đè phiên bản đã gửi hoặc đã đóng.
- Mọi hành động quan trọng cần xuất hiện trong nhật ký hoạt động.
