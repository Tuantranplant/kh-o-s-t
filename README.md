# Khảo sát sinh kế bền vững nông dân — GitHub Pages & Domain Guide

Trang web khảo sát này đã được chuẩn bị đầy đủ cấu trúc để đưa lên **GitHub Pages** và chạy với **Tên miền riêng (Custom Domain)** hoặc tên miền mặc định của GitHub (`username.github.io/ksat`).

---

## 📁 Các tệp đã được tạo / cấu hình

1. **[index.html](file:///c:/Users/Hi%20Windows%2011/Downloads/ksat/index.html)**: Trang chính của ứng dụng khảo sát (đã đổi tên và nâng cấp từ `Khao_sat_Mau_03_Nhom3_sinh_ke_Nghe_An.html`).
   - Tự động tương thích với web tĩnh GitHub Pages.
   - Hỗ trợ lưu bản nháp & lưu phiếu trên trình duyệt.
   - Hỗ trợ kết nối **Google Sheets Webhook URL** để nhận phiếu trực tuyến từ nhiều người điền khảo sát từ xa.
   - Thêm thẻ Open Graph (OG) để khi gửi link qua Zalo / Facebook / Messenger hiển thị tiêu đề và xem trước đẹp mắt.
2. **[CNAME](file:///c:/Users/Hi%20Windows%2011/Downloads/ksat/CNAME)**: Tệp cấu hình tên miền riêng cho GitHub Pages.
3. **[.nojekyll](file:///c:/Users/Hi%20Windows%2011/Downloads/ksat/.nojekyll)**: Tệp vô hiệu hóa trình biên dịch Jekyll để GitHub Pages load full file tĩnh nhanh chóng và không bỏ sót tệp.

---

## 🚀 Hướng dẫn đưa Web lên GitHub và tạo Domain

### Bước 1: Tạo Repository trên GitHub
1. Đăng nhập vào [GitHub](https://github.com).
2. Tạo một **Repository mới** (ví dụ đặt tên là `ksat` hoặc `khaosat-nghean`).
3. Chọn chế độ **Public**.

### Bước 2: Tải các tệp lên GitHub
**Cách 1: Sử dụng giao diện trang web GitHub (Nhanh nhất)**
1. Trong repository vừa tạo, nhấn nút **Add file** -> **Upload files**.
2. Kéo thả tất cả các tệp trong thư mục này (`index.html`, `CNAME`, `.nojekyll`, `README.md`, v.v.) vào trang web.
3. Nhấn **Commit changes**.

**Cách 2: Sử dụng Git Command Line**
```bash
git init
git add .
git commit -m "Initial commit for GitHub Pages"
git branch -M main
git remote add origin https://github.com/USERNAME/ksat.git
git push -u origin main
```

---

### Bước 3: Bật GitHub Pages
1. Trong Repository trên GitHub, truy cập **Settings** (Cài đặt) -> **Pages** (ở cột bên trái).
2. Tại mục **Build and deployment**:
   - Source: Chọn **Deploy from a branch**.
   - Branch: Chọn **main** và thư mục **/(root)**.
3. Nhấn **Save**.
4. Chờ 1–2 phút, trang web của bạn sẽ hoạt động tại link:
   `https://USERNAME.github.io/ksat/`

---

### Bước 4: Gắn Tên Miền Riêng (Custom Domain)

Nếu bạn muốn trang khảo sát chạy bằng tên miền riêng (ví dụ: `khaosat.ten-mien-cua-ban.com` hoặc `ten-mien-cua-ban.com`):

#### 4.1 Sửa tệp `CNAME`
- Mở tệp `CNAME` trong thư mục/repository và thay thế `yourdomain.com` bằng tên miền chính xác của bạn (ví dụ: `khaosat.dhn.edu.vn` hoặc `khaosat.com`).

#### 4.2 Cấu hình DNS tại nhà cung cấp Tên miền (Mắt Bão, TENTEN, Cloudflare, GoDaddy, v.v.)
- **Trường hợp 1: Tên miền phụ (Subdomain - Ví dụ: `khaosat.domain.com`)**
  - Loại (Type): `CNAME`
  - Tên (Name/Host): `khaosat`
  - Giá trị (Value/Target): `USERNAME.github.io`
- **Trường hợp 2: Tên miền gốc (Apex Domain - Ví dụ: `domain.com`)**
  - Loại (Type): `A`
  - Tên (Name/Host): `@`
  - Giá trị (Points to): Thêm 4 bản ghi A chỉ đến các IP sau của GitHub:
    - `185.199.108.153`
    - `185.199.109.153`
    - `185.199.110.153`
    - `185.199.111.153`

#### 4.3 Bật HTTPS trên GitHub Pages
1. Vào lại **Settings** -> **Pages** trên GitHub.
2. Tại mục **Custom domain**, nhập tên miền của bạn nếu chưa có.
3. Tích chọn **Enforce HTTPS** để kích hoạt chứng chỉ SSL miễn phí (chờ vài phút để GitHub cấp phát SSL).

---

## 📊 Hướng dẫn kết nối Google Sheets (Để lưu phiếu khảo sát tự động trực tuyến)

Vì GitHub Pages là trang web tĩnh, để lưu dữ liệu khi **nhiều người điền trực tuyến từ điện thoại/máy tính riêng**, bạn có thể tạo một bảng Google Sheet nhận dữ liệu miễn phí:

1. Mở **Google Sheets** -> Tạo bảng tính mới.
2. Vào menu **Extensions (Tiện ích mở rộng)** -> **Apps Script**.
3. Dán đoạn mã sau vào:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = JSON.parse(e.postData.contents);
  
  if (sheet.getLastRow() === 0) {
    sheet.appendRow(Object.keys(data));
  }
  
  var row = [];
  var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
  for (var i = 0; i < headers.length; i++) {
    row.push(data[headers[i]] || "");
  }
  sheet.appendRow(row);
  
  return ContentService.createTextOutput("OK");
}
```

4. Nhấn **Deploy** -> **New deployment**.
5. Chọn loại **Web app**:
   - *Execute as*: **Me**
   - *Who has access*: **Anyone** (Bất kỳ ai)
6. Nhấn **Deploy**, cấp quyền và sao chép đường dẫn **Web App URL** (có dạng `https://script.google.com/macros/s/.../exec`).
7. Mở trang web khảo sát -> Click **Khu vực quản trị** (Mã: `nghean2026`) -> Dán URL vào ô **Cấu hình Google Sheets Webhook URL** -> Click **Lưu URL**.

Tất cả các lượt điền phiếu trực tuyến từ bất kỳ đâu sẽ tự động đẩy về bảng Google Sheets của bạn theo thời gian thực!
