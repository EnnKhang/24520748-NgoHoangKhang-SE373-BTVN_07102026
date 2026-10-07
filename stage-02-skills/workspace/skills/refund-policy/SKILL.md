---
name: refund-policy
description: Tra cứu và kiểm tra điều kiện hoàn tiền của khách hàng dựa trên ngày mua hàng, ngày yêu cầu hoàn tiền và trạng thái kích hoạt thiết bị.
---

# Quy trình xử lý hoàn tiền

1. Sử dụng công cụ `list_files` trên thư mục `data/policies/` để tìm tất cả các tài liệu chính sách hiện có.
2. Dùng công cụ `read_file` đọc nội dung từng tài liệu chính sách để xác định phạm vi ngày hiệu lực. Chọn chính sách áp dụng theo ngày mua của khách hàng.
3. Kiểm tra các thông tin đầu vào:
   - Ngày mua
   - Ngày yêu cầu hoàn tiền
   - Trạng thái kích hoạt (đã kích hoạt hay chưa kích hoạt)
   Nếu thiếu bất kỳ thông tin nào trong 3 thông tin trên, PHẢI hỏi lại người dùng để làm rõ trước khi đưa ra kết luận. Tuyệt đối không tự giả định.
4. Tính số ngày theo ngày lịch (chênh lệch giữa ngày yêu cầu hoàn và ngày mua). Nếu bằng đúng giới hạn số ngày quy định thì vẫn đủ điều kiện về thời gian.
5. Định dạng câu trả lời theo đúng mẫu quy định tại file `references/answer-template.md`.