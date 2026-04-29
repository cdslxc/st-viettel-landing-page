# Y-Fest Viettel Landing Page

Đây là mã nguồn cho trang Landing Page quét QR hiển thị ảnh Mosaic của sự kiện Y-Fest Viettel. 
Dự án được tối ưu hóa đặc biệt cho thiết bị di động, đảm bảo hình ảnh hiển thị đúng tỷ lệ trên nhiều kích thước màn hình và hỗ trợ tính năng **Tải ảnh trực tiếp vào Thư viện Ảnh (Photos/Gallery)**.

## Tính năng nổi bật

1. **Hash Routing (Định tuyến qua `#`)**: Không sử dụng URL parameter `?` để tránh bị các trình duyệt cache/thay đổi. Tải ảnh từ Dropbox tự động dựa vào `hash`.
2. **Hiển thị Ảnh (Image Fitting)**: Tự động điều chỉnh kích thước ảnh (căn giữa) để luôn vừa vặn với màn hình mà không bị méo (bảo toàn Aspect Ratio).
3. **Download Ảnh (Mobile-Friendly)**:
   - Sử dụng **Web Share API (`navigator.share`)**: Mở bảng chia sẻ mặc định của hệ điều hành (Share Sheet) để người dùng chọn "Lưu hình ảnh" (Save Image) thẳng vào Kho ảnh.
   - **Xử lý CORS**: Tự động chuyển đổi link chia sẻ của Dropbox (`www.dropbox.com`) sang link tải tĩnh (`dl.dropboxusercontent.com`) kết hợp `crossorigin="anonymous"` để tránh lỗi CORS và bypass (bỏ qua) tính năng chặn Redirect/Tracking của Safari trên iOS.
   - **Canvas toBlob**: Tránh lỗi Timeout tương tác người dùng của Safari bằng cách vẽ ảnh ra Canvas và lấy Blob ngay lập tức thay vì dùng `fetch()` tải lại file qua mạng.
   - **Cơ chế dự phòng (Fallback)**: Tự động tạo thẻ `<a>` để tải trực tiếp trên PC hoặc chuyển hướng đến URL file nếu API không khả dụng.

## Cách sử dụng Link (Tạo mã QR)

Để hiển thị một bức ảnh cụ thể từ Dropbox, hệ thống nhận các tham số từ **Hash URL**.

**Ví dụ một link Dropbox gốc:**
`https://www.dropbox.com/scl/fi/c9g3ohh5za0v9yfwgnh8j/200000011.jpg?rlkey=7rsrnq1zq7ni2wuzoe0i35p2v&st=pjvsudj1&dl=0`

**Link để nhúng vào QR Code / Gửi cho khách hàng:**
```text
https://[domain-cua-ban.com]/#id=c9g3ohh5za0v9yfwgnh8j/200000011.jpg&rlkey=7rsrnq1zq7ni2wuzoe0i35p2v&st=pjvsudj1
```

*Cấu trúc:*
- `id`: Lấy từ đoạn sau `/scl/fi/` hoặc `/s/` trong link Dropbox.
- `rlkey`: Tham số bảo mật (bắt buộc).
- `st`: Session token (nếu có trong link gốc, giúp đảm bảo quyền truy cập).

## Hướng dẫn Deploy lên Vercel

Mã nguồn hoàn toàn là Static HTML/CSS/JS thuần, không cần build (No Framework). Bạn có thể yêu cầu team Outsource đẩy lên Vercel cực kỳ đơn giản:

1. **Kết nối Github với Vercel**: 
   - Đăng nhập vào [Vercel](https://vercel.com).
   - Chọn "Add New Project" -> Import repository này.
2. **Cấu hình Framework Preset**: 
   - Vercel sẽ tự nhận dạng là `Other` (Static HTML). Không cần sửa gì thêm.
3. **Build Command & Output Directory**:
   - Cứ để trống (Mặc định). Vercel sẽ lấy thẳng thư mục gốc chứa `index.html` làm website.
4. **Custom Domain / Subdomain**:
   - Vào mục **Settings -> Domains** của Project trên Vercel để thêm Subdomain mong muốn (ví dụ: `yfest.viettel.vn`).
   - Trỏ bản ghi CNAME hoặc A record từ trang quản lý Tên miền theo hướng dẫn của Vercel.

## Cấu trúc thư mục

```text
├── index.html          # File code chính (UI, CSS nội trú, và JS xử lý logic tải ảnh)
├── validated_svg.svg   # File Logo SVG trên header đã được tối ưu viewBox
├── g14ZDAfw.jpeg       # Ảnh nền/Ảnh mặc định fallback
├── README.md           # Hướng dẫn sử dụng
└── .gitignore          # Cấu hình bỏ qua file rác
```

## Lưu ý cho Developer
- **KHÔNG** xóa thuộc tính `crossorigin="anonymous"` trong thẻ `<img id="dynamic-mosaic">`. Đây là key quan trọng để `canvas.toBlob()` hoạt động được với ảnh từ domain của bên thứ ba (Dropbox).
- **Kiểm thử trên di động**: Chỉ có trên di động (cụ thể là iOS/Android thật) thì hàm `navigator.share` mới hoạt động hiển thị "Save Image". Trên PC, tính năng tải ảnh sẽ tự động sử dụng Fallback (tải file xuống thư mục Downloads).
