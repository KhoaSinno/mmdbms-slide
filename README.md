# Bộ Slide Thuyết trình 5 Phút (Slidev) — Đề tài 2 MMDBMS

Bộ slide thuyết trình được xây dựng bằng [Slidev](https://github.com/slidevjs/slidev) (`@slidev/cli`), thiết kế tối ưu cho bài báo cáo **5 phút (7 slides)** của học phần **Hệ Cơ sở dữ liệu Đa phương tiện**:
> **Đề tài 2**: *Xây dựng Hệ thống Chuyển đổi Âm thanh thành Văn bản Tiếng Việt trên Nền tảng Cơ sở dữ liệu Đa phương tiện Kết hợp WhisperX và Chỉ mục Vector Âm học FAISS*

---

## 1. Cấu trúc phân bổ thời lượng 5 phút (300 giây)

Mỗi slide đều tích hợp sẵn **Speaker Notes (Kịch bản lời thoại chi tiết theo mốc giây)** hiển thị trực tiếp trong chế độ **Presenter Mode** của Slidev:

| Slide | Nội dung chính | Thời lượng | Mốc thời gian |
| :---: | :--- | :---: | :---: |
| **Slide 1** | Trang bìa: Tên đề tài, thành viên nhóm (`HTTT2211026`, `HTTT2211060`, `HTTT2211003`) & điểm nhấn công nghệ | `30s` | `0:00 – 0:30` |
| **Slide 2** | **1. Đặt vấn đề & 4 thách thức kỹ thuật** khi triển khai WhisperX cho tiếng Việt | `45s` | `0:30 – 1:15` |
| **Slide 3** | **2. Kiến trúc tổng thể 5 tầng lai ghép** (`Hybrid WhisperX + Acoustic Label MMDBMS`) | `45s` | `1:15 – 2:00` |
| **Slide 4** | **3. Đột phá Tầng 1 & 2**: Nén khoảng lặng thích nghi & Khôi phục 21 tầng `feature_transform` CTC | `45s` | `2:00 – 2:45` |
| **Slide 5** | **4. Tầng 3**: Giải mã 6.652 tệp VNI/ASCII & CSDL Đa phương tiện 2 tầng (`SQLite` 5 bảng + `FAISS` 768-D) | `45s` | `2:45 – 3:30` |
| **Slide 6** | **5. Tầng 4 & 5**: Truy vấn Lân cận Ngữ âm (`Phonetic Cohort Search`) & Học nhãn tức thì (`< 50 ms`) | `45s` | `3:30 – 4:15` |
| **Slide 7** | **6. Kết quả thực nghiệm đối sánh A/B** trên GPU NVIDIA Tesla T4 (`WER`, `CER`, `TER`, `ERR`, `RTF`) | `30s` | `4:15 – 4:45` |
| **Slide 8** | **7. Minh họa phân tích mức từ (`WordAnalysis`)**, chuẩn hóa thực thể (`cafe`, `Đắk Lắk`) & Kết luận | `15s` | `4:45 – 5:00` |

---

## 2. Hướng dẫn chạy Slide thuyết trình

Mở terminal tại thư mục `d:\thuchanh-csdl-dpt\slides`:

```powershell
cd d:\thuchanh-csdl-dpt\slides

# Cài đặt dependencies (nếu chưa cài)
pnpm install
# hoặc: npm install

# Khởi chạy máy chủ trình chiếu Slidev
pnpm run dev
# hoặc: npm run dev
```

* **Chế độ Trình chiếu (Slide View)**: `http://localhost:3030`
* **Chế độ Người thuyết trình (Presenter Mode có đồng hồ đếm 5 phút & kịch bản đọc)**: `http://localhost:3030/presenter`
* **Chế độ Tổng quan toàn bộ Slide (Overview)**: Nhấn phím `o` trên bàn phím hoặc truy cập `http://localhost:3030/overview`
* **Chế độ Sáng / Tối (Dark / Light Mode)**: Nhấn biểu tượng mặt trăng/mặt trời ở thanh công cụ dưới cùng.

---

## 3. Xuất tĩnh (SPA Build) hoặc Xuất PDF

```powershell
# Đóng gói thành Web App tĩnh (thư mục dist/)
pnpm run build

# Xuất ra tệp PDF (yêu cầu cài thêm playwright-chromium)
pnpm run export
```
