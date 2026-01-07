# Lịch Vạn Niên - Sơ đồ Quan hệ Dữ liệu

## 1. Entity Relationship Diagram

```mermaid
erDiagram
    EVENTS {
        int id PK "Primary Key"
        text title "Tên sự kiện"
        int base_date "Timestamp ngày gốc"
        text event_type "SOLAR hoặc LUNAR"
        text repeat_type "NONE|WEEKLY|MONTHLY|YEARLY"
        int remind "0 hoặc 1"
        text remind_before_minutes "Danh sách ngày nhắc (comma-separated)"
        text notes "Ghi chú"
        int is_default "1 = holiday mặc định"
        int color_value "Mã màu ARGB"
        text emoji "Emoji hiển thị"
        int created_at "Timestamp tạo"
        int updated_at "Timestamp cập nhật"
    }
    
    PERSONS {
        int id PK "Primary Key"
        text name "Họ tên"
        text relationship_type FK "Loại quan hệ (key)"
        text relationship "Quan hệ cụ thể"
        int birth_date_solar "Ngày sinh dương (nullable)"
        int birth_day_lunar "Ngày sinh âm"
        int birth_month_lunar "Tháng sinh âm"
        int birth_year_lunar "Năm sinh âm"
        int birth_is_leap_month "Tháng nhuận"
        int death_date_solar "Ngày mất dương (nullable)"
        int death_day_lunar "Ngày mất âm"
        int death_month_lunar "Tháng mất âm"
        int death_year_lunar "Năm mất âm"
        int death_is_leap_month "Tháng nhuận"
        int is_deceased "Đã mất"
        text notes "Ghi chú"
        text image_path "Đường dẫn ảnh"
        int created_at "Timestamp"
        int updated_at "Timestamp"
    }
    
    DEATH_ANNIVERSARIES {
        int id PK "Primary Key"
        text person_name "Tên người mất"
        text relationship "Quan hệ"
        int death_date_solar "Ngày mất dương"
        int death_day_lunar "Ngày giỗ âm"
        int death_month_lunar "Tháng giỗ âm"
        int death_year_lunar "Năm mất âm"
        int is_leap_month "Tháng nhuận"
        int person_id FK "Link to Person (nullable)"
        text notes "Ghi chú"
        text image_path "Đường dẫn ảnh"
        int remind "Có nhắc"
        text remind_days_before "Ngày nhắc trước"
        int created_at "Timestamp"
        int updated_at "Timestamp"
    }
    
    RELATIONSHIP_TYPES {
        int id PK "Primary Key"
        text key UK "Khóa ổn định (family, friends, ...)"
        text name "Tên hiển thị"
        int icon_code "Mã icon"
        int display_order "Thứ tự hiển thị"
        int created_at "Timestamp"
        int updated_at "Timestamp"
    }
    
    RELATIONSHIP_ITEMS {
        int id PK "Primary Key"
        text type_key FK "Key của relationship_type"
        text name "Tên quan hệ (Ba, Mẹ, Anh, ...)"
        int display_order "Thứ tự"
        int is_default "Mặc định (không xóa được)"
        int created_at "Timestamp"
        int updated_at "Timestamp"
    }
    
    RELATIONSHIP_TYPES ||--o{ RELATIONSHIP_ITEMS : "có nhiều"
    RELATIONSHIP_TYPES ||--o{ PERSONS : "phân loại"
    PERSONS ||--o| DEATH_ANNIVERSARIES : "có ngày giỗ"
```

---

## 2. Domain Models Diagram

```mermaid
classDiagram
    class Event {
        +int? id
        +String title
        +DateTime baseDate
        +EventType type
        +RepeatType repeat
        +bool remind
        +List~int~ remindDaysList
        +String? notes
        +bool isDefault
        +int? colorValue
        +String? emoji
        +DateTime createdAt
        +DateTime updatedAt
        +Color get color
        +toMap() Map
        +fromMap(Map) Event
        +copyWith() Event
    }
    
    class EventType {
        <<enumeration>>
        SOLAR
        LUNAR
    }
    
    class RepeatType {
        <<enumeration>>
        NONE
        WEEKLY
        MONTHLY
        YEARLY
    }
    
    class Person {
        +int? id
        +String name
        +String relationshipType
        +String? relationship
        +DateTime? birthDateSolar
        +int? birthDayLunar
        +int? birthMonthLunar
        +int? birthYearLunar
        +bool? birthIsLeapMonth
        +DateTime? deathDateSolar
        +int? deathDayLunar
        +int? deathMonthLunar
        +int? deathYearLunar
        +bool? deathIsLeapMonth
        +bool isDeceased
        +String? notes
        +String? imagePath
        +DateTime createdAt
        +DateTime updatedAt
        +int? get age
        +bool get isAlive
        +String get displayName
    }
    
    class DeathAnniversary {
        +int? id
        +String personName
        +String? relationship
        +DateTime deathDateSolar
        +int deathDayLunar
        +int deathMonthLunar
        +int deathYearLunar
        +bool isLeapMonth
        +int? personId
        +String? notes
        +String? imagePath
        +bool remind
        +List~int~ remindDaysList
        +DateTime createdAt
        +DateTime updatedAt
        +String get lunarDeathDateString
        +String get displayName
    }
    
    class LunarDate {
        <<calculated - not stored>>
        +int day
        +int month
        +int year
        +bool isLeapMonth
        +String canDay
        +String chiDay
        +String canMonth
        +String chiMonth
        +String canYear
        +String chiYear
        +String? solarTerm
        +String get fullDateString
        +String get canChiDayString
        +bool get isSoc
        +bool get isVong
    }
    
    class CalendarCell {
        <<display model>>
        +DateTime solarDate
        +LunarDate lunarDate
        +bool isCurrentMonth
        +bool isToday
        +bool hasEvent
        +int eventCount
        +bool hasAnniversary
        +int anniversaryCount
        +bool get isWeekend
        +bool get isTet
    }
    
    class RelationshipType {
        +int? id
        +String key
        +String name
        +int? iconCode
        +int displayOrder
        +DateTime createdAt
        +DateTime updatedAt
        +IconData get icon
        +generateKey(String) String
    }
    
    class RelationshipItem {
        +int? id
        +String typeKey
        +String name
        +int displayOrder
        +bool isDefault
        +DateTime createdAt
        +DateTime updatedAt
    }
    
    Event --> EventType
    Event --> RepeatType
    DeathAnniversary --> Person : personId
    CalendarCell --> LunarDate
    Person --> RelationshipType : relationshipType
    RelationshipItem --> RelationshipType : typeKey
```

---

## 3. Data Flow Diagram

```mermaid
flowchart TB
    subgraph UserInterface["UI Layer"]
        S["Screens"]
        W["Widgets"]
    end
    
    subgraph StateManagement["State Management"]
        P["Riverpod Providers"]
        N["Notifiers"]
    end
    
    subgraph DataAccess["Data Access Layer"]
        ER["EventRepository"]
        PR["PersonRepository"]
        DAR["DeathAnniversaryRepository"]
        RTR["RelationshipTypeRepository"]
        RIR["RelationshipItemRepository"]
        BR["BackupRepository"]
    end
    
    subgraph Algorithm["Algorithm Layer"]
        LA["LunarAlgorithm"]
        VN["vnlunar package"]
    end
    
    subgraph Storage["Storage"]
        DB[(SQLite<br/>amlich.db)]
        SP[(SharedPreferences)]
        GD[(Google Drive)]
    end
    
    S --> P
    W --> P
    P --> N
    N --> ER
    N --> PR
    N --> DAR
    N --> RTR
    N --> RIR
    
    ER --> DB
    PR --> DB
    DAR --> DB
    RTR --> DB
    RIR --> DB
    
    P --> LA
    LA --> VN
    
    BR --> DB
    BR --> SP
    BR --> GD
```

---

## 4. Lunar Date Calculation Flow

```mermaid
flowchart LR
    subgraph Input["Input"]
        SD["Solar Date<br/>(DateTime)"]
    end
    
    subgraph Algorithm["LunarAlgorithm"]
        VNP["vnlunar.convertSolar2Lunar()"]
        CC["Can Chi Calculation"]
        ST["Solar Term Check"]
    end
    
    subgraph Output["Output"]
        LD["LunarDate<br/>(day, month, year, isLeapMonth,<br/>canDay, chiDay, solarTerm, ...)"]
    end
    
    SD --> VNP
    VNP --> CC
    CC --> ST
    ST --> LD
```

---

## 5. Event Matching Logic

```mermaid
flowchart TD
    Start["getEventsForDay(solarDate)"]
    Conv["Convert solarDate → LunarDate"]
    Fetch["Fetch all events from DB"]
    
    subgraph Match["For each Event"]
        Check{"Event Type?"}
        
        subgraph Solar["SOLAR Event"]
            MS1{"Repeat Type?"}
            MS2["YEARLY: match month+day"]
            MS3["MONTHLY: match day"]
            MS4["WEEKLY: match weekday"]
            MS5["NONE: exact match"]
        end
        
        subgraph Lunar["LUNAR Event"]
            ML1{"Repeat Type?"}
            ML2["YEARLY: match lunar month+day"]
            ML3["MONTHLY: match lunar day"]
            ML4["WEEKLY: match weekday"]
            ML5["NONE: exact lunar match"]
        end
    end
    
    Result["Return matched events"]
    
    Start --> Conv
    Conv --> Fetch
    Fetch --> Check
    Check -->|SOLAR| MS1
    Check -->|LUNAR| ML1
    MS1 --> MS2 & MS3 & MS4 & MS5
    ML1 --> ML2 & ML3 & ML4 & ML5
    MS2 & MS3 & MS4 & MS5 --> Result
    ML2 & ML3 & ML4 & ML5 --> Result
```

---

## 6. Database Migration Timeline

```mermaid
timeline
    title Database Version History
    
    v1 : Initial
       : events table
    
    v2 : +persons table
       : Family/Friends management
    
    v3 : +person_id in death_anniversaries
       : Link anniversary to person
    
    v4 : +relationship_types table
       : Custom relationship categories
    
    v5 : +key column in relationship_types
       : Stable identifier for queries
    
    v6 : +is_deceased in persons
       : Mark deceased without death date
    
    v7 : Make birth fields nullable
       : Allow persons without birth date
    
    v8 : +isDefault, colorValue, emoji in events
       : Seed default holidays
    
    v9 : +relationship_items table
       : Specific relationships per type
    
    v10 : Seed lunar holidays
        : Tết, Trung Thu, Vu Lan, ...
```

---

## 7. Backup Data Structure

```mermaid
classDiagram
    class BackupData {
        +String appVersion
        +int schemaVersion
        +DateTime createdAt
        +String? deviceInfo
        +List~Map~ events
        +List~Map~ persons
        +List~Map~ deathAnniversaries
        +List~Map~ relationshipTypes
        +List~Map~ relationshipItems
        +Map? userProfile
        +int get totalRecords
        +bool get isEmpty
        +toJson() Map
        +fromJson(Map) BackupData
    }
    
    class BackupMetadata {
        +String fileName
        +DateTime createdAt
        +String appVersion
        +int recordCount
        +bool isEncrypted
    }
    
    class BackupResult {
        +bool success
        +String? fileId
        +String? error
        +BackupMetadata? metadata
    }
    
    BackupData --> BackupMetadata : generates
    BackupResult --> BackupMetadata : contains
```
