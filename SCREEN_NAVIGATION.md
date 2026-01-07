# Lịch Vạn Niên - Sơ đồ Màn hình & Điều hướng

## 1. Bottom Navigation Structure

```mermaid
flowchart TB
    subgraph App["Lịch Vạn Niên App"]
        subgraph BottomNav["Bottom Navigation Bar (5 tabs)"]
            T1["🗓️ Ngày<br/>(DailyViewScreen)"]
            T2["📅 Tháng<br/>(MonthlyGridScreen)"]
            T3["📋 Sự Kiện<br/>(EventsListScreen)"]
            T4["🕯️ Ngày Giỗ<br/>(DeathAnniversaryListScreen)"]
            T5["👤 Cá nhân<br/>(ProfileHomeScreen)"]
        end
    end
    
    T1 <--> T2 <--> T3 <--> T4 <--> T5
```

---

## 2. Complete Navigation Flow

```mermaid
flowchart TB
    subgraph Tab1["Tab: Ngày"]
        DV["DailyViewScreen<br/>/"]
    end
    
    subgraph Tab2["Tab: Tháng"]
        MG["MonthlyGridScreen<br/>/monthly"]
    end
    
    subgraph Tab3["Tab: Sự Kiện"]
        EL["EventsListScreen<br/>/events"]
    end
    
    subgraph Tab4["Tab: Ngày Giỗ"]
        AL["DeathAnniversaryListScreen<br/>/death-anniversaries"]
    end
    
    subgraph Tab5["Tab: Cá nhân"]
        PH["ProfileHomeScreen<br/>/profile"]
    end
    
    subgraph EventRoutes["Event Routes (Full Screen)"]
        AE["AddEventScreen<br/>/events/add"]
        ED["EventDetailScreen<br/>/events/detail"]
        EE["EditEventScreen<br/>/events/edit"]
    end
    
    subgraph AnniversaryRoutes["Anniversary Routes (Full Screen)"]
        AA["AddDeathAnniversaryScreen<br/>/death-anniversaries/add"]
        AD["DeathAnniversaryDetailScreen<br/>/death-anniversaries/:id"]
        AEd["EditDeathAnniversaryScreen<br/>/death-anniversaries/:id/edit"]
    end
    
    subgraph ProfileRoutes["Profile Routes (Full Screen)"]
        PL["PersonListScreen<br/>/profile/persons"]
        PA["AddPersonScreen<br/>/profile/persons/add"]
        PD["PersonDetailScreen<br/>/profile/persons/:id"]
        PE["EditPersonScreen<br/>/profile/persons/:id/edit"]
        SS["SettingsScreen<br/>/profile/settings"]
        RT["RelationshipTypesScreen<br/>/profile/settings/relationship-types"]
        BS["BackupSettingsScreen<br/>/profile/settings/backup"]
        UP["UserProfileScreen<br/>/profile/my-profile"]
    end
    
    %% Calendar interactions
    MG -->|Tap cell| DV
    
    %% Event flows
    EL -->|FAB +| AE
    EL -->|Tap event| ED
    ED -->|Edit button| EE
    DV -->|Tap event| ED
    
    %% Anniversary flows
    AL -->|FAB +| AA
    AL -->|Tap anniversary| AD
    AD -->|Edit button| AEd
    DV -->|Tap anniversary| AD
    
    %% Profile flows
    PH -->|Người thân| PL
    PH -->|Cài đặt| SS
    PH -->|Hồ sơ| UP
    PL -->|FAB +| PA
    PL -->|Tap person| PD
    PD -->|Edit button| PE
    SS -->|Loại quan hệ| RT
    SS -->|Backup| BS
```

---

## 3. Screen Details

### 3.1 Tab: Ngày (Daily View)

```mermaid
flowchart LR
    subgraph DailyViewScreen["DailyViewScreen"]
        direction TB
        H["Header: Navigation arrows, Today button"]
        SD["Solar Date Card<br/>Ngày/Tháng/Năm dương"]
        LD["Lunar Date Card<br/>Ngày âm, Can Chi"]
        CC["Can Chi Detail<br/>Ngày/Tháng/Năm"]
        ST["Solar Term<br/>(if any)"]
        EV["Events List<br/>Sự kiện trong ngày"]
        AN["Anniversaries List<br/>Ngày giỗ trong ngày"]
    end
    
    H --> SD --> LD --> CC --> ST --> EV --> AN
```

### 3.2 Tab: Tháng (Monthly Grid)

```mermaid
flowchart TB
    subgraph MonthlyGridScreen["MonthlyGridScreen"]
        direction TB
        H["Header: < Tháng Năm >"]
        WD["Weekday Headers<br/>T2 T3 T4 T5 T6 T7 CN"]
        G["6x7 Grid of CalendarCells"]
        
        subgraph Cell["Each Cell"]
            SN["Solar Day Number"]
            LN["Lunar Day Number"]
            EI["Event Indicator (dot)"]
            AI["Anniversary Indicator (dot)"]
        end
    end
    
    H --> WD --> G
    G --> Cell
```

### 3.3 Tab: Sự Kiện (Events)

```mermaid
flowchart TB
    subgraph EventsListScreen["EventsListScreen"]
        H["AppBar: Sự Kiện"]
        SEARCH["Search Bar"]
        TABS["Tabs: Tất cả | Dương lịch | Âm lịch"]
        LIST["Event Cards List"]
        FAB["FAB: + Thêm sự kiện"]
    end
    
    subgraph EventCard["Event Card"]
        EMOJI["Emoji"]
        TITLE["Title"]
        DATE["Date (Solar/Lunar)"]
        REPEAT["Repeat Type Badge"]
    end
    
    H --> SEARCH --> TABS --> LIST --> FAB
    LIST --> EventCard
```

### 3.4 Tab: Ngày Giỗ (Anniversaries)

```mermaid
flowchart TB
    subgraph DeathAnniversaryListScreen["DeathAnniversaryListScreen"]
        H["AppBar: Ngày Giỗ"]
        UPCOMING["Upcoming Section<br/>Sắp tới trong 30 ngày"]
        ALL["All Section<br/>Tất cả ngày giỗ"]
        FAB["FAB: + Thêm ngày giỗ"]
    end
    
    subgraph AnniversaryCard["Anniversary Card"]
        NAME["Person Name"]
        REL["Relationship"]
        LUNAR["Lunar Death Date"]
        NEXT["Next Solar Date"]
        YEARS["Năm thứ X"]
    end
    
    H --> UPCOMING --> ALL --> FAB
    UPCOMING --> AnniversaryCard
    ALL --> AnniversaryCard
```

### 3.5 Tab: Cá nhân (Profile)

```mermaid
flowchart TB
    subgraph ProfileHomeScreen["ProfileHomeScreen"]
        H["AppBar: Cá nhân"]
        AD["AdMob Banner"]
        
        subgraph Menu["Menu Items"]
            M1["👤 Hồ sơ của tôi"]
            M2["👥 Người thân"]
            M3["⚙️ Cài đặt"]
            M4["☁️ Sao lưu & Khôi phục"]
            M5["ℹ️ Thông tin ứng dụng"]
        end
    end
    
    H --> AD --> Menu
    
    M1 -->|Navigate| UP["UserProfileScreen"]
    M2 -->|Navigate| PL["PersonListScreen"]
    M3 -->|Navigate| SS["SettingsScreen"]
    M4 -->|Navigate| BS["BackupSettingsScreen"]
```

---

## 4. Form Screens

### 4.1 Add/Edit Event Screen

```mermaid
flowchart TB
    subgraph AddEventScreen["AddEventScreen / EditEventScreen"]
        H["AppBar: Thêm/Sửa Sự kiện"]
        
        subgraph Form["Form Fields"]
            F1["📝 Tiêu đề *"]
            F2["📅 Loại: Dương lịch / Âm lịch"]
            F3["🗓️ Ngày (LunarCalendarBottomSheet)"]
            F4["🔄 Lặp lại: Không / Tuần / Tháng / Năm"]
            F5["🔔 Nhắc nhở: Toggle + Multi-select days"]
            F6["🎨 Màu sắc: Color picker"]
            F7["😀 Emoji: Emoji picker"]
            F8["📝 Ghi chú"]
        end
        
        BTN["💾 Lưu"]
    end
    
    H --> Form --> BTN
```

### 4.2 Add/Edit Death Anniversary Screen

```mermaid
flowchart TB
    subgraph AddDeathAnniversaryScreen["AddDeathAnniversaryScreen"]
        H["AppBar: Thêm/Sửa Ngày giỗ"]
        
        subgraph Form["Form Fields"]
            F1["👤 Tên người mất *"]
            F2["🔗 Quan hệ (Dropdown)"]
            F3["📅 Ngày mất (Solar) *"]
            F4["🌙 Ngày âm (auto-calculated)"]
            F5["🔔 Nhắc nhở: Toggle + Multi-select days"]
            F6["🔗 Liên kết người thân (optional)"]
            F7["📝 Ghi chú"]
        end
        
        BTN["💾 Lưu"]
    end
    
    H --> Form --> BTN
```

### 4.3 Add/Edit Person Screen

```mermaid
flowchart TB
    subgraph PersonScreen["AddPersonScreen / EditPersonScreen"]
        H["AppBar: Thêm/Sửa Người thân"]
        
        subgraph Form["Form Fields"]
            F1["👤 Họ tên *"]
            F2["📂 Nhóm: Gia đình / Bạn bè / Đồng nghiệp"]
            F3["🔗 Quan hệ cụ thể (Dropdown based on F2)"]
            F4["🎂 Ngày sinh (optional)"]
            F5["☠️ Đã mất: Toggle"]
            F6["⚰️ Ngày mất (if F5 = true)"]
            F7["📝 Ghi chú"]
        end
        
        BTN["💾 Lưu"]
    end
    
    H --> Form --> BTN
```

---

## 5. Route Configuration

### 5.1 Route Constants

```dart
// lib/core/routing/routes.dart

class AppRoutes {
  // Bottom Navigation Routes
  static const String daily = '/';
  static const String monthly = '/monthly';
  static const String events = '/events';
  static const String deathAnniversaries = '/death-anniversaries';
  static const String profile = '/profile';
  
  // Full-screen Routes
  static const String backupSettings = '/profile/settings/backup';
  static const String myProfile = '/profile/my-profile';
}

class RouteNames {
  static const String daily = 'daily';
  static const String monthly = 'monthly';
  static const String events = 'events';
  static const String deathAnniversaries = 'deathAnniversaries';
  static const String profile = 'profile';
  
  static const String addEvent = 'addEvent';
  static const String eventDetail = 'eventDetail';
  static const String editEvent = 'editEvent';
  
  static const String addDeathAnniversary = 'addDeathAnniversary';
  static const String deathAnniversaryDetail = 'deathAnniversaryDetail';
  static const String editDeathAnniversary = 'editDeathAnniversary';
  
  static const String personList = 'personList';
  static const String addPerson = 'addPerson';
  static const String personDetail = 'personDetail';
  static const String editPerson = 'editPerson';
  
  static const String settings = 'settings';
  static const String relationshipTypes = 'relationshipTypes';
  static const String backupSettings = 'backupSettings';
  static const String myProfile = 'myProfile';
}
```

### 5.2 Navigation Examples

```dart
// Navigate to add event
context.pushNamed(RouteNames.addEvent);

// Navigate to event detail (passing event object)
context.pushNamed(
  RouteNames.eventDetail,
  extra: event,
);

// Navigate to person detail (passing ID in path)
context.pushNamed(
  RouteNames.personDetail,
  pathParameters: {'id': person.id.toString()},
);

// Pop back
context.pop();

// Go to root (daily view)
context.go(AppRoutes.daily);
```

---

## 6. Screen Files Location

| Screen | File Path |
|--------|-----------|
| DailyViewScreen | `lib/features/calendar/presentation/screens/daily_view_screen.dart` |
| MonthlyGridScreen | `lib/features/calendar/presentation/screens/monthly_grid_screen.dart` |
| EventsListScreen | `lib/features/events/presentation/screens/events_list_screen.dart` |
| AddEventScreen | `lib/features/events/presentation/screens/add_event_screen.dart` |
| EditEventScreen | `lib/features/events/presentation/screens/edit_event_screen.dart` |
| EventDetailScreen | `lib/features/events/presentation/screens/event_detail_screen.dart` |
| DeathAnniversaryListScreen | `lib/features/death_anniversary/presentation/screens/death_anniversary_list_screen.dart` |
| AddDeathAnniversaryScreen | `lib/features/death_anniversary/presentation/screens/add_death_anniversary_screen.dart` |
| EditDeathAnniversaryScreen | `lib/features/death_anniversary/presentation/screens/edit_death_anniversary_screen.dart` |
| DeathAnniversaryDetailScreen | `lib/features/death_anniversary/presentation/screens/death_anniversary_detail_screen.dart` |
| ProfileHomeScreen | `lib/features/profile/presentation/screens/profile_home_screen.dart` |
| PersonListScreen | `lib/features/profile/presentation/screens/person_list_screen.dart` |
| AddPersonScreen | `lib/features/profile/presentation/screens/add_person_screen.dart` |
| EditPersonScreen | `lib/features/profile/presentation/screens/edit_person_screen.dart` |
| PersonDetailScreen | `lib/features/profile/presentation/screens/person_detail_screen.dart` |
| SettingsScreen | `lib/features/profile/presentation/screens/settings_screen.dart` |
| RelationshipTypesScreen | `lib/features/profile/presentation/screens/relationship_types_screen.dart` |
| BackupSettingsScreen | `lib/features/backup/presentation/screens/backup_settings_screen.dart` |
| UserProfileScreen | `lib/features/profile/presentation/screens/user_profile_screen.dart` |

---

## 7. Important UI Components

### 7.1 LunarCalendarBottomSheet

> **QUY ƯỚC QUAN TRỌNG**: Tất cả date picker trong app PHẢI dùng `LunarCalendarBottomSheet`

```dart
// lib/shared/widgets/lunar_calendar_bottom_sheet.dart

// Usage:
final DateTime? picked = await LunarCalendarBottomSheet.show(
  context,
  initialDate: _selectedDate,
  firstDate: DateTime(1900),
  lastDate: DateTime(2100),
);

if (picked != null) {
  setState(() => _selectedDate = picked);
}
```

**Features:**
- Hiển thị âm lịch dưới mỗi ngày dương
- Chọn năm/tháng nhanh (tap tiêu đề)
- Nút "Hôm nay" để nhảy về hiện tại
- Highlight ngày đầu tháng âm
- Xác nhận hiển thị ngày đã chọn
