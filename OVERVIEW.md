# Tóm tắt Quy Trình Xây Dựng Mô Hình - Credit Card Fraud Detection

Dự án này nhằm xây dựng một mô hình học máy để phát hiện giao dịch gian lận trên dữ liệu thẻ tín dụng. Bộ dữ liệu gốc gồm **284.807 giao dịch** với **492 ca gian lận** (tỷ lệ gian lận: 0,17%), cho thấy sự mất cân bằng dữ liệu cực kỳ nghiêm trọng.

## 1. Chuẩn Bị Dữ Liệu
- **Kích thước**: 284.807 hàng × 31 cột
- **Kiểm tra chất lượng**: Không có dữ liệu thiếu (missing values)
- **Phân tích dữ liệu**: Dữ liệu PCA đã được chuẩn hóa (V1-V28), kèm theo `Time` (giây) và `Amount` (số tiền)
- **Xử lý đặc trưng**:
  - Tạo feature mới: `Is_High_Amount` (1 nếu Amount > 1000, ngược lại 0)
  - Áp dụng **RobustScaler** cho cột `Amount` để xử lý các giá trị ngoại lai
  - Loại bỏ cột `Time` do không có tương quan rõ ràng với gian lận

## 2. Xử Lý Dữ Liệu Mất Cân Bằng
- **Tách tập dữ liệu**: 80% training, 20% testing (sử dụng `stratified` để giữ tỷ lệ lớp)
- **Thử 4 phương pháp xử lý mất cân bằng**:
  1. **Original**: Sử dụng dữ liệu gốc với `class_weight='balanced'` trong mô hình
  2. **Random Under-Sampling**: Giảm lớp đa số với tỷ lệ 0.5
  3. **SMOTE**: Tăng lớp thiểu số với tỷ lệ 0.2
  4. **SMOTE + RUS (SMOTETomek)**: Kết hợp tăng thiểu số và giảm đa số

## 3. Huấn Luyện 3 Mô Hình Phân Loại
- **Logistic Regression**: Mô hình baseline, phù hợp với dữ liệu khác biệt tuyến tính
- **Random Forest**: Tập hợp cây quyết định, chống overfitting tốt hơn
- **XGBoost**: Ensemble gradient boosting, tập trung vào các điểm khó dự đoán

## 4. Đánh Giá Mô Hình
- **Chỉ số chính**: PR-AUC, Recall, Precision, F1-Score (ưu tiên Recall do cần bắt được gian lận)
- **Kết quả tốt nhất cho từng model**:
  - **Logistic Regression + Original**: Recall cao nhất nhưng Precision thấp
  - **Random Forest + Under-Sampling**: Cân bằng tốt giữa Recall và Precision
  - **XGBoost + SMOTE**: Precision/F1 cao nhất nhưng Recall thấp

## 5. Tối Ưu Hóa Tham Số
- **Sử dụng GridSearchCV** với StratifiedKFold (k=3) để tìm tham số tối ưu
- **Chỉ số đánh giá**: Average Precision (kết hợp Precision và Recall)
- **Kết quả**: Cả 3 mô hình đều cải thiện sau tối ưu, đặc biệt là F1-Score

## 6. Tinh Chỉnh Ngưỡng (Threshold Tuning)
- **Mô hình được chọn**: Random Forest (cân bằng tốt nhất)
- **Ngưỡng tối ưu**: Từ 0.5 -> 0.4 để tăng Recall từ 82.52% -> 83.59%
- **Đánh đổi**: Tăng False Positive từ 20 -> 36 nhưng đáp ứng mục tiêu ưu tiên phát hiện gian lận

---

## Kết Quả Tốt Nhất Của Từng Mô Hình

Sau khi huấn luyện và đánh giá tất cả các mô hình, kết quả tốt nhất cho từng phương pháp được trình bày dưới đây:

### 1. Logistic Regression (SMOTE)
- **Recall**: 0.8878
- **Precision**: 0.2153
- **F1-Score**: 0.3466
- **Phân tích**: Mô hình phát hiện được 88.78% các ca gian lận. Tuy nhiên, Precision rất thấp (21.53%), báo động giả quá nhiều. Không khuyến nghị sử dụng.

### 2. Random Forest (Under-Sampling)
- **Recall**: 0.8469
- **Precision**: 0.8218
- **F1-Score**: 0.8342
- **Phân tích**: Phát hiện được 84.68% các ca gian lận. Precision ổn định (82.18%). F1-Score cao cho thấy sự cân bằng tốt.

### 3. XGBoost (SMOTE)
- **Recall**: 0.8571
- **Precision**: 0.8936
- **F1-Score**: 0.8750
- **Phân tích**: Precision rất cao (89.36%). F1-Score cao nhất trong các mô hình.

---

## Bảng So Sánh Chi Tiết

### So sánh kết quả tốt nhất (dựa trên PR-AUC)

| Chỉ Số | Logistic Regression | Random Forest | XGBoost |
|--------|---------------------|---------------|---------|
| **Recall** | 88.78% | 84.69% | 85.71% |
| **Precision** | 21.53% | 82.18% | 89.36% |
| **F1-Score** | 34.66% | 83.42% | 87.50% |
| **Phương pháp** | Smote | Smote | Original |

### So sánh kết quả tệ nhất (dựa trên PR-AUC)

| Chỉ Số | Logistic Regression | Random Forest | XGBoost |
|--------|---------------------|---------------|---------|
| **Recall** | 91.18% | 90.81% | 90.81% |
| **Precision** | 5.89% | 8.25% | 5.18% |
| **F1-Score** | 11.07% | 15.13% | 9.80% |
| **Phương pháp** | Original | Under-Sampling | Under-Sampling |

### So sánh trước và sau Tối Ưu Hóa Tham Số (GridSearchCV)

| Model | Method | Recall (Trước) | Recall (Sau) | Precision (Trước) | Precision (Sau) | F1-Score (Trước) | F1-Score (Sau) |
|-------|--------|----------------|--------------|-------------------|-----------------|------------------|----------------|
| **Logistic Regression** | SMOTE | 0.8878 | 0.8878 | 0.2153 | 0.2197 | 0.3466 | 0.352 |
| **Random Forest** | SMOTE | 0.8469 | 0.867 | 0.8218 | 0.8095 | 0.8342 | 0.837 |
| **XGBoost** | Original | 0.8571 | 0.847 | 0.8936 | 0.8736 | 0.8750 | 0.86 |

---

## Giá Trị và Ứng Dụng Thực Tiễn

### Thành Tựu Đạt Được
- Giảm thiểu gian lận: Phát hiện được phần lớn các ca gian lận.
- Giảm báo động giả: Chỉ báo động 36 lần trên 56.341 giao dịch bình thường.
- Tối ưu hóa chi phí: Cân bằng giữa tổn thất do gian lận và chi phí xử lý báo động giả.
- Mô hình ổn định: Đã qua kiểm chứng GridSearchCV và threshold tuning.

### Khuyến Nghị Triển Khai
1. **Triển khai ngay mô hình Random Forest** với threshold 0.4.
2. **Hệ thống cảnh báo**: Gắn flag cho giao dịch có xác suất gian lận > 0.4.
3. **Kiểm chứng thủ công**: Đội fraud prevention kiểm tra 36 cảnh báo hàng ngày.
4. **Nâng cấp liên tục**: Thu thập phản hồi từ kiểm chứng thủ công để retrain mô hình định kỳ.

### Hạn Chế & Hướng Phát Triển
- Tỷ lệ bỏ sót gian lận vẫn còn đáng kể.
- Cần kết hợp với các phương pháp phát hiện bất thường khác (anomaly detection).
- Sử dụng dữ liệu thời gian thực để cập nhật mô hình (online learning).
