# Implementation Plan: Vietnamese Lunar Calendar Mobile Application

## Overview

Building a standalone Flutter mobile application for Vietnamese users to view the Lunar Calendar (Lịch Vạn Niên) and manage cultural events (Death Anniversaries - Ngày Giỗ) for Android and iOS platforms.

**Core Principles:**
- Offline-first architecture
- Algorithm-based lunar calculation (Ho Ngoc Duc Algorithm)
- Clean Architecture with feature-first organization
- flutter_riverpod for state management
- SQLite for user data only

---

## Phase 1: Project Foundation & Setup
**Duration:** 1-2 days  
**Goal:** Establish project structure with proper architecture and dependencies

### 1.1 Project Initialization

#### [NEW] Create Flutter Project
```bash
flutter create amlich_app
cd amlich_app
flutter pub get
```

#### [MODIFY] pubspec.yaml
Add required dependencies:
- `flutter_riverpod: ^2.4.0`
- `riverpod_annotation: ^2.3.0`
- `sqflite: ^2.3.0`
- `go_router: ^12.0.0`
- `intl: ^0.18.0`
- `path_provider: ^2.1.0`

Dev dependencies:
- `riverpod_generator: ^2.3.0`
- `build_runner: ^2.4.0`
- `riverpod_lint: ^2.3.0`
- `flutter_lints: ^3.0.0`

### 1.2 Project Structure Setup

Create the following directory structure:

```
lib/
├── core/
│   ├── algorithm/
│   │   └── lunar_algo.dart
│   ├── constants/
│   │   └── app_constants.dart
│   └── utils/
│       └── date_helpers.dart
├── features/
│   ├── calendar/
│   │   ├── data/
│   │   ├── domain/
│   │   └── presentation/
│   └── events/
│       ├── data/
│       ├── domain/
│       └── presentation/
├── shared/
│   ├── models/
│   ├── providers/
│   └── widgets/
└── main.dart
```

### 1.3 Configuration Files

#### [NEW] analysis_options.yaml
Configure strict linting rules for code quality

#### [NEW] .gitignore
Add Flutter and IDE-specific ignore rules

**Verification:** 
- Project builds successfully on both Android and iOS
- All dependencies resolve without conflicts
- Directory structure is created

---

## Phase 2: Core Lunar Algorithm Engine
**Duration:** 3-5 days  
**Goal:** Implement the lunar calculation algorithm - the "brain" of the application

### 2.1 Algorithm Implementation

#### [NEW] lib/core/algorithm/lunar_algo.dart
Port the Ho Ngoc Duc Algorithm from JavaScript to Dart:
- **Function:** `jdFromDate(DateTime date)` - Convert solar date to Julian Day Number
- **Function:** `getLunarDate(int jd)` - Get lunar date from Julian Day
- **Function:** `solarToLunar(DateTime solarDate, int timeZone)` - Main conversion function
- **Function:** `lunarToSolar(LunarDate lunarDate, int timeZone)` - Reverse conversion
- **Function:** `calculateSolarTerm(DateTime date)` - Calculate 24 solar terms
- **Function:** `getCanChi(int year, int month, int day)` - Get Heavenly Stems & Earthly Branches

**Important Considerations:**
- Handle timezone correctly (GMT+7 for Vietnam)
- Account for leap months (tháng nhuận)
- Ensure precision in astronomical calculations
- Use `compute()` for heavy calculations to avoid blocking UI

### 2.2 Domain Models

#### [NEW] lib/shared/models/lunar_date.dart
```dart
class LunarDate {
  final int day;
  final int month;
  final int year;
  final bool isLeapMonth;
  final String canDay;
  final String chiDay;
  final String canMonth;
  final String chiMonth;
  final String canYear;
  final String chiYear;
  final String solarTerm;
  
  // Getters for formatted strings
  String get fullDateString;
  String get canChiDayString;
  String get canChiMonthString;
  String get canChiYearString;
}
```

#### [NEW] lib/shared/models/calendar_cell.dart
```dart
class CalendarCell {
  final DateTime solarDate;
  final LunarDate lunarDate;
  final bool isCurrentMonth;
  final bool isToday;
  final bool hasEvent;
  final List<Event> events;
}
```

### 2.3 Unit Testing

#### [NEW] test/core/algorithm/lunar_algo_test.dart
Create comprehensive test cases:
- **Test:** Tet 2024 (Jan 29, 2024 = Lunar 1/1/2024)
- **Test:** Tet 2025 (Jan 29, 2025 = Lunar 1/1/2025)
- **Test:** Mid-Autumn Festival 2024 (Sep 17, 2024 = Lunar 15/8/2024)
- **Test:** Leap month scenarios
- **Test:** Solar term calculations
- **Test:** Can Chi accuracy for known dates

**Verification:**
- All unit tests pass with 100% accuracy
- Algorithm performance: < 10ms per calculation
- Edge cases handled (leap months, year boundaries)

---

## Phase 3: Database Layer & Event System
**Duration:** 2-3 days  
**Goal:** Implement local storage for user events using SQLite

### 3.1 Database Setup

#### [NEW] lib/features/events/data/database/app_database.dart
- Initialize SQLite database
- Create `events` table with proper schema
- Handle database migrations
- Implement database singleton pattern

**Schema:**
```sql
CREATE TABLE events (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  base_date INTEGER NOT NULL,
  event_type TEXT NOT NULL,
  repeat_type TEXT NOT NULL,
  remind INTEGER NOT NULL DEFAULT 0,
  remind_before INTEGER NOT NULL DEFAULT 0,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL
)
```

### 3.2 Event Domain Models

#### [NEW] lib/features/events/domain/models/event.dart
```dart
enum EventType { SOLAR, LUNAR }
enum RepeatType { NONE, WEEKLY, MONTHLY, YEARLY }

class Event {
  final int? id;
  final String title;
  final DateTime baseDate;
  final EventType type;
  final RepeatType repeat;
  final bool remind;
  final int remindBefore;
  final DateTime createdAt;
  final DateTime updatedAt;
  
  // Conversion methods
  Map<String, dynamic> toMap();
  factory Event.fromMap(Map<String, dynamic> map);
}
```

### 3.3 Repository Pattern

#### [NEW] lib/features/events/data/repositories/event_repository.dart
Implement CRUD operations:
- `createEvent(Event event)` - Add new event
- `updateEvent(Event event)` - Update existing event
- `deleteEvent(int id)` - Remove event
- `getAllEvents()` - Fetch all events
- `getEventsForDay(DateTime solarDate)` - **Critical function**
  - Query all events from DB
  - Filter by date type (SOLAR/LUNAR)
  - For LUNAR events: Convert solar date to lunar, then match
  - Handle repeat patterns
  - Handle leap month logic

#### [NEW] lib/features/events/data/providers/event_providers.dart
Create Riverpod providers:
- `eventRepositoryProvider` - Repository instance
- `allEventsProvider` - Stream of all events
- `eventsForDayProvider(DateTime date)` - Events for specific day

**Verification:**
- Database creates successfully on first launch
- CRUD operations work correctly
- Event filtering logic handles all scenarios
- Memory leaks prevented (proper stream disposal)

---

## Phase 4: Daily View UI
**Duration:** 2-3 days  
**Goal:** Create the main daily view screen with navigation

### 4.1 State Management

#### [NEW] lib/shared/providers/date_providers.dart
```dart
@riverpod
class CurrentDate extends _$CurrentDate {
  @override
  DateTime build() => DateTime.now();
  
  void setDate(DateTime date) => state = date;
  void nextDay() => state = state.add(Duration(days: 1));
  void previousDay() => state = state.subtract(Duration(days: 1));
  void today() => state = DateTime.now();
}

@riverpod
LunarDate lunarDate(LunarDateRef ref) {
  final currentDate = ref.watch(currentDateProvider);
  return solarToLunar(currentDate, 7); // GMT+7
}
```

### 4.2 Daily View Screen

#### [NEW] lib/features/calendar/presentation/screens/daily_view_screen.dart
Layout structure:
- **Top Section:** Solar date (large, prominent)
- **Middle Section:** Lunar date with Can Chi
- **Bottom Section:** Solar term, events for the day
- **Navigation:** Previous/Next day buttons, "Today" button
- **Floating Action Button:** Add event

**Design Considerations:**
- Traditional Vietnamese aesthetics (red/gold accents)
- Paper texture background
- Readable typography (Google Fonts: Noto Sans Vietnamese)
- Smooth transitions between days

#### [NEW] lib/features/calendar/presentation/widgets/date_display.dart
Reusable widget for displaying dates

#### [NEW] lib/features/calendar/presentation/widgets/can_chi_display.dart
Widget for Can Chi information with proper formatting

**Verification:**
- UI displays correctly on various screen sizes
- Date navigation works smoothly
- Lunar calculation displays accurate data
- Events appear for the selected day

---

## Phase 5: Monthly Calendar Grid
**Duration:** 3-4 days  
**Goal:** Implement traditional calendar grid view with month navigation

### 5.1 Calendar Grid Logic

#### [NEW] lib/features/calendar/domain/services/calendar_service.dart
Key functions:
- `getMonthCells(int year, int month)` - Generate 35-42 calendar cells
  - Calculate first day of month
  - Add padding days from previous month
  - Add padding days for next month
  - For each day: Calculate lunar date using algorithm
  - Mark today, current month days, days with events
  - Return `List<CalendarCell>`

**Performance Optimization:**
- Run conversion for 42 days (acceptable performance)
- Consider caching if needed
- Use `compute()` for large date ranges

### 5.2 Monthly Grid UI

#### [NEW] lib/features/calendar/presentation/screens/monthly_view_screen.dart
Layout:
- **Header:** Month/Year selector with prev/next buttons
- **Week Days:** S M T W T F S headers
- **Grid:** 6 rows × 7 columns calendar grid
- **Cell Content:**
  - Solar date (primary)
  - Lunar date (secondary, smaller)
  - Event indicator (dot or badge)
  - Special day highlighting (Tet, festivals)

#### [NEW] lib/features/calendar/presentation/widgets/calendar_cell.dart
Individual cell widget:
- Tap to navigate to daily view
- Visual states: today, selected, has event, different month
- Lunar date display below solar date

#### [NEW] lib/features/calendar/presentation/widgets/month_header.dart
Month navigation header

**Verification:**
- Grid displays correctly with proper padding days
- All dates show accurate lunar information
- Month navigation works smoothly
- Event indicators appear correctly
- Performance: Smooth scrolling, no lag

---

## Phase 6: Event Management
**Duration:** 2-3 days  
**Goal:** Implement full event CRUD interface

### 6.1 Event Form

#### [NEW] lib/features/events/presentation/screens/event_form_screen.dart
Form fields:
- **Title:** Text input (e.g., "Giỗ Ông Nội")
- **Date Type:** Toggle between Solar/Lunar calendar
- **Date Picker:** Custom picker supporting both calendar types
  - For Lunar: Show lunar months, handle leap months
- **Repeat:** Dropdown (None, Weekly, Monthly, Yearly)
- **Reminder:** Toggle + time picker (minutes/hours/days before)
- **Save/Cancel buttons**

#### [NEW] lib/features/events/presentation/widgets/lunar_date_picker.dart
Custom date picker for lunar dates:
- Display lunar months (including leap months)
- Visual feedback for special dates
- Conversion preview (show corresponding solar date)

#### [NEW] lib/features/events/presentation/widgets/date_type_toggle.dart
Switch between Solar and Lunar calendar input

### 6.2 Event List

#### [NEW] lib/features/events/presentation/screens/event_list_screen.dart
- Display all events in chronological order
- Group by upcoming/past
- Swipe to delete
- Tap to edit
- Search/filter functionality

#### [NEW] lib/features/events/presentation/widgets/event_card.dart
Individual event display card

**Verification:**
- Can create events with both Solar and Lunar dates
- Leap month selection works correctly
- Events appear on correct dates in calendar
- Repeat logic works as expected (yearly anniversaries)
- Reminders can be set and configured

---

## Phase 7: Navigation & Routing
**Duration:** 1-2 days  
**Goal:** Implement app-wide navigation using go_router

### 7.1 Router Configuration

#### [NEW] lib/core/router/app_router.dart
Define routes:
- `/` - Daily View (home)
- `/monthly` - Monthly Grid View
- `/events` - Event List
- `/event/new` - Create Event
- `/event/:id` - Edit Event
- `/settings` - Settings Screen

Configure transitions and navigation guards

#### [NEW] lib/core/router/router_providers.dart
Riverpod provider for router instance

### 7.2 Bottom Navigation

#### [NEW] lib/shared/widgets/app_bottom_nav.dart
Bottom navigation bar:
- Daily View icon
- Monthly View icon
- Events icon
- Settings icon

**Verification:**
- Navigation works smoothly between all screens
- Deep linking supported
- Back button behavior is correct
- State persists during navigation

---

## Phase 8: Assets & Localization
**Duration:** 2 days  
**Goal:** Add cultural content and Vietnamese localization

### 8.1 Asset Integration

#### [NEW] assets/data/quotes.json
Traditional Vietnamese sayings and proverbs for each day

#### [NEW] assets/data/festivals.json
Important Vietnamese holidays and festivals with descriptions

#### [NEW] assets/images/
- Background textures (paper texture)
- Icons for app (traditional designs)
- Splash screen graphic

#### [MODIFY] pubspec.yaml
Register all assets

### 8.2 Theme & Styling

#### [NEW] lib/core/theme/app_theme.dart
Define app theme:
- Traditional color palette (red, gold, beige)
- Typography (Noto Sans Vietnamese from Google Fonts)
- Paper texture backgrounds
- Custom widget themes

#### [NEW] lib/core/theme/app_colors.dart
Color constants following Vietnamese aesthetic

### 8.3 Localization

#### [NEW] lib/core/l10n/app_localizations_vi.dart
Vietnamese strings:
- All UI labels
- Month names (both solar and lunar)
- Can/Chi names
- Solar term names
- Error messages

**Verification:**
- All text displays in Vietnamese
- Cultural elements appear correctly
- Theme is consistent across app
- Assets load without errors

---

## Phase 9: Advanced Features
**Duration:** 2-3 days  
**Goal:** Add specialized features for Vietnamese users

### 9.1 Lunar Age Calculator

#### [NEW] lib/features/calendar/presentation/screens/age_calculator_screen.dart
Calculate age based on lunar calendar (important for ceremonies)

### 9.2 Good/Bad Day Indicator

#### [NEW] lib/features/calendar/domain/services/feng_shui_service.dart
Calculate auspicious/inauspicious days based on traditional feng shui:
- Good days for weddings, business, travel
- Bad days to avoid
- Display on daily view with color coding

### 9.3 Zodiac Hour Display

#### [NEW] lib/features/calendar/presentation/widgets/zodiac_hour_widget.dart
Display 12 zodiac hours of the day with fortune indications

**Verification:**
- Age calculation matches traditional Vietnamese counting
- Feng shui indicators display correctly
- Zodiac hours accurate for selected date

---

## Phase 10: Notifications & Reminders
**Duration:** 2-3 days  
**Goal:** Implement local notifications for event reminders

### 10.1 Notification Setup

Add dependencies:
- `flutter_local_notifications: ^16.0.0`
- `timezone: ^0.9.0`

#### [NEW] lib/core/services/notification_service.dart
- Initialize notification channels
- Schedule notifications for events
- Handle notification taps
- Cancel notifications when events deleted

#### [NEW] lib/core/services/notification_scheduler.dart
**Critical Logic:**
- For SOLAR events: Schedule directly
- For LUNAR events: Convert to solar date first, then schedule
- Handle recurring events (reschedule after trigger)
- Update schedules when events modified

**Verification:**
- Notifications appear at correct times
- Lunar event notifications trigger on correct solar dates
- Tapping notification opens relevant event
- Notifications respect system settings

---

## Phase 11: Testing & Quality Assurance
**Duration:** 3-4 days  
**Goal:** Comprehensive testing across all features

### 11.1 Unit Tests

Create tests for:
- Lunar algorithm accuracy (extensive date validation)
- Event filtering logic
- Date conversion edge cases
- Repository operations

Target: 80%+ code coverage on business logic

### 11.2 Widget Tests

Test UI components:
- Calendar grid rendering
- Date pickers
- Event forms
- Navigation flows

### 11.3 Integration Tests

#### [NEW] integration_test/app_test.dart
Test complete user flows:
- Create event → View on calendar → Edit → Delete
- Navigate between views → Check data consistency
- Add lunar event → Verify appears on correct solar date

### 11.4 Manual Testing Checklist

Test on physical devices:
- Android (various versions, screen sizes)
- iOS (iPhone, iPad)
- Landscape/portrait orientation
- Dark mode (if implemented)
- Performance under load (many events)

**Verification:**
- All automated tests pass
- No crashes or freezes
- UI/UX is smooth on target devices
- Edge cases handled gracefully

---

## Phase 12: Platform-Specific Polish
**Duration:** 2-3 days  
**Goal:** Optimize for Android and iOS platforms

### 12.1 Android Configuration

#### [MODIFY] android/app/src/main/AndroidManifest.xml
- Set proper permissions
- Configure app name and icon
- Set launch mode

#### [NEW] android/app/src/main/res/values/styles.xml
Custom splash screen

#### [MODIFY] android/app/build.gradle
- Set minSdkVersion: 21
- Set targetSdkVersion: 34
- Configure app signing

### 12.2 iOS Configuration

#### [MODIFY] ios/Runner/Info.plist
- Configure app permissions
- Set supported orientations
- Configure app name

#### [NEW] ios/Runner/Assets.xcassets/AppIcon.appiconset/
Design app icons for all iOS sizes

#### [MODIFY] ios/Podfile
Set platform version: `platform :ios, '12.0'`

### 12.3 App Icons & Splash Screen

Use `flutter_launcher_icons`:
- Design traditional Vietnamese-themed icon
- Generate all required sizes
- Create adaptive icon for Android

Use `flutter_native_splash`:
- Create branded splash screen
- Configure for both platforms

**Verification:**
- App installs and runs on Android devices
- App installs and runs on iOS devices
- Icons display correctly
- Splash screen appears properly
- No platform-specific crashes

---

## Phase 13: Performance Optimization
**Duration:** 2 days  
**Goal:** Ensure smooth performance on all devices

### 13.1 Performance Analysis

- Run Flutter DevTools profiler
- Measure frame rendering times
- Identify bottlenecks
- Check memory usage

### 13.2 Optimizations

**Algorithm Performance:**
- Cache lunar calculations for visible month (memory vs. speed tradeoff)
- Use `compute()` for batch date conversions if needed

**UI Performance:**
- Implement lazy loading for event lists
- Use `const` constructors where possible
- Optimize image assets (compress, use appropriate formats)
- Minimize widget rebuilds with proper provider usage

**Database Performance:**
- Add indexes on frequently queried fields
- Batch database operations
- Use transactions for multiple inserts

#### [MODIFY] lib/core/algorithm/lunar_algo.dart
Add caching mechanism if needed

**Verification:**
- App maintains 60 FPS during normal use
- Initial load time < 2 seconds
- Calendar grid renders smoothly
- Memory usage stays under 100MB

---

## Phase 14: Documentation & Code Quality
**Duration:** 1-2 days  
**Goal:** Ensure code maintainability and quality

### 14.1 Code Documentation

Add comprehensive documentation:
- **Vietnamese comments** for cultural/business logic
- **English comments** for technical implementation
- DartDoc comments for public APIs
- README files in major directories

#### [NEW] README.md
Project overview, setup instructions, architecture

#### [NEW] docs/ARCHITECTURE.md
Detailed architecture documentation

#### [NEW] docs/LUNAR_ALGORITHM.md
Explanation of lunar calculation algorithm

### 14.2 Code Quality

- Run `flutter analyze` - fix all issues
- Run `dart format` - ensure consistent formatting
- Apply riverpod_lint rules
- Remove unused code and dependencies

**Verification:**
- Zero analyzer warnings/errors
- Code follows Dart style guide
- All public APIs documented
- Architecture diagram created

---

## Phase 15: Pre-Release Preparation
**Duration:** 2-3 days  
**Goal:** Prepare for store submission

### 15.1 App Store Assets

Create marketing materials:
- App screenshots (5-8 per platform)
- Feature graphic for Google Play
- App preview videos (optional but recommended)
- App description (Vietnamese and English)
- Privacy policy
- Terms of service

### 15.2 Beta Testing

- Deploy to Google Play Internal Testing
- Deploy to TestFlight
- Gather feedback from 10-20 beta testers
- Fix critical bugs
- Iterate based on feedback

### 15.3 Release Configuration

#### Android Release Build:
```bash
flutter build appbundle --release
```

#### iOS Release Build:
```bash
flutter build ios --release
```

Configure:
- Version number (1.0.0)
- Build number
- App signing certificates
- Store listings

**Verification:**
- Release builds work on real devices
- No debug code in release
- App size optimized (< 50MB)
- Beta testers approve

---

## Phase 16: Launch & Monitoring
**Duration:** 1 day  
**Goal:** Launch app and monitor initial performance

### 16.1 Store Submission

- Submit to Google Play Store
- Submit to Apple App Store
- Answer any review questions
- Address rejection reasons if needed

### 16.2 Monitoring Setup

Consider adding:
- Firebase Analytics (optional, conflicts with offline-first)
- Crashlytics for crash reporting
- Or use device-only logging

### 16.3 Post-Launch

- Monitor store reviews
- Track crash reports
- Gather user feedback
- Plan first update

**Verification:**
- App live on both stores
- No critical crashes reported
- User reviews monitored
- Support channel established

---

## Summary Timeline

| Phase | Duration | Cumulative |
|-------|----------|------------|
| 1. Foundation & Setup | 1-2 days | 2 days |
| 2. Lunar Algorithm Engine | 3-5 days | 7 days |
| 3. Database & Event System | 2-3 days | 10 days |
| 4. Daily View UI | 2-3 days | 13 days |
| 5. Monthly Calendar Grid | 3-4 days | 17 days |
| 6. Event Management | 2-3 days | 20 days |
| 7. Navigation & Routing | 1-2 days | 22 days |
| 8. Assets & Localization | 2 days | 24 days |
| 9. Advanced Features | 2-3 days | 27 days |
| 10. Notifications | 2-3 days | 30 days |
| 11. Testing & QA | 3-4 days | 34 days |
| 12. Platform Polish | 2-3 days | 37 days |
| 13. Performance Optimization | 2 days | 39 days |
| 14. Documentation | 1-2 days | 41 days |
| 15. Pre-Release Prep | 2-3 days | 44 days |
| 16. Launch & Monitoring | 1 day | 45 days |

**Total Estimated Time:** 6-7 weeks of focused development

---

## Critical Success Factors

1. **Algorithm Accuracy:** The lunar calculation MUST be 100% accurate. Verify against multiple sources.

2. **Offline-First:** App must work perfectly without internet connection. No API dependencies.

3. **Cultural Authenticity:** UI/UX must resonate with Vietnamese users. Traditional aesthetics are crucial.

4. **Leap Month Handling:** Special attention to leap month logic in both calculation and event matching.

5. **Performance:** Calendar grid must render smoothly even with many events.

6. **Data Integrity:** User events must never be lost. Implement proper backup/export.

---

## Risk Mitigation

| Risk | Impact | Mitigation |
|------|--------|------------|
| Algorithm inaccuracy | High | Extensive testing with known dates, cross-reference with official calendars |
| Poor performance on low-end devices | Medium | Early performance testing, optimize grid rendering |
| Complex lunar event matching logic | High | Write comprehensive unit tests, document thoroughly |
| App store rejection | Medium | Follow guidelines strictly, prepare appeal documentation |
| Database migration issues | Medium | Implement proper versioning, test upgrade paths |

---

## Next Steps

1. **Set up development environment** (Flutter SDK, Android Studio, Xcode)
2. **Create project repository** with Git
3. **Begin Phase 1** - Project foundation
4. **Establish CI/CD pipeline** early (GitHub Actions or similar)
5. **Schedule regular testing milestones** (end of each phase)

