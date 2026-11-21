# Đối chiếu cảnh báo QC1-02 trên ảnh gốc — Day 11

**Phạm vi:** A05, A09, A14; bộ annotation Front 16 ảnh. Đối chiếu ảnh gốc do học viên cung cấp (`anh-can-sua.zip`) với các shape hiện có trong `nguyentronghung-front.zip` đã gộp. Kết quả này là đánh giá trực quan, không thay cho quyết định của người kiểm hoặc chấm Ground Truth.

| Ảnh | Cảnh báo lint | Quan sát trên ảnh gốc | Đề xuất xử lý |
| --- | --- | --- | --- |
| A05 | Thiếu L1, thiếu R1 | Đường đô thị không có vạch phân làn sơn nhìn rõ; biên vùng lái chủ yếu theo xe đỗ và mép đường | Không thêm lane giả; ghi ngoại lệ vì không thấy vạch |
| A09 | Thiếu R1 | Mép phải đi sát vỉa hè / dải tối, đã có `road_curb`; không xác định được vạch sơn R1 rõ ràng | Không đổi `road_curb` thành `lane` và không vẽ R1 suy đoán |
| A14 | Thiếu R1 | Cảnh ban đêm, phần đường trước bị xe che; không xác định được vạch R1 rõ ràng. Các vạch ngang gần giao lộ không phải ranh làn R1 | Không thêm R1 khi chưa có bằng chứng vạch dọc |

**Kết luận:** 4 cảnh báo nghiêm trọng QC1-02 là **cảnh báo cần người kiểm xác nhận**, chưa đủ bằng chứng là lỗi nhãn cần sửa. Cố tình thêm Polyline để xóa cảnh báo sẽ tạo nhãn không có căn cứ, gây rủi ro giảm điểm GT. Giữ nguyên 2 ZIP annotation và giải trình khi QC. Bộ kiểm tự động có thể tiếp tục in ra 4 cảnh báo này.

**Ảnh đối chiếu:** `A05_overlay.png`, `A09_overlay.png`, `A14_overlay.png`. Màu đường vẽ trên ảnh là annotation đang tồn tại, không phải sơn đường thực tế.
