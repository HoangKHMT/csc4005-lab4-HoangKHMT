# CSC4005 Lab 4 Report – CRNN for UrbanSound8K

## 1. Thông tin sinh viên

- Họ tên: Nguyễn Đức Hoàng
- Mã sinh viên: 1771040015
- Lớp:KHMT 17-01
- Link GitHub repo:
- Link W&B project:https://wandb.ai/hoangnguyen2200568-dainam-vietnam/csc4005-lab4-urbansound8k-crnn/runs/4o28bwve?nw=nwuserhoangnguyen2200568

## 2. Mục tiêu thí nghiệm

Thí nghiệm này tập trung vào việc áp dụng kiến trúc CRNN (Convolutional Recurrent Neural Network) để phân loại âm thanh trên tập dữ liệu UrbanSound8K. Việc sử dụng log-mel spectrogram giúp biểu diễn âm thanh dưới dạng ảnh tần số, phù hợp để CNN trích xuất đặc trưng cục bộ. So với 1D-CNN ở Lab 3, sự kết hợp với lớp RNN giúp mô hình học được tính tuần tự và các phụ thuộc thời gian (temporal dependencies) trong âm thanh. Mục tiêu cuối cùng là đánh giá hiệu năng và so sánh sự ảnh hưởng của các cấu trúc RNN khác nhau (GRU vs BiLSTM) đến độ chính xác.

## 3. Cấu hình dữ liệu

| Thành phần | Giá trị |
|---|---|
| Dataset | UrbanSound8K |
| Số lớp | 10 |
| Train folds | 1–8 |
| Validation fold | 9 |
| Test fold | 10 |
| Feature | log-mel spectrogram |
| Sampling rate | 16 kHz |
| Duration | 4 giây |

## 4. Cấu hình mô hình

| Thành phần | Giá trị |
|---|---|
| Model | CRNN |
| CNN blocks | 3-4 blocks |
| RNN type | GRU / LSTM |
| Hidden size | 64 | 
| Dropout | 0.3 / 0.35 |
| Optimizer | AdamW |
| Learning rate |0.001 / 0.007 |
| Batch size |32|
| Epochs |25 |

## 5. Kết quả huấn luyện

Điền kết quả tốt nhất từ W&B hoặc `metrics.json`.

| Run | best_val_acc | test_acc | Ghi chú |
|---|---:|---:|---|
| logmel_crnn_gru_baseline | 0.7377|0.7599 |Hội tụ tốt, hiệu năng ổn định |
| extension_bilstm_crnn |0.6605| 0.7133|Độ phức tạp cao hơn, overfitting nhẹ |

## 6. Learning curves

D:\HocSau\csc4005-lab4-HoangKHMT-main\outputs\logmel_crnn_bilstm_extension\curves.png

Nhận xét:

 - Baseline (GRU): val_loss giảm khá ổn định song hành cùng train_loss, cho thấy mô hình học tốt và ít overfitting.

 - xtension (BiLSTM): train_loss giảm sâu nhưng val_loss chững lại và bắt đầu tăng nhẹ ở cuối quá trình, cho thấy mô hình BiLSTM với cấu trúc phức tạp hơn dễ bị overfitting trên tập dữ liệu UrbanSound8K hơn so với GRU.

## 7. Confusion matrix

![Chèn hình `confusion_matrix.png`.](outputs/logmel_crnn_gru_baseline/confusion_matrix.png)

Nhận xét:

- Các âm thanh có đặc điểm riêng biệt như dog_bark hay gun_shot được mô hình phân loại với độ chính xác cao.

- Sự nhầm lẫn lớn nhất nằm ở cặp air_conditioner và engine_idling. Nguyên nhân là do cả hai loại âm thanh này đều chứa tần số nền ổn định, kéo dài, khiến mô hình khó phân biệt nếu chỉ dựa vào đặc trưng spectrogram trong 4 giây.

## 8. So sánh với Lab 3 1D-CNN

| Tiêu chí | Lab 3: 1D-CNN | Lab 4: CRNN |
|---|---|---|
| Feature chính | MFCC / log-mel | log-mel |
| Khả năng học pattern cục bộ | Có | Có |
| Khả năng học quan hệ thời gian | Hạn chế | Tốt hơn |
| Test accuracy |0.5333  |~76% |
| Nhận xét |Dùng 1D-CNN bị hạn chế trong việc nắm bắt diễn biến âm thanh theo thời gian dài. |CRNN ưu việt hơn nhờ sự kết hợp giữa trích xuất đặc trưng hình ảnh và tuần tự. |

## 9. Kết luận

CRNN đã chứng minh được sự cải thiện đáng kể so với 1D-CNN nhờ khả năng nắm bắt được ngữ cảnh thời gian của âm thanh. Tuy nhiên, kết quả cho thấy việc sử dụng BiLSTM không tự động mang lại kết quả tốt hơn mà đòi hỏi phải tinh chỉnh kỹ hơn về dropout và learning rate để kiểm soát overfitting. Nếu có cơ hội phát triển tiếp, em sẽ (1) áp dụng thêm các kỹ thuật Data Augmentation như thêm nhiễu nền hoặc thay đổi cao độ để mô hình bền bỉ hơn, và (2) sử dụng Early Stopping để tự động dừng training đúng thời điểm val_loss đạt cực tiểu.
## 10. Link minh chứng

- GitHub commit cuối:
- W&B run baseline: https://wandb.ai/hoangnguyen2200568-dainam-vietnam/csc4005-lab4-urbansound8k-crnn/runs/4o28bwve?nw=nwuserhoangnguyen2200568
- W&B run mở rộng:
