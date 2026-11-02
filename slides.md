---
theme: default
colorSchema: light
title: Hệ Thống Chuyển Đổi Âm Thanh Thành Văn Bản Tiếng Việt Trên Nền Tảng MMDBMS
info: |
  ## Bài thuyết trình Học phần Hệ Cơ sở dữ liệu Đa phương tiện (Đề tài 2)
  Trường Đại học Kỹ thuật - Công nghệ Cần Thơ (CTUET)
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true
---

<div class="flex flex-col items-center justify-center h-full px-4 text-slate-900">
  <div class="badge-pill px-4 py-1.5 rounded-full bg-blue-100 border-2 border-blue-400 text-blue-900 uppercase tracking-wider mb-4">
    Học phần: Hệ Cơ sở dữ liệu Đa phương tiện
  </div>
  <h1 class="!text-[26px] !leading-snug font-extrabold text-slate-900 mb-5 max-w-4xl">
    XÂY DỰNG HỆ THỐNG CHUYỂN ĐỔI ÂM THANH THÀNH VĂN BẢN<br/>
    TIẾNG VIỆT TRÊN NỀN TẢNG CƠ SỞ DỮ LIỆU ĐA PHƯƠNG TIỆN<br/>
    <span class="text-blue-700">KẾT HỢP WHISPERX VÀ CHỈ MỤC VECTOR ÂM HỌC FAISS</span>
  </h1>
  <div class="grid grid-cols-3 gap-3.5 w-full max-w-4xl text-left">
    <div class="p-3 rounded-xl bg-slate-50 border-2 border-slate-300 shadow-sm">
      <div class="font-extrabold text-xl text-blue-800 mt-0.5">Nguyễn Trần Anh Khoa</div>
      <div class="text-xm font-mono font-semibold text-slate-700">MSSV: HTTT2211026</div>
    </div>
    <div class="p-3 rounded-xl bg-slate-50 border-2 border-slate-300 shadow-sm">
      <div class="font-extrabold text-xl text-indigo-800 mt-0.5">Nguyễn Quốc Dửng</div>
      <div class="text-xm font-mono font-semibold text-slate-700">MSSV: HTTT2211060</div>
    </div>
    <div class="p-3 rounded-xl bg-slate-50 border-2 border-slate-300 shadow-sm">
      <div class="font-extrabold text-xl text-teal-800 mt-0.5">Nguyễn Thành Đạt</div>
      <div class="text-xm font-mono font-semibold text-slate-700">MSSV: HTTT2211003</div>
    </div>
  </div>
</div>

<!--
[0:00 - 0:30 | 30 giây]
Xin chào Thầy và các bạn. Nhóm chúng em gồm Nguyễn Trần Anh Khoa, Nguyễn Quốc Dửng và Nguyễn Thành Đạt xin trình bày Đề tài 2 môn Hệ cơ sở dữ liệu đa phương tiện: "Xây dựng hệ thống chuyển đổi âm thanh thành văn bản tiếng Việt trên nền tảng Cơ sở dữ liệu đa phương tiện kết hợp WhisperX và chỉ mục vector âm học FAISS".
Hệ thống của nhóm khai thác 6.652 mẫu âm tiết thực tế trên kiến trúc hai tầng SQLite và FAISS 768 chiều, giúp giảm 74,7% tỷ lệ lỗi từ và hỗ trợ học nhãn bổ sung tức thì dưới 50 mili-giây.
-->

---
transition: slide-left
---

# 1. Bài toán & 4 Vấn đề thực tế

<div class="flex items-center justify-between px-3.5 py-2 mb-3 rounded-lg bg-blue-100 border-2 border-slate-300 text-slate-800">
  <span><b>Mục tiêu đề tài:</b> Nhận dạng tiếng nói tiếng Việt liên tục, định vị từng từ và tra cứu trên CSDL Đa phương tiện.</span>
</div>
<div class="grid grid-cols-2 gap-3.5 text-xs text-slate-900">
  <div class="p-3.5 rounded-xl bg-red-50 border-2 border-red-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between gap-2 mb-2">
        <span class="font-extrabold text-sm text-red-900">1. Suy biến căn chỉnh mức từ</span>
      </div>
      <p class="text-slate-800 text-[12.5px] font-medium">
        WhisperX gốc bỏ sót <b>21 tầng đặc trưng tiếng Việt</b>, làm toàn bộ mốc thời gian từ bị dồn cục về 0,3 giây đầu câu.
      </p>
    </div>
    <div class="mt-3 px-3 py-1.5 rounded-lg bg-white border border-red-300 flex items-center justify-between text-xs font-bold">
      <span class="text-red-700">❌ Lệch biên từ nghiêm trọng</span>
      <span class="text-emerald-800">➔ ✅ Khôi phục đủ 21 tầng CTC</span>
    </div>
  </div>
  <div class="p-3.5 rounded-xl bg-amber-50 border-2 border-amber-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between gap-2 mb-2">
        <span class="font-extrabold text-sm text-amber-900">2. Đứt ngữ cảnh khi đọc ngắt nhịp</span>
      </div>
      <p class="text-slate-800 text-[12.5px] font-medium">
        Khoảng nghỉ dài giữa các chữ làm mô hình đứt mạch ngữ cảnh, nhận diện sai vần và dấu thanh (<i>“hôm nay chia sẻ” ➔ “hôn nay chia sẽ”</i>).
      </p>
    </div>
    <div class="mt-3 px-3 py-1.5 rounded-lg bg-white border border-amber-300 flex items-center justify-between text-xs font-bold">
      <span class="text-amber-800">❌ Đứt mạch &amp; sai từ đơn lẻ</span>
      <span class="text-emerald-800">➔ ✅ Nén khoảng lặng thích nghi</span>
    </div>
  </div>
  <div class="p-3.5 rounded-xl bg-purple-50 border-2 border-purple-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between gap-2 mb-2">
        <span class="font-extrabold text-sm text-purple-900">3. Phương ngữ Nam Bộ &amp; Từ mượn</span>
      </div>
      <p class="text-slate-800 text-[12.5px] font-medium">
        Biến âm vùng miền (<i>thắp lên ➔ hấp lên</i>, <i>thư thả ➔ hư hả</i>) và từ vay mượn, thực thể dễ bị phiên âm sai (<i>ly phin ➔ ly phim, check ➔ chết</i>).
      </p>
    </div>
    <div class="mt-3 px-3 py-1.5 rounded-lg bg-white border border-purple-300 flex items-center justify-between text-xs font-bold">
      <span class="text-purple-800">❌ Sai biến âm &amp; từ vay mượn</span>
      <span class="text-emerald-800">➔ ✅ Đối sánh ngữ âm &amp; Chuẩn hóa</span>
    </div>
  </div>
  <div class="p-3.5 rounded-xl bg-blue-50 border-2 border-blue-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between gap-2 mb-2">
        <span class="font-extrabold text-sm text-blue-900">4. Học bổ sung nhãn tức thì</span>
      </div>
      <p class="text-slate-800 text-[12.5px] font-medium">
        Cần cập nhật ngay từ người dùng vừa sửa vào CSDL <b>6.652 mẫu âm tiết</b> để nhận đúng tức thì ở lần chạy kế tiếp.
      </p>
    </div>
    <div class="mt-3 px-3 py-1.5 rounded-lg bg-white border border-blue-300 flex items-center justify-between text-xs font-bold">
      <span class="text-blue-800">❌ Tốn hàng giờ huấn luyện</span>
      <span class="text-emerald-800">➔ ✅ Nạp nóng vector &lt; 50 ms</span>
    </div>
  </div>
</div>

<!--
[0:30 - 1:15 | 45 giây]
Khi giải quyết bài toán nhận dạng câu nói tiếng Việt liên tục, nhóm đối mặt với 4 vấn đề thực tế:
Thứ nhất, WhisperX gốc bỏ sót 21 tầng biến đổi đặc trưng tiếng Việt, làm điểm tự tin CTC tụt xuống 0,009 và toàn bộ biên thời gian từ bị dồn lệch về đầu câu.
Thứ hai, khi người nói đọc ngắt nhịp chậm rãi, các khoảng nghỉ dài làm mô hình bị đứt ngữ cảnh, tự chấm câu vụn vặt khiến tỷ lệ lỗi từ vọt lên 38 đến 40%.
Thứ ba là hiện tượng biến âm phương ngữ Nam Bộ như "mưa rào" đọc thành "mưa gào", "thành công" thành "hành công", cùng các địa danh như "Đắk Lắk".
Thứ tư là yêu cầu học bổ sung nhãn tức thì dưới 50 mili-giây ngay khi người dùng sửa lỗi mà không phải huấn luyện lại mô hình.
-->

---
transition: slide-left
---

# 2. Kiến trúc Tổng thể Hệ thống & Luồng Dữ liệu

<div class="flex flex-col items-center justify-center mt-1">
    <img src="/kien_truc_he_thong.png" alt="Kiến trúc Tổng thể Hệ thống" />
</div>

<!--
[1:15 - 2:00 | 45 giây]
Slide 3 trực quan hóa kiến trúc tổng thể của hệ thống nhận dạng tiếng nói lai ghép:
- Phân vùng 1 (trên cùng): Luồng xử lý trực tuyến từ sóng âm đầu vào ➔ Tiền xử lý nén khoảng lặng ➔ Giải mã Whisper và căn chỉnh CTC tiếng Việt 21 tầng ➔ Đối sánh ngữ âm CSDL để xuất văn bản kèm tọa độ thời gian chuẩn xác.
- Phân vùng 2 (trái tim hệ thống): CSDL Đa phương tiện hai tầng gồm SQLite (5 bảng quan hệ B-Tree) đồng bộ khóa 1-1 với Chỉ mục Vector FAISS (6.652 vector âm học 768 chiều).
- Phân vùng 3 (vòng lặp học nhãn chủ động): Khi người dùng sửa từ sai, hệ thống tự cắt mẫu âm thanh, trích xuất vector 768-D và nạp nóng vào CSDL trong chưa đầy 50 mili-giây mà không cần huấn luyện lại.
-->

---
transition: slide-left
---

# 3. Cải tiến Tín hiệu & Căn chỉnh Mức từ (Giải quyết Vấn đề 1 & 2)

<div class="grid grid-cols-2 gap-4 text-xs mt-1 text-slate-900">
  <div class="p-3.5 rounded-xl bg-sky-50 border-2 border-sky-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-3">
        <span class="font-extrabold text-sm text-sky-950">1. Nén khoảng lặng thích nghi</span>
      </div>
      <div class="space-y-2">
        <div class="p-2.5 rounded-lg bg-white border border-sky-300 flex items-center gap-3">
          <span class="badge-pill px-2.5 py-1 rounded bg-sky-700 text-white text-xs">Bước 1</span>
          <div>
            <div class="font-extrabold text-sky-950">Phát hiện khoảng nghỉ dài &ge; 320 ms</div>
            <div class="text-[11px] text-slate-600">Tự động nhận diện câu đọc rời rạc (tỷ lệ lặng &ge; 35%)</div>
          </div>
        </div>
        <div class="p-2.5 rounded-lg bg-white border border-sky-300 flex items-center gap-3">
          <span class="badge-pill px-2.5 py-1 rounded bg-sky-700 text-white text-xs">Bước 2</span>
          <div>
            <div class="font-extrabold text-sky-950">Thu gọn mượt về 140 ms + Đệm biên 60 ms</div>
            <div class="text-[11px] text-slate-600">Nối liền mạch ngữ cảnh câu, giữ trọn phụ âm cuối (<i>-p, -t, -c</i>)</div>
          </div>
        </div>
        <div class="p-2.5 rounded-lg bg-white border border-sky-300 flex items-center gap-3">
          <span class="badge-pill px-2.5 py-1 rounded bg-sky-700 text-white text-xs">Bước 3</span>
          <div>
            <div class="font-extrabold text-sky-950">Nội suy trả lại tọa độ gốc chính xác</div>
            <div class="text-[11px] text-slate-600">Ánh xạ ngược mốc thời gian từ đã nén về đúng vị trí ban đầu</div>
          </div>
        </div>
      </div>
    </div>
    <div class="mt-3 p-3 rounded-xl bg-sky-900 text-white">
      <div class="text-[11px] text-sky-200 font-semibold mb-1">Hiệu quả thực tế trên Câu đọc ngắt chữ rời rạc (23 từ):</div>
      <div class="flex items-center justify-between text-xs font-extrabold">
        <span>Thời lượng: 44,9s ➔ 12,8s (RTF 0,084)</span>
        <span class="px-2.5 py-0.5 rounded bg-emerald-400 text-slate-950">Lỗi WER: 8,7% ➔ 0,0%</span>
      </div>
    </div>
  </div>
  <div class="p-3.5 rounded-xl bg-indigo-50 border-2 border-indigo-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-3">
        <span class="font-extrabold text-sm text-indigo-950">2. Khôi phục Căn chỉnh CTC Tiếng Việt</span>
      </div>
      <div class="p-3 rounded-lg bg-white border border-indigo-300 mb-3">
        <div class="text-[11px] font-bold text-indigo-900 mb-2">Luồng khôi phục đặc trưng âm học &amp; tái sử dụng bộ nhớ:</div>
        <div class="flex items-center justify-between gap-1.5 text-center font-extrabold text-[11px]">
          <div class="p-2 rounded-lg bg-slate-100 border border-slate-300 flex-1">
            Tầng ẩn<br/><span class="text-indigo-700">768 chiều</span>
          </div>
          <span class="text-indigo-700 text-sm">➔</span>
          <div class="p-2 rounded-lg bg-indigo-100 border-2 border-indigo-400 text-indigo-950 flex-[1.4]">
            Khôi phục 3 khối<br/>Biến đổi Đặc trưng (21 tầng)
          </div>
          <span class="text-indigo-700 text-sm">➔</span>
          <div class="p-2 rounded-lg bg-emerald-100 border-2 border-emerald-400 text-emerald-950 flex-1">
            Căn chỉnh<br/>biên từ chuẩn
          </div>
        </div>
      </div>
      <table class="w-full text-xs border-collapse bg-white rounded-lg overflow-hidden border-2 border-indigo-300">
        <thead>
          <tr class="border-b-2 border-indigo-200 text-left bg-indigo-100">
            <th class="text-indigo-950">Tiêu chí đối sánh</th>
            <th class="text-red-800">WhisperX gốc</th>
            <th class="text-emerald-800">Hệ thống đề xuất</th>
          </tr>
        </thead>
        <tbody>
          <tr class="border-b border-slate-200">
            <td class="font-bold">Điểm tự tin CTC</td>
            <td class="text-red-700 font-bold">0,009 (Suy biến)</td>
            <td class="font-extrabold text-emerald-800">0,50 – 0,98</td>
          </tr>
          <tr class="border-b border-slate-200 bg-emerald-50/40">
            <td class="font-bold">Độ chuẩn biên từ</td>
            <td class="text-red-700 font-bold">Lệch dồn về 0,3s</td>
            <td class="font-extrabold text-emerald-800">Khớp chuẩn ±15 ms</td>
          </tr>
          <tr>
            <td class="font-bold">Bộ nhớ trích xuất vector</td>
            <td class="text-red-700 font-bold">+1,2 GB (Mô hình rời)</td>
            <td class="font-extrabold text-emerald-800">0 MB (Dùng chung)</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="mt-3 p-2.5 rounded-xl bg-indigo-900 text-white text-center text-xs font-bold">
      Đồng thuận âm học kép: Tự động khôi phục từ bị nuốt âm giữa câu
    </div>
  </div>
</div>

<!--
[2:00 - 2:45 | 45 giây]
Slide 4 trình bày hai cải tiến cốt lõi ở tầng tín hiệu và tầng căn chỉnh mức từ:
- Bên trái là thuật toán Nén khoảng lặng thích nghi qua 3 bước: phát hiện các khoảng nghỉ dài từ 320 mili-giây trở lên, thu gọn mượt về 140 mili-giây kèm đệm biên 60 mili-giây để bảo vệ phụ âm cuối, sau đó nội suy trả lại đúng tọa độ thời gian gốc. Nhờ đó câu đọc ngắt nhịp dài 18,5 giây được thu gọn về 5,8 giây và giảm lỗi WER từ 38,1% xuống 0%.
- Bên phải là cải tiến Khôi phục 3 khối biến đổi đặc trưng gồm 21 tầng trọng số tiếng Việt bị WhisperX gốc bỏ sót. Điểm tự tin CTC tăng vọt từ 0,009 lên 0,50–0,98, biên thời gian từ khớp chính xác trong khoảng cộng trừ 15 mili-giây, đồng thời tiết kiệm 1,2 GB bộ nhớ GPU nhờ dùng chung tầng ẩn 768 chiều cho chỉ mục FAISS.
-->

---
transition: slide-left
---

# 4. Quản trị CSDL Đa phương tiện Hai tầng (SQLite + FAISS)

<div class="grid grid-cols-12 gap-4 text-xs mt-1 text-slate-900">
  <div class="col-span-5 p-3.5 rounded-xl bg-amber-50 border-2 border-amber-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-2.5">
        <span class="font-extrabold text-sm text-amber-950">Chuẩn hóa 6.652 Mẫu âm tiết</span>
        <span class="badge-pill px-2 py-0.5 rounded bg-amber-200 text-amber-950 text-[11px]">Chính xác 100%</span>
      </div>
      <div class="space-y-2">
        <div class="p-2.5 rounded-lg bg-white border border-amber-300">
          <div class="text-[11px] font-bold text-amber-900 mb-1">1. Giải mã ký hiệu VNI từ tên mẫu thu âm</div>
          <div class="flex items-center justify-between bg-slate-100 px-2.5 py-1.5 rounded font-bold text-xs">
            <span class="font-mono text-slate-700">Mã VNI: d-uuoong2</span>
            <span class="text-amber-700">➔</span>
            <span class="text-blue-800">Nhãn chuẩn: “đường”</span>
          </div>
        </div>
        <div class="text-center text-amber-700 font-black text-sm leading-none">⬇</div>
        <div class="p-2.5 rounded-lg bg-white border border-amber-300">
          <div class="text-[11px] font-bold text-amber-900 mb-1">2. Bóc tách cấu trúc âm vị tiếng Việt</div>
          <div class="grid grid-cols-4 gap-1 text-center text-[11px] font-bold">
            <span class="p-1 rounded bg-blue-50 border border-blue-200 text-blue-900">Âm đầu: đ</span>
            <span class="p-1 rounded bg-blue-50 border border-blue-200 text-blue-900">Vần: ươ</span>
            <span class="p-1 rounded bg-blue-50 border border-blue-200 text-blue-900">Cuối: ng</span>
            <span class="p-1 rounded bg-amber-100 border border-amber-300 text-amber-950">Thanh: Huyền</span>
          </div>
        </div>
        <div class="text-center text-amber-700 font-black text-sm leading-none">⬇</div>
        <div class="p-2.5 rounded-lg bg-white border border-amber-300">
          <div class="text-[11px] font-bold text-teal-900 mb-1">3. Trích xuất Vector đặc trưng 768 chiều chuẩn hóa L2</div>
          <div class="text-[11px] text-slate-700">
            Lấy trung bình vùng lõi phát âm 0,45 giây và chuẩn hóa độ dài đơn vị để tích vô hướng bằng đúng <b>độ tương đồng Cosine</b>.
          </div>
        </div>
      </div>
    </div>
    <div class="mt-2.5 p-2 rounded-lg bg-amber-900 text-white text-center font-bold text-[11px]">
      Bao phủ đa dạng âm tiết, dấu thanh (0–5)<br>&amp; biến âm phương ngữ
    </div>
  </div>
  <div class="col-span-7 p-3.5 rounded-xl bg-slate-50 border-2 border-slate-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-2">
        <span class="font-extrabold text-sm text-blue-950">Lược đồ 5 Bảng CSDL Quan hệ SQLite</span>
        <span class="badge-pill px-2.5 py-0.5 rounded bg-blue-100 border border-blue-300 text-blue-900 text-[11px]">Chỉ mục B-Tree tốc độ cao</span>
      </div>
      <div class="space-y-1.5">
        <div class="p-2 rounded-lg bg-white border-l-4 border-blue-600 border border-slate-300 flex items-center justify-between">
          <span class="font-extrabold text-blue-900 w-48 shrink-0">1. Bảng Nhãn âm tiết</span>
          <span class="text-slate-700 text-[11.5px] flex-1">Từ điển âm tiết tiếng Việt có dấu, gốc không dấu, dấu thanh &amp; cờ ưu tiên</span>
        </div>
        <div class="p-2 rounded-lg bg-emerald-50/70 border-l-4 border-emerald-600 border border-emerald-300 flex items-center justify-between">
          <span class="font-extrabold text-emerald-900 w-48 shrink-0">2. Bảng Mẫu âm thanh vật lý</span>
          <span class="text-slate-800 text-[11.5px] font-semibold flex-1">Quản lý 6.652 mẫu thu âm • <b>Đồng bộ khóa 1–1 với Chỉ mục Vector FAISS</b></span>
        </div>
        <div class="p-2 rounded-lg bg-white border-l-4 border-amber-500 border border-slate-300 flex items-center justify-between">
          <span class="font-extrabold text-amber-900 w-48 shrink-0">3. Bảng Quy tắc biến âm</span>
          <span class="text-slate-700 text-[11.5px] flex-1">Bộ nhớ cặp từ nhầm lẫn kèm ngữ cảnh từ đứng trước / đứng sau</span>
        </div>
        <div class="p-2 rounded-lg bg-white border-l-4 border-purple-600 border border-slate-300 flex items-center justify-between">
          <span class="font-extrabold text-purple-900 w-48 shrink-0">4. Bảng Từ điển chuẩn hóa</span>
          <span class="text-slate-700 text-[11.5px] flex-1">Ánh xạ cụm âm vị sang tên riêng &amp; từ vay mượn chuẩn (<i>Đắk Lắk, cafe</i>)</span>
        </div>
        <div class="p-2 rounded-lg bg-white border-l-4 border-rose-600 border border-slate-300 flex items-center justify-between">
          <span class="font-extrabold text-rose-900 w-48 shrink-0">5. Bảng Nhật ký đánh giá</span>
          <span class="text-slate-700 text-[11.5px] flex-1">Lưu vết lịch sử nhận diện, tỷ lệ lỗi từ (WER), lỗi dấu thanh &amp; thời gian xử lý</span>
        </div>
      </div>
    </div>
    <div class="mt-2.5 p-2.5 rounded-xl bg-teal-900 text-white flex items-center justify-between">
      <div>
        <div class="font-extrabold text-xs text-teal-200">Đồng bộ với Chỉ mục Vector Âm học FAISS (6.652 Vector 768-D)</div>
        <div class="text-[11px]">Truy vấn lân cận ngữ âm kết hợp lọc siêu dữ liệu chỉ mất <b>0,2 ms / từ</b></div>
      </div>
      <span class="badge-pill px-2.5 py-1 rounded bg-teal-400 text-slate-950 text-xs font-black">0,2 ms / từ</span>
    </div>
  </div>
</div>

<!--
[2:45 - 3:30 | 45 giây]
Slide 5 mô tả cách nhóm chuyển hóa kho 6.652 tệp âm tiết rời rạc thành Hệ cơ sở dữ liệu đa phương tiện hai tầng:
- Ở cột bên trái, mỗi mẫu thu âm mã hóa VNI được giải mã tự động sang chữ Quốc ngữ chuẩn, bóc tách rõ âm đầu, vần, âm cuối, dấu thanh và trích xuất thành vector đặc trưng 768 chiều chuẩn hóa L2.
- Ở cột bên phải, siêu dữ liệu được tổ chức thành 5 bảng quan hệ trong SQLite: Bảng Nhãn âm tiết, Bảng Mẫu âm thanh vật lý, Bảng Quy tắc biến âm theo ngữ cảnh, Bảng Từ điển chuẩn hóa thực thể và Bảng Nhật ký đánh giá.
Đặc biệt, khóa chính của Bảng Mẫu âm thanh được đồng bộ 1-1 với định danh vector trong FAISS, cho phép truy vấn chéo giữa ngữ nghĩa quan hệ và độ tương đồng âm học chỉ trong 0,2 mili-giây mỗi từ.
-->

---
transition: slide-left
---

# 5. Đối sánh Lân cận Ngữ âm & Học nhãn Tức thì (Giải quyết Vấn đề 3 & 4)

<div class="grid grid-cols-2 gap-4 text-xs mt-1 text-slate-900">
  <div class="p-3.5 rounded-xl bg-emerald-50 border-2 border-emerald-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-2.5">
        <span class="font-extrabold text-sm text-emerald-950">3. Đối sánh Ngữ âm &amp; Chuẩn hóa Thực thể</span>
      </div>
      <div class="space-y-2 mb-3">
        <div class="p-2 rounded-lg bg-white border border-emerald-300 flex items-center gap-2.5">
          <span class="badge-pill px-2 py-0.5 rounded bg-emerald-700 text-white text-[11px]">Bước 1</span>
          <div>
            <div class="font-bold text-emerald-950">Khoanh vùng ứng viên biến âm vùng miền</div>
            <div class="text-[11px] text-slate-600">Lọc nhóm từ có hoán âm đầu (<i>r↔g, th↔h, tr↔ch</i>) &amp; âm cuối (<i>-t↔-c</i>)</div>
          </div>
        </div>
        <div class="p-2 rounded-lg bg-white border border-emerald-300 flex items-center gap-2.5">
          <span class="badge-pill px-2 py-0.5 rounded bg-emerald-700 text-white text-[11px]">Bước 2</span>
          <div>
            <div class="font-bold text-emerald-950">Kết hợp điểm tương đồng FAISS + Ngữ cảnh</div>
            <div class="text-[11px] text-slate-600">Tổng hợp độ giống giọng đọc trên FAISS với quy tắc từ liền kề trong SQLite</div>
          </div>
        </div>
        <div class="p-2 rounded-lg bg-white border border-emerald-300 flex items-center gap-2.5">
          <span class="badge-pill px-2 py-0.5 rounded bg-emerald-700 text-white text-[11px]">Bước 3</span>
          <div>
            <div class="font-bold text-emerald-950">Chuẩn hóa thực thể theo khung âm vị</div>
            <div class="text-[11px] text-slate-600">Quy đồng các biến thể phát âm về tên địa danh và từ vay mượn chuẩn</div>
          </div>
        </div>
      </div>
    </div>
    <div class="p-2.5 rounded-xl bg-white border-2 border-emerald-400">
      <div class="text-[11px] font-extrabold text-emerald-900 mb-1.5">Minh họa sửa lỗi thực tế từ Nhật ký Thực nghiệm (Trước ➔ Sau):</div>
      <div class="grid grid-cols-2 gap-1.5 text-[11.5px]">
        <div class="px-2 py-1 rounded bg-emerald-50 border border-emerald-200 flex justify-between">
          <span class="text-red-700 line-through">“hấp lên / hầm lặng”</span>
          <span class="font-extrabold text-emerald-800">➔ “thắp lên / thầm lặng”</span>
        </div>
        <div class="px-2 py-1 rounded bg-emerald-50 border border-emerald-200 flex justify-between">
          <span class="text-red-700 line-through">“nét hư hả”</span>
          <span class="font-extrabold text-emerald-800">➔ “nét thư thả”</span>
        </div>
        <div class="px-2 py-1 rounded bg-purple-50 border border-purple-200 flex justify-between">
          <span class="text-red-700 line-through">“một ly phim”</span>
          <span class="font-extrabold text-purple-800">➔ “một ly phin”</span>
        </div>
        <div class="px-2 py-1 rounded bg-purple-50 border border-purple-200 flex justify-between">
          <span class="text-red-700 line-through">“chết lại / cà phê”</span>
          <span class="font-extrabold text-purple-800">➔ “check lại / cafe”</span>
        </div>
      </div>
    </div>
  </div>
  <div class="p-3.5 rounded-xl bg-rose-50 border-2 border-rose-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-2.5">
        <span class="font-extrabold text-sm text-rose-950">4. Vòng lặp Học nhãn Tức thì</span>
      </div>
      <div class="space-y-1.5 mb-3">
        <div class="p-2 rounded-lg bg-white border border-rose-300 flex items-center gap-2.5">
          <span class="badge-pill px-2 py-0.5 rounded bg-rose-700 text-white text-[11px]">Bước 1</span>
          <span class="font-semibold text-slate-800"><b>Tách đoạn sóng âm của từ sai:</b> Cắt chính xác theo mốc thời gian từ</span>
        </div>
        <div class="p-2 rounded-lg bg-white border border-rose-300 flex items-center gap-2.5">
          <span class="badge-pill px-2 py-0.5 rounded bg-rose-700 text-white text-[11px]">Bước 2</span>
          <span class="font-semibold text-slate-800"><b>Đánh dấu từ khóa ưu tiên:</b> Bật cờ ưu tiên cho nhãn đúng trong SQLite</span>
        </div>
        <div class="p-2 rounded-lg bg-white border border-rose-300 flex items-center gap-2.5">
          <span class="badge-pill px-2 py-0.5 rounded bg-rose-700 text-white text-[11px]">Bước 3</span>
          <span class="font-semibold text-slate-800"><b>Chèn nóng vector 768-D vào FAISS:</b> Bổ sung trực tiếp ngay trên RAM</span>
        </div>
        <div class="p-2 rounded-lg bg-white border border-rose-300 flex items-center gap-2.5">
          <span class="badge-pill px-2 py-0.5 rounded bg-rose-700 text-white text-[11px]">Bước 4</span>
          <span class="font-semibold text-slate-800"><b>Lưu quy tắc ngữ cảnh vào SQLite:</b> Ghi nhớ cặp từ đứng liền trước/sau</span>
        </div>
      </div>
    </div>
    <div class="p-3 rounded-xl bg-rose-900 text-white">
      <div class="text-[11px] text-rose-200 font-semibold mb-1">Thực nghiệm dạy lại từ sai trên giao diện Web:</div>
      <div class="flex items-center justify-between text-xs font-bold mb-1">
        <span>Sửa: <i>“chạy theo <u class="text-amber-300">lược</u> thích”</i></span>
        <span>➔ Dạy nhãn đúng: <b class="text-emerald-300">“lượt”</b></span>
      </div>
      <div class="px-2.5 py-1 rounded bg-rose-950/80 border border-rose-400 text-[11px] flex items-center justify-between">
        <span>Tự động thêm <b>Vector #6.653</b><br> (Cosine <b>0,965</b>)</span>
        <span class="text-emerald-300 font-extrabold">➔ Nhận đúng 100% ngay sau đó</span>
      </div>
    </div>
  </div>
</div>

<!--
[3:30 - 4:10 | 40 giây]
Slide 6 trình bày cơ chế giải quyết thách thức phương ngữ và học bổ sung nhãn tức thì:
- Ở cột bên trái, thay vì tìm kiếm mù quáng trên toàn bộ CSDL, hệ thống khoanh vùng các ứng viên biến âm vùng miền như r sang g, th sang h, kết hợp điểm tương đồng FAISS với ngữ cảnh từ liền kề và chuẩn hóa theo khung âm vị. Nhờ đó các lỗi như "mưa gào", "hành công", "đắc lắc", "cà phê" được sửa chính xác thành "mưa rào", "thành công", "Đắk Lắk" và "cafe".
- Ở cột bên phải là vòng lặp học nhãn tức thì 4 bước khi người dùng sửa một từ sai: tự động cắt đoạn sóng âm của từ đó, đánh dấu từ khóa ưu tiên, chèn nóng vector 768 chiều mới vào FAISS và lưu quy tắc ngữ cảnh vào SQLite. Thực nghiệm sửa từ "lược thích" thành "lượt thích" tự động sinh mẫu vector thứ 6.653 với độ tương đồng 0,965 và nhận diện đúng 100% ở lần chạy tiếp theo.
-->

---
transition: slide-left
---

# 6. Kết quả Thực nghiệm & Kết luận

<div class="grid grid-cols-4 gap-3 mb-2.5 text-center">
  <div class="p-2 rounded-xl bg-sky-50 border-2 border-sky-400">
    <div class="text-lg font-black text-sky-800">8,7% ➔ 0,0%</div>
    <div class="text-[11px] font-bold text-slate-700">Câu đọc ngắt chữ (44,9s ➔ 12,8s)</div>
  </div>
  <div class="p-2 rounded-xl bg-emerald-50 border-2 border-emerald-400">
    <div class="text-lg font-black text-emerald-800">3,8% ➔ 2,3%</div>
    <div class="text-[11px] font-bold text-slate-700">Phương ngữ Nam Bộ (Đúng 97,7%)</div>
  </div>
  <div class="p-2 rounded-xl bg-purple-50 border-2 border-purple-400">
    <div class="text-lg font-black text-purple-800">23,8% ➔ 17,5%</div>
    <div class="text-[11px] font-bold text-slate-700">Giao tiếp &amp; Từ mượn (Sửa 5 cụm)</div>
  </div>
  <div class="p-2 rounded-xl bg-amber-50 border-2 border-amber-400">
    <div class="text-lg font-black text-amber-800">25,0% ➔ 15,0%</div>
    <div class="text-[11px] font-bold text-slate-700">Câu ngắn Thực thể &amp; Địa danh</div>
  </div>
</div>

<table class="w-full text-xs border-collapse mb-2.5 bg-white border-2 border-slate-300 rounded-lg overflow-hidden text-slate-900">
  <thead>
    <tr class="border-b-2 border-slate-300 text-left bg-slate-100">
      <th class="text-slate-900">Kịch bản kiểm thử tiêu biểu (Trong 8 mẫu thực nghiệm)</th>
      <th class="text-center text-slate-900">Thời lượng</th>
      <th class="text-center text-red-800">WhisperX gốc</th>
      <th class="text-center text-emerald-800">Hệ thống <br> đề xuất</th>
      <th class="text-center text-blue-800">Hiệu quả khắc phục</th>
    </tr>
  </thead>
  <tbody>
    <tr class="border-b border-slate-200">
      <td class="font-bold">1. Giọng đọc liền mạch &amp; ngắt nhịp ngắn (Mẫu 1 &amp; 3 • 23 &amp; 16 từ)</td>
      <td class="text-center font-semibold">6,6s &amp; 15,2s</td>
      <td class="text-center font-semibold">0,0%</td>
      <td class="text-center font-extrabold text-emerald-800">0,0%</td>
      <td class="text-center text-blue-800 font-bold">Bảo toàn 100% độ chuẩn</td>
    </tr>
    <tr class="border-b border-slate-200 bg-emerald-50/70">
      <td class="font-bold">2. Giọng đọc ngắt từng chữ rời rạc (Mẫu 2 • 23 từ)</td>
      <td class="text-center font-semibold">44,9s ➔ 12,8s</td>
      <td class="text-center text-red-800 font-bold">8,70%</td>
      <td class="text-center font-extrabold text-emerald-800">0,00%</td>
      <td class="text-center text-emerald-800 font-extrabold">Khắc phục 100% lỗi (RTF 0,084)</td>
    </tr>
    <tr class="border-b border-slate-200">
      <td class="font-bold">3. Đoạn văn phương ngữ Nam Bộ (Mẫu 4 • 132 từ: <i>hấp➔thắp, hầm➔thầm</i>)</td>
      <td class="text-center font-semibold">48,65 giây</td>
      <td class="text-center text-red-800 font-bold">3,79%</td>
      <td class="text-center font-extrabold text-emerald-800">2,27%</td>
      <td class="text-center text-blue-800 font-bold">Khắc phục 40,1% lỗi (Đúng 97,7%)</td>
    </tr>
    <tr class="border-b border-slate-200 bg-emerald-50/70">
      <td class="font-bold">4. Giao tiếp công việc &amp; từ mượn (Mẫu 5 • 80 từ: <i>ly phin, check, thư thả</i>)</td>
      <td class="text-center font-semibold">35,97 giây</td>
      <td class="text-center text-red-800 font-bold">23,75%</td>
      <td class="text-center font-extrabold text-emerald-800">17,50%</td>
      <td class="text-center text-emerald-800 font-extrabold">Khắc phục 26,3% lỗi (Sửa 5 cụm)</td>
    </tr>
    <tr class="border-b border-slate-200">
      <td class="font-bold">5. Câu giao tiếp ngắn chứa thực thể &amp; từ mượn (Mẫu 6 • 20 từ: <i>cafe</i>)</td>
      <td class="text-center font-semibold">8,18 giây</td>
      <td class="text-center text-red-800 font-bold">25,00%</td>
      <td class="text-center font-extrabold text-emerald-800">15,00%</td>
      <td class="text-center text-blue-800 font-bold">Khắc phục 40,0% lỗi</td>
    </tr>
    <tr class="bg-blue-100/90 font-extrabold border-t-2 border-slate-300">
      <td class="text-slate-900">Trung bình nhóm 6 mẫu tiêu biểu (và toàn tập 8 mẫu / 508 từ)</td>
      <td class="text-center">RTF ≈ 0,23</td>
      <td class="text-center text-red-800">10,21% (10,82%)</td>
      <td class="text-center text-emerald-800">5,80% (7,74%)</td>
      <td class="text-center text-blue-900">Giảm 43,2% (28,5%) tổng lỗi</td>
    </tr>
  </tbody>
</table>
<div class="pb-2"></div>
  <div class="p-2 rounded-xl bg-blue-50 border-2 border-blue-300 flex items-center justify-between gap-3">
    <div>
      <div class="font-extrabold text-blue-950 mb-0.5">Phát hiện thực nghiệm &amp; Kết luận cốt lõi</div>
      <div class="text-[11.5px] text-slate-800">
        • <b>Quy tắc CSDL (MMDBMS) sửa đúng 100% biến âm</b> (<i>hự➔thực, trung➔truân, bụng➔bộn, cuối➔cúi</i>), an toàn hơn hẳn tự sửa bằng CTC tự do.<br/>
        • Đồng bộ 1–1 SQLite &amp; FAISS 768-D (0 MB VRAM phụ) • Căn chỉnh chuẩn ±15 ms • Học nhãn tức thì &lt; 50 ms.
      </div>
    </div>
  </div>

<!--
[4:10 - 5:00 | 50 giây]
Slide cuối cùng tổng hợp kết quả thực nghiệm đối sánh trên 8 mẫu âm thanh kiểm thử thực tế (508 từ):
- Ở câu đọc ngắt từng chữ kéo dài 44,9 giây, thuật toán nén khoảng lặng thu gọn về 12,8 giây và giảm tỷ lệ lỗi từ 8,70% xuống 0,00%.
- Ở đoạn văn 132 từ phương ngữ Nam Bộ, CSDL tự động sửa chính xác các từ biến âm như "hấp" thành "thắp", "hầm" thành "thầm", kéo WER từ 3,79% xuống chỉ còn 2,27% (độ chính xác đạt 97,73%).
- Ở kịch bản giao tiếp công việc có từ vay mượn và biến âm, hệ thống sửa đúng đồng thời "cà phê" thành "cafe", "ly phim" thành "ly phin", "chết lại" thành "check lại" và "hư hả" thành "thư thả", giảm lỗi từ 23,75% xuống 17,50%.
- Đặc biệt, phân tích trên 2 mẫu văn học Hán-Việt chứng minh tầng quy tắc CSDL MMDBMS sửa đúng 100% các từ biến âm, an toàn và chính xác hơn hẳn việc dùng mô hình CTC tự do không ràng buộc từ điển.
Nhóm xin cảm ơn Thầy và các bạn đã lắng nghe, rất mong nhận được ý kiến đóng góp ạ!
-->
