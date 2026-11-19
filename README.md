# Quản lý phòng khám

Web app quản lý phòng khám: **Tiếp đón (phát số) → Khám bệnh (kê đơn) → Soạn thuốc → Quản lý trong ngày**, kèm hồ sơ bệnh nhân, danh mục thuốc và thống kê.

- **Giao diện** chạy trên **GitHub Pages** (miễn phí).
- **Dữ liệu** (bệnh nhân, toa thuốc, cài đặt) lưu trên **Firebase Firestore** của Google (miễn phí ở mức sử dụng của phòng khám).
- Chỉ người có **tài khoản đã xác nhận email** và **có tên trong danh sách nhân viên** mới vào được.

| File | Dùng để |
|---|---|
| `index.html` | Toàn bộ ứng dụng |
| `config.js` | Cấu hình Firebase của phòng khám (bước 1.5 phải điền) |
| `firestore.rules` | Luật bảo vệ dữ liệu (bước 1.6 dán vào Firebase) |

Cần làm 3 phần, mỗi phần chỉ làm một lần: **Firebase** → **GitHub** → **Tài khoản quản trị**. Tổng cộng khoảng 20–30 phút.

---

## Phần 1. Tạo Firebase (nơi lưu dữ liệu)

1. Vào https://console.firebase.google.com, đăng nhập bằng tài khoản Google của phòng khám.
2. Bấm **Create a project** (Tạo dự án), đặt tên, ví dụ `phong-kham-dong-nai`. Có thể tắt Google Analytics. Bấm Create.
3. **Tạo cơ sở dữ liệu:** menu trái **Build → Firestore Database → Create database**.
   - Location: chọn **asia-southeast1 (Singapore)**, gần Việt Nam nhất. *Không đổi được sau khi tạo.*
   - Chọn **Start in production mode** → Create.
4. **Bật đăng nhập:** **Build → Authentication → Get started → Sign-in method → Email/Password → Enable → Save**.
5. **Lấy cấu hình:** bấm bánh răng ⚙ cạnh "Project Overview" → **Project settings** → kéo xuống **Your apps** → bấm biểu tượng **`</>`** (Web) → đặt tên `phong-kham` → **Register app**.
   Firebase hiện một đoạn `const firebaseConfig = { apiKey: "...", ... }`. Chép các giá trị này vào file **`config.js`**, thay cho các chữ `DIEN_...`, `ten-du-an`. Giữ nguyên dòng `window.FIREBASE_CONFIG = {`.
6. **Dán luật bảo vệ:** **Firestore Database → tab Rules** → xóa hết nội dung cũ → dán toàn bộ nội dung file **`firestore.rules`** → **Publish**.

> Các giá trị trong `config.js` không phải mật khẩu. Để công khai trên GitHub là bình thường; dữ liệu được bảo vệ bởi bước 1.6.

## Phần 2. Đưa lên GitHub Pages (nơi chạy giao diện)

1. Vào https://github.com, đăng nhập. Bấm **+ → New repository**.
   - Repository name: ví dụ `phong-kham`.
   - Chọn **Public** (GitHub Pages miễn phí yêu cầu repo công khai; chỉ code là công khai, **dữ liệu bệnh nhân nằm ở Firebase, không nằm trong repo**).
   - Bấm **Create repository**.
2. Ở trang repo mới, bấm **uploading an existing file** → kéo thả 4 file `index.html`, `config.js` (đã điền ở bước 1.5), `firestore.rules`, `README.md` → **Commit changes**.
3. Vào **Settings → Pages**. Ở mục *Build and deployment*: Source = **Deploy from a branch**, Branch = **main**, thư mục **/ (root)** → **Save**.
4. Đợi 1–2 phút, tải lại trang Settings → Pages. Địa chỉ web hiện ở đầu trang, dạng:
   **`https://<tên-github>.github.io/phong-kham/`**
5. **Cho phép tên miền đăng nhập:** quay lại Firebase → **Authentication → Settings → Authorized domains → Add domain** → nhập `<tên-github>.github.io` (không có `https://`, không có `/phong-kham`) → Add.

## Phần 3. Tạo tài khoản quản trị đầu tiên

1. Trong Firebase → **Firestore Database → Data → + Start collection**:
   - Collection ID: `nhanvien`
   - Document ID: **email của bạn, viết thường**, ví dụ `chuphongkham@gmail.com`
   - Thêm field: `quyen` (kiểu string) = `admin` → **Save**.
2. Mở địa chỉ web ở bước 2.4 → nhập email đó + mật khẩu (ít nhất 6 ký tự) → **Tạo tài khoản**.
3. Mở hộp thư, bấm link **xác nhận email** (xem cả thư mục Spam), quay lại trang bấm **Đăng nhập**.
4. Vào **Cài đặt**:
   - Điền tên, địa chỉ, số điện thoại phòng khám, phí khám, danh sách bác sĩ → **Lưu cài đặt**.
   - **Nhập dữ liệu từ Google Sheets cũ:** trên Google Sheets chọn *Tệp → Tải xuống → Microsoft Excel (.xlsx)*, chọn file đó → **Bắt đầu nhập**. (Nhập danh mục thuốc, hồ sơ bệnh nhân, toa thuốc cũ, bác sĩ. Nhập lại lần hai không bị trùng.)
   - **Người dùng & phân quyền:** thêm email từng người (tiếp đón, bác sĩ, quầy thuốc). Mỗi người tự mở web, bấm **Tạo tài khoản** bằng đúng email đó và xác nhận email.

## Phân quyền

- **Quản trị viên** (nên có 1–2 người): vào được mọi mục, sửa danh mục thuốc, cài đặt, nhập/sao lưu dữ liệu và phân quyền.
- **Nhân viên**: chỉ thấy các mục được tích ở **Cài đặt → Người dùng & phân quyền**. Mặc định: Tiếp đón, Khám bệnh, Soạn thuốc, Bệnh nhân. Các mục *Quản lý trong ngày, Danh mục thuốc, Thống kê* bị khóa trừ khi quản trị viên tích cho từng người. Mục *Cài đặt* chỉ dành cho quản trị viên.
- Đổi vai trò/quyền rồi bấm **Lưu** trên dòng của người đó; có hiệu lực ngay trên máy của họ. Luôn phải còn ít nhất một quản trị viên.
- Lưu ý: nhân viên kê đơn/soạn thuốc vẫn thấy tiền của từng toa họ làm; khóa mục chỉ ngăn xem tổng hợp doanh thu, thống kê và sửa danh mục thuốc.

---

## Dùng hằng ngày

- Mỗi người mở cùng một địa chỉ web trên máy của mình, đăng nhập một lần (trình duyệt nhớ).
- Trên điện thoại: mở web bằng Chrome/Safari → **Thêm vào màn hình chính** để dùng như app.
- Cần có mạng Internet để phát số và gửi toa.

## Sửa đổi sau này

| Muốn đổi | Làm ở đâu |
|---|---|
| Giá thuốc, thêm thuốc, đánh dấu hết thuốc | Tab **Danh mục thuốc** |
| Hồ sơ bệnh nhân | Tab **Bệnh nhân** |
| Phí khám, khoản cộng thêm, bác sĩ, tên phòng khám, lời dặn | Tab **Cài đặt** |
| Thêm/xóa người dùng, phân quyền | **Cài đặt → Người dùng & phân quyền** (chỉ quản trị viên) |
| Chức năng, giao diện | Sửa `index.html` (nhờ Claude sửa), rồi trên GitHub mở file → biểu tượng ✏️ hoặc *Add file → Upload files* để thay file mới → Commit. Web cập nhật sau 1–2 phút, **dữ liệu giữ nguyên**. |

## Chi phí và giới hạn

- **GitHub Pages:** miễn phí.
- **Firebase gói Spark (miễn phí):** mỗi ngày 50.000 lượt đọc, 20.000 lượt ghi; lưu trữ 1 GB. Phòng khám ~100–150 lượt/ngày khám dùng khoảng vài nghìn lượt đọc/ghi mỗi ngày, nằm trong mức miễn phí. Nếu có ngày vượt mức, Firebase tạm chặn đến hôm sau; khi đó có thể chuyển gói **Blaze** (trả theo mức dùng; vẫn được miễn phí phần trong hạn mức trên) và nên đặt **cảnh báo ngân sách** để không phát sinh chi phí bất ngờ.
- **Không có hạn sử dụng:** chạy chừng nào repo GitHub và dự án Firebase còn tồn tại.

## Sao lưu

- Hằng tuần/tháng: **Xuất Excel** ở tab Bệnh nhân, Quản lý trong ngày, Thống kê; lưu vào Google Drive.
- Firebase gói Blaze có thể bật **sao lưu tự động** (Firestore → Disaster recovery / Backups).

## Bảo mật dữ liệu bệnh nhân

- Dữ liệu sức khỏe là dữ liệu cá nhân nhạy cảm. Chỉ thêm đúng người cần dùng vào danh sách nhân viên; xóa ngay khi nghỉ việc.
- Không chia sẻ mật khẩu; mỗi người một tài khoản.
- Dữ liệu đặt tại máy chủ Google ở Singapore. Nên tìm hiểu thêm quy định về bảo vệ dữ liệu cá nhân hiện hành của Việt Nam (đặc biệt về dữ liệu sức khỏe và chuyển dữ liệu ra nước ngoài).
