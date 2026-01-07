# Lịch Vạn Niên - Tổng quan Trực quan Dự án

> **Mục đích**: Tài liệu trực quan để hiểu nhanh kiến trúc và cấu trúc ứng dụng

---

## 1. Kiến trúc Tổng quan

![Kiến trúc ứng dụng](images/app_architecture.png)

**Giải thích:**
- **Presentation Layer**: 5 màn hình chính tương ứng 5 tab bottom navigation
- **State Management**: Riverpod Providers quản lý state cho toàn app
- **Data Layer**: Repository pattern truy cập SQLite database
- **Lunar Algorithm**: Module tính toán âm lịch (vnlunar package)

---

## 2. Sơ đồ Quan hệ Dữ liệu (ERD)

![Database ERD](images/database_erd.png)

**5 Tables:**

| Table | Mô tả |
|-------|-------|
| **EVENTS** | Sự kiện (lễ hội, sinh nhật, ...) - hỗ trợ Solar/Lunar |
| **PERSONS** | Người thân (gia đình, bạn bè) |
| **DEATH_ANNIVERSARIES** | Ngày giỗ (luôn dựa trên âm lịch) |
| **RELATIONSHIP_TYPES** | Loại quan hệ (Gia đình, Bạn bè, Đồng nghiệp) |
| **RELATIONSHIP_ITEMS** | Quan hệ cụ thể (Ba, Mẹ, Anh, ...) |

**Quan hệ:**
- `PERSONS` → `RELATIONSHIP_TYPES`: 1 person thuộc 1 type
- `DEATH_ANNIVERSARIES` → `PERSONS`: Optional link
- `RELATIONSHIP_ITEMS` → `RELATIONSHIP_TYPES`: Many items per type

---

## 3. Luồng Điều hướng Màn hình

![Screen Navigation](images/screen_navigation.png)

**5 Tab chính:**

| Tab | Tên | Màn hình | Chức năng |
|-----|-----|----------|-----------|
| 1 | Ngày | DailyViewScreen | Xem chi tiết 1 ngày (âm-dương, Can Chi) |
| 2 | Tháng | MonthlyGridScreen | Lưới tháng 6x7, tap để xem ngày |
| 3 | Sự Kiện | EventsListScreen | CRUD sự kiện Solar/Lunar |
| 4 | Ngày Giỗ | DeathAnniversaryListScreen | CRUD ngày giỗ |
| 5 | Cá nhân | ProfileHomeScreen | Người thân, Cài đặt, Backup |

---

## 4. Domain Models

![Domain Models](images/domain_models.png)

**4 Model chính:**

### EVENT
```
- Sự kiện có thể là SOLAR (dương lịch) hoặc LUNAR (âm lịch)
- Repeat: None / Weekly / Monthly / Yearly
- Default events = Holidays (không xóa được)
```

### PERSON
```
- Lưu thông tin người thân
- Birth date: Optional (có thể không biết ngày sinh)
- Death date: Optional (cho người đã mất)
```

### DEATH_ANNIVERSARY
```
- Ngày giỗ luôn dựa trên âm lịch
- Có thể link với Person record
- Hỗ trợ tháng nhuận (isLeapMonth)
```

### LUNAR_DATE
```
- KHÔNG lưu database - tính toán real-time
- Bao gồm Can Chi (Thiên Can Địa Chi)
- Tiết khí (Solar Terms)
```

---

## 5. Tech Stack Tóm tắt

| Thành phần | Công nghệ |
|------------|-----------|
| Framework | Flutter 3.9+ |
| State Management | flutter_riverpod 2.5.1 |
| Database | sqflite (SQLite) |
| Navigation | go_router |
| Lunar Algorithm | vnlunar (Ho Ngoc Duc) |
| Notifications | flutter_local_notifications |
| Backup | Google Drive API |
| Ads | Google AdMob |

---

## 📚 Tài liệu Chi tiết

| File | Nội dung |
|------|----------|
| [PROJECT_DOCUMENTATION.md](PROJECT_DOCUMENTATION.md) | Tài liệu đầy đủ với code examples |
| [DATA_MODEL_DIAGRAM.md](DATA_MODEL_DIAGRAM.md) | Mermaid diagrams (text-based) |
| [SCREEN_NAVIGATION.md](SCREEN_NAVIGATION.md) | Navigation routes & screens |

---

## 🚀 Quick Start cho Developer Mới

```bash
# 1. Clone & install
flutter pub get

# 2. Code generation (Riverpod)
flutter pub run build_runner build --delete-conflicting-outputs

# 3. Run app
flutter run
```

**⚠️ Lưu ý quan trọng:**
- LUÔN dùng `LunarCalendarBottomSheet` thay vì `showDatePicker()`
- Chạy `build_runner` sau khi sửa `@riverpod` annotations
- Database version hiện tại: **10** (thêm version mới khi thay đổi schema)
