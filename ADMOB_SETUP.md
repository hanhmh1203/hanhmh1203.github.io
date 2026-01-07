# Hướng dẫn lấy Real ID từ Google AdMob

Để thay thế Test ID bằng ID thật và bắt đầu kiếm tiền, bạn cần thực hiện các bước sau trên Google AdMob Console.

## Bước 1: Tạo tài khoản AdMob
1. Truy cập [apps.admob.com](https://apps.admob.com).
2. Đăng nhập bằng tài khoản Google của bạn.
3. Hoàn tất đăng ký nếu đây là lần đầu tiên (khai báo thông tin thanh toán, v.v.).

## Bước 2: Thêm ứng dụng vào AdMob
Bạn cần thêm ứng dụng 2 lần (một cho Android, một cho iOS).

### Cho Android:
1. Chọn **Apps** > **Add App**.
2. Chọn Platform: **Android**.
3. Chọn:
   - **Yes** nếu app đã có trên Google Play (bạn sẽ tìm theo tên app).
   - **No** nếu app chưa publish (chọn **Add** để thêm thủ công, sau này link store sau).
4. Đặt tên App (ví dụ: `Lịch Giỗ - Android`).
5. Sau khi tạo xong, bạn sẽ nhận được **App ID** (dạng `ca-app-pub-xxxxxxxxxxxxxxxx~yyyyyyyyyy`).
   - Copy ID này để dùng trong `AndroidManifest.xml`.

### Cho iOS:
1. Làm tương tự các bước trên nhưng chọn Platform: **iOS**.
2. Đặt tên App (ví dụ: `Lịch Giỗ - iOS`).
3. Nhận **App ID** cho iOS.
   - Copy ID này để dùng trong `Info.plist`.

## Bước 3: Tạo Ad Unit (Đơn vị quảng cáo)
Làm lần lượt cho cả 2 app Android và iOS vừa tạo.

1. Vào app vừa tạo > Chọn **Ad units** ở menu trái.
2. Chọn **Get started** (hoặc **Add ad unit**).
3. Chọn loại quảng cáo: **Banner**.
4. Đặt tên Ad Unit (ví dụ: `Home_Screen_Banner`).
5. Nhấn **Create ad unit**.
6. Bạn sẽ nhận được **Ad Unit ID** (dạng `ca-app-pub-xxxxxxxxxxxxxxxx/zzzzzzzzzz`).
   - Copy ID này để dùng trong file `lib/core/services/ad_helper.dart`.

## Bước 4: Cập nhật vào Code

### 1. Cập nhật App ID
- **Android:** File `android/app/src/main/AndroidManifest.xml`
  ```xml
  <meta-data
      android:name="com.google.android.gms.ads.APPLICATION_ID"
      android:value="PASTE_YOUR_ANDROID_APP_ID_HERE"/>
  ```
- **iOS:** File `ios/Runner/Info.plist`
  ```xml
  <key>GADApplicationIdentifier</key>
  <string>PASTE_YOUR_IOS_APP_ID_HERE</string>
  ```

### 2. Cập nhật Ad Unit ID
- Mở file `lib/core/services/ad_helper.dart`.
- Thay thế các chuỗi Test ID bằng ID thật bạn vừa tạo ở Bước 3.

```dart
static String get bannerAdUnitId {
  if (Platform.isAndroid) {
    return 'PASTE_YOUR_ANDROID_BANNER_ID_HERE';
  } else if (Platform.isIOS) {
    return 'PASTE_YOUR_IOS_BANNER_ID_HERE';
  }
  // ...
}
```

## Lưu ý quan trọng
- **Thời gian duyệt:** Khi mới tạo ID, có thể mất vài giờ đến 24h để quảng cáo bắt đầu hiển thị.
- **Test Device:** Khi chạy trên máy dev của bạn, hãy **LUÔN** khai báo thiết bị đó là Test Device trong AdMob console (Settings > Test devices) để tránh bị Google khóa tài khoản vì tự click quảng cáo.
- **App-ads.txt:** Đừng quên cấu hình file `app-ads.txt` nếu bạn publish app để bảo vệ doanh thu.
