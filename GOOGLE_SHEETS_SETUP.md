# Hướng dẫn Setup Google Sheets để lưu dữ liệu Form

## Bước 1: Tạo Google Sheet mới

1. Truy cập https://sheets.google.com
2. Tạo một Google Sheet mới
3. Đặt tên Sheet (ví dụ: "RSVP Data")
4. Tạo header row với các cột sau (dòng 1):
   - Thời gian
   - Tên khách
   - Sẽ đến
   - Bữa tiệc
   - Khách mời của
   - Đi cùng
   - Lời chúc

## Bước 2: Tạo Google Apps Script

1. Trong Google Sheet, click **Extensions** → **Apps Script**
2. Xóa code mặc định và paste code sau:

```javascript
function doPost(e) {
  try {
    // Lấy dữ liệu từ POST request
    let data;
    
    // Xử lý cả JSON và form data
    if (e.postData && e.postData.contents) {
      try {
        data = JSON.parse(e.postData.contents);
      } catch (e) {
        // Nếu không phải JSON, thử parse form data
        const params = e.parameter;
        data = {
          guest_name: params.guest_name || '',
          attendance: params.attendance || '',
          event_slot: params.event_slot || '',
          invited_by: params.invited_by || '',
          plus_one: params.plus_one || '',
          message: params.message || ''
        };
      }
    } else {
      // Lấy từ parameter nếu không có postData
      data = {
        guest_name: e.parameter.guest_name || '',
        attendance: e.parameter.attendance || '',
        event_slot: e.parameter.event_slot || '',
        invited_by: e.parameter.invited_by || '',
        plus_one: e.parameter.plus_one || '',
        message: e.parameter.message || ''
      };
    }
    
    // Lấy Sheet ID từ URL của Google Sheet
    // Ví dụ: https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID/edit
    const SHEET_ID = 'YOUR_SHEET_ID_HERE'; // Thay bằng Sheet ID của bạn
    const sheet = SpreadsheetApp.openById(SHEET_ID).getActiveSheet();
    
    // Thêm dữ liệu vào Sheet
    const timestamp = new Date();
    const row = [
      Utilities.formatDate(timestamp, Session.getScriptTimeZone(), 'yyyy-MM-dd HH:mm:ss'),
      data.guest_name || '',
      data.attendance || '',
      data.event_slot || '',
      data.invited_by || '',
      data.plus_one || '',
      data.message || ''
    ];
    
    sheet.appendRow(row);
    
    // Trả về response thành công (với CORS headers)
    return ContentService
      .createTextOutput(JSON.stringify({
        'status': 'success',
        'message': 'Dữ liệu đã được lưu thành công!'
      }))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch (error) {
    // Log lỗi để debug
    Logger.log('Error: ' + error.toString());
    
    // Trả về lỗi nếu có
    return ContentService
      .createTextOutput(JSON.stringify({
        'status': 'error',
        'message': error.toString()
      }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  return ContentService
    .createTextOutput('Google Sheets API is running!')
    .setMimeType(ContentService.MimeType.TEXT);
}
```

3. **Lấy Sheet ID:**
   - Trong URL của Google Sheet: `https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID/edit`
   - Copy phần `YOUR_SHEET_ID` và thay vào code ở dòng `const SHEET_ID = 'YOUR_SHEET_ID_HERE';`

4. **Lưu project:**
   - Click **File** → **Save** (hoặc Ctrl+S)
   - Đặt tên project (ví dụ: "RSVP Form Handler")

## Bước 3: Deploy Web App

1. Click **Deploy** → **New deployment**
2. Click icon ⚙️ (Settings) bên cạnh "Select type"
3. Chọn **Web app**
4. Điền thông tin:
   - **Description:** RSVP Form Handler
   - **Execute as:** Me
   - **Who has access:** Anyone
5. Click **Deploy**
6. **Copy Web App URL** (sẽ có dạng: `https://script.google.com/macros/s/.../exec`)
7. **Quan trọng:** Authorize access khi được yêu cầu:
   - Click "Authorize access"
   - Chọn tài khoản Google
   - Click "Advanced" → "Go to [project name] (unsafe)"
   - Click "Allow"

## Bước 4: Cập nhật URL trong HTML

1. Mở file `index.html`
2. Tìm dòng có `const GOOGLE_SCRIPT_URL = '';`
3. Paste Web App URL vào đó

## Bước 5: Test

1. Mở trang web
2. Điền form và submit
3. Kiểm tra Google Sheet xem dữ liệu đã được thêm chưa

## Lưu ý:

- Mỗi lần sửa code trong Apps Script, cần **Deploy lại** với version mới
- Có thể export Google Sheet sang Excel: **File** → **Download** → **Microsoft Excel (.xlsx)**
- Dữ liệu sẽ được cập nhật real-time trên Google Sheets

