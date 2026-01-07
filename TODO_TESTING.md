# TODO: Testing & Improvements - Person ↔ Anniversary Linking

**Date:** 2025-12-07  
**Feature:** Person-Anniversary Linking  
**Status:** Implementation Complete ✅ | Testing Pending 🔍

---

## ✅ Đã hoàn thành (13 commits)

### Backend
- ✅ Database v3 migration (person_id + CASCADE DELETE)
- ✅ DeathAnniversary model với personId field
- ✅ PersonRepository auto-management logic
  - Auto-create anniversary khi add deceased person
  - Auto-sync khi update person
  - Cascade delete via database FK

### UI
- ✅ Anniversary detail → Person card với navigation
- ✅ Person detail → Anniversary card với navigation  
- ✅ Add Anniversary form → Person selection dropdown
- ✅ Edit Anniversary form → Person selection dropdown
- ✅ Empty state message khi chưa có deceased persons

---

## 🔍 Cần test (Manual Testing Required)

### Test Case 1: Auto-Create
**Steps:**
1. Mở Profile → Thành viên
2. Thêm người mới với:
   - Tên: "Ông Nội Test"
   - Loại: Gia đình
   - Ngày sinh: bất kỳ
   - ✅ Chọn "Đã mất"
   - Ngày mất: bất kỳ
3. Lưu

**Expected:**
- Tự động tạo ngày giỗ tương ứng
- Vào Ngày Giỗ → thấy "Ông Nội Test" trong danh sách
- Card "Liên kết thành viên" hiển thị trong anniversary detail

### Test Case 2: Auto-Sync
**Steps:**
1. Sửa person đã có anniversary (từ test 1)
2. Thay đổi ngày mất (solar date)
3. Lưu

**Expected:**
- Anniversary tự động update với ngày mới
- Lunar date tính lại đúng
- Tên/quan hệ cũng sync nếu thay đổi

### Test Case 3: Alive → Deceased
**Steps:**
1. Sửa person đang sống (chưa có anniversary)
2. Check "Đã mất" + nhập ngày mất
3. Lưu

**Expected:**
- Tự động tạo anniversary mới

### Test Case 4: Deceased → Alive
**Steps:**
1. Sửa person đã mất (có anniversary)
2. Uncheck "Đã mất"
3. Lưu

**Expected:**
- Anniversary bị xóa tự động

### Test Case 5: Cascade Delete
**Steps:**
1. Xóa person có liên kết anniversary
2. Confirm delete

**Expected:**
- Person deleted
- Anniversary cũng deleted (cascade)
- Không còn trong danh sách ngày giỗ

### Test Case 6: Manual Link
**Steps:**
1. Vào Ngày Giỗ → Thêm mới
2. Nhập thông tin
3. Click "Liên kết với thành viên"
4. Chọn person từ list
5. Lưu

**Expected:**
- Anniversary tạo với personId
- Person detail hiện "Ngày giỗ" card

### Test Case 7: Edit Link
**Steps:**
1. Sửa anniversary đã có link
2. Clear link (nút X)
3. Lưu
4. Sửa lại, chọn person khác

**Expected:**
- Link được update đúng
- Navigation cards update accordingly

### Test Case 8: Navigation
**Steps:**
1. Person detail → Click "Ngày giỗ" card
2. Anniversary detail → Click "Liên kết thành viên" card

**Expected:**
- Navigate qua lại giữa 2 screens
- Data consistent

---

## 🚀 Future Enhancements (Optional)

### Priority: Low
- [ ] **Bulk Link Tool**: Tool để link existing anniversaries với persons
- [ ] **Search in Dropdown**: Search persons by name trong dialog picker
- [ ] **Link Statistics**: Dashboard hiển thị số lượng linked/unlinked
- [ ] **Orphaned Report**: Report anniversaries không có person link
- [ ] **Quick Create**: Từ anniversary → create new person nhanh
- [ ] **Duplicate Detection**: Detect trùng khi tạo anniversary cho person đã có

### Priority: Very Low
- [ ] **Photo Sync**: Sync photo giữa person và anniversary
- [ ] **Relationship Suggestions**: AI suggest relationship type
- [ ] **Export/Import**: Backup/restore with links intact

---

## 📝 Notes

**Database Migration:**
- Migration v2→v3 an toàn cho existing data
- person_id nullable, không bắt buộc
- Không cần re-create existing anniversaries

**Performance:**
- Query optimized với personId index
- CASCADE DELETE ở database level (fast)
- Provider invalidation selective

**Edge Cases Handled:**
- ✅ Empty deceased persons list
- ✅ Person deleted → anniversary cascade
- ✅ Deceased → alive → anniversary deleted
- ✅ Multiple anniversaries per person (allowed)

**Not Implemented:**
- ⚠️ 1 person chỉ nên có 1 anniversary (business rule chưa enforce)
- ⚠️ Prevent duplicate manual creation (user có thể tạo 2 anniversary cho 1 person)

---

## 🐛 Known Issues

**None reported** - Feature mới, chưa có user feedback

---

## 🔗 Related Files

**Backend:**
- `lib/features/events/data/database/app_database.dart` (migration)
- `lib/features/death_anniversary/domain/models/death_anniversary.dart` (model)
- `lib/features/profile/data/repositories/person_repository.dart` (auto-management)

**UI:**
- `lib/features/death_anniversary/presentation/screens/death_anniversary_detail_screen.dart`
- `lib/features/profile/presentation/screens/person_detail_screen.dart`
- `lib/features/death_anniversary/presentation/screens/add_death_anniversary_screen.dart`
- `lib/features/death_anniversary/presentation/screens/edit_death_anniversary_screen.dart`

**Artifacts:**
- `walkthrough.md` - Complete feature documentation
- `implementation_plan.md` - Original implementation plan

---

**Last Updated:** 2025-12-07 21:57  
**Next Action:** Manual testing theo test cases trên ☝️
