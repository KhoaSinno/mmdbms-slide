# Bộ Slide Thuyết trình 3 – 5 Phút (Slidev) — Đề tài 2 MMDBMS

Bộ slide thuyết trình được xây dựng bằng [Slidev](https://github.com/slidevjs/slidev) (`@slidev/cli`), thiết kế tối ưu cho bài báo cáo **3 – 5 phút (7 slides)** của học phần **Hệ Quản trị Cơ sở dữ liệu Đa phương tiện**:
> **Đề tài 2**: *Xây dựng Hệ thống Chuyển đổi Âm thanh thành Văn bản Tiếng Việt trên Nền tảng Cơ sở dữ liệu Đa phương tiện Kết hợp WhisperX, Wav2Vec2-VI và CSDL SQLite + FAISS 768-D*

---

## 1. Cấu trúc phân bổ thời lượng 5 phút (7 Slides chuẩn)

Mỗi slide đều tích hợp sẵn **Speaker Notes (Kịch bản lời thoại chi tiết theo mốc giây)** hiển thị trực tiếp trong chế độ **Presenter Mode** của Slidev:

| Slide | Nội dung chính | Thời lượng | Mốc thời gian | Người trình bày (gợi ý) |
| :---: | :--- | :---: | :---: | :--- |
| **Slide 1** | **Trang bìa**: Tên đề tài & thành viên nhóm (`HTTT2211026`, `HTTT2211060`, `HTTT2211003`) | `25s` | `0:00 – 0:25` | Nguyễn Trần Anh Khoa |
| **Slide 2** | **1. Định hướng & 4 Trụ cột Giải pháp Thực hiện**: WhisperX tinh chỉnh + Wav2Vec2-VI Validate + SQLite & FAISS + Bổ sung nhãn tức thì | `40s` | `0:25 – 1:05` | Nguyễn Trần Anh Khoa |
| **Slide 3** | **2. Kiến trúc Hệ thống Lai ghép 4 Tầng (Acoustic RAG Pipeline)**: Sơ đồ luồng xử lý từ Tầng 1 DSP đến Tầng 4 MMDBMS | `45s` | `1:05 – 1:50` | Nguyễn Trần Anh Khoa |
| **Slide 4** | **3. Tiền xử lý Tín hiệu DSP & Kiểm định Wav2Vec2-VI 20ms**: Nén khoảng lặng (`TimeMap`) & Khôi phục 21 tầng CTC tiếng Việt | `45s` | `1:50 – 2:35` | Nguyễn Quốc Dửng |
| **Slide 5** | **4. Thiết kế CSDL Đa phương tiện Hai tầng (`SQLite` + `FAISS 768-D`)**: Khai thác kho `quoc_dung` (`6.652` mẫu) & bộ từ điển (`1.564` biến thể) | `50s` | `2:35 – 3:25` | Nguyễn Quốc Dửng |
| **Slide 6** | **5. Thuật toán Đối soát Ngữ âm, Chuẩn hóa Địa danh & Học Nhãn**: Phonetic Cohort + Trọng tài `S_hybrid >= 0.40` + 7-gram ITN (`Đắk Lắk`, `Kon Tum`, `cafe`) + Active Learning `< 0,2s` | `50s` | `3:25 – 4:15` | Nguyễn Thành Đạt |
| **Slide 7** | **6. Kết quả Thực nghiệm (8 Mẫu Kiểm thử) & Chuyển sang Live Demo** | `45s` | `4:15 – 5:00` | Nguyễn Thành Đạt |

---

## 2. Hướng dẫn chạy Slide thuyết trình

Mở terminal tại thư mục `W:\WorkSpace_IT\mmdbms-slide`:

```powershell
cd W:\WorkSpace_IT\mmdbms-slide

# Cài đặt dependencies (nếu chưa cài)
pnpm install
# hoặc: npm install

# Khởi chạy máy chủ trình chiếu Slidev
pnpm run dev
# hoặc: npm run dev
```

* **Chế độ Trình chiếu (Slide View)**: `http://localhost:3030`
* **Chế độ Người thuyết trình (Presenter Mode có đồng hồ đếm & kịch bản đọc)**: `http://localhost:3030/presenter`
* **Chế độ Tổng quan toàn bộ Slide (Overview)**: Nhấn phím `o` trên bàn phím hoặc truy cập `http://localhost:3030/overview`

---

## 3. Xuất tĩnh (SPA Build) hoặc Xuất PDF

```powershell
# Đóng gói thành Web App tĩnh (thư mục dist/)
pnpm run build

# Xuất ra tệp PDF
pnpm run export
```
