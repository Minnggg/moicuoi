# Hướng dẫn sử dụng Form RSVP với Google Sheets

## Tổng quan

Form RSVP đã được cấu hình để tự động lưu dữ liệu vào Google Sheets khi người dùng submit form. Bạn có thể theo dõi và xuất dữ liệu sang Excel.

## Các bước thiết lập

### 1. Tạo Google Sheet và lấy Sheet ID

1. Truy cập https://sheets.google.com
2. Tạo Sheet mới với tên "RSVP Data"
3. Tạo header row (dòng 1) với các cột:
   ```
   Thời gian | Tên khách | Sẽ đến | Bữa tiệc | Khách mời của | Đi cùng | Lời chúc
   ```
4. Copy Sheet ID từ URL:
   - URL có dạng: `https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID/edit`
   - Copy phần `YOUR_SHEET_ID`

### 2. Tạo Google Apps Script

1. Trong Google Sheet, click **Extensions** → **Apps Script**
2. Xóa code mặc định
3. Copy code từ file `GOOGLE_SHEETS_SETUP.md` (phần code JavaScript)
4. Thay `YOUR_SHEET_ID_HERE` bằng Sheet ID của bạn
5. Lưu project (Ctrl+S)

### 3. Deploy Web App

1. Click **Deploy** → **New deployment**
2. Chọn **Web app** (click icon ⚙️ nếu không thấy)
3. Cấu hình:
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Click **Deploy**
5. **Copy Web App URL** (dạng: `https://script.google.com/macros/s/.../exec`)
6. **Authorize** khi được yêu cầu:
   - Click "Authorize access"
   - Chọn tài khoản Google
   - Click "Advanced" → "Go to [project name] (unsafe)"
   - Click "Allow"

### 4. Cập nhật URL trong HTML

1. Mở file `index.html`
2. Tìm dòng: `const GOOGLE_SCRIPT_URL = '';`
3. Paste Web App URL vào giữa dấu nháy đơn:
   ```javascript
   const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
   ```
4. Lưu file

### 5. Test

1. Mở trang web trong trình duyệt
2. Điền form và click "XÁC NHẬN THAM DỰ"
3. Kiểm tra Google Sheet - dữ liệu sẽ xuất hiện ngay lập tức

## Xuất sang Excel

1. Mở Google Sheet
2. Click **File** → **Download** → **Microsoft Excel (.xlsx)**
3. File Excel sẽ được tải về máy

## Theo dõi real-time

- Dữ liệu được cập nhật ngay khi có người submit form
- Có thể mở Google Sheet trên nhiều thiết bị để theo dõi
- Có thể chia sẻ Google Sheet với người khác để cùng theo dõi

## Xử lý lỗi

### Lỗi: "Vui lòng cấu hình Google Script URL"
- **Nguyên nhân:** Chưa paste Google Script URL vào file HTML
- **Giải pháp:** Làm lại bước 4

### Lỗi: "Có lỗi xảy ra khi gửi dữ liệu"
- **Nguyên nhân:** Google Apps Script chưa được authorize hoặc Sheet ID sai
- **Giải pháp:** 
  - Kiểm tra lại Sheet ID trong Google Apps Script
  - Deploy lại Web App và authorize lại

### Dữ liệu không xuất hiện trong Sheet
- Kiểm tra Google Apps Script có lỗi không (View → Execution log)
- Kiểm tra Sheet ID có đúng không
- Thử submit lại form

## Lưu ý quan trọng

- Mỗi lần sửa code trong Apps Script, cần **Deploy lại** với version mới
- Nếu thay đổi Sheet ID, cần cập nhật trong Apps Script và Deploy lại
- Google Apps Script có giới hạn 20,000 requests/ngày (miễn phí)

## Hỗ trợ

Nếu gặp vấn đề, kiểm tra:
1. Console của trình duyệt (F12 → Console) để xem lỗi JavaScript
2. Execution log trong Google Apps Script (View → Execution log)
3. Đảm bảo Web App đã được authorize đúng cách

