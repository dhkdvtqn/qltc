# MyMoney — Quản lý tài chính cá nhân

Web tĩnh (HTML/CSS/JS thuần, không cần build) cho repo **`dhkdvtqn/qltc`**,
chạy bằng GitHub Pages. Frontend chỉ gọi Cloudflare Worker API, không tự
lưu dữ liệu tài chính vào localStorage (chỉ lưu token đăng nhập và URL API).

**Database và Worker hoàn toàn mới, chạy độc lập — không dùng chung,
không liên quan tới website `co-che-duq` cũ:**
- Database D1 mới: **`mymoney-db`**
- Worker mới: **`mymoney-api`**
- Repo mới: **`dhkdvtqn/qltc`** (file ở đây: `index.html`, `config.js`)

## 1. Bật GitHub Pages

1. Đẩy toàn bộ file trong thư mục này lên repo `dhkdvtqn/qltc`, nhánh `main`,
   ở thư mục gốc của repo (không để trong thư mục con).
2. Vào repo trên GitHub: **Settings → Pages → Build and deployment →
   Deploy from a branch**, chọn nhánh `main`, thư mục `/ (root)`, bấm **Save**.
3. Mở trang: `https://dhkdvtqn.github.io/qltc/`

## 2. Tạo database và Worker mới (làm 1 lần, trên Cloudflare Dashboard)

Chi tiết đầy đủ trong `../mymoney-cloudflare/README.md` (gói Worker + D1).
Tóm tắt:

1. **D1:** Storage & Databases → D1 → Create database, tên `mymoney-db`.
   Mở database → tab Console, dán toàn bộ file `schema.sql` trong gói
   `mymoney-cloudflare` rồi Run (tạo 12 bảng).
2. **Worker:** Workers & Pages → Create → Create Worker, tên `mymoney-api`,
   Deploy → Edit code, dán toàn bộ file `worker.js` trong gói
   `mymoney-cloudflare`, Save and deploy.
3. **Gắn D1:** trong Worker → Settings → Bindings → Add binding → D1
   Database, Variable name ghi đúng `DB`, chọn `mymoney-db`, Save, Deploy lại.
4. **Secret:** Settings → Variables and Secrets → Add → Secret, tên
   `AUTH_SECRET`, giá trị là một chuỗi dài ngẫu nhiên (giữ lại, không gửi
   cho ai).
5. **Kiểm tra:** mở `https://mymoney-api.<subdomain>.workers.dev/api/health`
   phải hiện `{"success":true,"database":"ok","ok":1}`.

## 3. Điền Worker URL vào website

Sửa `config.js`, điền URL Worker vào `window.MYMONEY_API_BASE`, đẩy lên
GitHub. Địa chỉ Worker **không** nhập được trên giao diện nữa (đã bỏ ô nhập
ở màn hình đăng nhập và ở Cài đặt).

Lưu ý: Worker cần cho phép domain GitHub Pages gọi tới — trong
`wrangler.jsonc` của gói Worker, `ALLOWED_ORIGIN` để `*` khi test, lên
production nên đặt đúng `https://dhkdvtqn.github.io`.

## 4. Tài khoản

- **Tài khoản đầu tiên đăng ký sẽ tự thành admin** (không có mật khẩu
  mặc định, không có tài khoản demo). Đăng ký bằng **email hoặc số điện
  thoại** — sau này chính là tài khoản đăng nhập.
- Đăng nhập hết hạn sau 12 giờ thì đăng nhập lại.
- Quên mật khẩu: nhập email/SĐT đã đăng ký rồi **trả lời đúng tất cả câu hỏi
  bí mật** (đặt tối thiểu 3 câu khi đăng ký) là được đặt mật khẩu mới — không
  gửi email. Tài khoản cũ chưa có câu hỏi thì vào **Cài đặt → Câu hỏi bí mật**
  để đặt trước.
- Số tiền gửi lên API luôn là số nguyên đồng (không dùng số thập phân).
- Múi giờ: Asia/Ho_Chi_Minh. Tiền tệ mặc định: VND.
