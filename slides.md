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
    <span class="text-blue-700">KẾT HỢP WHISPERX, WAV2VEC2-VI VÀ CSDL SQLITE + FAISS 768-D</span>
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
[0:00 - 0:25 | 25 giây]
Em xin kính chào Thầy và các bạn. Hôm nay nhóm chúng em gồm Nguyễn Trần Anh Khoa, Nguyễn Quốc Dửng và Nguyễn Thành Đạt xin trình bày đề tài môn Hệ Quản trị Cơ sở dữ liệu Đa phương tiện: "Xây dựng Hệ thống Chuyển đổi Âm thanh thành Văn bản Tiếng Việt trên nền tảng Cơ sở dữ liệu Đa phương tiện kết hợp WhisperX, Wav2Vec2-VI và Chỉ mục Vector Âm học FAISS 768 chiều".
-->

---
transition: slide-left
---

# 1. Định hướng và giải pháp thực hiện

<div class="flex items-center justify-between px-3.5 py-1.5 mb-2.5 rounded-lg bg-blue-100 border-2 border-blue-300 text-slate-900 text-xs">
  <span><b>Định hướng cốt lõi:</b> Kết hợp sức mạnh ngữ cảnh của AI với Hệ CSDL Đa phương tiện (<b>SQLite + FAISS</b>) để kiểm định, sửa lỗi phương ngữ, chuẩn hóa địa danh và học nhãn bổ sung tức thì.</span>
</div>

<div class="grid grid-cols-2 gap-3 text-xs text-slate-900">
  <div class="p-3 rounded-xl bg-sky-50 border-2 border-sky-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between gap-2 mb-1.5">
        <span class="font-extrabold text-[17px] text-sky-900">1. Áp dụng modal WhisperX</span>
        <span class="badge-pill px-2 py-0.5 rounded bg-sky-200 text-sky-900 text-[10px]">Tầng Nhận dạng</span>
      </div>
      <p class="text-slate-800 text-[14.5px] font-medium leading-snug">
        Tích hợp <b>Whisper Large-v3 (FP16)</b>, được dùng ở bước đầu nhận dạng, và kết hợp để tinh chỉnh bộ giải mã bằng <b>Hotwords động</b> từ CSDL, <b>Prompt khởi tạo.</b>
      </p>
    </div>
    <div class="mt-2.5 px-2.5 py-1.5 rounded-lg bg-white border border-sky-300 flex items-center justify-between text-[11.5px] font-bold">
      <span class="text-sky-800">Mô hình nền tảng</span>
      <span class="text-emerald-800">➔ Xử lý phần lớn Tiếng Việt</span>
    </div>
  </div>
  <div class="p-3 rounded-xl bg-indigo-50 border-2 border-indigo-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between gap-2 mb-1.5">
        <span class="font-extrabold text-[17px] text-indigo-900">2. Mô hình Wav2Vec2-VI Validate</span>
        <span class="badge-pill px-2 py-0.5 rounded bg-indigo-200 text-indigo-900 text-[10px]">Tầng Kiểm định</span>
      </div>
      <p class="text-slate-800 text-[14.5px] font-medium leading-snug">
        Được dùng để căn chỉnh mốc <b>[Start, End]</b> và đo <b>điểm vật lý CTC mỗi 20ms</b> nhằm phát hiện các từ đặc thù bị mô hình nhận diện sai.
      </p>
    </div>
    <div class="mt-2.5 px-2.5 py-1.5 rounded-lg bg-white border border-indigo-300 flex items-center justify-between text-[11.5px] font-bold">
      <span class="text-indigo-800">Tính điểm CTC</span>
      <span class="text-emerald-800">➔ Phát hiện biến âm</span>
    </div>
  </div>
  <div class="p-3 rounded-xl bg-amber-50 border-2 border-amber-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between gap-2 mb-1.5">
        <span class="font-extrabold text-[17px] text-amber-900">3. MMDBMS với SQLite + FAISS</span>
        <span class="badge-pill px-2 py-0.5 rounded bg-amber-200 text-amber-900 text-[10px]">Tầng lưu trữ</span>
      </div>
      <p class="text-slate-800 text-[14.5px] font-medium leading-snug">
        Kết hợp <b>FAISS 768-D</b> (<b>6.652</b> mẫu âm tiết chuẩn đã được vector hóa) và <b>SQLite 5 bảng</b> (thêm luật ngữ cảnh phương ngữ &amp; <b>1.564</b> biến thể địa danh, từ mượn).
      </p>
    </div>
    <div class="mt-2.5 px-2.5 py-1.5 rounded-lg bg-white border border-amber-300 flex items-center justify-between text-[11.5px] font-bold">
      <span class="text-amber-900">Đối soát Vector + SQL</span>
      <span class="text-emerald-800">➔ Chuẩn hóa: Đắk Lắk, Kon Tum, cafe, check lại</span>
    </div>
  </div>
  <div class="p-3 rounded-xl bg-emerald-50 border-2 border-emerald-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between gap-2 mb-1.5">
        <span class="font-extrabold text-[17px] text-emerald-900">4. Bổ sung nhãn</span>
        <span class="badge-pill px-2 py-0.5 rounded bg-emerald-200 text-emerald-900 text-[10px]">Active Learning</span>
      </div>
      <p class="text-slate-800 text-[14.5px] font-medium leading-snug">
        Khi từ bị thiếu nhãn hoặc sai, hệ thống <b>tự cắt sóng âm [Start, End]</b> lưu <code>.wav</code>, trích xuất vector 768-D <b>nạp nóng vào FAISS</b> &amp; tạo luật ngữ cảnh trong <b>SQLite</b>.
      </p>
    </div>
    <div class="mt-2.5 px-2.5 py-1.5 rounded-lg bg-white border border-emerald-300 flex items-center justify-between text-[11.5px] font-bold">
      <span class="text-emerald-800">Cơ chế mở rộng đơn giản</span>
      <span class="text-blue-800">➔ Cập nhật nóng, không cần Train lại</span>
    </div>
  </div>
</div>

<!--
[0:25 - 1:05 | 40 giây]
Thưa Thầy, để giải quyết bài toán nhận dạng giọng nói tiếng Việt có phương ngữ Nam Bộ, địa danh đặc thù và từ thiếu nhãn, nhóm em triển khai 4 trụ cột giải pháp thực hiện:
- Thứ nhất, áp dụng mô hình WhisperX (Large-v3) và tinh chỉnh bộ giải mã bằng Hotwords động từ CSDL cùng thuật toán nén khoảng lặng để phù hợp đặc thù tiếng Việt.
- Thứ hai, tích hợp thêm mô hình Wav2Vec2 tiếng Việt (khôi phục đủ 21 tầng đặc trưng) để đo điểm tin cậy vật lý CTC trên từng khung 20ms, giúp validate và phát hiện ngay các từ đặc thù mà mô hình đầu tiên nhận diện sai.
- Thứ ba, xây dựng kiến trúc lưu trữ đa phương tiện kết hợp SQLite (5 bảng) và FAISS Vector 768 chiều, khai thác 6.652 mẫu âm thanh quoc_dung và bộ từ điển 1.564 biến thể để sửa lỗi biến âm cũng như chuẩn hóa địa danh Đắk Lắk, Kon Tum, cafe.
- Thứ tư, thiết kế cơ chế bổ sung nhãn tức thì khi từ bị thiếu nhãn: tự động cắt sóng âm nạp nóng vào FAISS và SQLite để cải thiện ngay lập tức dưới 0,2 giây mà không cần huấn luyện lại.
-->

---
transition: slide-left
---

# 2. Kiến trúc hệ thống

<div class="flex justify-center items-center w-full mt-1">
  <img src="/overview_sys.png" alt="Kiến trúc Hệ thống Lai ghép 4 Tầng" class="max-h-[475px] w-auto object-contain rounded-lg" />
</div>

<!-- <div class="grid grid-cols-3 gap-3 mt-1.5 text-xs text-slate-900">
  <div class="p-2 rounded-xl bg-indigo-50 border-2 border-indigo-300">
    <div class="font-extrabold text-indigo-950 text-[13px] mb-0.5">1. Đồng thuận Hai luồng (Dual-Stream)</div>
    <div class="text-[11.5px] text-slate-700 leading-snug">Kết hợp ngữ cảnh toàn câu của <b>Whisper Large-v3</b> với kiểm định dao động vật lý mỗi <b>20ms</b> của <b>Wav2Vec2-VI</b>.</div>
  </div>
  <div class="p-2 rounded-xl bg-amber-50 border-2 border-amber-300">
    <div class="font-extrabold text-amber-950 text-[13px] mb-0.5">2. Phân luồng Kiểm định Thông minh</div>
    <div class="text-[11.5px] text-slate-700 leading-snug">Giữ nguyên các từ phát âm chuẩn (<b>CTC &ge; 0,85</b>); chỉ kích hoạt CSDL cứu viện khi điểm CTC thấp hoặc gặp biến âm.</div>
  </div>
  <div class="p-2 rounded-xl bg-emerald-50 border-2 border-emerald-300">
    <div class="font-extrabold text-emerald-950 text-[13px] mb-0.5">3. Vòng lặp Học Nhãn Khép Kín</div>
    <div class="text-[11.5px] text-slate-700 leading-snug">Từ đầu ra trên Web UI, người dùng sửa từ sai sẽ tự động cắt sóng âm nạp ngược vào <b>FAISS</b> &amp; <b>SQLite</b> (&lt; 0,2s).</div>
  </div>
</div> -->

<!--
[1:05 - 1:50 | 45 giây]
Slide 3 trình bày sơ đồ kiến trúc hệ thống lai ghép 4 tầng theo mô hình Acoustic RAG:
- Tầng 1 (Tiền xử lý DSP): Chuẩn hóa âm thanh 16kHz Mono, lọc thông dải Butterworth 75 đến 7600Hz và tự động nén các khoảng lặng dài về 0,14 giây kèm bảng TimeMap để bảo toàn tọa độ gốc.
- Tầng 2 (Nhận dạng ngữ cảnh): Sử dụng Whisper Large-v3 kết hợp danh sách Hotwords ưu tiên từ SQLite để sinh văn bản thô.
- Tầng 3 (Kiểm định Wav2Vec2-VI): Căn chỉnh mốc thời gian [Start, End] của từng từ và đo điểm tin cậy vật lý CTC trên từng lát cắt 20ms.
- Tầng 4 (Trọng tài Đa phương tiện MMDBMS): Những từ phát âm rõ, điểm CTC từ 0,85 trở lên sẽ đi thẳng tới bước chuẩn hóa thực thể. Những từ có điểm CTC thấp hoặc thuộc diện nghi vấn phương ngữ sẽ được trích xuất vector 768 chiều để đối soát qua FAISS và SQLite trước khi xuất ra văn bản chuẩn xác.
-->

---
hide: true
transition: slide-left
---

# 3. Tiền xử lý Tín hiệu DSP & Kiểm định Wav2Vec2-VI (Tầng 1 – 3)

<div class="grid grid-cols-2 gap-4 text-xs mt-1 text-slate-900">
  <div class="p-3.5 rounded-xl bg-sky-50 border-2 border-sky-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-2.5">
        <span class="font-extrabold text-xl text-sky-950">1. Nén khoảng lặng thích nghi + TimeMap</span>
      </div>
      <div class="space-y-2">
        <div class="p-2.5 rounded-lg bg-white border border-sky-300 flex items-start gap-2.5">
          <span class="font-black text-sky-700 text-sm shrink-0">B1:</span>
          <div>
            <div class="font-extrabold text-[13.5px] leading-snug text-sky-950">Phát hiện khoảng nghỉ dài &ge; 320 ms</div>
            <div class="text-[12px] leading-snug text-slate-600 mt-0.5">Tự động nhận diện câu đọc rời rạc từng chữ (tỷ lệ lặng &ge; 35%)</div>
          </div>
        </div>
        <div class="p-2.5 rounded-lg bg-white border border-sky-300 flex items-start gap-2.5">
          <span class="font-black text-sky-700 text-sm shrink-0">B2:</span>
          <div>
            <div class="font-extrabold text-[13.5px] leading-snug text-sky-950">Thu gọn mượt về 140 ms + Đệm biên 60 ms</div>
            <div class="text-[12px] leading-snug text-slate-600 mt-0.5">Nối liền mạch ngữ cảnh cho Whisper, giữ trọn phụ âm cuối (<i>-p, -t, -c</i>)</div>
          </div>
        </div>
        <div class="p-2.5 rounded-lg bg-white border border-sky-300 flex items-start gap-2.5">
          <span class="font-black text-sky-700 text-sm shrink-0">B3:</span>
          <div>
            <div class="font-extrabold text-[13.5px] leading-snug text-sky-950">Nội suy tuyến tính từng khúc (Piecewise TimeMap)</div>
            <div class="text-[12px] leading-snug text-slate-600 mt-0.5">Ánh xạ ngược mốc <code>[Start, End]</code> từ audio nén về đúng thời gian gốc</div>
          </div>
        </div>
      </div>
    </div>
    <div class="mt-3 p-3 rounded-xl bg-sky-900 text-white">
      <div class="text-[11px] text-sky-200 font-semibold mb-1">Thực nghiệm trên file đọc ngắt từng chữ (<code>ai_sentence_paced.wav</code>):</div>
      <div class="flex items-center justify-between text-xs font-extrabold">
        <span>Thời lượng: 44,9s ➔ 12,8s (RTF 0,084)</span>
        <span class="px-2.5 py-0.5 rounded bg-emerald-400 text-slate-950">Lỗi WER: 8,70% ➔ 0,00%</span>
      </div>
    </div>
  </div>
  <div class="p-3.5 rounded-xl bg-indigo-50 border-2 border-indigo-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-2.5">
        <span class="font-extrabold text-xl text-indigo-950">2. Validate bằng Điểm Vật lý CTC 20ms</span>
      </div>
      <div class="p-2.5 rounded-lg bg-white border border-indigo-300 mb-2.5">
        <div class="text-[11.5px] font-bold text-indigo-900 mb-1.5">Cơ chế đo xác suất âm vị mỗi 20ms sóng âm (50 khung/giây):</div>
        <div class="flex items-center justify-between gap-1.5 text-center font-extrabold text-[11px]">
          <div class="p-2 rounded-lg bg-slate-100 border border-slate-300 flex-1">
            Tầng ẩn Wav2Vec2<br/><span class="text-indigo-700">Vector 768-D</span>
          </div>
          <span class="text-indigo-700 text-sm">➔</span>
          <div class="p-2 rounded-lg bg-indigo-100 border-2 border-indigo-400 text-indigo-950 flex-[1.35]">
            Khôi phục 21 tầng<br/><code>feature_transform</code>
          </div>
          <span class="text-indigo-700 text-sm">➔</span>
          <div class="p-2 rounded-lg bg-emerald-100 border-2 border-emerald-400 text-emerald-950 flex-1">
            Chấm điểm CTC<br/>&amp; Biên <code>[Start, End]</code>
          </div>
        </div>
      </div>
      <table class="w-full text-xs border-collapse bg-white rounded-lg overflow-hidden border-2 border-indigo-300">
        <thead>
          <tr class="border-b-2 border-indigo-200 text-left bg-indigo-100">
            <th class="text-indigo-950">Tiêu chí kỹ thuật</th>
            <th class="text-red-800">WhisperX gốc</th>
            <th class="text-emerald-800">Hệ thống đề xuất</th>
          </tr>
        </thead>
        <tbody>
          <tr class="border-b border-slate-200">
            <td class="font-bold">Điểm kiểm định CTC</td>
            <td class="text-red-700 font-bold">0,009 (Suy biến)</td>
            <td class="font-extrabold text-emerald-800">0,50 – 0,98 (Phân loại chuẩn)</td>
          </tr>
          <tr class="border-b border-slate-200 bg-emerald-50/40">
            <td class="font-bold">Độ chính xác biên từ</td>
            <td class="text-red-700 font-bold">Lệch dồn về 0,3s</td>
            <td class="font-extrabold text-emerald-800">Khớp chuẩn ±15 ms</td>
          </tr>
          <tr>
            <td class="font-bold">VRAM trích xuất 768-D</td>
            <td class="text-red-700 font-bold">+1,2 GB (Tải 2 lần)</td>
            <td class="font-extrabold text-emerald-800">0 MB (Dùng chung Encoder)</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="mt-3 p-2.5 rounded-xl bg-indigo-900 text-white text-center text-xs font-bold">
      Từ bị Whisper đoán mò lệch sóng âm ➔ Điểm CTC tụt (&lt; 0,85) ➔ Chuyển sang CSDL sửa!
    </div>
  </div>
</div>

<!--
[1:50 - 2:35 | 45 giây]
Slide 4 đi sâu vào cơ chế xử lý tín hiệu và dùng Wav2Vec2-VI để validate kết quả của Whisper:
- Ở cột bên trái, khi người dùng đọc ngắt từng chữ rời rạc, các khoảng lặng dài trên 320ms được thu gọn mượt về 140ms kèm đệm biên 60ms để không mất phụ âm cuối, sau đó nội suy trả lại đúng tọa độ gốc qua bảng TimeMap. Thực nghiệm trên file đọc ngắt chữ dài 44,9 giây đã rút ngắn thời gian xử lý xuống 12,8 giây và kéo tỷ lệ lỗi WER từ 8,7% về 0%.
- Ở cột bên phải, nhóm khôi phục đủ 21 tầng biến đổi đặc trưng tiếng Việt cho mô hình Wav2Vec2-VI. Cứ mỗi 20ms sóng âm (tức 50 lần mỗi giây), mô hình đo trực tiếp xác suất âm vị trên phổ tần số. Nếu Whisper đoán mò một từ không khớp với dao động vật lý thực tế, điểm CTC của từ đó sẽ tụt xuống dưới 0,85, lập tức kích hoạt tầng CSDL Đa phương tiện vào cuộc.
-->

---
transition: slide-left
---

# 4. Thiết kế CSDL Đa phương tiện Hai tầng (SQLite + FAISS 768-D)

<div class="flex justify-center items-center w-full mt-1">
  <img src="/db_sys.png" alt="Thiết kế CSDL Đa phương tiện Hai tầng (SQLite + FAISS 768-D)" class="max-h-[485px] w-auto object-contain rounded-lg" />
</div>

<!-- <div class="grid grid-cols-2 gap-3 mt-1.5 text-xs text-slate-900">
  <div class="px-3 py-1.5 rounded-lg bg-amber-50 border-2 border-amber-300 flex items-center justify-between">
    <span><b>Vai trò kho <code>quoc_dung</code> (6.652 file .wav):</b> Ngân hàng Âm học Chuẩn tham chiếu (Ground-Truth Acoustic Reference Bank) để nắn chỉnh phát âm lệch.</span>
  </div>
  <div class="px-3 py-1.5 rounded-lg bg-emerald-50 border-2 border-emerald-300 flex items-center justify-between">
    <span><b>Liên kết Chéo 1–1 (Cross-Modal Link):</b> Mỗi vector 768 chiều trong <code>FAISS</code> ánh xạ chính xác 1–1 với <code>audio_samples</code> trong <code>SQLite</code> qua <code>sample_id</code>.</span>
  </div>
</div> -->

<!--
[2:35 - 3:25 | 50 giây]
Thưa Thầy, Slide 5 thể hiện kiến trúc CSDL Đa phương tiện 2 tầng và cách nhóm khai thác kho dữ liệu 6.652 file âm thanh quoc_dung của Thầy:
- Ở nhánh bên trái, toàn bộ 6.652 file .wav đơn âm tiết trong kho quoc_dung được đưa qua tầng Transformer của Wav2Vec2 để trích xuất vùng lõi 0,45 giây thành 6.652 vector đặc trưng 768 chiều đã chuẩn hóa L2, nạp vào chỉ mục FAISS IndexFlatIP. Kho dữ liệu của Thầy đóng vai trò là Ngân hàng mẫu phát âm chuẩn tham chiếu.
- Ở nhánh giữa và phải, module syllable_decoder tự động giải mã tên file VNI (ví dụ d-uuoong2 thành chữ "đường", tách phụ âm, vần và dấu thanh từ 0 đến 5) để nạp vào bảng syllable_labels và audio_samples trong SQLite.
- Hai tầng FAISS và SQLite liên kết chặt chẽ 1-1 qua khóa chính sample_id. Đồng thời, SQLite còn quản lý bảng confusion_rules gồm 102 quy tắc ngữ cảnh phương ngữ và bảng canonical_lexicon chứa 1.564 biến thể địa danh, từ vay mượn được mở rộng tự động từ 963 thực thể gốc.
-->

---
transition: slide-left
---

# 5. Thuật toán tầng 4 CSDL và học nhãn

<div class="grid grid-cols-12 gap-3.5 text-xs mt-1 text-slate-900">
  <div class="col-span-7 p-3 rounded-xl bg-emerald-50 border-2 border-emerald-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-2">
        <span class="font-extrabold text-[17px] text-emerald-950">Thuật toán tầng CSDL</span>
        <span class="badge-pill px-2 py-0.5 rounded bg-emerald-200 text-emerald-900 text-[10px]">FAISS + SQLite</span>
      </div>
      <div class="space-y-1.5 mb-2">
        <div class="p-2 rounded-lg bg-white border border-emerald-300">
          <div class="font-extrabold text-[12.5px] text-emerald-950">Giai đoạn 1: Sửa lỗi phương ngữ mức âm tiết</div>
          <div class="text-[11.5px] text-slate-700 leading-snug mt-0.5">
            • <b>Khóa bảo vệ Code-Switching:</b> Giữ nguyên từ mượn tiếng Anh (<code>laptop</code>, <code>checklist</code>).<br/>
            <!-- • <b>Khoanh vùng Phonetic Cohort:</b> Lọc nhóm biến âm Nam Bộ (<i>th↔h, r↔g, tr↔ch, -t↔-c</i>) trước khi truy vấn Cosine trên <b>FAISS 768-D</b> để triệt tiêu nhiễu khác giọng người nói.<br/> -->
            • <b>Cách (<code>confusion_rules</code>) hoạt động:</b> Kiểm tra từ liền kề trái/phải và duyệt sửa khi:
          </div>
          <div class="formula-box mt-1 font-mono font-bold text-[11.5px] bg-emerald-50/60">
            S_hybrid = 0,50 × Sim_FAISS + B_ctx(left, right) &ge; 0,40 &nbsp;➔&nbsp; VD: "ngập tràn [hấp ➔ thắp] lên" (0,89)
          </div>
        </div>
        <div class="p-2 rounded-lg bg-white border border-purple-300">
          <div class="font-extrabold text-[12.5px] text-purple-950">Giai đoạn 2: Chuẩn hóa địa danh &amp; từ mượn (7-gram ➔ 1-gram ITN)</div>
          <div class="text-[11.5px] text-slate-700 leading-snug mt-0.5">
            • Quét cửa sổ trượt từ <b>7-gram xuống 1-gram</b> trên bảng <code>canonical_lexicon</code> (<b>1.564 mẫu</b>).<br/>
            • <b>Khóa âm vị bất biến <code>_to_phonetic_key</code>:</b> Quy đồng <i>“đắc lắc / đát lát / đắt lác”</i> về khóa <code>đac1 lac1</code> ➔ đổi thành <b>“Đắk Lắk”</b> và gộp mốc thời gian: <code>Start = Start(đắc), End = End(lắc)</code>.
          </div>
        </div>
      </div>
    </div>
    <div class="grid grid-cols-3 gap-1.5 text-[11.5px] font-bold text-center">
      <div class="p-1.5 rounded-lg bg-white border border-emerald-300">
        <span class="text-red-700 line-through">“hấp lên / hư hả”</span><br/>
        <span class="text-emerald-800 font-extrabold">➔ “thắp lên / thư thả”</span>
      </div>
      <div class="p-1.5 rounded-lg bg-white border border-purple-300">
        <span class="text-red-700 line-through">“đát lát / con tum”</span><br/>
        <span class="text-purple-800 font-extrabold">➔ “Đắk Lắk / Kon Tum”</span>
      </div>
      <div class="p-1.5 rounded-lg bg-white border border-blue-300">
        <span class="text-red-700 line-through">“cà phê / chết lại”</span><br/>
        <span class="text-blue-800 font-extrabold">➔ “cafe / check lại”</span>
      </div>
    </div>
  </div>
  <div class="col-span-5 p-3 rounded-xl bg-rose-50 border-2 border-rose-300 flex flex-col justify-between">
    <div>
      <div class="flex items-center justify-between mb-2">
        <span class="font-extrabold text-[17px] text-rose-950">Bổ sung nhãn</span>
        <span class="badge-pill px-2 py-0.5 rounded bg-rose-200 text-rose-900 text-[10px]">Zero Fine-tuning</span>
      </div>
      <div class="space-y-1.5 mb-2">
        <div class="p-2 rounded-lg bg-white border border-rose-300 flex items-center gap-2">
          <span class="font-black text-rose-700 text-xs shrink-0">B1:</span>
          <div class="text-[11.5px] leading-snug"><b class="text-rose-950">Auto-Slice Sóng âm:</b> Chọn <code>word_index</code> bị sai trên Web UI ➔ Tự cắt đoạn <code>[Start, End]</code> lưu thành file <code>.wav</code> chuẩn 16kHz.</div>
        </div>
        <div class="p-2 rounded-lg bg-white border border-rose-300 flex items-center gap-2">
          <span class="font-black text-rose-700 text-xs shrink-0">B2:</span>
          <div class="text-[11.5px] leading-snug"><b class="text-rose-950">Nạp nóng Vector FAISS:</b> Trích xuất vector 768-D, gọi <code>index.add_with_ids()</code> ngay trên RAM &amp; lưu đĩa.</div>
        </div>
        <div class="p-2 rounded-lg bg-white border border-rose-300 flex items-center gap-2">
          <span class="font-black text-rose-700 text-xs shrink-0">B3:</span>
          <div class="text-[11.5px] leading-snug"><b class="text-rose-950">Cập nhật SQLite:</b> Bật cờ <code>is_hotword=1</code> và tự sinh quy tắc ngữ cảnh (<code>boost=0.40</code>) vào <code>confusion_rules</code>.</div>
        </div>
        <div class="p-2 rounded-lg bg-white border border-rose-300 flex items-center gap-2">
          <span class="font-black text-rose-700 text-xs shrink-0">B4:</span>
          <div class="text-[11.5px] leading-snug"><b class="text-rose-950">Mở rộng Từ điển:</b> Hỗ trợ thêm trực tiếp địa danh/từ mượn mới trên Web UI hoặc đồng bộ từ file CSV.</div>
        </div>
      </div>
    </div>
    <div class="p-2.5 rounded-xl bg-rose-900 text-white">
      <div class="text-[11px] text-rose-200 font-semibold mb-0.5">Thực nghiệm dạy lại từ thiếu nhãn trên Web UI:</div>
      <div class="flex items-center justify-between text-[11.5px] font-bold mb-1">
        <span>Sửa: <i>“chạy theo <u class="text-amber-300">lược</u> thích”</i></span>
        <span>➔ Nhãn đúng: <b class="text-emerald-300">“lượt”</b></span>
      </div>
      <div class="px-2 py-1 rounded bg-rose-950/80 border border-rose-400 text-[10.5px] flex items-center justify-between">
        <span>Sinh thêm <b>Vector</b></span>
        <span class="text-emerald-300 font-extrabold">➔ Cải thiện tức thì!</span>
      </div>
    </div>
  </div>
</div>

<!--
[3:25 - 4:15 | 50 giây]
Slide 6 trình bày chi tiết thuật toán đối soát tại Tầng 4 và cơ chế học nhãn tức thì:
- Ở cột bên trái là thuật toán Trọng tài 2 giai đoạn:
  + Giai đoạn 1 sửa lỗi phương ngữ mức âm tiết: Sau khi khóa bảo vệ các từ tiếng Anh như laptop, checklist, hệ thống không tìm mù quáng trên toàn bộ 6.652 vector mà khoanh vùng vào Nhóm lân cận ngữ âm (Phonetic Cohort) theo quy luật biến âm như th sang h, r sang g. Khi FAISS trả về ứng viên "thắp" với độ tương đồng 0,88, Tòa án trọng tài SQLite sẽ kiểm tra ngữ cảnh từ liền kề và tính điểm lai ghép S_hybrid. Chỉ khi S_hybrid đạt từ 0,40 trở lên mới duyệt sửa "hấp" thành "thắp", ngăn chặn tuyệt đối lỗi sửa ẩu.
  + Giai đoạn 2 chuẩn hóa địa danh và từ vay mượn: Quét cửa sổ trượt từ 7-gram xuống 1-gram kết hợp Khóa âm vị bất biến. Nhờ đó dù người nói đọc là "đắc lắc" hay "đát lát", tất cả đều quy về khóa âm vị đac1 lac1 để đổi thành "Đắk Lắk", "con tum" thành "Kon Tum", "cà phê" thành "cafe" và gộp lại mốc thời gian [Start, End].
- Ở cột bên phải là vòng lặp Bổ sung nhãn tức thì trong 4 bước: Khi phát hiện một từ sai hoặc thiếu nhãn trên Web UI, hệ thống tự cắt đoạn sóng âm [Start, End] lưu thành file .wav, trích xuất vector 768 chiều nạp thẳng vào FAISS trong RAM và ghi luật ngữ cảnh vào SQLite, giúp nhận diện đúng 100% chỉ sau 0,2 giây.
-->

---
transition: slide-left
---

# 6. Kết quả thực nghiệm và Live Demo

<div class="grid grid-cols-4 gap-3 mb-2 text-center">
  <div class="p-2 rounded-xl bg-sky-50 border-2 border-sky-400">
    <div class="text-lg font-black text-sky-800">8,70% ➔ 0,00%</div>
    <div class="text-[11px] font-bold text-slate-700">Đọc ngắt từng chữ (44,9s ➔ 12,8s)</div>
  </div>
  <div class="p-2 rounded-xl bg-emerald-50 border-2 border-emerald-400">
    <div class="text-lg font-black text-emerald-800">3,79% ➔ 2,27%</div>
    <div class="text-[11px] font-bold text-slate-700">Phương ngữ Nam Bộ (Đúng 97,73%)</div>
  </div>
  <div class="p-2 rounded-xl bg-purple-50 border-2 border-purple-400">
    <div class="text-lg font-black text-purple-800">23,75% ➔ 17,50%</div>
    <div class="text-[11px] font-bold text-slate-700">Đa thực thể (Đắk Lắk, cafe, laptop)</div>
  </div>
  <div class="p-2 rounded-xl bg-amber-50 border-2 border-amber-400">
    <div class="text-lg font-black text-amber-800">&lt; 0,2 giây</div>
    <div class="text-[11px] font-bold text-slate-700">Học nhãn mới tức thì (Zero Train)</div>
  </div>
</div>

<table class="w-full text-xs border-collapse mb-2 bg-white border-2 border-slate-300 rounded-lg overflow-hidden text-slate-900">
  <thead>
    <tr class="border-b-2 border-slate-300 text-left bg-slate-100">
      <th class="text-slate-900">Kịch bản kiểm thử thực nghiệm (8 mẫu âm thanh thực tế)</th>
      <th class="text-center text-slate-900">Thời lượng</th>
      <th class="text-center text-red-800">WhisperX gốc</th>
      <th class="text-center text-emerald-800">Hybrid + MMDBMS</th>
      <th class="text-center text-blue-800">Hiệu quả khắc phục (ERR)</th>
    </tr>
  </thead>
  <tbody>
    <tr class="border-b border-slate-200">
      <td class="font-bold">1. Đọc liền mạch chuẩn &amp; ngắt nhịp ngắn (<code>ai_continuous</code>, <code>khoa_voice</code>)</td>
      <td class="text-center font-semibold">6,6s &amp; 15,2s</td>
      <td class="text-center font-semibold">0,00% / 18,75%</td>
      <td class="text-center font-extrabold text-emerald-800">0,00% / 6,25%</td>
      <td class="text-center text-blue-800 font-bold">Bảo toàn 100% / Giảm 66,7% lỗi</td>
    </tr>
    <tr class="border-b border-slate-200 bg-emerald-50/70">
      <td class="font-bold">2. Đọc ngắt từng chữ rời rạc (<code>ai_sentence_paced.wav</code> • 23 từ)</td>
      <td class="text-center font-semibold">44,9s ➔ 12,8s</td>
      <td class="text-center text-red-800 font-bold">8,70%</td>
      <td class="text-center font-extrabold text-emerald-800">0,00%</td>
      <td class="text-center text-emerald-800 font-extrabold">Khắc phục 100% lỗi (RTF 0,084)</td>
    </tr>
    <tr class="border-b border-slate-200">
      <td class="font-bold">3. Phương ngữ Nam Bộ (<code>dung_voice.wav</code> • 132 từ: <i>hấp➔thắp, hầm➔thầm</i>)</td>
      <td class="text-center font-semibold">48,65 giây</td>
      <td class="text-center text-red-800 font-bold">3,79%</td>
      <td class="text-center font-extrabold text-emerald-800">2,27%</td>
      <td class="text-center text-blue-800 font-bold">Khắc phục 40,1% lỗi (Acc: 97,73%)</td>
    </tr>
    <tr class="border-b border-slate-200 bg-emerald-50/70">
      <td class="font-bold">4. Văn học &amp; Giao tiếp đa thực thể (<code>hem_311_1..3</code>: <i>Đắk Lắk, cafe, ly phin, check</i>)</td>
      <td class="text-center font-semibold">35,97 giây</td>
      <td class="text-center text-red-800 font-bold">23,75%</td>
      <td class="text-center font-extrabold text-emerald-800">17,50%</td>
      <td class="text-center text-emerald-800 font-extrabold">Khắc phục 26,3% lỗi (Sửa 5 cụm)</td>
    </tr>
    <tr class="border-b border-slate-200">
      <td class="font-bold">5. Câu thực tế Địa danh &amp; Từ vay mượn (<code>khoa_voice_cafe_daklak.mp3</code>)</td>
      <td class="text-center font-semibold">8,18 giây</td>
      <td class="text-center text-red-800 font-bold">25,00%</td>
      <td class="text-center font-extrabold text-emerald-800">15,00%</td>
      <td class="text-center text-blue-800 font-bold">Khắc phục 40,0% lỗi</td>
    </tr>
    <tr class="bg-blue-100/90 font-extrabold border-t-2 border-slate-300">
      <td class="text-slate-900">Trung bình toàn hệ thống (Đo lường chuẩn ICASSP: WER / CER / TER / RTF)</td>
      <td class="text-center">RTF ≈ 0,16</td>
      <td class="text-center text-red-800">11,96%</td>
      <td class="text-center text-emerald-800">6,19% (Acc: 93,81%)</td>
      <td class="text-center text-blue-900">Khắc phục ~56% tổng lỗi từ</td>
    </tr>
  </tbody>
</table>

<!-- <div class="p-2.5 rounded-xl bg-gradient-to-r from-blue-900 to-indigo-900 text-white flex items-center justify-between gap-3">
  <div>
    <div class="font-extrabold text-[13.5px] text-amber-300">🚀 CHUYỂN SANG DEMO TRỰC TIẾP TRÊN GIAO DIỆN WEB GRADIO (3 KỊCH BẢN):</div>
    <div class="text-[11.5px] text-slate-100 mt-0.5">
      <b>(1)</b> Chuẩn hóa Địa danh &amp; Từ mượn (<code>Đắk Lắk</code>, <code>Kon Tum</code>, <code>cafe</code>) &nbsp;•&nbsp;
      <b>(2)</b> Sửa biến âm Nam Bộ (<code>hấp ➔ thắp</code>) &nbsp;•&nbsp;
      <b>(3)</b> Bổ sung nhãn mới tức thì (&lt; 0,2s)
    </div>
  </div>
</div> -->

<!--
[4:15 - 5:00 | 45 giây]
Slide cuối cùng tổng hợp kết quả thực nghiệm đối sánh A/B Testing trên toàn bộ 8 mẫu âm thanh kiểm thử thực tế:
- Ở câu đọc ngắt từng chữ kéo dài 44,9 giây, thuật toán nén khoảng lặng đã giảm tỷ lệ lỗi từ 8,70% về 0,00%.
- Ở đoạn văn 132 từ phương ngữ Nam Bộ của bạn Dửng, CSDL tự động sửa chính xác các từ biến âm như "hấp" thành "thắp", "hầm" thành "thầm", kéo WER xuống chỉ còn 2,27% (độ chính xác đạt 97,73%).
- Ở các kịch bản chứa địa danh và từ vay mượn như "Đắk Lắk", "Kon Tum", "cafe", "ly phin", "check lại", tầng CSDL MMDBMS đã tự động chuẩn hóa chính xác, giúp giảm tỷ lệ lỗi WER trung bình toàn hệ thống từ 11,96% xuống còn 6,19% (độ chính xác đạt 93,81%) với tốc độ xử lý nhanh gấp 6 lần thời gian thực (RTF = 0,16).
Ngay sau đây, nhóm em xin phép chuyển sang màn hình Giao diện Web Demo trực tiếp để chạy thử nghiệm và mời Thầy kiểm tra ạ!
-->
