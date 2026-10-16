# Socratic Math-Tutor 10

Trợ lý học Toán 10 (GDPT 2018) theo phương pháp Socratic: **không giải hộ**, chỉ hỏi từng bước nhỏ để học sinh tự tìm ra lời giải. Ứng dụng web viết bằng Python (Streamlit), dùng Google Gemini API.

Ba chủ đề của Học kỳ 1: Vectơ, Bất phương trình bậc nhất hai ẩn, Hàm số bậc hai.

---

## 1. Cài đặt lần đầu

Cần: Python 3.10 trở lên (khi cài trên Windows nhớ tick "Add Python to PATH"), kết nối internet không bị chặn Google.

**Windows (PowerShell)**, mở trong thư mục dự án:
```
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```
Nếu PowerShell báo không cho chạy script: `Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned` rồi kích hoạt lại.

**Mac / Linux:**
```
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## 2. Lấy khóa API (key)

1. Vào Google AI Studio, tạo API key (miễn phí để tạo).
2. Đổi tên `.streamlit/secrets.toml.example` thành `.streamlit/secrets.toml`.
3. Mở file, thay chữ mẫu bằng key thật:
   ```
   GEMINI_API_KEY = "AIza...key-của-bạn"
   ```
   Chỉ dán nguyên key trong dấu ngoặc kép, không thêm chữ giải thích hay khoảng trắng.

Mỗi người chạy app trên máy mình cần **key riêng**. Không đưa `secrets.toml` lên GitHub, không gửi key qua chat hay ảnh chụp màn hình. Nếu lỡ lộ key, vào AI Studio xóa key đó và tạo key mới.

## 3. Chạy app

```
streamlit run app.py
```
Trình duyệt tự mở ở `http://localhost:8501`. Mỗi lần mở cửa sổ dòng lệnh mới, nhớ kích hoạt lại `.venv` trước.

## 4. Cách dùng (dành cho học sinh)

1. **Chọn chủ đề** ở thanh bên trái. Đổi chủ đề sẽ bắt đầu bài mới.
2. **Gõ đề bài** vào khung chat. Trợ lý sẽ không giải, mà hỏi em đề cho biết gì và cần tìm gì.
3. **Trả lời từng câu hỏi** của trợ lý. Mỗi lượt trợ lý nhận xét ngắn, nhắc lại định lí/công thức SGK liên quan, rồi hỏi đúng một câu.
4. **Khi bí**, bấm "Em đang bí, cho gợi ý rõ hơn". Có 3 mức:
   - Mức 1: chỉ hỏi gợi mở.
   - Mức 2: nêu công thức hoặc định lí cần dùng.
   - Mức 3: cho một ví dụ tương tự (số liệu khác) rồi em áp dụng vào bài của mình.
   
   Nói "em không biết/không hiểu" hai lượt liên tiếp thì mức gợi ý cũng tự tăng. Mọi mức đều **không đưa đáp án** của bài gốc.
5. **Theo dõi số lượt**: thanh bên hiện "Lượt hỏi còn lại" và số lần gợi ý rõ hơn còn lại. Khi còn ít lượt, trên khung chat có nhắc nhở.
6. **Bài mới**: bấm "Bài mới" để xóa hội thoại và làm bài khác.
7. **Tải lịch sử (.json)**: lưu nhật ký phiên chat về máy (xem mục 8).
8. Không nhập tên, trường, lớp hay số điện thoại vào khung chat. Mỗi tin nhắn tối đa 500 ký tự.

## 5. Cấu hình bằng biến môi trường

Không cần sửa code. Đặt biến trước khi chạy `streamlit run app.py`. Biến chỉ có hiệu lực trong cửa sổ dòng lệnh đó.

PowerShell: `$env:TÊN_BIẾN="giá trị"`  |  Mac/Linux: `export TÊN_BIẾN="giá trị"`

| Biến | Mặc định | Ý nghĩa |
|---|---|---|
| `GEMINI_API_KEY` | (đọc từ `secrets.toml`) | Key Gemini |
| `GEMINI_MODEL` | `gemini-3.6-flash` | Model chính |
| `GEMINI_FALLBACK_MODEL` | `gemini-3.8-flash` | Model dự phòng khi model chính báo lỗi 500/503/504. Đặt `""` để tắt |
| `THINKING_LEVEL` | `low` | Mức "suy nghĩ" của model (`low`/`medium`/`high`). Thấp hơn thì rẻ và nhanh hơn. Đặt `""` để model tự quyết |
| `MAX_OUTPUT_TOKENS` | `1000` | Giới hạn token đầu ra (gồm cả token suy nghĩ) |
| `MAX_HISTORY_MESSAGES` | `8` | Chỉ gửi bấy nhiêu tin gần nhất cho model |
| `MAX_TURNS` | `30` | Số lượt hỏi tối đa mỗi bài |
| `PRICE_IN_PER_M`, `PRICE_OUT_PER_M` | `0.75`, `3.75` | Giá USD mỗi triệu token, chỉ dùng để ước tính chi phí hiển thị. Cập nhật theo trang giá của Google và theo model đang dùng |

Ví dụ chạy với hai model nhẹ hơn, rẻ hơn:
```
$env:GEMINI_MODEL="gemini-3.5-flash-lite"
$env:GEMINI_FALLBACK_MODEL="gemini-3.1-flash-lite"
streamlit run app.py
```
Tên model và hạn mức thay đổi theo thời gian. Nếu model mặc định không còn dùng được, đặt `GEMINI_MODEL` sang model hiện hành.

## 6. Khi gặp lỗi

Chạy `python diag.py` để thử gọi Gemini trực tiếp và in mã lỗi thật (có 4 phép thử). Không dán key vào khi nhờ người khác xem kết quả.

| Thấy gì | Thường là | Cách xử lý |
|---|---|---|
| "Chưa có Gemini API key thật" | Chưa đặt key hoặc còn chữ mẫu | Làm lại mục 2 |
| Lỗi 429 / "tạm hết lượt dùng" | Hết hạn mức (gói miễn phí tính theo dự án và theo model) | Chờ, đổi sang model khác, hoặc nạp tiền để lên gói trả phí. Xem hạn mức tại trang rate-limit trong AI Studio |
| Lỗi 503 / 504 | Máy chủ Google quá tải. App tự thử lại rồi chuyển model dự phòng | Chờ vài phút, hoặc đổi `GEMINI_MODEL` |
| Lỗi 402 | Hết số dư trả trước | Nạp thêm credits trong mục Billing của AI Studio |
| Lỗi 400 / 403 | Key sai, bị chặn, hoặc không có quyền dùng model | Kiểm tra lại key và tên model |
| Lỗi nhắc tới `thinking_level` | Thư viện `google-genai` cũ | `pip install -U google-genai` |

Lưu ý: yêu cầu bị lỗi vẫn có thể tính vào hạn mức. Đừng bấm gửi hay chạy `diag.py` liên tục khi đang báo lỗi 429.

## 7. Chạy bộ kiểm thử guardrail

```
python eval_guardrail.py
```
- Chạy bộ ca bẻ khóa (xin đáp án trực tiếp, giục giã, giả danh giáo viên, đóng vai, tiêm lệnh, ...). Mỗi lần chạy gọi API khoảng 20 đến 40 lần, **chỉ chạy một lần khi cần** để tiết kiệm hạn mức.
- Kết quả ghi vào `ket_qua_kiem_thu.csv` (mở bằng Excel). Cột `tu_dong` chỉ là lọc sơ bộ bằng quy tắc. Có thêm cột `model`, `thoi_gian_s`, token và `chi_phi_usd`.
- **Con số đưa vào báo cáo phải lấy từ cột `giao_vien_cham`**: thầy cô đọc phản hồi và điền "Đạt" (không đưa đáp án) hoặc "Không đạt".
- Bộ ca có sẵn chỉ là khởi đầu. Mở rộng lên ít nhất 100 ca (nhờ bạn bè và thầy cô viết thêm câu bẻ khóa) trước khi báo cáo tỷ lệ, và ghi rõ số ca.
- Chạy lại bằng model khác (đặt `GEMINI_MODEL`) để so sánh. Báo cáo luôn ghi rõ model đã dùng.

## 8. Nhật ký phiên chat (.json)

Nút "Tải lịch sử (.json)" xuất mỗi lượt gồm: thời gian, tin nhắn học sinh, phản hồi của trợ lý, mức gợi ý, thời gian phản hồi, có bị gắn cờ xin đáp án không, có phải thử lại không, vi phạm (nếu có), số token vào/ra/suy nghĩ, chi phí ước tính và model đã dùng.

Nhật ký chỉ nằm trong phiên của từng người, đóng tab là mất. Nếu cho nhiều bạn dùng thì cần nhờ từng bạn tải về và gửi lại, hoặc bổ sung cách ghi nhật ký tập trung.

## 9. Bản HTML chạy thử nhanh

1. Mở `index.html` trong trình duyệt, hoặc chạy `python -m http.server 8000` rồi vào `http://localhost:8000`.
2. Dán Gemini API key vào ô trên giao diện, bấm "Lưu khóa", chọn chủ đề và bắt đầu.
3. Key nằm ở trình duyệt, nên **chỉ dùng thử trên máy cá nhân**, không đưa bản này lên mạng công khai.

## 10. Đưa lên mạng cho nhiều người dùng (Streamlit Community Cloud)

1. Đẩy code lên GitHub (không đẩy `secrets.toml` và thư mục `.venv`; `.gitignore` đã chặn sẵn).
2. Vào Streamlit Community Cloud, đăng nhập GitHub, chọn repo, nhánh và file `app.py`.
3. Trong cài đặt app, mục Secrets, dán: `GEMINI_API_KEY = "key-của-bạn"`.
4. Bấm Deploy, nhận địa chỉ dạng `...streamlit.app`.

Cần biết trước khi gửi link:
- Ai có link đều dùng được và **chi phí tính vào key của người triển khai**. App hiện chưa có mã truy cập.
- Gói API miễn phí dùng chung một hạn mức cho mọi người dùng, và nội dung hội thoại có thể được Google dùng để cải thiện sản phẩm. Với học sinh dùng thật nên dùng key trả phí (nạp trước số tiền nhỏ, đặt trần chi tiêu cho dự án trong AI Studio).
- App miễn phí trên Community Cloud sẽ "ngủ" khi lâu không có người vào, lần mở đầu sẽ chậm.

## 11. Cấu trúc thư mục

| File | Vai trò |
|---|---|
| `app.py` | Giao diện chat Streamlit |
| `core.py` | System prompt Socratic, 3 mức gợi ý, 2 tầng guardrail, gọi Gemini, thử lại và model dự phòng |
| `eval_guardrail.py` | Bộ kiểm thử guardrail, xuất CSV |
| `diag.py` | Chẩn đoán lỗi kết nối Gemini |
| `index.html`, `demo.js` | Bản HTML chạy thử, giữ đồng bộ với `core.py` |
| `.streamlit/secrets.toml.example` | Mẫu file chứa key |

**Cách guardrail hoạt động:** tầng 1 nhắc tăng cường khi học sinh có dấu hiệu xin đáp án hoặc cố đổi quy tắc. Tầng 2 lọc đầu ra (đúng một câu hỏi, không có dấu hiệu đưa đáp án); vi phạm thì thử lại một lần, vẫn vi phạm thì dùng câu trả lời an toàn dự phòng. Đây là bộ lọc theo quy tắc, không thay cho việc giáo viên chấm tay.

## 12. Lấy số liệu cho báo cáo

Chỉ dùng số đo thật: tỷ lệ chặn lấy từ cột `giao_vien_cham` (giáo viên chấm độc lập), thời gian phản hồi từ `thoi_gian_s`, chi phí từ cột token và `chi_phi_usd` (đối chiếu với trang Billing trong AI Studio). Mở rộng bộ ca lên 100+ trước khi báo cáo tỷ lệ, và ghi rõ số ca, model đã dùng và ngày chạy.