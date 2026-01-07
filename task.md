# Task Checklist: Vietnamese Lunar Calendar App

## Phase 1: Project Foundation & Setup
- [x] Create Flutter project
- [x] Configure dependencies in pubspec.yaml
- [x] Set up project directory structure
- [x] Create configuration files (analysis_options.yaml, .gitignore)
- [x] Verify project builds on Android
- [x] Verify project builds on iOS

## Phase 2: Core Lunar Algorithm Engine
- [ ] Port Ho Ngoc Duc Algorithm to Dart
  - [ ] jdFromDate function
  - [ ] getLunarDate function
  - [ ] solarToLunar function
  - [ ] lunarToSolar function
  - [ ] calculateSolarTerm function
  - [ ] getCanChi function
- [ ] Create LunarDate model
- [ ] Create CalendarCell model
- [ ] Write unit tests for algorithm
  - [ ] Test Tet 2024
  - [ ] Test Tet 2025
  - [ ] Test Mid-Autumn Festival
  - [ ] Test leap month scenarios
  - [ ] Test solar terms
  - [ ] Test Can Chi accuracy
- [ ] Verify algorithm performance (<10ms per calculation)

## Phase 3: Database Layer & Event System
- [ ] Set up SQLite database
- [ ] Create events table schema
- [ ] Implement database singleton
- [ ] Create Event domain model
- [ ] Implement EventRepository
  - [ ] createEvent
  - [ ] updateEvent
  - [ ] deleteEvent
  - [ ] getAllEvents
  - [ ] getEventsForDay (critical logic)
- [ ] Create Riverpod providers for events
- [ ] Test CRUD operations
- [ ] Test event filtering logic

## Phase 4: Daily View UI
- [ ] Create date state providers
  - [ ] currentDateProvider
  - [ ] lunarDateProvider
- [ ] Build DailyViewScreen
- [ ] Create DateDisplay widget
- [ ] Create CanChiDisplay widget
- [ ] Implement navigation controls (prev/next/today)
- [ ] Add floating action button for new event
- [ ] Test UI on various screen sizes

## Phase 5: Monthly Calendar Grid
- [ ] Create CalendarService
- [ ] Implement getMonthCells logic
- [ ] Build MonthlyViewScreen
- [ ] Create CalendarCell widget
- [ ] Create MonthHeader widget
- [ ] Implement month navigation
- [ ] Add event indicators
- [ ] Test grid rendering performance

## Phase 6: Event Management
- [ ] Build EventFormScreen
  - [ ] Title input
  - [ ] Date type toggle (Solar/Lunar)
  - [ ] Custom date picker
  - [ ] Repeat type selector
  - [ ] Reminder configuration
- [ ] Create LunarDatePicker widget
- [ ] Create DateTypeToggle widget
- [ ] Build EventListScreen
- [ ] Create EventCard widget
- [ ] Implement search/filter
- [ ] Test event creation and editing

## Phase 7: Navigation & Routing
- [ ] Configure go_router
- [ ] Define all routes
- [ ] Create router providers
- [ ] Build bottom navigation bar
- [ ] Test navigation flows
- [ ] Test deep linking

## Phase 8: Assets & Localization
- [ ] Create quotes.json
- [ ] Create festivals.json
- [ ] Add background textures
- [ ] Add app icons
- [ ] Create splash screen graphic
- [ ] Define app theme
- [ ] Create color constants
- [ ] Add Vietnamese localization strings
- [ ] Test asset loading

## Phase 9: Advanced Features
- [ ] Build age calculator screen
- [ ] Create FengShuiService
- [ ] Implement good/bad day indicators
- [ ] Create ZodiacHourWidget
- [ ] Test feng shui calculations
- [ ] Test zodiac hour accuracy

## Phase 10: Notifications & Reminders
- [ ] Add notification dependencies
- [ ] Create NotificationService
- [ ] Create NotificationScheduler
- [ ] Implement notification channels
- [ ] Handle notification taps
- [ ] Schedule lunar event notifications
- [ ] Test notification timing
- [ ] Test recurring notifications

## Phase 11: Testing & Quality Assurance
- [ ] Write unit tests for all services
- [ ] Write widget tests
- [ ] Write integration tests
- [ ] Achieve 80%+ code coverage
- [ ] Manual testing on Android devices
- [ ] Manual testing on iOS devices
- [ ] Test landscape/portrait modes
- [ ] Performance testing

## Phase 12: Platform-Specific Polish
- [ ] Configure Android manifest
- [ ] Set up Android splash screen
- [ ] Configure Android build.gradle
- [ ] Configure iOS Info.plist
- [ ] Create iOS app icons
- [ ] Set up iOS Podfile
- [ ] Generate app icons with flutter_launcher_icons
- [ ] Set up native splash with flutter_native_splash
- [ ] Test on physical Android device
- [ ] Test on physical iOS device

## Phase 13: Performance Optimization
- [ ] Run Flutter DevTools profiler
- [ ] Measure frame rendering times
- [ ] Optimize algorithm performance
- [ ] Optimize UI rendering
- [ ] Optimize database queries
- [ ] Compress image assets
- [ ] Achieve 60 FPS target
- [ ] Reduce memory usage

## Phase 14: Documentation & Code Quality
- [ ] Add Vietnamese comments for business logic
- [ ] Add English comments for technical code
- [ ] Write DartDoc comments for public APIs
- [ ] Create README.md
- [ ] Create ARCHITECTURE.md
- [ ] Create LUNAR_ALGORITHM.md
- [ ] Run flutter analyze (fix all issues)
- [ ] Run dart format
- [ ] Apply riverpod_lint rules
- [ ] Remove unused code

## Phase 15: Pre-Release Preparation
- [ ] Create app screenshots
- [ ] Create feature graphic
- [ ] Write app description (Vietnamese)
- [ ] Write app description (English)
- [ ] Create privacy policy
- [ ] Create terms of service
- [ ] Deploy to Google Play Internal Testing
- [ ] Deploy to TestFlight
- [ ] Gather beta tester feedback
- [ ] Fix critical bugs from beta
- [ ] Build Android release bundle
- [ ] Build iOS release bundle
- [ ] Configure version numbers
- [ ] Set up app signing

## Phase 16: Launch & Monitoring
- [ ] Submit to Google Play Store
- [ ] Submit to Apple App Store
- [ ] Respond to review feedback
- [ ] Address any rejections
- [ ] Set up crash reporting
- [ ] Monitor initial launch
- [ ] Track store reviews
- [ ] Gather user feedback
- [ ] Plan first update
