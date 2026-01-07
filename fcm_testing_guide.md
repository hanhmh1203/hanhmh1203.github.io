# Firebase Cloud Messaging - Testing Guide

## Bước 1: Lấy FCM Token

1. Hot restart app
2. Mở app trên thiết bị
3. Check console logs, tìm dòng:
   ```
   🔔 [FCM] Token: xxxxxxxxxxxxxx...
   ```
4. Copy toàn bộ token

---

## Bước 2: Vào Firebase Console

1. Truy cập: https://console.firebase.google.com/
2. Chọn project của bạn
3. Vào **Cloud Messaging** (menu bên trái)
4. Click **Send your first message** hoặc **New campaign** → **Firebase Notification messages**

---

## Bước 3: Gửi Test Notification

### Case A: Gửi cho 1 máy cụ thể (Dùng Token - Để Test)
Cách này an toàn nhất khi đang code, vì chỉ máy bạn nhận được:
1. Click **"Send test message"** trên Firebase Console.
2. Paste FCM token của máy bạn vào.
3. Click **"Test"**.

### Case B: Gửi cho toàn bộ Device (Không cần Token)
Dùng khi bạn muốn push tin tức hoặc sự kiện mới cho tất cả người dùng:
1. Chỗ mục **"Target"**, chọn **"User segment"**.
2. Chọn ứng dụng của bạn (ví dụ: `com.vn.egn.lichgio`).
3. Firebase sẽ tự động gửi cho **tất cả** các máy đã cài app.
4. **Không cần nhập bất kỳ token nào.**

---

## Bước 4: Thêm Custom Data

Sau khi click **Send test message**, bạn cần thêm custom data:

### Test 1: Navigate to Daily View (Today)

**Additional options** → **Custom data**

| Key | Value |
|-----|-------|
| type | navigate |
| screen | daily_view |

### Test 2: Navigate to Daily View (Specific Date)

| Key | Value |
|-----|-------|
| type | navigate |
| screen | daily_view |
| date | 2026-01-29 |

### Test 3: Navigate to Events List

| Key | Value |
|-----|-------|
| type | navigate |
| screen | events_list |

### Test 4: Navigate to Feng Shui

| Key | Value |
|-----|-------|
| type | navigate |
| screen | feng_shui |

### Test 5: Navigate to Culture

| Key | Value |
|-----|-------|
| type | navigate |
| screen | culture |

### Test 6: Import Event (Tết Nguyên Đán)

| Key | Value |
|-----|-------|
| type | import_event |
| event | `{"title":"Tết Nguyên Đán","day":1,"month":1,"event_type":"LUNAR","color_value":4294198070,"emoji":"🧧","notes":"Năm mới âm lịch","repeat":"YEARLY"}` |

**Lưu ý:** Value của `event` phải là JSON string (không có khoảng trắng)

### Test 7: Import Event (Valentine)

| Key | Value |
|-----|-------|
| type | import_event |
| event | `{"title":"Valentine","day":14,"month":2,"event_type":"SOLAR","color_value":4293467747,"emoji":"💘","notes":"Lễ tình nhân","repeat":"YEARLY"}` |

---

## Bước 5: Test Scenarios

### Scenario 1: Foreground (App đang mở)
1. Mở app
2. Send notification từ Firebase Console
3. **Expected:** Console logs hiển thị message
4. Tap notification → Navigate to screen

### Scenario 2: Background (App ở background)
1. Minimize app (home button)
2. Send notification
3. **Expected:** Notification xuất hiện trong notification tray
4. Tap notification → App mở và navigate

### Scenario 3: Terminated (App đã đóng)
1. Force close app
2. Send notification
3. **Expected:** Notification xuất hiện
4. Tap notification → App khởi động và navigate

---

## Payload Format cho Backend API

Nếu bạn muốn gửi từ backend server, dùng format này:

### Navigate to Daily View
```json
{
  "to": "FCM_TOKEN_HERE",
  "notification": {
    "title": "Xem lịch hôm nay",
    "body": "Kiểm tra ngày âm lịch"
  },
  "data": {
    "type": "navigate",
    "screen": "daily_view",
    "date": "2026-01-29"
  }
}
```

### Import Event
```json
{
  "to": "FCM_TOKEN_HERE",
  "notification": {
    "title": "Sự kiện mới: Tết Nguyên Đán",
    "body": "Thêm vào lịch của bạn"
  },
  "data": {
    "type": "import_event",
    "event": {
      "title": "Tết Nguyên Đán",
      "day": 1,
      "month": 1,
      "event_type": "LUNAR",
      "color_value": 4294198070,
      "emoji": "🧧",
      "notes": "Năm mới âm lịch",
      "repeat": "YEARLY"
    }
  }
}
```

**API Endpoint:** `https://fcm.googleapis.com/fcm/send`

**Headers:**
```
Content-Type: application/json
Authorization: key=YOUR_SERVER_KEY
```

---

## Troubleshooting

### Không nhận được notification?

1. **Check token:** Đảm bảo token đúng và mới nhất
2. **Check permissions:** iOS cần approve notification permissions
3. **Check logs:** Xem console có error không
4. **Check internet:** Device phải có internet

### Notification không navigate?

1. **Check payload:** Đảm bảo `type` và `screen` đúng
2. **Check logs:** Xem NotificationHandler có log không
3. **Check routing:** Đảm bảo routes tồn tại trong app_router.dart

### Event không import?

1. **Check JSON format:** Event data phải là valid JSON
2. **Check required fields:** title, day, month, event_type
3. **Check logs:** Xem có validation error không

---

## Quick Test Commands

### Using cURL (for backend testing)

```bash
curl -X POST https://fcm.googleapis.com/fcm/send \
  -H "Content-Type: application/json" \
  -H "Authorization: key=YOUR_SERVER_KEY" \
  -d '{
    "to": "FCM_TOKEN",
    "data": {
      "type": "navigate",
      "screen": "daily_view"
    }
  }'
```

---

## Color Values Reference

Các màu phổ biến cho events:

| Color | Value | Hex |
|-------|-------|-----|
| Đỏ | 4294198070 | #FF5446 |
| Hồng | 4293467747 | #FFB3E3 |
| Xanh dương | 4280424179 | #42A5F5 |
| Xanh lá | 4280723098 | #66BB6A |
| Vàng | 4294944550 | #FFEB3B |
| Cam | 4294192950 | #FF9800 |

---

## Next Steps

1. ✅ Test tất cả navigation scenarios
2. ✅ Test event import
3. ⚠️ Implement actual event save to database
4. ⚠️ Add duplicate detection
5. ⚠️ Setup backend server để gửi notifications
