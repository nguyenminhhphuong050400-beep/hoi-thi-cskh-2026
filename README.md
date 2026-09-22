# Hội thi Tuyên truyền CSKH — Nước sạch Hà Nội 2026

Website thi trắc nghiệm trực tuyến thời gian thực (kiểu Crowdpurr/Kahoot), phục vụ
**"HỘI THI TUYÊN TRUYỀN NÂNG CAO CHẤT LƯỢNG DỊCH VỤ, CHĂM SÓC KHÁCH HÀNG CÔNG TY
NƯỚC SẠCH HÀ NỘI NĂM 2026"**.

Công nghệ: **React + Vite** (giao diện) + **Firebase Firestore** (cơ sở dữ liệu
thời gian thực, miễn phí — gói Spark, không cần thẻ tín dụng). Nhiều thiết bị
(điện thoại, iPad, máy chiếu) cùng kết nối một dự án Firebase nên luôn thấy
**cùng một trạng thái** câu hỏi, thời gian, kết quả — không dùng dữ liệu giả lập.

## 1. Ba loại giao diện

| Đường dẫn | Dành cho | Mô tả |
|---|---|---|
| `/admin` | Ban tổ chức | Đăng nhập bằng mật khẩu, quản lý câu hỏi, đội thi, mở/khóa câu hỏi, xem bảng xếp hạng trực tiếp, xuất Excel/CSV |
| `/doi` | Đội thi | Đăng nhập bằng **mã đội** hoặc quét **mã QR**, chỉ thấy câu hỏi khi BTC mở, chọn đáp án và nộp bài |
| `/screen` | Màn hình sân khấu | Hiển thị câu hỏi hiện tại, đồng hồ đếm ngược, số đội đã trả lời, bảng xếp hạng — mở trên máy chiếu, không cần đăng nhập |

Trang chủ `/` có sẵn 3 nút bấm dẫn tới từng giao diện.

## 2. Cài đặt Firebase (miễn phí, ~5 phút)

1. Vào https://console.firebase.google.com → **Add project** → đặt tên bất kỳ,
   ví dụ `hoi-thi-cskh-2026` → bỏ qua Google Analytics nếu không cần.
2. Trong project, vào **Build → Firestore Database → Create database** → chọn
   **Start in production mode** → chọn vị trí gần Việt Nam (vd. `asia-southeast1`).
3. Vào **Build → Authentication → Get started** → tab **Sign-in method** →
   bật **Anonymous** (Ẩn danh). Đây là cách app xác thực thiết bị mà không cần
   người dùng tạo tài khoản.
4. Vào **Project settings** (biểu tượng bánh răng) → mục **Your apps** → bấm
   biểu tượng **Web `</>`** → đặt tên app → **Register app**. Firebase sẽ hiện
   một đoạn cấu hình `firebaseConfig` — copy các giá trị đó.
5. Vào **Firestore Database → Rules**, dán nội dung file `firestore.rules`
   (đã có sẵn trong dự án) rồi bấm **Publish**.

## 3. Chạy thử ở máy local

```bash
# 1. Cài Node.js 18+ (https://nodejs.org) nếu chưa có
# 2. Giải nén dự án rồi vào thư mục
cd hoi-thi-quiz
npm install

# 3. Tạo file cấu hình
cp .env.example .env
# Mở .env, dán các giá trị Firebase đã copy ở bước 2.4, và tự đặt mật khẩu BTC

# 4. Chạy
npm run dev
```

Mở trình duyệt tại địa chỉ Vite in ra (thường là `http://localhost:5173`).
Để thử "nhiều thiết bị", mở thêm các tab/trình duyệt khác cùng địa chỉ đó — vì
dữ liệu nằm trên Firestore thật nên mọi tab đều đồng bộ thời gian thực.

### Chạy thử nhanh

1. Vào `/admin`, đăng nhập bằng mật khẩu đặt trong `.env`.
2. Tab **Câu hỏi** → bấm **"Nạp 10 câu mẫu"** để có ngay 10 câu hỏi mẫu về
   dịch vụ cấp nước & CSKH.
3. Tab **Đội thi** → hệ thống tự tạo sẵn 5 đội (Đội 1..5, mã `DOI1`..`DOI5`),
   bạn có thể đổi tên/mã hoặc thêm/xóa đội tại đây; mỗi đội có QR riêng.
4. Mở `/doi` ở một tab khác (hoặc điện thoại), nhập mã `DOI1` để vào phòng thi
   với vai trò Đội 1.
5. Mở `/screen` ở một tab khác để xem màn hình trình chiếu.
6. Quay lại `/admin` → tab **Trực tiếp** → bấm **"Bắt đầu kỳ thi"**, sau đó
   **"Khóa & chấm điểm câu này"** rồi **"Câu tiếp theo"** để điều khiển toàn bộ
   luồng thi.

## 4. Triển khai miễn phí lên Vercel hoặc Netlify

### Cách A — Vercel

1. Đẩy code lên một repo GitHub (hoặc kéo-thả thư mục dự án).
2. Vào https://vercel.com → **Add New → Project** → chọn repo.
3. Ở bước cấu hình, Vercel tự nhận diện dự án Vite. Mở mục
   **Environment Variables** và nhập toàn bộ các biến trong `.env.example`
   (giá trị Firebase thật + mật khẩu BTC).
4. Bấm **Deploy**. Sau khi xong, bạn có một URL dạng
   `https://ten-du-an.vercel.app` — dùng URL đó (thêm `/admin`, `/doi`,
   `/screen`) cho toàn bộ hội thi.

### Cách B — Netlify

1. Đẩy code lên GitHub, hoặc chạy `npm run build` ở máy local để tạo thư mục
   `dist/`.
2. Vào https://app.netlify.com → **Add new site**:
   - Nếu nối GitHub: chọn repo, **Build command**: `npm run build`,
     **Publish directory**: `dist`.
   - Nếu kéo-thả: kéo thư mục `dist/` vào Netlify (cách này không đọc được
     biến môi trường lúc chạy, nên **phải** build ở bước 1 với file `.env` đã
     điền sẵn giá trị Firebase thật, hoặc dùng cách nối GitHub).
3. Vào **Site settings → Environment variables**, nhập các biến giống bước 3
   ở Vercel rồi **Deploy lại (Trigger deploy)** để biến môi trường có hiệu lực.
4. Netlify cần biết đây là ứng dụng một trang (SPA) để các đường dẫn `/admin`,
   `/doi`, `/screen` không bị lỗi 404 khi tải lại trang — file `netlify.toml`
   trong dự án đã cấu hình sẵn redirect này.

> Cả hai nền tảng đều miễn phí ở mức sử dụng cho một hội thi nội bộ.

## 5. Cách hoạt động / mô hình dữ liệu Firestore

Toàn bộ dữ liệu nằm trong `exams/{examId}` (đổi `VITE_EXAM_ID` trong `.env`
để tạo một "kỳ thi" hoàn toàn tách biệt, ví dụ khi muốn dùng lại hệ thống cho
năm sau mà không đụng dữ liệu cũ):

```
exams/{examId}                     (document cấu hình chung)
  - status: idle | question_active | question_locked | finished
  - currentQuestionIndex, currentQuestionStartedAt
  - shuffleQuestions, shuffleAnswers
  - questionOrder: [id câu hỏi theo thứ tự hiển thị]

exams/{examId}/teams/{teamId}
  - name, code, score, correctCount, totalTimeMs

exams/{examId}/questions/{questionId}
  - text, options[4], correctAnswer (0-3), timeLimit (giây)
  - maxScore, speedScoringEnabled, imageUrl, videoUrl, order

exams/{examId}/answers/{teamId_questionId}
  - teamId, questionId, selectedOption, timeTakenMs, submittedAt
  - graded, isCorrect, scoreAwarded   (được BTC chấm khi khóa câu hỏi)
```

Mọi trang (Admin, Đội thi, Màn hình) đều dùng `onSnapshot` để lắng nghe thay
đổi thời gian thực trên các collection này — vì vậy khi BTC mở/khóa câu hỏi,
tất cả thiết bị đang mở trang đều cập nhật ngay lập tức, không cần tải lại.

**Không mất dữ liệu khi tải lại trang**: mọi trạng thái (câu hỏi đang mở, điểm
số, câu trả lời đã nộp) đều lưu trên Firestore chứ không lưu trong bộ nhớ tạm
của trình duyệt, nên F5 hoặc mất mạng rồi vào lại vẫn giữ nguyên tiến trình.

**Trộn câu hỏi / trộn đáp án**: khi bấm "Bắt đầu kỳ thi" với tùy chọn "Trộn
thứ tự câu hỏi" bật, hệ thống tạo một `questionOrder` ngẫu nhiên áp dụng chung
cho toàn bộ hội thi (đảm bảo màn hình sân khấu và tất cả đội luôn xem cùng một
câu ở cùng thời điểm). Tùy chọn "Trộn đáp án theo từng đội" làm xáo trộn vị
trí hiển thị A/B/C/D **khác nhau cho từng đội** (dựa trên mã đội + mã câu hỏi)
để hạn chế nhìn bài chéo giữa các đội, trong khi màn hình sân khấu luôn hiển
thị theo thứ tự gốc.

**Chấm điểm theo tốc độ**: nếu bật cho một câu hỏi, đội trả lời đúng càng
nhanh thì điểm càng cao (tối đa 100% điểm nếu trả lời gần như ngay lập tức,
tối thiểu 50% điểm nếu trả lời đúng ngay trước khi hết giờ). Nếu tắt, trả lời
đúng luôn được trọn điểm tối đa bất kể nhanh/chậm.

**Khóa đáp án khi hết giờ / đã nộp**: đội không thể sửa câu trả lời sau khi đã
nộp (được lưu bằng `setDoc` không ghi đè cho tài liệu đã tồn tại ở tầng UI —
nút chọn đáp án bị vô hiệu hóa ngay khi có bản ghi trả lời), và bị khóa hoàn
toàn khi BTC bấm "Khóa & chấm điểm" hoặc khi hết thời gian.

## 6. Lưu ý bảo mật

Đây là giải pháp không dùng dịch vụ trả phí và không dùng Cloud Functions, nên
việc phân quyền Admin hiện được xử lý ở tầng giao diện (mật khẩu trong biến
môi trường `VITE_ADMIN_PASSWORD`) chứ chưa phải ở tầng máy chủ Firestore. Với
quy mô một hội thi nội bộ công ty, mức bảo vệ này (kết hợp Firestore Rules yêu
cầu đăng nhập ẩn danh mới được ghi dữ liệu) thường là đủ dùng. Nếu muốn chặt
chẽ hơn (ví dụ hội thi có giải thưởng giá trị lớn, công khai rộng rãi), nên
nâng cấp lên Firebase Authentication có phân quyền (custom claims) và xử lý
chấm điểm ở Cloud Functions phía máy chủ thay vì phía client.

## 7. Cấu trúc thư mục

```
hoi-thi-quiz/
  src/
    firebase.js            Kết nối Firebase + đăng nhập ẩn danh
    App.jsx                Định tuyến 4 trang: /, /admin, /doi, /screen
    hooks/                 useExamConfig, useTeams, useQuestions, useAnswers
    utils/                 shuffle.js, scoring.js, csv.js
    data/sampleQuestions.js 10 câu hỏi mẫu
    pages/                 HomePage, AdminLogin, AdminDashboard, TeamLogin,
                           TeamPlay, ScreenDisplay
    pages/admin/           LiveControl, QuestionsManager, TeamsManager,
                           ResultsExport
    components/            Countdown, Leaderboard, QRCodeBox, Logo
  firestore.rules
  .env.example
  netlify.toml
```

## 8. Tùy biến thêm

- Đổi màu chủ đạo (xanh navy / vàng): sửa các biến CSS ở đầu file
  `src/styles/global.css` (`--navy`, `--gold`, ...).
- Tăng/giảm giới hạn 50 câu hỏi: sửa hằng số `MAX_QUESTIONS` trong
  `src/pages/admin/QuestionsManager.jsx`.
- Ảnh/video đính kèm câu hỏi: dán trực tiếp đường dẫn URL công khai (ví dụ ảnh
  tải lên Google Drive/Imgur, video YouTube dạng nhúng hoặc link .mp4 trực
  tiếp) vào ô "Link ảnh/video" khi tạo câu hỏi.
