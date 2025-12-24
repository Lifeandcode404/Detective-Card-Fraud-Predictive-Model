# Credit Card Fraud Detection System / Hệ Thống Phát Hiện Gian Lận Thẻ Tín Dụng

##  Project Overview / Tổng Quan Dự Án
**English:**
This project focuses on building robust predictive models to detect fraudulent credit card transactions. Dealing with highly imbalanced data (fraud cases are extremely rare), the project implements advanced techniques like **Resampling (UNDER_SAMPLING, SMOTE, SMOTE+UNDER_SAMPLING, SMOTETOMEK)**, **Model (LOGISTIC REGRESSION, RANDOM FOREST, XGBOOST)**, **Threshold Optimization**.

**Tiếng Việt:**
Dự án này tập trung xây dựng các mô hình dự đoán có độ tin cậy cao nhằm phát hiện các giao dịch thẻ tín dụng gian lận. Do dữ liệu có mức độ mất cân bằng rất lớn (các giao dịch gian lận chiếm tỷ lệ cực nhỏ), dự án áp dụng các kỹ thuật nâng cao như **Resampling (UNDER_SAMPLING, SMOTE, SMOTE kết hợp UNDER_SAMPLING, SMOTETOMEK), xây dựng mô hình (LOGISTIC REGRESSION, RANDOM FOREST, XGBOOST) và tối ưu ngưỡng phân loại (Threshold Optimization)**.

---

##  Tech-Using / Công Nghệ Sử Dụng
* **Language:** Python
* **Libraries:** Scikit-learn, XGBoost, Pandas, NumPy, Matplotlib, Seaborn, Imbalanced-learn.
* **Environment:** Jupyter Notebook / Google Colab.

---

##  Key Features & Methodology / Các Tính Năng & Phương Pháp Chính
The project follows a rigorous Data Science workflow / Dự án tuân theo quy trình Khoa học Dữ liệu chặt chẽ:

1.  **Data Preprocessing (Tiền xử lý):** Scaling (StandardScaler) and handling imbalanced datasets / Chuẩn hóa dữ liệu và xử lý mất cân bằng.
2.  **Resampling Imbalanced Data (Xử lý dữ liệu mất cân bằng):** Implemented 4 methods to handle the class imbalance problem effectively / Sử dụng 4 phương thức để giải quyết vấn đề chênh lệch dữ liệu.
3.  **Model Training (Huấn luyện mô hình):**
    * Logistic Regression (Baseline)
    * Random Forest Classifier
    * XGBoost Classifier
4.  **Hyperparameter Tuning (Tối ưu tham số):** Used `GridSearchCV` to find optimal parameters for each model / Sử dụng GridSearch để tìm bộ tham số tốt nhất.
5.  **Threshold Optimization (Tối ưu ngưỡng):** Instead of the default 0.5 threshold, I utilized **Precision-Recall Curves** to find the optimal decision threshold (maximizing F1-Score) / Thay vì dùng ngưỡng 0.5 mặc định, tôi dùng biểu đồ Precision-Recall để tìm ngưỡng quyết định tối ưu nhất.
---

##  Model Performance & Results / Hiệu Quả Mô Hình & Kết Quả

After extensive testing and optimization, the **Random Forest** performed best.
(Sau quá trình kiểm thử và tối ưu, Random Forest cho kết quả tốt nhất.) 

| Model | Technique | Optimal Threshold | Recall (Fraud) | False Positives |
|-------|-----------|-------------------|----------------|-----------------|
| **Random Forest** | SMOTE | **0.43** | ~87% | Low (Minimizes customer disturbance) |
| **XGBoost** | SMOTE | **0.23** | ~87% | Moderate |
| **Logistic Reg** | SMOTE | 0.96 | ~85% | Higher |

###  Key Insight / Điểm Nhấn:
**English:**
By moving the decision threshold from the default **0.5** to **0.43**, the Random Forest model significantly reduced False Negatives (missed fraud) without causing a spike in False Positives (locking valid cards).

**Tiếng Việt:**
Bằng cách dịch chuyển ngưỡng quyết định từ mức mặc định **0.5** xuống **0.43**, mô hình Random Forest đã giảm đáng kể số vụ gian lận bị bỏ sót (False Negatives) mà không làm gia tăng đột biến số lượng báo động giả (gây khóa nhầm thẻ của khách).

---

## Visualizations / Biểu Đồ Minh Họa
*(Comparison between Default Threshold vs. Optimized Threshold)*
*(So sánh giữa Ngưỡng mặc định và Ngưỡng tối ưu)*

<img width="1151" height="441" alt="image" src="https://github.com/user-attachments/assets/764f8b70-393c-4e18-bc7b-d320bd1b3f1a" />
<img width="1116" height="436" alt="image" src="https://github.com/user-attachments/assets/9bd5a957-7954-488f-b12f-45b1856d2b92" />
<img width="1149" height="432" alt="image" src="https://github.com/user-attachments/assets/ea723066-7652-4b48-973c-d3315e98dbeb" />

---
