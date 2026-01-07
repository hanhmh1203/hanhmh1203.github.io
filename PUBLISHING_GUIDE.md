# Hướng dẫn Publish App lên Google Play Store

Tài liệu này hướng dẫn chi tiết các bước để build bản release và đưa ứng dụng Lịch Giỗ lên Google Play Console.

## 1. Thông tin Ứng dụng
- **Tên App:** Lịch Giỗ
- **Package Name:** `com.vn.egn.lichgio` (Cần xác nhận trong `build.gradle.kts`)
- **Version:** `1.0.0+1` (Cập nhật trong `pubspec.yaml` mỗi lần release mới)

## 2. Chuẩn bị Signing Key
Dự án đã có sẵn file Keystore tại:
`android/app/lichgio-release.keystore`

Thông tin mật khẩu và alias được lưu trong file:
`android/key.properties` (File này không được commit lên git để bảo mật)

**Nội dung file `key.properties` mẫu:**
```properties
storePassword=YOUR_STORE_PASSWORD
keyPassword=YOUR_KEY_PASSWORD
keyAlias=YOUR_KEY_ALIAS
storeFile=lichgio-release.keystore
```

## 3. Tạo bản Release (App Bundle)
Google Play yêu cầu định dạng `.aab` (Android App Bundle) thay vì `.apk`.

Chạy lệnh sau tại thư mục gốc dự án:
```bash
flutter build appbundle --release
```

Sau khi build xong, file sẽ nằm tại:
`build/app/outputs/bundle/release/app-release.aab`

## 4. Upload lên Google Play Console

### Bước 4.1: Tạo Ứng dụng trên Console
1. Truy cập [play.google.com/console](https://play.google.com/console).
2. Chọn **Create app**.
3. Điền thông tin:
   - App name: **Lịch Giỗ**
   - Default language: **Vietnamese (vi-VN)**
   - App or Game: **App**
   - Free or Paid: **Free**

### Bước 4.2: Thiết lập thông tin (Store Listing)
Bạn cần chuẩn bị các thông tin sau:
- **Ngắn gọn (Short description):** (Tối đa 80 ký tự)
  VD: *Lịch vạn niên, lịch âm dương, xem ngày tốt xấu và nhắc lịch giỗ.*
- **Chi tiết (Full description):** (Tối đa 4000 ký tự)
  Mô tả đầy đủ các tính năng: Lịch Âm/Dương, Quản lý ngày giỗ, Sự kiện, Widget, v.v.
- **Ảnh chụp màn hình (Screenshots):**
  - Tối thiểu 2 ảnh cho điện thoại.
  - Tối thiểu 1 ảnh 7-inch tablet (nếu có hỗ trợ).
- **Icon:** 512x512 px (PNG/JPEG).
- **Feature Graphic:** 1024x500 px.

### Bước 4.3: Upload App Bundle
1. Vào mục **Production** (hoặc **Testing** > **Internal testing** nếu muốn test trước).
2. Chọn **Create new release**.
3. Upload file `app-release.aab` vừa tạo ở Bước 3.
4. Đặt tên Release (VD: `1.0.0 - Initial Release`).
5. Ghi chú thay đổi (Release notes).

### Bước 4.4: Chính sách & Quyền riêng tư
1. Khai báo **Data Safety** (An toàn dữ liệu):
   - App có thu thập dữ liệu gì không? (Tùy vào code, ví dụ AdMob có thu thập ID thiết bị để quảng cáo).
   - Có chia sẻ dữ liệu không?
2. Link **Privacy Policy** (Chính sách quyền riêng tư):
   - Bạn cần có một trang web hiển thị chính sách này. Nếu chưa có, có thể dùng Google Sites hoặc các dịch vụ tạo Privacy Policy miễn phí.

### Bước 4.5: Quảng cáo (Ads)
Trong mục **App content** > **Ads**, chọn:
- **Yes, my app contains ads** (Vì đã tích hợp AdMob).

## 5. Review & Publish
Sau khi hoàn tất các mục trên, bạn gửi bản release để Google xét duyệt (Review).
- Thời gian xét duyệt lần đầu: Thường từ 3-7 ngày.
- Các lần cập nhật sau: Thường nhanh hơn (vài giờ đến 24h).

## Lưu ý quan trọng
- **Tăng version:** Trước khi build bản mới để update, nhớ tăng `version` trong `pubspec.yaml` (ví dụ: `1.0.1+2`).
- **Test kỹ:** Nên dùng Internal Testing để mời team test trước khi Publish ra Production.
