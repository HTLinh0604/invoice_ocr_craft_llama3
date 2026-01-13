# Enhancing the Efficiency of Invoice Information Recognition Systems  
*(Nâng Cao Hiệu Quả Hệ Thống Nhận Diện Thông Tin Hóa Đơn*)
### Using Object Detection and Semantic Extraction Models  
*(Ứng dụng Mô hình Phát hiện Đối tượng và Trích xuất Ngữ nghĩa*)

---

## 1. Project Overview *(Tổng quan dự án*)

This project focuses on automating the extraction of semantic information from retail invoices, addressing the limitations of manual processing and traditional OCR systems based on rigid rule-based or Regex approaches. Conventional methods often suffer from low accuracy, poor robustness, and high error rates when dealing with complex invoice layouts.  
*(Dự án này tập trung vào việc tự động hóa quá trình trích xuất thông tin ngữ nghĩa từ hóa đơn bán lẻ, nhằm khắc phục các hạn chế của phương pháp xử lý thủ công và các hệ thống OCR truyền thống dựa trên Regex hoặc luật cứng. Các phương pháp cũ thường có độ chính xác thấp và kém ổn định khi xử lý hóa đơn có bố cục phức tạp.)*

By integrating **Computer Vision** techniques with **Generative AI / Large Language Models (LLMs)**, the proposed system is capable of understanding invoices with diverse layouts, tilted angles, physical distortions, and low image quality.  
*(Thông qua việc kết hợp **Thị giác máy tính (Computer Vision)** và **Trí tuệ nhân tạo tạo sinh (LLM/GenAI)**, hệ thống có khả năng xử lý hiệu quả các hóa đơn bị nghiêng, nhàu nát, mờ hoặc có chất lượng ảnh kém.
)*






### Key Objectives *(Mục tiêu chính*)

- **Automation | Tự động hóa:** Convert invoice images into structured JSON data.  
- **High Accuracy | Độ chính xác cao:** Improve Vietnamese text and tabular data extraction compared to Regex-based OCR.  
- **Robustness | Tính ổn định:** Handle physical distortions such as crumpling, glare, and skewed angles.

---

## 2. System Architecture *(Kiến trúc hệ thống*)

The system is designed as a **multi-stage processing pipeline**, simulating the human ability to *detect – read – understand* invoice content.  
*(Hệ thống được xây dựng theo mô hình **pipeline đa tầng**, mô phỏng quá trình *phát hiện – đọc – hiểu* của con người.*)

### Stage 1: Text Detection – CRAFT  
*(Giai đoạn 1: Phát hiện vùng văn bản – CRAFT*)

The system employs **CRAFT (Character Region Awareness for Text Detection)** to precisely localize text regions:  
*(Hệ thống sử dụng mô hình **CRAFT** để định vị chính xác các vùng chứa văn bản:)*
- Utilizes **Region Score** to detect character centers.  
  *(**Region Score** xác định tâm ký tự.)*
- Uses **Affinity Score** to link adjacent characters.  
  *(**Affinity Score** liên kết các ký tự liền kề.)*
- Effectively handles curved, skewed, and densely packed text lines.  
  *(Hoạt động tốt với văn bản cong, nghiêng và các dòng sát nhau.)*

**Output:** Tight bounding boxes corresponding to each text line.

---

### Stage 2: Text Recognition – Tesseract OCR  
### Giai đoạn 2: Nhận dạng ký tự – Tesseract OCR

**English**

Detected text regions are processed by **Tesseract OCR (LSTM-based)**:
- Performs **local OCR** on each cropped region.
- Optimized for Vietnamese language (`lang='vie'`, `--psm 6`).
- Applies confidence-based filtering (threshold ≥ 70%).

**Tiếng Việt**

Các vùng văn bản sau khi phát hiện được đưa vào **Tesseract OCR**:
- Thực hiện OCR cục bộ trên từng vùng ảnh.
- Tối ưu cho tiếng Việt (`lang='vie'`, `--psm 6`).
- Loại bỏ kết quả có độ tin cậy thấp (< 70%).

**Preprocessing Techniques | Tiền xử lý:**
- CLAHE (contrast enhancement)
- Otsu thresholding
- Noise reduction and Gaussian blur

---

### Stage 3: Semantic Extraction – Llama 3.1  
### Giai đoạn 3: Trích xuất ngữ nghĩa – Llama 3.1

**English**

A Large Language Model (**Llama-3.1-8B-Instruct**) is used to:
- Understand invoice context.
- Correct OCR spelling and layout errors.
- Convert unstructured text into standardized **JSON**.

**Tiếng Việt**

Mô hình ngôn ngữ lớn **Llama 3.1 (8B-Instruct)** đóng vai trò phân tích ngữ nghĩa:
- Hiểu ngữ cảnh hóa đơn.
- Sửa lỗi OCR.
- Chuẩn hóa dữ liệu đầu ra dưới dạng **JSON**.

**Standard Output Fields | Trường dữ liệu chuẩn hóa:**
- Store Name | Tên cửa hàng  
- Address | Địa chỉ  
- Date & Time | Ngày giờ  
- Total Amount | Tổng tiền  
- Line Items | Danh sách sản phẩm  

---

## 3. Workflow & Technology Stack  
## Quy trình xử lý & Công nghệ sử dụng

### Processing Strategy | Chiến lược xử lý

**Check-then-Refine Pipeline:**
1. **Quick Pass:** Fast global OCR.
2. **Deep Processing:** Triggered if more than three key fields are missing:
   - CRAFT-based cropping
   - Adaptive morphological operations
   - Local image enhancement

### Technology Stack | Công nghệ

- **AI Models:** CRAFT, Llama 3.1  
- **OCR Engine:** Tesseract 4.0+ (LSTM)  
- **Image Processing:** OpenCV  
- **Backend:** Flask, Flask-CORS  
- **Deployment:** Google Colab Pro (Tesla T4 GPU) + Pyngrok  

---

## 4. Experimental Results | Kết quả thực nghiệm

The system was evaluated on **1,250 Vietnamese invoice images** and compared with a Regex-based baseline.

Hệ thống được đánh giá trên **1.250 hóa đơn tiếng Việt**, so sánh với hệ thống OCR truyền thống.

| Criteria / Tiêu chí | Baseline (Regex) | Proposed System | Improvement |
| --- | --- | --- | --- |
| Core Technology | Rule-based | Deep Learning + GenAI | Significant |
| Overall Accuracy | ~65% | **~94%** | +29% |
| Line Item Extraction | <65% | **92%** | Strong |
| Skewed / Crumpled Images | Poor | Robust | Major |
| Processing Time | <10s | ~17.5s | Accuracy trade-off |

**Key Highlights | Kết quả nổi bật**
- Total Amount accuracy: **98.5%**
- Transaction Date accuracy: **97.0%**
- Automatic detection of missing fields for user validation

---

## 5. Installation & API Usage  
## Hướng dẫn cài đặt & sử dụng

### System Requirements | Yêu cầu hệ thống
- Python, PyTorch (GPU support)
- Libraries: `transformers`, `craft-detector`, `pytesseract`, `opencv-python`, `flask`

### API Endpoints
- `POST /api/ocr` – Upload invoice image, receive JSON + annotated image  
- `GET /api/health` – System health check  

---

## 6. Future Roadmap | Hướng phát triển

- **Speed Optimization | Tối ưu tốc độ:** Quantization (4-bit), TensorRT, ONNX Runtime  
- **Image Enhancement | Cải thiện ảnh:** Dewarping, Super-Resolution  
- **End-to-End Models | Mô hình E2E:** Donut, TrOCR  
- **Enterprise Integration | Tích hợp nghiệp vụ:** ERP & Accounting systems (MISA, SAP)

---

## Authors | Nhóm thực hiện

- **Students | Sinh viên:** Huỳnh Thái Linh, Hồ Gia Thành, Trương Minh Khoa  
- **Supervisor | Giảng viên:** ThS. Nguyễn Quang Phúc  
- **Institution | Đơn vị:** HUTECH University – Faculty of Information Technology / Data Science  

---
