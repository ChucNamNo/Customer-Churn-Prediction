# Mô hình Dự đoán Khách hàng Rời bỏ - IBM Telco Customer Churn Prediction

Dự án phân tích hành vi khách hàng và xây dựng mô hình học máy (Machine Learning) nhằm dự đoán chính xác nguy cơ rời bỏ dịch vụ (Customer Churn) tại một doanh nghiệp viễn thông. Từ đó, đề xuất các giải pháp tối ưu hóa chiến lược giữ chân khách hàng (Customer Retention) và giảm thiểu thiệt hại doanh thu.

---

## 1. Bối Cảnh & Mục Tiêu Dự Án

### Bối cảnh doanh nghiệp
* Trong ngành viễn thông, việc giữ chân khách hàng hiện tại luôn có chi phí thấp hơn rất nhiều so với việc tìm kiếm khách hàng mới.
* Hiện tượng khách hàng hủy dịch vụ, chuyển sang đối thủ cạnh tranh hoặc ngừng gia hạn hợp đồng đang ảnh hưởng trực tiếp đến doanh thu.

### Nhiệm vụ cốt lõi
* Phân tích khám phá dữ liệu (EDA) để tìm ra các xu hướng hành vi của nhóm khách hàng rời đi.
* Sử dụng thuật toán học máy để phát hiện sớm các khách hàng có nguy cơ rời bỏ dịch vụ cao.
* Đưa ra các khuyến nghị thực tế giúp doanh nghiệp tối ưu chiến lược giữ chân khách hàng.

---

## 2. Thông Tin Tập Dữ Liệu (Dataset)

Dự án sử dụng bộ dữ liệu IBM Telco Customer Churn Dataset gồm 7,043 dòng và 21 cột. Mỗi dòng đại diện cho thông tin của một khách hàng duy nhất.

### Các nhóm thông tin chính:
* Thông tin cá nhân: Giới tính, trạng thái người cao tuổi, tình trạng hôn nhân, người phụ thuộc.
* Thông tin dịch vụ đăng ký: Dịch vụ thoại, nhiều đường dây, dịch vụ Internet và các dịch vụ gia tăng (Bảo mật, Sao lưu, Bảo hiểm thiết bị, Hỗ trợ kỹ thuật, Truyền hình/Phim trực tuyến).
* Thông tin tài khoản & Cước phí: Số tháng gắn bó (tenure), Loại hợp đồng, Hình thức hóa đơn, Phương thức thanh toán, Cước phí hằng tháng và Tổng cước tích lũy.
* Biến mục tiêu (Target): Churn (Yes: Đã rời bỏ dịch vụ / No: Vẫn đang sử dụng).

Lưu ý: Biến mục tiêu bị mất cân bằng (73.5% No vs 26.5% Yes), do đó dự án tập trung vào các chỉ số F1-Score và Recall thay vì chỉ dựa vào Accuracy.

---

## 3. Quy Trình Thực Hiện (Pipeline)

Dự án được đóng gói qua một Pipeline tiền xử lý và huấn luyện hoàn chỉnh để tránh hiện tượng rò rỉ dữ liệu (Data Leakage):

1. Tiền xử lý dữ liệu: Ép kiểu dữ liệu `TotalCharges` về dạng số, xử lý giá trị khuyết thiếu bằng phương pháp điền giá trị trung vị (Median Imputer) , chuẩn hóa biến số lượng với `StandardScaler` và mã hóa biến phân loại bằng `OneHotEncoder`.
2. Kỹ nghệ tính năng (Feature Engineering): Tạo tính năng tổng hợp `Total_Addon_Services` (đếm số dịch vụ gia tăng dạng 'Yes'), phân nhóm khách hàng theo thời gian gắn bó `tenure_group` (New, Mid-term, Loyal Customer) và loại bỏ các biến thành phần để giảm bớt số chiều dữ liệu.
3. Lựa chọn mô hình: Thử nghiệm nhanh các mô hình phân loại thông qua thư viện `LazyClassifier`, lựa chọn Logistic Regression làm mô hình chính nhờ hiệu suất tốt và tính tường minh cao.
4. Tối ưu hóa siêu tham số: Sử dụng `GridSearchCV` với 5-fold cross-validation để tìm ra bộ tham số tối ưu cho mô hình.

---

## 4. Kết Quả Mô Hình (Model Performance)

Bộ tham số tốt nhất được tìm thấy qua GridSearch là: `{'model_C': 10.0, 'model_max_iter': 100, 'model_penalty': 'l2'}`.

### Hiệu suất trên tập kiểm thử (Test Set):

| Lớp (Class) | Precision | Recall | F1-Score | Support |
| :--- | :---: | :---: | :---: | :---: |
| Ở lại (No) | 0.84 | 0.90 | 0.87 | 1035 |
| Rời đi (Yes) | 0.66 | 0.52 | 0.58 | 374 |
| Accuracy | | | 0.80 | 1409 |
| macro avg | 0.75 | 0.71 | 0.73 | 1409 |
| weighted avg | 0.79 | 0.80 | 0.79 | 1409 |

*(Các chỉ số trên đều được trích xuất từ Classification Report của mô hình).*

### Phân tích Ma Trận Nhầm Lẫn (Confusion Matrix):
* True Negative = 934: Nhận diện chính xác 934 khách hàng sẽ tiếp tục ở lại.
* True Positive = 196: Phát hiện đúng 196 khách hàng có nguy cơ rời mạng để doanh nghiệp kịp thời can thiệp.
* False Positive = 101: 101 trường hợp báo động giả (Mô hình đoán rời đi nhưng thực tế ở lại). Tỷ lệ này hoàn toàn chấp nhận được trong bài toán kinh doanh.
* False Negative = 178: 178 trường hợp bỏ sót nguy hiểm (Khách thực tế rời mạng nhưng mô hình đoán ở lại). Đây là điểm cần cải tiến thêm trong tương lai.

---

## 5. Key Insights & Hành Động Thực Tế

Dựa trên bảng trọng số tính năng (Coefficients) của mô hình Logistic Regression, dự án rút ra 3 yếu tố hàng đầu tác động đến hành vi rời đi của khách hàng:

1. Cước phí hàng tháng (`MonthlyCharges` - Trọng số: +1.09):
   * Insight: Yếu tố gây áp lực tài chính lớn nhất. Khách hàng sử dụng gói Cáp quang (Fiber optic) giá cao có mật độ rời mạng cực kỳ dày đặc.
   * Hành động: Thay vì giảm giá vô điều kiện, doanh nghiệp nên thiết kế các gói cước Combo (Tích hợp Internet + Thoại + Data) để khách hàng cảm thấy nhận lại được nhiều giá trị hơn trên số tiền bỏ ra.
2. Hợp đồng ngắn hạn (`Month-to-month` - Trọng số: +0.33):
   * Insight: Khách hàng ký theo tháng không bị ràng buộc pháp lý hay chi phí phạt, sẵn sàng rời đi ngay trong vài tháng đầu nếu không hài lòng.
   * Hành động: Triển khai chiến dịch nâng cấp: Tặng 1 tháng cước hoặc giảm giá 10% tổng hóa đơn nếu họ đồng ý chuyển sang hợp đồng cam kết 1 hoặc 2 năm.
3. Phương thức thanh toán Séc điện tử (`Electronic check` - Trọng số: +0.11):
   * Insight: Việc phải chủ động thao tác thanh toán mỗi tháng vô tình tạo cơ hội để khách hàng cân nhắc lại chi phí dịch vụ.
   * Hành động: Khuyến khích đăng ký Thanh toán tự động (Auto-pay) qua ngân hàng hoặc thẻ tín dụng bằng cách tặng voucher chiết khấu 5% cho kỳ hóa đơn đầu tiên.
