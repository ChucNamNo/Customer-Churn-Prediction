# Mô hình Dự đoán Khách hàng Rời bỏ - IBM Telco Customer Churn Prediction

[cite_start]Dự án phân tích hành vi khách hàng và xây dựng mô hình học máy (Machine Learning) nhằm dự đoán chính xác nguy cơ rời bỏ dịch vụ (Customer Churn) tại một doanh nghiệp viễn thông[cite: 21, 22]. [cite_start]Từ đó, đề xuất các giải pháp tối ưu hóa chiến lược giữ chân khách hàng (Customer Retention) và giảm thiểu thiệt hại doanh thu[cite: 15].

---

## 1. Bối Cảnh & Mục Tiêu Dự Án

### Bối cảnh doanh nghiệp
* [cite_start]Trong ngành viễn thông, việc giữ chân khách hàng hiện tại luôn có chi phí thấp hơn rất nhiều so với việc tìm kiếm khách hàng mới[cite: 4, 5].
* [cite_start]Hiện tượng khách hàng hủy dịch vụ, chuyển sang đối thủ cạnh tranh hoặc ngừng gia hạn hợp đồng đang ảnh hưởng trực tiếp đến doanh thu[cite: 6, 7, 8, 9, 10].

### Nhiệm vụ cốt lõi
* [cite_start]Phân tích khám phá dữ liệu (EDA) để tìm ra các xu hướng hành vi của nhóm khách hàng rời đi[cite: 16, 17].
* [cite_start]Sử dụng thuật toán học máy để phát hiện sớm các khách hàng có nguy cơ rời bỏ dịch vụ cao[cite: 13, 18].
* [cite_start]Đưa ra các khuyến nghị thực tế giúp doanh nghiệp tối ưu chiến lược giữ chân khách hàng[cite: 15].

---

## 2. Thông Tin Tập Dữ Liệu (Dataset)

[cite_start]Dự án sử dụng bộ dữ liệu IBM Telco Customer Churn Dataset gồm 7,043 dòng và 21 cột[cite: 21, 101]. [cite_start]Mỗi dòng đại diện cho thông tin của một khách hàng duy nhất[cite: 22].

### Các nhóm thông tin chính:
* [cite_start]Thông tin cá nhân: Giới tính, trạng thái người cao tuổi, tình trạng hôn nhân, người phụ thuộc[cite: 24, 26, 27, 28, 29].
* [cite_start]Thông tin dịch vụ đăng ký: Dịch vụ thoại, nhiều đường dây, dịch vụ Internet và các dịch vụ gia tăng (Bảo mật, Sao lưu, Bảo hiểm thiết bị, Hỗ trợ kỹ thuật, Truyền hình/Phim trực tuyến)[cite: 30, 31, 32, 34, 35, 36, 38, 39, 40, 41].
* [cite_start]Thông tin tài khoản & Cước phí: Số tháng gắn bó (tenure), Loại hợp đồng, Hình thức hóa đơn, Phương thức thanh toán, Cước phí hằng tháng và Tổng cước tích lũy[cite: 42, 43, 44, 45, 46, 47, 48].
* [cite_start]Biến mục tiêu (Target): Churn (Yes: Đã rời bỏ dịch vụ / No: Vẫn đang sử dụng)[cite: 49, 50, 51, 52].

[cite_start]Lưu ý: Biến mục tiêu bị mất cân bằng (73.5% No vs 26.5% Yes), do đó dự án tập trung vào các chỉ số F1-Score và Recall thay vì chỉ dựa vào Accuracy[cite: 301, 303, 304].

---

## 3. Quy Trình Thực Hiện (Pipeline)

[cite_start]Dự án được đóng gói qua một Pipeline tiền xử lý và huấn luyện hoàn chỉnh để tránh hiện tượng rò rỉ dữ liệu (Data Leakage)[cite: 61, 464, 466]:

1. [cite_start]Tiền xử lý dữ liệu: Ép kiểu dữ liệu `TotalCharges` về dạng số [cite: 182][cite_start], xử lý giá trị khuyết thiếu bằng phương pháp điền giá trị trung vị (Median Imputer) [cite: 182][cite_start], chuẩn hóa biến số lượng với `StandardScaler` và mã hóa biến phân loại bằng `OneHotEncoder`[cite: 62, 448, 450].
2. [cite_start]Kỹ nghệ tính năng (Feature Engineering): Tạo tính năng tổng hợp `Total_Addon_Services` (đếm số dịch vụ gia tăng dạng 'Yes') [cite: 306][cite_start], phân nhóm khách hàng theo thời gian gắn bó `tenure_group` (New, Mid-term, Loyal Customer) [cite: 306] [cite_start]và loại bỏ các biến thành phần để giảm bớt số chiều dữ liệu[cite: 306].
3. [cite_start]Lựa chọn mô hình: Thử nghiệm nhanh các mô hình phân loại thông qua thư viện `LazyClassifier` [cite: 66, 470][cite_start], lựa chọn Logistic Regression làm mô hình chính nhờ hiệu suất tốt và tính tường minh cao[cite: 473, 478].
4. [cite_start]Tối ưu hóa siêu tham số: Sử dụng `GridSearchCV` với 5-fold cross-validation để tìm ra bộ tham số tối ưu cho mô hình[cite: 67, 490].

---

## 4. Kết Quả Mô Hình (Model Performance)

[cite_start]Bộ tham số tốt nhất được tìm thấy qua GridSearch là: `{'model_C': 10.0, 'model_max_iter': 100, 'model_penalty': 'l2'}`[cite: 803].

### Hiệu suất trên tập kiểm thử (Test Set):

| Lớp (Class) | Precision | Recall | F1-Score | Support |
| :--- | :---: | :---: | :---: | :---: |
| Ở lại (No) | 0.84 | 0.90 | 0.87 | 1035 |
| Rời đi (Yes) | 0.66 | 0.52 | 0.58 | 374 |
| Toàn bộ (Accuracy) | | | 0.80 | 1409 |

[cite_start]*(Các chỉ số trên đều được trích xuất từ Classification Report của mô hình [cite: 806]).*

### Phân tích Ma Trận Nhầm Lẫn (Confusion Matrix):
* [cite_start]True Negative = 934: Nhận diện chính xác 934 khách hàng sẽ tiếp tục ở lại[cite: 831].
* [cite_start]True Positive = 196: Phát hiện đúng 196 khách hàng có nguy cơ rời mạng để doanh nghiệp kịp thời can thiệp[cite: 832].
* [cite_start]False Positive = 101: 101 trường hợp báo động giả (Mô hình đoán rời đi nhưng thực tế ở lại)[cite: 833]. [cite_start]Tỷ lệ này hoàn toàn chấp nhận được trong bài toán kinh doanh[cite: 834].
* [cite_start]False Negative = 178: 178 trường hợp bỏ sót nguy hiểm (Khách thực tế rời mạng nhưng mô hình đoán ở lại)[cite: 835, 836]. [cite_start]Đây là điểm cần cải tiến thêm trong tương lai[cite: 838].

---

## 5. Key Insights & Hành Động Thực Tế

[cite_start]Dựa trên bảng trọng số tính năng (Coefficients) của mô hình Logistic Regression, dự án rút ra 3 yếu tố hàng đầu tác động đến hành vi rời đi của khách hàng[cite: 854, 856]:

1. [cite_start]Cước phí hàng tháng (`MonthlyCharges` - Trọng số: +1.09)[cite: 863, 865]:
   * [cite_start]Insight: Yếu tố gây áp lực tài chính lớn nhất[cite: 926]. [cite_start]Khách hàng sử dụng gói Cáp quang (Fiber optic) giá cao có mật độ rời mạng cực kỳ dày đặc[cite: 229, 230].
   * [cite_start]Hành động: Thay vì giảm giá vô điều kiện, doanh nghiệp nên thiết kế các gói cước Combo (Tích hợp Internet + Thoại + Data) để khách hàng cảm thấy nhận lại được nhiều giá trị hơn trên số tiền bỏ ra[cite: 928, 929].
2. [cite_start]Hợp đồng ngắn hạn (`Month-to-month` - Trọng số: +0.33)[cite: 867, 868]:
   * [cite_start]Insight: Khách hàng ký theo tháng không bị ràng buộc pháp lý hay chi phí phạt, sẵn sàng rời đi ngay trong vài tháng đầu nếu không hài lòng[cite: 208, 931, 932].
   * [cite_start]Hành động: Triển khai chiến dịch nâng cấp: Tặng 1 tháng cước hoặc giảm giá 10% tổng hóa đơn nếu họ đồng ý chuyển sang hợp đồng cam kết 1 hoặc 2 năm[cite: 933, 934].
3. [cite_start]Phương thức thanh toán Séc điện tử (`Electronic check` - Trọng số: +0.11)[cite: 877, 878]:
   * [cite_start]Insight: Việc phải chủ động thao tác thanh toán mỗi tháng vô tình tạo cơ hội để khách hàng cân nhắc lại chi phí dịch vụ[cite: 937, 938].
   * [cite_start]Hành động: Khuyến khích đăng ký Thanh toán tự động (Auto-pay) qua ngân hàng hoặc thẻ tín dụng bằng cách tặng voucher chiết khấu 5% cho kỳ hóa đơn đầu tiên[cite: 939, 940].
