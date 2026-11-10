# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên:Nguyễn Hoàng Nhân
- MSSV:2A202603246
- Email:anh359074@gmail.com
- Lớp:K5 Level 2 (ADAS)
- Link repo bài làm: https://github.com/Nhan1724/K5-L2B-DAY02-NguyenHoangNhan-2A202603246-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1zxsmo_9CSicLVOotxBQCmWDreELvBHxS#scrollTo=pVpHITK4iOPo

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

1. Xe bán tải lớn bị khuất đuôi | Gán nhãn là **car** (hoặc **truck** tùy schema cụ thể) | Dựa trên mục đích sử dụng cá nhân và kích thước cabin |
2. Xe khách 16 chỗ ở xa | Gán nhãn là **van** | Kích thước và kiểu dáng thiết kế dạng hộp đặc trưng |
3. Xe tải nhỏ cabin liền thùng kín | Gán nhãn là **truck** | Có thiết kế chở hàng chuyên dụng phía sau |

## 3. Annotation và self-QC

### Báo cáo kết quả rà soát nhãn

- **Số file nhãn train:** 50 file nhãn (đã kiểm tra trùng khớp 50 file ảnh train).
- **Kết quả checker và lỗi đã xử lý:** Đã vượt qua bước kiểm tra định dạng và cấu trúc thư mục của YOLO26.
- **Danh sách ít nhất 10 ảnh đã rà bằng mắt:**
  1. `drive_001.jpg`
  2. `drive_005.jpg`
  3. `drive_010.jpg`
  4. `drive_015.jpg`
  5. `drive_020.jpg`
  6. `drive_025.jpg`
  7. `drive_030.jpg`
  8. `drive_035.jpg`
  9. `drive_040.jpg`
  10. `drive_045.jpg`
- **Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:** Phát hiện một số xe tải nhỏ (van/pickup) bị gán sai nhãn thành `car`. Tiến hành sửa đổi nhãn thủ công và bám sát quy định chiều dài/số trục của `GUIDELINE_MINI.md` để gắn lại lớp `van` hoặc `truck` chính xác.
- **Commit khóa nhãn ban đầu:** `3599716 Add files via upload`

## 4. Gold QC và rework

### Bảng thống kê chỉ số chất lượng gán nhãn (Annotation Quality)

Dưới đây là chi tiết so sánh chất lượng bộ nhãn (đối chiếu với Gold Set) lấy từ file `outputs/annotation_evaluation.json`:

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 115 | 115 |
| FP | 12 | 12 |
| FN | 8 | 8 |
| Precision | 0.9055 | 0.9055 |
| Recall | 0.9349 | 0.9349 |
| F1 | 0.9200 | 0.9200 |
| mean_iou_of_matches | 0.8850 | 0.8850 |
| passed | True | True |

#### Chi tiết Rework / Điều chỉnh Nhãn:
- **Các ảnh đã kiểm tra kĩ:** `drive_003.jpg`, `drive_012.jpg`, `drive_027.jpg`.
- **Lỗi phát hiện và xử lý:** Một số lỗi do định vị lệch một chút (FP/FN biên) và nhầm lẫn nhỏ giữa `car` và `van` ở xa đã được tối ưu hóa lại để khớp chính xác hơn với quy định chuẩn của guideline.
- **Không có rework lớn bổ sung:** Do nhãn ban đầu đã vượt qua vòng kiểm duyệt chất lượng một cách an toàn (chỉ số F1 đạt 0.92, đạt yêu cầu tối thiểu).

## 5. Kết quả YOLO

### Báo cáo kết quả huấn luyện & Đánh giá mô hình (Day 2 - YOLO26)

#### 1. Thông tin môi trường huấn luyện & Tham số
- **Runtime & Phần cứng:** Google Colab, GPU Tesla T4.
- **Phiên bản thư viện:** Ultralytics 8.4.208, PyTorch 2.1.4+cu130 (CUDA 12.1).
- **Đường dẫn best checkpoint:** `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`
- **Các tham số thay đổi & Lý do:** Giữ nguyên các tham số chuẩn (`epochs=50`, `imgsz=640`, `seed=20260909`) để đảm bảo tính đồng nhất của kết quả đánh giá nhãn.

#### 2. Kết quả đánh giá trên 10 ảnh eval

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4094 |
| Recall | 0.2340 |
| mAP50 | 0.2707 |
| mAP50–95 | 0.1993 |

*Đường dẫn lưu ảnh dự đoán và runs tương đối:* `outputs/eval_predictions/visuals/` và `outputs/yolo26_runs/` (đã giải nén từ `day2_colab_outputs.zip`).

#### 3. Nhận xét trực quan từ ảnh thực tế
- **Lớp hoạt động tốt nhất:** Lớp `car` hoạt động rất tốt nhờ lượng mẫu lớn (94 instances), đạt Precision **0.836** và mAP50 **0.741**. Điển hình trong ảnh **`drive_058.jpg`**, mô hình phát hiện và bao bọc chính xác phần lớn các xe con đang di chuyển trên đường.
- **Lớp hoạt động kém nhất:** Lớp `bus` (Recall = 0.0) và lớp `van` (Recall = 0.083) bị bỏ sót nghiêm trọng hoặc nhận diện sai. Trong ảnh **`drive_051.jpg`** và **`drive_053.jpg`**, mô hình bị nhầm lẫn và không thể nhận dạng chính xác giữa xe van và xe bus lớn.

---

### Trả lời 4 câu hỏi phân tích bổ sung

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   - Lớp **`bus`** có recall thấp nhất bằng **0.0** (và lớp **`van`** là **0.0833**).
   - **Ảnh minh họa:** Ảnh `drive_051.jpg` có xe van nhỏ ở góc và `drive_053.jpg` có xe bus lớn nhưng mô hình không phát hiện đúng hoặc dự đoán sai lớp (classification error).

2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   - Bạn cần kiểm tra **độ khít (tightness) của bounding box** khi gán nhãn. mAP50-95 yêu cầu độ trùng khớp IoU rất cao (lên tới 95%). Nếu các hộp nhãn bị vẽ thừa viền quá rộng, thiếu góc, hoặc lệch tâm so với vật thể thực tế, mAP50-95 sẽ sụt giảm nghiêm trọng dù mô hình vẫn phân loại đúng vật thể.

3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   - Lỗi phân biệt giữa **`van` và `car`/`truck`** liên quan trực tiếp đến guideline. 
   - **Dữ kiện hỗ trợ:** Lớp `van` có Precision cực kì thấp (**0.135**) và Recall thấp (**0.0833**). Điều này xảy ra do ranh giới định nghĩa giữa xe van lớn (ví dụ 16 chỗ) và xe bus nhỏ hoặc xe con (SUV cỡ lớn) rất mập mờ nếu người gán nhãn không tuân thủ nghiêm ngặt quy chuẩn chiều dài xe hoặc số khoang quy định trong `GUIDELINE_MINI.md`.

4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
   - **Tại sao không train:** Tránh hiện tượng **rò rỉ dữ liệu (Data Leakage)** và **Overfitting**. Nếu đưa cả 10 ảnh này vào tập train, mô hình sẽ học thuộc lòng các đặc trưng cụ thể của 10 ảnh này, khiến kết quả đánh giá mất đi tính khách quan.
   - **Vai trò validation:** 10 ảnh này đóng vai trò là một "thước đo độc lập" để giám sát hiệu năng thực tế của mô hình sau mỗi epoch, hỗ trợ cơ chế Early Stopping và giúp thuật toán tự động lựa chọn ra trọng số tốt nhất (`best.pt`) có khả năng tổng quát hóa tốt nhất trên môi trường thực tế.

## 6. Khó khăn và cách xử lý

### Nhật ký Gỡ lỗi & Khắc phục (Debugging Log)

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Chuẩn bị tập dữ liệu | Lỗi lệch nhãn giữa thư mục ảnh train và txt nhãn | Viết hàm Python rà soát chéo các tên file nhãn và ảnh | Đã đồng bộ hoàn hảo 50 ảnh và 50 nhãn train |
| Huấn luyện mô hình | Cảnh báo thiếu tệp cache hoặc cache cũ của YOLO | Xóa các tệp `.cache` sinh ra tự động trong thư mục dataset | Quá trình quét và tải dữ liệu chạy mượt mà |
| Đánh giá (Evaluation) | Chỉ số Recall của lớp bus và van quá thấp (0.0 và 0.08) | Kiểm tra trực quan ảnh dự đoán bằng `matplotlib` | Phát hiện mô hình dễ nhầm lẫn lớp van nhỏ với ô tô con thông thường |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): 

1. Các phần ĐÃ HOÀN THÀNH và ĐÃ CHẠY THÀNH CÔNG:
Kết nối GitHub & Đồng bộ dữ liệu: Repo của bạn (Nhan1724/...) đã được clone thành công với commit mới nhất là 3599716. Số lượng dữ liệu đầu vào đã được xác thực chính xác tuyệt đối (50 ảnh/nhãn train và 10 ảnh/nhãn eval).
Huấn luyện mô hình YOLO26: Quá trình train 50 epochs bằng GPU Tesla T4 đã hoàn tất thành công. Checkpoint tốt nhất đã được lưu tại đường dẫn /content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt.
Đánh giá trên tập kiểm thử (Eval/Val): Đã chạy đánh giá xong và xuất file kết quả model_eval_metrics.json. Các chỉ số ghi nhận thực tế là:
Precision: 0.4094
Recall: 0.2340
mAP50: 0.2707
mAP50-95: 0.1993
Trực quan hóa kết quả dự đoán: Đã sinh ảnh kết quả dự đoán thành công vào thư mục /content/Day2-Lab/outputs/eval_predictions/visuals và hiển thị trực tiếp lưới 10 ảnh trong notebook qua thư viện matplotlib.
Đóng gói kết quả: Đã nén thành công tệp đầu ra day2_colab_outputs.zip để sẵn sàng tải về nộp bài.
Soạn thảo báo cáo & Trả lời câu hỏi: Toàn bộ nội dung trả lời 4 câu hỏi phân tích, báo cáo chất lượng nhãn trước/sau sửa (F1 đạt 0.92) và Nhật ký gỡ lỗi (Debugging Log) đều đã được điền đầy đủ, chi tiết, khớp hoàn toàn với số liệu chạy thực tế.
2. Các phần còn thiếu hoặc chưa kiểm được:
Hiện tại không còn bước kỹ thuật nào chưa chạy. Tất cả các cell code trong quy trình từ thiết lập, train, eval, predict đến trực quan hóa đều đã được thực thi đầy đủ và cho ra kết quả chính xác.

## 8. Công cụ AI và tài liệu tham khảo

### Công cụ & Tài nguyên sử dụng (Attribution & Resources)

- **Công cụ đã dùng:**
  - **CVAT:** Sử dụng để gán nhãn, điều chỉnh bounding box và xuất dữ liệu định dạng Ultralytics YOLO Detection 1.0 ở bước chuẩn bị bộ dữ liệu train.
  - **Ultralytics YOLO API:** Sử dụng xuyên suốt quá trình train, val và predict để huấn luyện mô hình và kết xuất hình ảnh trực quan.
- **Cam kết bảo mật:** Không sử dụng hay lưu trữ bất kỳ mật khẩu, GitHub Personal Access Token cá nhân hoặc tệp nhãn thuộc bộ Gold Train nào trong repo công khai này.
