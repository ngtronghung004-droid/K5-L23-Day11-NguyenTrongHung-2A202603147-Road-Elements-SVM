# Phiếu QC 1 — Road v1.0

Lô được kiểm (học viên): Nguyễn Trọng Hùng (2A202603147)        Người kiểm (học viên): Nguyễn Trọng Hùng (2A202603147)  
Ngày: 2026-11-21                      Phiên bản guideline: Road v1.0  
File được kiểm (tên + `revision` trong manifest): `nguyentronghung-front.zip` (A13–A16), `nguyentronghung-fisheye.zip` (B09–B12) — `revision 1`  
Số ảnh kiểm: 8 ảnh (4 Front: A13–A16; 4 Fisheye: B09–B12)   Số nhãn đã kiểm (shape + tag): 115 (83 shape + 32 tag)

## Thống kê nhãn trên từng ảnh đã kiểm

| Ảnh | Camera | Số shape | Số tag | Chi tiết shape theo label | Kết quả đối chiếu ảnh |
| --- | --- | ---: | ---: | --- | --- |
| A13 | `front` | 8 | 4 | `drivable` 3, `lane` 3, `road_curb` 1, `traffic_sign` 1 | Không lỗi |
| A14 | `front` | 15 | 4 | `crosswalk` 3, `drivable` 2, `lane` 3, `road_curb` 1, `traffic_light` 5, `traffic_sign` 1 | Thiếu `lane` `R1` (QC1-02, nặng) |
| A15 | `front` | 20 | 4 | `drivable` 3, `lane` 5, `traffic_sign` 12 | Không lỗi |
| A16 | `front` | 16 | 4 | `crosswalk` 3, `drivable` 3, `lane` 6, `traffic_light` 2, `traffic_sign` 2 | Không lỗi |
| B09 | `fisheye_front` | 7 | 4 | `crosswalk` 2, `drivable` 1, `lane` 3, `road_curb` 1 | Không lỗi |
| B10 | `fisheye_left` | 4 | 4 | `drivable` 3, `road_curb` 1 | Không lỗi |
| B11 | `fisheye_rear` | 7 | 4 | `drivable` 1, `lane` 6 | Không lỗi |
| B12 | `fisheye_right` | 6 | 4 | `crosswalk` 3, `drivable` 1, `lane` 1, `road_curb` 1 | Không lỗi |
| **Tổng** | | **83** | **32** | **115 nhãn (83 shape + 32 tag)** | **7 ảnh không lỗi, 1 ảnh có lỗi nặng** |

## Tổng hợp theo mã
| Mã | Loại lỗi | Số mục kiểm | Số lỗi | Mức |
| --- | --- | ---: | ---: | --- |
| QC1-01 tag camera | sai thuộc tính / bỏ sót | 32 | 0 | nặng |
| QC1-02 đủ L1, R1 | bỏ sót | 8 | 1 | nặng |
| QC1-03 position | sai thuộc tính / thừa | 27 | 0 | nặng |
| QC1-04 tâm vạch, chiều, điểm dừng | sai phạm vi / bỏ sót | 27 | 0 | chính |
| QC1-05 vạch đứt, che | sai phạm vi / sai thuộc tính | 27 | 0 | chính |
| QC1-06 style, color, kind | sai thuộc tính | 27 | 0 | chính |
| QC1-07 vùng lái | sai phạm vi / bỏ sót / thừa | 33 | 0 | chính |
| QC1-08 đèn, màu | bỏ sót / sai phạm vi / sai thuộc tính | 7 | 0 | nặng / chính |
| QC1-09 facing, type, group, biển phụ | sai thuộc tính | 23 | 0 | chính |
| QC1-10 hình in, đủ biển | bỏ sót / sai phạm vi | 16 | 0 | chính |
| QC1-11 fisheye | sai phạm vi / sai thuộc tính | 24 | 0 | chính |
| QC1-12 file | lỗi file | 115 | 0 | nhẹ / nặng |

## Danh sách lỗi (mỗi lỗi một dòng)
| # | Mã | Ảnh | Nhãn (label, id) | Mức | Mô tả ngắn |
| --- | --- | --- | --- | --- | --- |
| 1 | QC1-02 | A14 | `lane` (`position=R1` bị thiếu) | nặng | Thiếu polyline `lane` `R1`: đối chiếu ảnh gốc A14 thấy vạch sơn trắng bên phải làn xe chạy từ khoảng (989, 455) tới mép phải (1280, 555), nhưng trong XML chỉ có 1 `lane` `L1`, 2 `lane` `other` và 1 `road_curb`. |

## Ca mơ hồ
| Ảnh | Câu hỏi cho lead | Lời đáp (nếu có) |
| --- | --- | --- |
| A14 | Vạch trắng chéo bên phải từ (989, 455) đến (1280, 555) tại giao lộ ban đêm trên A14 có bắt buộc gán `lane` `position=R1` hay được xem là `road_curb` / vạch dẫn hướng giao lộ? | Chưa có phản hồi từ lead; theo quy tắc bảo toàn chất lượng của phiếu, xếp mức nặng (QC1-02) và đề xuất Rework. |

## Quyết định
Lỗi nặng: 1   Lỗi chính: 0 / 115 nhãn = 0,0 %   Lỗi nhẹ: 0   Ca chờ lead: 0  
Quyết định (Release / Hold / Rework): **Rework**  
Lý do (theo luật của phiếu): Có ít nhất một lỗi nặng (`Lỗi nặng = 1 ≥ 1`, mã `QC1-02` thiếu vạch `R1` tại ảnh `A14` sau khi đối chiếu ảnh gốc) → bắt buộc **Rework** theo mục 3 của [thang mức lỗi](docs/qc_severity_road_v1.0.md). Các ảnh `A13`, `A15`, `A16`, `B09`, `B10`, `B11`, `B12` đã kiểm tra đủ 4 tag, đúng camera (`front`, `fisheye_front`, `fisheye_left`, `fisheye_rear`, `fisheye_right`), `position=other` ở camera hai bên (`B10`, `B12`) và không phát hiện lỗi.

Loại lỗi: bỏ sót = thiếu nhãn phải có; thừa = gán cái không phải đối tượng; sai phạm vi = hình lệch, sai chiều, dừng sai chỗ, tràn vùng. Ba loại này theo cách chia lỗi gán nhãn của ISO/IEC 5259-4 (mục 8.4.2); "sai thuộc tính" và "lỗi file" là khóa học thêm. Cách xếp mức: [thang mức lỗi](docs/qc_severity_road_v1.0.md).
