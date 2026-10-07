# Báo Cáo Phân Tích (analysis.md)

## 1. Kết quả kiểm tra tool trực tiếp

- **Thư mục hợp lệ (`path="data/policies"`):** Trả về danh sách các tệp và thư mục con với đầy đủ `name`, `path`, `type`.
- **Đường dẫn là tệp (`path="data/policies/policy-before-oct.md"`):** Trả về mã lỗi `NOT_A_DIRECTORY`.
- **Đường dẫn không tồn tại (`path="data/unknown"`):** Trả về mã lỗi `FILE_NOT_FOUND`.
- **Đường dẫn vượt workspace (`path="../../"`):** Bị `_resolve` chặn và trả về lỗi `PATH_OUTSIDE_WORKSPACE`.

---

## 2. Kết quả các kịch bản kiểm tra & bằng chứng trong Trace

| Kịch bản | Yêu cầu | Kết quả thực tế | Bằng chứng |
| :--- | :--- | :--- | :--- |
| **Trường hợp A** | Mua 28/09/2026, yêu cầu hoàn 06/10/2026, chưa kích hoạt | Agent chọn chính sách trước tháng 10; tính 8 ngày; **không đủ điều kiện hoàn tiền** vì vượt giới hạn 7 ngày | `20261007-222930_1b8abca8_turn01_d616e02e.jsonl` |
| **Trường hợp B** | Mua 02/10/2026, yêu cầu hoàn 12/10/2026, chưa kích hoạt | Agent áp dụng chính sách từ tháng 10; tính 10 ngày; **đủ điều kiện hoàn tiền và không thu phí** | `20261007-223302_1b8abca8_turn02_d199300e.jsonl` |
| **Thiếu thông tin** | Mua 02/10/2026, yêu cầu hoàn 05/10/2026 nhưng không cung cấp trạng thái kích hoạt | Agent nhận biết trạng thái kích hoạt chưa xác định và **không tự giả định**; đưa ra kết quả tương ứng cho trường hợp đã kích hoạt và chưa kích hoạt | `20261007-223438_1b8abca8_turn03_9c93cef9.jsonl` |
| **Đổi tên file** | Đổi `policy-before-oct.md` thành `refund-rule-old.md` và `policy-from-oct.md` thành `refund-rule-new.md`, sau đó chạy lại trường hợp B | Agent vẫn tìm và áp dụng đúng chính sách. `list_files` phát hiện các tên file mới và `read_file` đọc `refund-rule-new.md`; kết quả vẫn **đủ điều kiện hoàn tiền, không thu phí** | `20261007-230025_0a026e88_turn01_e06a831a.jsonl` |

### Nhận xét

Kết quả cho thấy agent không phụ thuộc vào tên file chính sách được ghi cứng. Sau khi đổi tên hai file, agent vẫn sử dụng `list_files` để khám phá các file hiện có và chọn file phù hợp để đọc.

Trong trường hợp thiếu trạng thái kích hoạt, agent không tự giả định giá trị còn thiếu mà thể hiện kết quả theo từng khả năng của trạng thái kích hoạt.

---

## 3. Trả lời câu hỏi cuối bài

**Câu hỏi:**

> Vì sao cần tool để tìm file và skill để hướng dẫn chọn chính sách? Nếu agent chưa có tool tìm file, việc sửa prompt có giải quyết được yêu cầu đổi tên file không? Giải thích.

### 1. Vì sao cần tool để tìm file (`list_files`)?

LLM không tự biết cấu trúc và tên file hiện tại trong workspace. Tool `list_files` cho phép agent khám phá động (dynamic discovery) các file đang tồn tại trong thư mục.

Nhờ đó, agent không cần ghi cứng tên file. Khi `policy-from-oct.md` được đổi thành `refund-rule-new.md`, agent vẫn có thể dùng `list_files` để phát hiện tên mới rồi sử dụng `read_file` để đọc nội dung.

### 2. Vì sao cần skill để hướng dẫn chọn chính sách?

Skill `refund-policy` chứa quy trình nghiệp vụ mà agent cần tuân theo khi xử lý yêu cầu hoàn tiền:

1. Liệt kê các file chính sách.
2. Đọc chính sách và xác định điều kiện hiệu lực.
3. Kiểm tra ngày mua, ngày yêu cầu hoàn và trạng thái kích hoạt.
4. Tính số ngày giữa ngày mua và ngày yêu cầu hoàn.
5. Áp dụng đúng chính sách.
6. Trả lời theo cấu trúc thống nhất.

Như vậy, tool cung cấp khả năng thao tác với dữ liệu thực tế, còn skill hướng dẫn agent cách sử dụng các tool và dữ liệu đó đúng quy trình.

### 3. Chỉ sửa prompt có giải quyết được yêu cầu đổi tên file không?

**Không.**

Nếu agent không có tool tìm file, prompt chỉ có thể cung cấp hoặc ghi cứng tên file đã biết trước. Khi file được đổi tên, thông tin trong prompt sẽ trở nên lỗi thời và `read_file` có thể tiếp tục truy cập đường dẫn cũ dẫn đến `FILE_NOT_FOUND`.

Việc sửa prompt sau mỗi lần đổi tên file cũng không giải quyết được yêu cầu một cách tổng quát vì hệ thống vẫn phụ thuộc vào con người cập nhật tên file.

Do đó, `list_files` là cần thiết để agent có thể tự khám phá tên file hiện tại và thích ứng khi file được đổi tên.