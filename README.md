# 📄 Giải mã Vigenère Tự động

## 1. Tổng quan
Script `Vigene.py` là một công cụ thám mã (cryptanalysis) tự động dành cho mật mã Vigenère. Chương trình có khả năng khôi phục văn bản gốc từ văn bản mã hóa (ciphertext) mà **không cần biết trước từ khóa (Key)**.

Giải pháp dựa trên các phương pháp thống kê tần suất ký tự của tiếng Anh, bao gồm **Chỉ số trùng khớp (Index of Coincidence)** và **Kiểm định Chi bình phương (Chi-squared statistic)**.

---

## 2. Cơ sở Toán học & Thuật toán

Để phá vỡ mã Vigenère, chương trình thực hiện theo quy trình hai bước:

### 2.1. Bước 1: Tìm độ dài khóa (Key Length)
Mã Vigenère thực chất là sự kết hợp của nhiều mã Caesar. Nếu độ dài khóa là $k$, thì cứ mỗi ký tự thứ $k$ trong văn bản sẽ được mã hóa bởi cùng một phép dịch chuyển.

* **Phương pháp:** Sử dụng **Index of Coincidence (IoC)**.
* **Công thức:**
    $$IoC = \frac{\sum f_i (f_i - 1)}{N (N - 1)}$$
    *(Trong đó $f_i$ là số lần xuất hiện của ký tự thứ $i$, $N$ là tổng số ký tự)*.
* **Nguyên lý:**
    * Văn bản tiếng Anh chuẩn có $IoC \approx 0.0667$.
    * Văn bản ngẫu nhiên có $IoC \approx 0.038$.
* **Thực thi trong code:** Hàm `find_best_key_length` thử chia văn bản thành các nhóm với độ dài $k$ từ 1 đến 15. Giá trị $k$ nào cho IoC trung bình gần **0.0667** nhất sẽ được chọn là độ dài khóa.

### 2.2. Bước 2: Tìm từ khóa (Key Recovery)
Sau khi biết độ dài khóa $k$, văn bản được chia thành $k$ cột dọc. Mỗi cột này là một mã Caesar đơn giản.

* **Phương pháp:** Phân tích tần suất (Frequency Analysis) sử dụng **Chi-squared ($\chi^2$)**.
* **Công thức:**
    $$\chi^2 = \sum \frac{(Observed - Expected)^2}{Expected}$$
* **Thực thi trong code:** Hàm `solve_key` thực hiện:
    1.  Trích xuất các ký tự thuộc cùng một vị trí trong chu kỳ khóa.
    2.  Thử dịch chuyển (shift) nhóm ký tự đó 26 lần (A-Z).
    3.  So sánh tần suất sau khi dịch chuyển với bảng tần suất chuẩn `ENGLISH_FREQ`.
    4.  Phép dịch chuyển có chỉ số $\chi^2$ thấp nhất chính là ký tự của khóa.

---

## 3. Cấu trúc Source Code (`Vigene.py`)

### A. Dữ liệu chuẩn
* **`ENGLISH_FREQ`**: Dictionary chứa xác suất xuất hiện của 26 chữ cái trong tiếng Anh (ví dụ: 'E': 0.12702, 'T': 0.09056).

### B. Các hàm xử lý chính
1.  **`get_index_of_coincidence(text)`**: Tính toán độ "mượt" của văn bản để xác định xem nó có giống tiếng Anh tự nhiên hay không.
2.  **`find_best_key_length(text, max_len=15)`**: Vòng lặp kiểm tra các độ dài khóa giả định và chọn độ dài tối ưu nhất.
3.  **`solve_key(ciphertext)`**: Hàm cốt lõi kết hợp việc tìm độ dài khóa và tìm từng ký tự khóa để trả về từ khóa hoàn chỉnh (ví dụ: "SECRET").

### C. Class `VigenereCipher`
Chịu trách nhiệm thực hiện phép giải mã cuối cùng khi đã có Key:
* Xử lý riêng biệt chữ Hoa và chữ Thường.
* Bảo toàn các ký tự đặc biệt (dấu câu, khoảng trắng) không nằm trong bảng chữ cái.

### D. Xử lý File (I/O)
Code sử dụng thư viện `os` để đảm bảo tính ổn định khi chạy trên các môi trường khác nhau:
* Tự động xác định đường dẫn tuyệt đối của file script.
* Đọc file đầu vào: `ciphertext.txt`.
* Ghi file kết quả: `final_result.txt`.

---

## 4. Hướng dẫn sử dụng

1.  **Chuẩn bị:**
    * Cài đặt Python 3.x.
    * Tạo file `ciphertext.txt` chứa đoạn mã cần giải trong cùng thư mục với `Vigene.py`.

2.  **Chạy chương trình:**
    ```bash
    python Vigene.py
    ```

3.  **Kết quả:**
    * Key tìm được sẽ hiển thị trên màn hình console.
    * Nội dung giải mã đầy đủ được lưu vào file `final_result.txt`.

---
