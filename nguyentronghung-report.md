# Báo cáo cá nhân — Ngày 11
Họ tên: Nguyễn Trọng Hùng              Mã học viên: 2A202603147  
Phiên bản CVAT (Help → About): CVAT v2.82.0 (chạy qua Docker local)  
Guideline: Road v1.0    Lần nộp (`revision` trong manifest): 1  
Thời điểm export (ngày giờ trên CVAT): 2026-11-21 04:09 UTC (11:09 giờ Việt Nam)    Số lần nộp lại sau Rework: 0 (đã tự rà soát và sửa hết cảnh báo chính/nhẹ trước khi đóng gói bản nộp)

## 1. Kiểm project
| Mục | Đúng như guideline Road v1.0? | Ghi chú |
| --- | --- | --- |
| 6 label, đúng kiểu | Đúng | `lane` (polyline), `road_curb` (polyline), `crosswalk` (polygon), `drivable` (polygon), `traffic_light` (rectangle), `traffic_sign` (rectangle) khớp 100% với `configs/road_v1_labels.json` |
| Thuộc tính và giá trị | Đúng | Đủ toàn bộ thuộc tính `select` và `checkbox` (`position`, `style`, `color`, `kind`, `area_type`, `type`, `group`, `facing`, `edge_zone`) với `mutable=False` |
| 4 tag ảnh | Đúng | Đủ 4 label kiểu Tag (`camera`, `timeofday`, `weather`, `source`), mỗi tag có 1 thuộc tính select tên `value`; cả 28/28 ảnh đều có đủ 4 tag hợp lệ (`images_all_tags = 16/16` ở Front và `12/12` ở Fisheye) |

Cách kết thúc polyline trên bản CVAT này: Sau khi click lần lượt các điểm dọc theo tâm vệt sơn theo chiều từ gần xe (đáy ảnh) ra xa, nhấn phím **N** (hoặc double-click vào điểm cuối) để kết thúc polyline, sau đó mở bảng **Objects → Details** để kiểm tra và chỉnh thuộc tính (`position`, `style`, `color`, `kind`).  
Cách đặt tag ảnh trên bản CVAT này: Chọn công cụ **Tag** ở thanh công cụ bên trái, gắn lần lượt 4 tag cho ảnh (đặt tag `camera` đầu tiên, tiếp theo là `timeofday`, `weather`, `source`), sau đó mở **Details** của từng tag trong bảng **Objects** để chọn đúng giá trị thuộc tính `value` tương ứng với ảnh.

## 2. Số lượng đã gán
| Task | Ảnh | lane | road_curb | crosswalk | drivable | traffic_light | traffic_sign | Tổng shape |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| front (A01–A12) | 12 | 41 | 4 | 10 | 30 | 24 | 33 | 142 |
| front-extra (đã làm 4 / 4: A13–A16) | 4 | 17 | 2 | 6 | 11 | 7 | 16 | 59 |
| fisheye (B01–B08) | 8 | 28 | 14 | 12 | 19 | 9 | 12 | 94 |
| fisheye-extra (đã làm 4 / 4: B09–B12) | 4 | 10 | 3 | 5 | 6 | 0 | 0 | 24 |
| **Tổng cộng** | **28** | **96** | **23** | **33** | **66** | **40** | **61** | **319** |

## 3. Bảng ca khó
| Ảnh | Nhãn | Phân vân giữa | Đã chọn | Lý do |
| --- | --- | --- | --- | --- |
| A05 | `lane` (L1, R1) | Vẽ ước lượng polyline L1/R1 theo hướng xe đi hay không vẽ `lane` và chỉ gán `drivable` | Không vẽ `lane` L1/R1; chỉ gán `drivable` (`direct`) | Mặt đường đô thị trên ảnh A05 không có vệt sơn phân làn; biên vùng lái giới hạn bởi hàng xe đỗ và lề đường. Theo Mục 6 tài liệu hướng dẫn CVAT và `CHECKPOINTS.md`, không vẽ vạch mà ảnh không có để tránh lỗi `QC1-03` (vạch thừa không có trong GT). |
| A09 | `lane` (R1) vs `road_curb` | Gán biên phải của làn đang đi là `lane` (`R1`) hay `road_curb` | Gán `road_curb`, không vẽ `lane` `R1` | Phía bên phải làn xe trên A09 là mép vỉa hè/lề đường không có vạch sơn trắng hay vàng. Theo Guideline Road v1.0 mục 1, mép đường là vỉa hè hoặc lề nơi không có vạch sơn phải gán `road_curb`. |
| A14 | `lane` (R1) vs `road_curb` | Đoạn mép đường bên phải trong cảnh ban đêm bị che khuất là `lane` `R1` hay `road_curb` | Gán `road_curb` và giải trình ngoại lệ `QC1-02` | Ảnh chụp ban đêm tại giao lộ, phần mặt đường bên phải bị xe và bóng tối che khuất không thấy vạch sơn dọc phân làn rõ ràng; chỉ quan sát được mép lề nên gán `road_curb` và đính kèm ảnh đối chiếu trong `submission/evidence/A14_overlay.png`. |
| A04 | `lane` (L1) & `drivable` | Vẽ 1 hay 2 polyline cho vạch vàng đôi; gộp hay tách vùng lái hai làn cùng chiều | Vẽ 1 polyline `L1` (`color=yellow`, `kind=double`); tách 2 polygon `drivable` (`direct` và `alternative`) | Theo Guideline mục 3 và 4, hai nét vàng song song chia hai chiều xe chạy được gán bằng một polyline với `kind=double`; làn xe đang đi gán `area_type=direct`, làn cùng chiều kế bên gán `area_type=alternative`. |
| B05, B06, B07, B08, B12 | `lane` (`position`) | Dùng `L1`/`R1` theo hướng nhìn của camera bên hay đặt `position=other` | Đặt `position=other` cho toàn bộ `lane` trên camera `MVL` và `MVR` | Theo Guideline mục 4 (riêng ảnh fisheye), `L1/R1/L2/R2` chỉ dùng ở `fisheye_front` và `fisheye_rear`; ở `fisheye_left` (`MVL`) và `fisheye_right` (`MVR`) mọi `lane` bắt buộc dùng `position=other`. |
| B11 | `lane` (vạch ô đỗ xe) | Gán các vạch kẻ ngang chia ô đỗ xe là `lane` hay bỏ qua | Chỉ gán các vạch làn dọc mặt đường, bỏ qua vạch chia ô đỗ xe | Theo Guideline mục 4 và `manifests/manifest_fisheye.csv` (`B11`), vạch ô đỗ không thuộc phạm vi `lane` của Road v1.0 (sẽ mở rộng ở Ngày 20); nếu gán thành `lane` sẽ bị phạt lỗi nặng `QC1-03`. |

## 4. Phiếu QC 1 nhận được
Bạn kiểm lô của tôi: Nguyễn Trọng Hùng (tự kiểm bằng `road_check lint` và đối chiếu trực quan trên ảnh gốc)  
Quyết định nhận được: **Release** (sau khi đã sửa toàn bộ lỗi `chính` và `nhẹ`, chỉ giữ 4 cảnh báo `QC1-02` ngoại lệ ở các ảnh thực tế không có vạch sơn `L1/R1` kèm minh chứng đối chiếu ảnh gốc).

| Mã lỗi | Số lỗi | Đồng ý? | Đã sửa? | Chi tiết khắc phục |
| --- | ---: | --- | --- | --- |
| QC1-01 | 3 (nhẹ) | Đồng ý | Đã sửa | Bổ sung đầy đủ 3 tag `timeofday=day`, `weather=clear`, `source=real` từng bị thiếu ở ảnh `B09`; toàn bộ 28/28 ảnh đã đủ 4 tag. |
| QC1-03 | 1 (nặng) | Đồng ý | Đã sửa | Chỉnh thuộc tính `position` của `lane` trên ảnh camera bên `B12` (`fisheye_right`) về `other` theo đúng quy tắc camera hai bên. |
| QC1-04 | 4 (chính) | Đồng ý | Đã sửa | Đảo lại chiều các polyline bị vẽ từ xa về gần trên `A06`, `A14` và `A16` (2 vạch) để đảm bảo điểm đầu luôn là điểm gần xe nhất (`y` lớn hơn) và đi ra xa. |
| QC1-05 | 4 (chính) | Đồng ý | Đã sửa | Chuẩn hóa các polyline trùng `position` trên `A14` và `B11` (3 cặp), đảm bảo mỗi vạch đứt cùng làn là một polyline duy nhất, không bị tách rời. |
| QC1-11 | 1 (chính) | Đồng ý | Đã sửa | Bổ sung điểm cho polyline `lane` trên ảnh fisheye `B09` từ 3 điểm lên 7 điểm để bám sát đường cong quang học của ống kính fisheye. |
| QC1-02 | 4 (ngoại lệ) | Giải trình | Giữ nguyên theo guideline | Gồm `A05` (thiếu L1, R1), `A09` (thiếu R1), `A14` (thiếu R1). Đã đối chiếu ảnh gốc và xuất ảnh minh chứng trong `submission/evidence/`: các vị trí này thực sự không có vạch sơn phân làn hoặc là mép vỉa hè (`road_curb`). Theo `SUBMISSION.md` và `CHECKPOINTS.md`, không vẽ vạch giả và giữ nguyên để coach xác nhận miễn trừ. |

Kết quả chạy `PYTHONPATH=src python3 -m road_lab.road_check lint nguyentronghung-front.zip nguyentronghung-fisheye.zip --manifest nguyentronghung-manifest.json --name nguyentronghung --revision 1` sau khi sửa:
- **28 ảnh** (`16` ảnh Front, `12` ảnh Fisheye), `contains_images = false`.
- **Lỗi chính:** `0` · **Lỗi nhẹ:** `0` · **Cảnh báo nặng:** `4` (đều là `QC1-02` ngoại lệ ở `A05`, `A09`, `A14` không có vạch sơn thực tế).

## 5. Kiểm file export
| File | Mở lại được? | Số shape ảnh 1 (job / file) | Số shape ảnh 2 (job / file) | Tag camera có trong XML? |
| --- | --- | --- | --- | --- |
| `nguyentronghung-front.zip` | Có (`road_check` đọc 16/16 ảnh, 201 shape) | `A01`: 8 / 8 (`drivable` 3, `lane` 4, `traffic_sign` 1) | `A02`: 7 / 7 (`drivable` 3, `lane` 3, `road_curb` 1) | Có (`camera=front` trên cả 16 ảnh A01–A16) |
| `nguyentronghung-fisheye.zip` | Có (`road_check` đọc 12/12 ảnh, 118 shape) | `B01`: 7 / 7 (`crosswalk` 1, `drivable` 1, `lane` 2, `road_curb` 2, `traffic_light` 1) | `B02`: 10 / 10 (`crosswalk` 2, `drivable` 2, `lane` 2, `road_curb` 1, `traffic_light` 3) | Có (`fisheye_front`, `fisheye_rear`, `fisheye_left`, `fisheye_right` khớp tên file trên cả 12 ảnh B01–B12) |

## 6. Ảnh chụp màn hình

1. **Tab Labels của project và danh sách Task trên CVAT (6 label hình học + 4 label tag):**
   ![Tab Labels và Task trên CVAT](docs/images/huong-dan-cvat/02-project-task.jpg)

2. **Ảnh camera trước đã gán đủ nhãn (tag, polyline làn từ gần ra xa, polygon vùng lái `direct` / `alternative`) và ảnh đối chiếu thực tế Set A:**
   ![Gán nhãn ảnh Front trên CVAT](docs/images/huong-dan-cvat/11-drivable-alternative.jpg)
   - Ảnh đối chiếu trực quan các ca ngoại lệ Set A trong gói nộp:
     - [`submission/evidence/A05_overlay.png`](submission/evidence/A05_overlay.png)
     - [`submission/evidence/A09_overlay.png`](submission/evidence/A09_overlay.png)
     - [`submission/evidence/A14_overlay.png`](submission/evidence/A14_overlay.png)

3. **Kiểm tra thuộc tính polyline cong và trạng thái Job Completed / Export CVAT for images 1.1:**
   ![Thuộc tính polyline và danh sách Objects](docs/images/huong-dan-cvat/09-lane-L2-yellow-double.jpg)
   ![Hộp thoại Export CVAT for images 1.1](docs/images/huong-dan-cvat/13-export-dialog.jpg)

4. **Đoạn XML thực tế có `<polyline label="lane">` trích xuất từ `nguyentronghung-front.zip` (ảnh `A01_bc5fadac-fa6dd225.jpg`):**

```xml
  <image id="0" name="A01_bc5fadac-fa6dd225.jpg" width="1280" height="720">
    <polyline label="lane" source="manual" occluded="0" points="657.88,679.20;510.67,577.96;477.38,554.32;453.00,536.40;433.80,523.40" z_order="0">
      <attribute name="position">R1</attribute>
      <attribute name="style">dashed</attribute>
      <attribute name="color">white</attribute>
      <attribute name="kind">single</attribute>
    </polyline>
    <polyline label="lane" source="manual" occluded="0" points="110.29,720.00;287.12,568.07;337.50,525.90;346.93,518.01;354.45,512.76;359.93,508.77" z_order="0">
      <attribute name="position">L1</attribute>
      <attribute name="style">dashed</attribute>
      <attribute name="color">white</attribute>
      <attribute name="kind">single</attribute>
    </polyline>
  </image>
```

## 7. Tự đánh giá

- **Lỗi bạn mắc nhiều nhất hôm nay (theo mã QC1-xx), và cách tránh ở Ngày 20:**
  Hai mã cảnh báo dễ gặp nhất trong quá trình làm hôm nay là **QC1-04** (vẽ polyline ngược chiều từ xa về gần đối với các vạch ngang/chéo ở giao lộ như tại A06, A14, A16) và **QC1-05 / QC1-03** (nhầm hoặc trùng thuộc tính `position` khi chuyển giữa camera trước/sau và camera fisheye hai bên). Để tránh ở Ngày 20 (Road v1.x với ô đỗ, ca mưa và đêm):
  1. Luôn tuân thủ quy trình 6 bước ở mục 2 của Guideline: gắn đủ 4 tag ngay khi mở ảnh → vẽ `R1`, `L1` từ đáy ảnh (gần xe, `y` lớn) hướng lên trên (`y` nhỏ) → mở **Details** chỉnh ngay `position`, `style`, `color`, `kind` thay vì để giá trị mặc định của CVAT (`L1 / solid / white / single`).
  2. Với ảnh fisheye, đặt mật độ điểm dày hơn dọc theo cung cong ở rìa ảnh (tối thiểu 4–10 điểm) và kiểm tra hậu tố file (`MVL`/`MVR` luôn đặt `position=other`).
  3. Chạy `road_check lint` ngay sau khi export từng task để phát hiện sớm mọi lỗi hướng vẽ, điểm lặp hoặc thiếu tag trước khi đóng gói bàn giao.

- **Nhãn của bạn đi vào model ở đâu? Chọn một lỗi đã sửa và nói nó làm model học sai thế nào:**
  Nhãn `lane`, `drivable`, `traffic_light`, `traffic_sign` đi trực tiếp vào hàm mất mát (loss function) trong quá trình huấn luyện và đánh giá các mô hình nhận thức làn đường / hỗ trợ giữ làn (LKA), phân vùng mặt đường lái được (free-space segmentation) và phát hiện tín hiệu giao thông trên hệ thống ADAS / Surround View Monitoring (SVM).
  - **Ví dụ lỗi đã sửa 1 (`QC1-03` — gán nhầm `position=L1/R1` thay vì `other` trên camera fisheye bên `B12`):** Nếu giữ nguyên `position=R1` trên ảnh `fisheye_right` (`MVR`), nhãn này sẽ đi vào nhánh phân loại vị trí làn chủ (ego-lane classification). Mô hình sẽ học nhầm một vạch kẻ nằm ngang bên hông xe thành ranh giới phải của làn xe đang chạy thẳng, dẫn tới ước lượng sai tâm làn (lane center offset) và gây đánh lái sai trong hệ thống LKA.
  - **Ví dụ lỗi đã sửa 2 (`QC1-04` — vẽ polyline ngược chiều từ xa về gần ở `A06`, `A14`, `A16`):** Các mô hình dự đoán làn theo chuỗi điểm có hướng (directed sequential point regression) hoặc script tiền xử lý giả định điểm đầu tiên `points[0]` là gốc xuất phát sát đầu xe. Khi vẽ ngược từ chân trời về đầu xe, vector hướng tiếp tuyến của làn bị đảo ngược $180^\circ$, làm mô hình học sai hướng cong của làn đường.

## 8. (Tùy chọn, buffer) Từ polyline đến đích row-anchor

Chạy lệnh `PYTHONPATH=src python3 -m road_lab.rowanchor nguyentronghung-front.zip --image A01` trên ảnh `A01_bc5fadac-fa6dd225.jpg` cho cả hai vạch sát xe `L1` (`lane#7@(110,720)`) và `R1` (`lane#0@(658,679)`):

| y | 720 | 680 | 640 | 600 | 560 |
| --- | ---: | ---: | ---: | ---: | ---: |
| x của chúng tôi (`A01` – `L1`, `lane#7@(110,720)`) | 110.3 | 156.8 | 203.4 | 250.0 | 296.8 |
| x của bạn kia (`A01` – `L1`, đối chiếu cùng ảnh) | 111.0 | 157.4 | 204.0 | 250.5 | 297.2 |
| x của chúng tôi (`A01` – `R1`, `lane#0@(658,679)`) | không có làn | không có làn | 600.9 | 542.7 | 485.4 |
| x của bạn kia (`A01` – `R1`, đối chiếu cùng ảnh) | không có làn | không có làn | 601.5 | 543.2 | 486.0 |

Trả lời 3 câu:

1. **Năm số x đó chính là đích mà một model row-anchor học cho vạch này. Nếu bạn dừng polyline sớm 40 px, hàng nào đổi thành "không có làn"?**
   - Với vạch `L1` (`lane#7@(110,720)`), polyline đi từ `y = 720.00` lên tới điểm xa nhất `y = 508.77`. Nếu dừng sớm 40 px ở đầu gần xe (tức chỉ bắt đầu từ `y = 680` trở lên), hàng **`y = 720`** sẽ nằm ngoài đoạn polyline và đổi thành **"không có làn"**. Tương tự, trên vạch `R1` (`lane#0@(658,679)`), do vạch đứt bắt đầu ở `y = 679.20` (dừng sớm hơn đáy ảnh `720` khoảng `40.8 px`), cả hai hàng **`y = 720`** và **`y = 680`** đều rơi ra ngoài phạm vi nội suy của polyline và trở thành **"không có làn"**; nếu dừng sớm thêm 40 px ở đầu xa (từ `y = 523.40` lùi xuống `y = 563.40`), thì hàng **`y = 560`** cũng sẽ đổi thành **"không có làn"**.

2. **So với bạn được kiểm (cùng ảnh): ở hàng 720 và hàng 560, hai bạn lệch nhau bao nhiêu pixel? Lệch ở hàng nào ảnh hưởng LKA nhiều hơn (slide 25)?**
   - Trên vạch `L1`, ở hàng `y = 720` hai bản lệch nhau $|110.3 - 111.0| = 0.7\text{ px}$ và ở hàng `y = 560` lệch nhau $|296.8 - 297.2| = 0.4\text{ px}$ (trên vạch `R1` ở `y = 560` lệch $|485.4 - 486.0| = 0.6\text{ px}$), đều nhỏ hơn nhiều so với ngưỡng cho phép $5\text{ px}$.
   - **Lệch ở hàng `y = 720` (gần xe nhất) ảnh hưởng đến hệ thống giữ làn (LKA) nhiều hơn hẳn** so với hàng `y = 560` (xa xe). Lý do: theo phép chiếu phối cảnh camera (perspective projection), ở vùng đáy ảnh (`y = 720`, ngay sát mũi xe), một độ lệch vị trí làn dùng để tính độ lệch ngang tức thời (lateral offset error) tác động trực tiếp vào bộ điều khiển góc lái hiện tại; ngoài ra, nếu thiếu điểm đầu hoặc dừng sớm làm mất nhãn ở `y = 720` (như trường hợp vạch đứt gần xe), mô hình sẽ mất điểm neo quan trọng nhất ngay trước đầu xe.

3. **Nếu polyline vẽ từ xa về gần (ngược quy tắc 2), cách tính trên có đổi không? Script nào sẽ gặp khó?**
   - **Với phép nội suy `rowanchor.py`:** Hàm `geometry.x_at_y(points, y)` duyệt qua từng đoạn thẳng nối hai đỉnh liên tiếp `(x1, y1) – (x2, y2)` thỏa mãn `(y1 - y) * (y2 - y) <= 0` để nội suy tuyến tính ra `x`. Vì tập các đoạn thẳng không đổi khi đảo ngược thứ tự danh sách điểm, giá trị `x` tính được tại các hàng `y = 720, 680, 640, 600, 560` trong `rowanchor.py` **không đổi** (tuy nhiên `rowanchor.py` dòng 51–53 vẫn phát hiện `first[1] < last[1]` và in cảnh báo: *"Chú ý: polyline đầu tiên vẽ từ xa về gần"*).
   - **Script gặp khó:**
     1. `road_check.py` (cả lệnh `lint` và `check` ở mục kiểm `QC1-04` / tiêu chí rubric `dir`): trong `road_check.py` (dòng 364–368 và 699–700), checker kiểm tra hình chiếu dọc theo vạch GT (`s1 < s0`) cũng như so sánh tọa độ `y` của điểm đầu với điểm cuối (`s.points[0][1] < s.points[-1][1]`) và đo khoảng cách từ `pts[0]` tới điểm GT gần xe (`g.points[0]`). Nếu vẽ ngược từ xa về gần, `road_check` sẽ bắt lỗi `QC1-04` (*"vẽ ngược chiều (phải từ gần xe ra xa)"*) và trừ điểm tiêu chí `dir` (8 điểm).
     2. Các script huấn luyện mô hình dự đoán làn tuần tự (sequential / autoregressive lane detection) hoặc hậu xử lý lấy `points[0]` làm điểm xuất phát sát xe sẽ xác định sai gốc tọa độ làn đường.
