# Changelog — Sales Analyst Dashboard

รูปแบบเวอร์ชัน: **Semantic Versioning** `MAJOR.MINOR.PATCH`
- **MAJOR** = เปลี่ยนโครงสร้าง/ดีไซน์ใหญ่ (breaking)
- **MINOR** = ฟีเจอร์ใหม่ (ไม่ทำของเดิมพัง)
- **PATCH** = แก้บั๊ก / แก้สูตรคำนวณ / แก้ UI / performance

สถานะ: `DRAFT` → `TESTING` → `STABLE` → `DEPRECATED`
กติกา: **ห้ามมาร์ก STABLE เอง** ต้องรอผู้ใช้ยืนยัน "Approved" / "ใช้งานได้" / "Stable"

Snapshot สมบูรณ์ที่รันได้ของแต่ละเวอร์ชันเก็บไว้ใน `versions/vX.Y.Z/`

---

## v2.3.0

- **Date:** 2026-09-18
- **Status:** ✅ STABLE (ผู้ใช้ยืนยัน "ใช้งานได้" 2026-09-18) — เวอร์ชันปัจจุบันบนเว็บจริง
- **Snapshot:** `versions/v2.3.0/` — **Rollback:** v2.0.0 (`versions/v2.0.0/`)
- **Files:** index.html, sw.js (cache `sales-dash-v25`), manifest.json, icon.svg
- **ที่มา:** ต่อยอดจาก v2.0.0 (สะอาด) — แก้บั๊กฟิลเตอร์เดือน แล้วดึงงานที่ดีจาก v2.2.0–v2.2.3
  (อัปหลายไฟล์ / รองรับไฟล์หลากรูปแบบ / dense mode) กลับมาทีละส่วนพร้อมทดสอบ

### 1) แก้บั๊ก "ฟิลเตอร์เดือนแล้วยอดไม่เปลี่ยน"
- **ต้นเหตุ:** คลิกชิปเดือนเป็นแบบ toggle (เพิ่ม/ลบจากหลายเดือน) — ถ้าเลือกหลายเดือนอยู่แล้วคลิก 1 เดือน
  จะแค่ลบเดือนนั้นออก ยอดเปลี่ยนนิดเดียว ผู้ใช้เลยรู้สึกว่า "ไม่เปลี่ยน"
- **แก้:** **คลิกเดือน = ดูเฉพาะเดือนนั้น (single-select)** → ยอดสลับเป็นเดือนนั้นทันทีชัดเจน;
  **Ctrl/⌘/Shift-คลิก = เลือกหลายเดือน**; ปุ่มลัด (3/6 เดือน/ทั้งปี) ยังไว้ดูช่วง
- เพิ่มคำอธิบายวิธีใช้ใต้แถบเดือน
- **กันเงียบ:** ถ้าเลือกเดือนที่ยังไม่โหลด (ออฟไลน์/ข้ามอุปกรณ์แล้วโหลดจากคลาวด์ไม่ได้) จะขึ้นเตือนชัดเจน
  ว่า "ยอดที่แสดงยังไม่รวมเดือนนี้" แทนที่จะดูเหมือนไม่มีอะไรเกิดขึ้น

### 2) ดึงงานรองรับไฟล์ + อัปหลายไฟล์กลับมา (จาก v2.2.0 + v2.2.1 + v2.2.2)
- **อัปหลายไฟล์พร้อมกัน** — เลือก/ลากไฟล์รายเดือนหลายไฟล์ทีเดียว, ประมวลผลต่อเนื่อง, สะสมรายเดือน
  (1 ไฟล์ = แสดงทุกเดือนในไฟล์; หลายไฟล์ = แสดงเดือนล่าสุด แล้วคลิกเดือนที่แถบเพื่อดูเดือนนั้น)
- จับคู่ชื่อคอลัมน์แบบไม่สนตัวพิมพ์/ช่องว่าง/อักขระซ่อน (`normHeader`)
- สแกนหา header ลึก 40 แถวแรก (รองรับไฟล์ที่มีหัวเรื่อง/ตัวกรองเหนือตาราง)
- ซ่อม `!ref` อัตโนมัติ (ไฟล์ที่อ่านออกมาว่าง เช่น "UnFinal") + error บอกรายละเอียด (sheet/!ref/cells)

### 3) รองรับไฟล์ใหญ่ (~40MB/ไฟล์) — ดึง dense mode + ตัวเร่งจาก v2.2.3 กลับมา
- **อาการ:** ไฟล์ Final Close ก.ค. (39.7MB) + ส.ค. (36.9MB) อัปไม่ได้ — โครงสร้างไฟล์ปกติทุกอย่าง
  (ตรวจแล้วผ่าน Google Drive) แต่หนักเกินไปเมื่ออ่านแบบธรรมดา 2 ไฟล์พร้อมกัน
- **dense mode** (อ่าน/แปลงเร็วขึ้น ~25 เท่า + ใช้แรมน้อยลง) พร้อม **fallback** เป็นการอ่านแบบปกติ
  ถ้าไฟล์ไหน parse ด้วย dense ไม่ผ่าน; `fixSheetRefs` รองรับทั้ง dense และ sparse
- `pickSheet` อ่านแค่ 40 แถวแรกแทนทั้ง sheet; ตรวจ dd/mm จากตัวอย่าง 5,000 แถว; ไฟล์สต็อกอ่านแบบ dense ด้วย
- ผล: parseWorkbook 8,000ms → ~2,900ms (sparse) / ~300ms (dense) ต่อไฟล์

### ✅ ผลตรวจสอบ (headless)
- คลิกเดือน single-select: ส.ค. ฿15.7M ↔ ก.ย. ฿62.7M เปลี่ยนถูกต้องทุก view
- ไฟล์ที่ !ref หาย → ซ่อมแล้วอ่านได้; Mer-Raw 33,194 แถว/17,947 บิล; regression `฿27,053,060`, errors NONE

---

## ⏪ ROLLBACK — 2026-09-18

- **ผู้ใช้สั่ง "ย้อนไป v2.0.0"** (แจ้งว่าเวอร์ชันหลังๆ "มั่ว")
- คืนไฟล์แอป (`index.html`, `manifest.json`, `icon.svg`) จาก `versions/v2.0.0/` เป็นเวอร์ชันที่ deploy จริง
- `sw.js` = เนื้อหา v2.0.0 แต่ bump cache เป็น `sales-dash-v24` เพื่อบังคับ browser โหลดใหม่สะอาด
- **เวอร์ชันที่ใช้งานจริงตอนนี้ = v2.0.0** (Multi-month + ตัด SEEDING + STOCK เดือนล่าสุด)
- **v2.1.0 – v2.2.3 ถูกย้อนออก** (snapshot ทุกตัวยังเก็บครบใน `versions/` — กลับไปใช้ใหม่ได้ทุกเมื่อ)
- ⚠️ สิ่งที่หายไปเมื่อกลับมา v2.0.0 (เผื่อทราบ): แก้ Store Type ในแอป, อัปหลายไฟล์ทีเดียว,
  การรองรับไฟล์ที่ header ลึก/`!ref` หาย (ไฟล์ ก.ค. "UnFinal" อาจอัปไม่ได้อีก), และความเร็ว dense
  — ถ้าต้องการฟีเจอร์ไหนกลับมาแบบเลือกเฉพาะ บอกได้ ผมหยิบทีละตัวจาก snapshot มาต่อยอดได้

---

## v2.2.3

- **Date:** 2026-09-17
- **Status:** 🗄️ DEPRECATED (ไม่ได้ deploy — งานถูกรวมเข้า v2.3.0 STABLE แล้ว)
- **Snapshot:** `versions/v2.2.3/` — **Rollback:** v2.2.2 (`versions/v2.2.2/`)
- **Files:** index.html, sw.js (cache `sales-dash-v23`), manifest.json, icon.svg

### เร่งความเร็วการประมวลผลไฟล์ (สำคัญเมื่ออัปหลายไฟล์ เช่น 21 ไฟล์)
- **ปัญหา:** อัป 21 ไฟล์ช้ามาก (~42 วิ/ไฟล์ ≈ 15 นาที) — v2.2.2 ยิ่งช้าเพราะ `fixSheetRefs` ไล่ทุกเซลล์ทุกไฟล์
- **แก้ให้เร็วขึ้น:**
  - อ่านไฟล์ด้วย **dense mode** → parse เร็วขึ้น **~25 เท่า** (8,000ms → ~300ms/ไฟล์)
  - `fixSheetRefs` รันเฉพาะตอน detection ล้มเหลว (ไม่ไล่ 7 ล้านเซลล์ทุกไฟล์) + รองรับ dense
  - `pickSheet` อ่านแค่ 40 แถวแรก (จากเดิมอ่านทั้ง sheet ซ้ำ)
  - ตรวจ dd/mm จากตัวอย่าง 5,000 แถวแรก (จากเดิมทั้งไฟล์)
  - **Fallback:** ถ้า dense parse ไม่ผ่าน อ่านซ้ำแบบ sparse (กันไฟล์แปลกๆ เช่นไฟล์ที่ !ref หาย)
- **UX:** แสดงความคืบหน้ารายไฟล์ "กำลังประมวลผลไฟล์ x/N: ชื่อไฟล์..." + yield ให้จอไม่ค้าง
- **ผลรวม:** ~42 วิ/ไฟล์ → **~25 วิ/ไฟล์** (ส่วน parse แทบหายไป; ที่เหลือคือเวลาอ่านไฟล์ดิบ ~27MB
  ซึ่งเป็นพื้นฐานของ SheetJS ลดต่อได้ยาก)
- **ทดสอบ:** dense ให้ผลเท่า sparse เป๊ะ (Mer-Raw 33,194 แถว, 17,947 บิล); regression `฿27,053,060`,
  stock/multi-file/!ref-fix ผ่านหมด, errors NONE

> 💡 คำแนะนำ: การอัปย้อนหลัง 21 เดือนเป็นงาน "ครั้งเดียว" (หลังจากนั้นเดือนละ 1 ไฟล์) — ทำทีละชุด
> 3–6 ไฟล์จะสบายกว่า; และถ้า export ไฟล์ยอดขายโดย**ไม่มีแถว STOCK** (~74% ของไฟล์) จะเล็กลงและเร็วขึ้นมาก

---

## v2.2.2

- **Date:** 2026-09-17
- **Status:** ✅ STABLE (ผู้ใช้ยืนยัน "อัพได้แล้ว" 2026-09-17)
  (รวมงาน v2.2.0 อัปหลายไฟล์ + v2.2.1 จับคู่คอลัมน์ยืดหยุ่น + v2.2.2 ซ่อม !ref)
- **Snapshot:** `versions/v2.2.2/` — **Rollback:** v2.2.1 (`versions/v2.2.1/`)
- **Files:** index.html, sw.js (cache `sales-dash-v22`), manifest.json, icon.svg

### แก้ไฟล์อ่านออกมาว่าง (`!ref` หาย) — ต่อจาก v2.2.1
- **อาการที่พบ:** ไฟล์ ก.ค. ขึ้น error `[sheets: Sheet1 • คอลัมน์ที่เจอ: (ว่าง)]` — SheetJS อ่าน sheet
  ได้แต่ได้ข้อมูลว่าง เพราะไฟล์ export ไม่ได้ประกาศช่วงข้อมูล (`!ref`) ของ sheet
- **แก้:** เพิ่ม `fixSheetRefs()` — คำนวณช่วงข้อมูลใหม่จาก**ที่อยู่เซลล์จริง**แล้วตั้ง `!ref` ให้ถูก
  ก่อนตรวจ header (แก้อัตโนมัติทุกไฟล์ที่ !ref หาย/แคบเกินจริง)
- **Diagnostic ละเอียดขึ้น:** ถ้ายังไม่ผ่าน error จะบอก `!ref` และจำนวนเซลล์ (`cells=N`) ด้วย
  — ถ้า `cells=0` แปลว่า SheetJS อ่านไฟล์ไม่ออกเลย (เช่นรูปแบบ Strict OOXML) → ต้อง Save As เป็น
  .xlsx มาตรฐานก่อน
- **ทดสอบ:** จำลอง !ref หาย → อ่านได้ 0 แถว → หลัง fixSheetRefs อ่านได้ครบ → parse สำเร็จ;
  ไฟล์เดิมไม่กระทบ (net `฿27,053,060`), errors NONE

---

## v2.2.1

- **Date:** 2026-09-17
- **Status:** 🗄️ DEPRECATED (รวมใน v2.2.2 STABLE — เก็บไว้ rollback)
- **Snapshot:** `versions/v2.2.1/` — **Rollback:** v2.2.0 (`versions/v2.2.0/`)
- **Files:** index.html, sw.js (cache `sales-dash-v21`), manifest.json, icon.svg

### แก้ความเข้ากันได้ของไฟล์ (บางไฟล์ขึ้น "ไม่พบ sheet ที่มีคอลัมน์ยอดขาย")
- **ปัญหา:** ไฟล์บางตัว (เช่น export แบบ "UnFinal") อัปไม่ได้ แม้จะมีคอลัมน์ครบ
- **แก้ให้ทนทานขึ้น:**
  - `normHeader()` — จับคู่ชื่อคอลัมน์แบบ **ไม่สนตัวพิมพ์ใหญ่/เล็ก, ช่องว่างซ้ำ, และอักขระซ่อน**
    (zero-width space / BOM / NBSP) ทั้งตอนหา sheet และ map คอลัมน์
  - ขยายการค้นหาแถว header จาก 8 → **40 แถวแรก** (รองรับไฟล์ที่มีแถวหัวเรื่อง/ตัวกรองอยู่เหนือ header)
  - ข้าม sheet ที่อ่านไม่ได้แทนที่จะล้มทั้งไฟล์
- **Error แจ้งรายละเอียดขึ้น:** ถ้ายังหา sheet ไม่เจอ จะบอก **รายชื่อ sheet + ชื่อคอลัมน์ที่ระบบเห็นจริง**
  เพื่อวินิจฉัยได้แม้ไฟล์ใหญ่เกินอัปโหลด
- **Regression:** ไฟล์เดิมยังอ่านถูกต้อง (Mer-Raw 33,194 แถว; ไฟล์เก่า net `฿27,053,060`), errors NONE

---

## v2.2.0

- **Date:** 2026-09-17
- **Status:** 🗄️ DEPRECATED (รวมใน v2.2.2 STABLE — เก็บไว้ rollback)
- **Snapshot:** `versions/v2.2.0/` (index.html, sw.js, manifest.json, icon.svg)
- **Rollback:** v2.1.0 (`versions/v2.1.0/`)
- **Files:** index.html, sw.js (cache `sales-dash-v20`), manifest.json, icon.svg

### อัปโหลดหลายไฟล์พร้อมกัน (Multi-file upload)
- เลือก/ลากไฟล์รายเดือน **หลายไฟล์ทีเดียว**ได้ (เช่น 23 ไฟล์) — เดิมอัปได้ทีละไฟล์
- ประมวลผลทีละไฟล์ต่อเนื่อง (async) แต่ละไฟล์แทนที่เฉพาะเดือนของตัวเอง (สะสม)
- กฎเดิมยังทำงาน: ตัด SEEDING ทุกไฟล์, เก็บ STOCK/Aging เฉพาะเดือนล่าสุดในบรรดาไฟล์ที่อัป
- หลังอัป: 1 เดือน → แสดงเดือนนั้น; หลายเดือน → **แสดงเดือนล่าสุด** (เบา) เลือกเพิ่มจากแถบเดือนได้
- ซิงค์ทุกเดือนที่อัปขึ้นคลาวด์ทีเดียว, ไฟล์ที่เปิดไม่สำเร็จจะข้ามและรายงานใน warnbar (ไม่ล้มทั้งชุด)
- input เพิ่ม `multiple`; reset ค่า input หลังอัปเพื่อเลือกไฟล์เดิมซ้ำได้

### ✅ ผลตรวจสอบ (headless)
- อัป 2 ไฟล์ทีเดียว (ก.ย. จริง + ส.ค. สังเคราะห์): ได้ 2 เดือน, เลือกเดือนล่าสุดอัตโนมัติ,
  SEEDING ตัดครบ (รวม 5 แถว), stockMonth=2026-09, ชิป 2 อัน, errors NONE
- single-file (regression) ยังทำงานปกติ net `฿27,053,060`

---

## v2.1.0

- **Date:** 2026-09-17
- **Status:** 🗄️ DEPRECATED (เก็บไว้ rollback — v2.2.2 ขึ้นแทน)
- **Snapshot:** `versions/v2.1.0/` (index.html, sw.js, manifest.json, icon.svg)
- **Rollback:** v2.0.0 (`versions/v2.0.0/` หรือ commit `eb4f903`)
- **Files:** index.html, sw.js (cache `sales-dash-v19`), manifest.json, icon.svg

### Store Type Library — แก้เอง/อัปเองได้ (ไม่ต้องแก้โค้ด)
- **ปุ่มใหม่ 2 ปุ่ม** (แถบเครื่องมือด้านบน):
  - **🏷️ Template ประเภทร้าน** — ดาวน์โหลดไฟล์ Excel ที่มีทุกร้านในข้อมูล **เติมประเภทปัจจุบันให้แล้ว**
    (คอลัมน์ Store_Code / Store_Name / Store_Type) → แก้ในไฟล์แล้วเซฟ
  - **🏷️ อัปโหลดประเภทร้าน** — อัปไฟล์ที่แก้กลับเข้าไป ระบบอัปเดตทันที
- **ลำดับความสำคัญใหม่:** คอลัมน์ Store Type ในไฟล์ยอดขาย (ถ้ามี) > **Library ที่อัปเอง (S.storeTypeLib)**
  > Library ฝังในโค้ด (ค่าเริ่มต้น) > "—"
- **อัปแบบผสาน (merge):** อัปไฟล์แล้วจะ**อัปเดต/เพิ่มเฉพาะรหัสในไฟล์** ร้านอื่นคงเดิม (แก้ทีละส่วนได้)
- **มีผลย้อนหลังทันที:** เมื่ออัป Library ใหม่ ระบบคำนวณ `stp` ของทุกแถวที่โหลดอยู่ใหม่ (ทุกเดือน + aging)
  แล้ว render ใหม่ — ไม่ต้องอัปไฟล์ยอดขายซ้ำ
- **บันทึกถาวร:** เก็บใน localStorage + คลาวด์ (`company/main/meta/storeTypes`) ใช้ข้ามอุปกรณ์ได้
- ค่าเริ่มต้น 164 ร้าน (จาก v1.6.0) ยังอยู่ — Library ที่อัปเองแค่มา "ทับ" เฉพาะรหัสที่ต้องการ

### ✅ ผลตรวจสอบ (headless)
- อัปไฟล์ override: KS001 Shop→FLAGSHIP-TEST, HS002 Shop→TESTMALL — เปลี่ยนทันที, ร้านอื่น (KS002) คงเดิม
- ตาราง/กราฟ "ราย Store Type" อัปเดตกลุ่มใหม่ทันที, ดาวน์โหลด template ได้ (store-type-library.xlsx)
- **Regression:** ไฟล์เก่า net คงที่ `฿27,053,060`, errors NONE
- **ไม่ใช่ KPI definition change** — เป็นการปรับ dimension (การจัดกลุ่ม) เท่านั้น

---

## v2.0.0

- **Date:** 2026-09-17
- **Status:** 🗄️ DEPRECATED (เก็บไว้ rollback — v2.1.0 ขึ้นแทน)
- **หมายเหตุการทดสอบ:** logic ทั้งหมดผ่าน headless test แล้ว; **ส่วนอ่าน/เขียนคลาวด์รายเดือน + migration
  ยังไม่ได้ทดสอบสด** (ติด Firebase auth/unauthorized-domain — ต้องเพิ่มโดเมน southboyth.github.io ใน
  Firebase Console ก่อน) ผู้ใช้ยอมรับความเสี่ยงและสั่งมาร์ก STABLE โดยมี rollback = v1.6.0 รองรับ
- **Snapshot:** `versions/v2.0.0/` (index.html, sw.js, manifest.json, icon.svg)
- **Rollback:** v1.6.0 (`versions/v1.6.0/` หรือ commit `6e0d291`)
- **Files:** index.html, sw.js (cache `sales-dash-v18`), manifest.json, icon.svg

### ระบบหลายเดือน (Multi-month) — อัปหลายไฟล์รายเดือนโดยไม่ทับกัน
- **โครงสร้างใหม่:** ข้อมูลยอดขายถูกจัดกลุ่มตามเดือน (YYYY-MM) — `S.byMonth`; `S.rows` (ที่ทุกกราฟใช้)
  = เดือนที่เลือกมารวมกัน
- **อัปโหลดแบบสะสม:** ไฟล์ใหม่จะ**แทนที่เฉพาะเดือนที่อยู่ในไฟล์นั้น** เดือนอื่นที่โหลดไว้ยังอยู่ครบ
  (เดิม: อัปไฟล์ใหม่ทับข้อมูลเก่าทั้งหมด)
- **ตัวเลือกเดือน (Month Picker):** แถบเลือกเดือนบนหน้า Sales
  - ปุ่มลัด: เดือนล่าสุด / 3 เดือน / 6 เดือน / ทั้งปีนี้ / ทั้งหมด
  - คลิกชิปเดือนเพื่อเลือกทีละเดือน หรือหลายเดือนรวมกัน (โหมด ทีละเดือน / ช่วง / รวมทั้งปี)
  - ชิปเส้นประ = เดือนที่มีบนคลาวด์แต่ยังไม่โหลด (คลิกแล้วโหลดจากคลาวด์อัตโนมัติ = ไม่กินแรม)

### กฎการตัดข้อมูลตอนอัปโหลด (ตามที่ผู้ใช้ยืนยัน)
- **ตัด SEEDING เสมอ** — Source = SEEDING เป็นสินค้าแจก/การตลาด ไม่ใช่การขายจริง → ไม่นับเป็นยอดขาย
- **STOCK/Aging เก็บเฉพาะเดือนล่าสุด** — ไฟล์เดือนเก่ากว่าเดือนล่าสุดที่อัปแล้ว จะ**ตัดแถว STOCK ทิ้ง**
  (สต็อกเป็น snapshot ปัจจุบัน ของเดือนเก่าไม่มีประโยชน์) — `S.stockMonth` เก็บว่าเดือนไหนเป็นเจ้าของ snapshot

### โครงสร้างคลาวด์ใหม่ (Firestore)
- แต่ละเดือนเก็บแยก collection `company/main/sales_<YYYY-MM>` (หั่นชิ้น < 900KB เหมือนเดิม)
- Registry `company/main/meta/months` = รายชื่อเดือน + meta (จำนวนแถว/ยอด) + stockMonth
- โหลดเริ่มต้น = โหลดแค่ registry + เดือนล่าสุด (เร็ว) เดือนอื่นโหลดเมื่อเลือก
- **รองรับข้อมูลเดิม (backward-compat):** ถ้าคลาวด์ยังเป็นแบบเก่า (collection `sales` เดี่ยว) ระบบจะ
  แยกเป็นรายเดือนและย้ายเข้าโครงสร้างใหม่ให้อัตโนมัติครั้งแรกที่โหลด
- **ประหยัดพื้นที่:** 870 MB (23 ไฟล์ xlsx) → ~55–90 MB หลังบีบอัด (Firestore ฟรีให้ 1 GB)

### ✅ ผลตรวจสอบ (headless)
- อัปไฟล์ Mer-Raw: แยกเป็นเดือน 2026-09, ตัด SEEDING 4 แถว (33,198 → 33,194), aging เก็บ 23,799, stockMonth=2026-09
- จำลอง 2 เดือน: เลือกทีละเดือน/รวมเดือน คำนวณถูก (Sept 33,194 + Aug 16,597 = 49,791), ชิป 2 อัน
- **Regression:** ไฟล์เก่า net คงที่ `฿27,053,060`, ไฟล์มีคอลัมน์ Bill Count = 312, Store Type ครบ — errors NONE
- **KPI definition change:** ตัด SEEDING ออกจากยอดขาย (ยอด/บิลเปลี่ยนเล็กน้อยตามจำนวน SEEDING ที่ตัด) — บันทึกตามกฎ
- ⚠️ **ต้องทดสอบกับคลาวด์จริง (Firebase):** ส่วนอ่าน/เขียนรายเดือน + registry + migration ทดสอบ headless ไม่ได้
  (test harness stub firebase) — ผู้ใช้ควรลอง: เข้าสู่ระบบ → อัป 2 เดือน → รีเฟรช → เห็นครบทั้ง 2 เดือน

---

## v1.6.0

- **Date:** 2026-09-17
- **Status:** 🗄️ DEPRECATED (เก็บไว้ rollback — v2.0.0 ขึ้นแทน)
- **Snapshot:** `versions/v1.6.0/` (index.html, sw.js, manifest.json, icon.svg)
- **Rollback:** v1.5.0 (`versions/v1.5.0/` หรือ commit `7c14096`)
- **Files:** index.html, sw.js (cache `sales-dash-v17`), manifest.json, icon.svg

### Store Type Library — เติมประเภทร้านให้ไฟล์ที่ไม่มีคอลัมน์ Store Type
- **ปัญหาเดิม:** ไฟล์ Mer-Raw ไม่มีคอลัมน์ Store Type → กราฟ/ตาราง "ราย Store Type" รวมเป็นกลุ่มเดียว ("—")
- **แก้เป็น:** ฝัง `STORE_TYPE_LIB` (Store_Code → Store Type, 164 ร้าน จาก mapping ที่ผู้ใช้ส่งมา)
  แล้วเติมให้ทุกแถวตอนอ่านไฟล์ผ่าน `storeTypeOf(code, fileVal)`
  - **ลำดับความสำคัญ:** ถ้าไฟล์มีคอลัมน์ Store Type และไม่ว่าง → ใช้ค่าจากไฟล์ก่อน;
    ถ้าไม่มี → ดึงจาก Library; ถ้าไม่พบรหัสใน Library → "—"
  - ใช้กับทั้งแถวขายและแถว aging (Source=STOCK)
- **ประเภทที่ใช้ (ผู้ใช้กำหนด):** Shop, SIS, Rev Runnr, Rev Lifestyle, Outlet, ECOM, WHS, WH, Event
- **การอัปเดต Library:** แก้ในบล็อก `STORE_TYPE_LIB` ใน index.html (มีคอมเมนต์กำกับ) — ร้านเปลี่ยนน้อย

### ✅ ผลตรวจสอบกับไฟล์จริง (Mer-Raw 16.09.2026)
| Store Type | ยอดขาย (Excl VAT) | จำนวนร้าน |
|---|---|---|
| ECOM | ฿17,706,615 | 19 |
| Rev Runnr | ฿16,864,671 | 37 |
| Shop | ฿15,575,822 | 28 |
| WHS | ฿6,058,930 | 7 |
| Outlet | ฿4,532,806 | 6 |
| SIS | ฿952,844 | 16 |
| Rev Lifestyle | ฿927,422 | 5 |
| Event | ฿51,336 | 1 |
| **รวม** | **฿62,670,445** | **119** |

- ร้านที่มียอดขายทั้งหมด mapped ครบ — **ไม่มีร้านประเภท "—"** (unknown = 0)
- WH (คลัง/HQ) ไม่มียอดขายในไฟล์นี้จึงไม่ปรากฏ (ปกติ)
- **ไม่ใช่ KPI definition change** — เป็นการเติม dimension (การจัดกลุ่ม) เท่านั้น ไม่กระทบสูตรคำนวณ
- **Regression:** ไฟล์เก่า (มีคอลัมน์ Store.Type เอง) net คงที่ `฿27,053,060`, errors NONE

---

## v1.5.0

- **Date:** 2026-09-17
- **Status:** ✅ STABLE (ผู้ใช้ยืนยัน "ใช้ได้" 2026-09-17)
- **Snapshot:** `versions/v1.5.0/` (index.html, sw.js, manifest.json, icon.svg)
- **Rollback:** v1.4.0 (`versions/v1.4.0/` หรือ commit `cf8ae7e`)
- **Files:** index.html, sw.js (cache `sales-dash-v16`), manifest.json, icon.svg

### 1) แก้บั๊กร้ายแรง — การอ่านวันที่ dd/mm/yyyy (Raw sales ของไทย)
- **ปัญหาเดิม:** ไฟล์ที่ใช้วันที่แบบ `dd/mm/yyyy` (เช่น `16/09/2026`) ถูกอ่านเป็น `mm/dd/yyyy`
  → วันที่ 1-12 สลับวัน/เดือน และ **วันที่ 13-31 ถูกทิ้งทั้งหมด** (ทดสอบไฟล์ Mer-Raw 16.09.2026
  พบแถวขายหายไป 6,984 แถว จาก 33,198)
- **แก้เป็น:** เพิ่มตัวตรวจจับรูปแบบวันที่อัตโนมัติต่อไฟล์ (`detectDateOrder`) — ดูว่าคอลัมน์วันที่
  เป็น dd/mm หรือ mm/dd แล้วอ่านให้ถูก; รูปแบบ ISO (`YYYY-MM-DD`) และ Excel serial ไม่ได้รับผลกระทบ
  → **ไฟล์เก่ายังทำงานเหมือนเดิมทุกประการ** (regression net คงที่ `฿27,053,060`)
- ผลหลังแก้: อ่านครบ 33,198 แถว, ช่วงวันที่ถูกต้อง `2026-09-01 → 2026-09-16`

### 2) Bill Count / Transaction Count — คิดระดับ "ธุรกรรม" (ไม่ใช่นับแถว)
- **นิยาม:** 1 บิล = 1 คู่ `Store_Code + Receipt_No` ที่มีรายการขายจริง คือ `SUM(MAX(Sold_Qty,0)) > 0`
  - Receipt เดียวมีหลายรายการสินค้า → นับเป็น **1 บิล** (ไม่ใช่หลายบิล)
  - บิลคืนสินค้าล้วน (qty ติดลบทั้งใบ) และบิล qty=0 → **ไม่นับเป็นบิลขาย**
  - ใช้คีย์ `Store_Code + Receipt_No` เผื่อ Receipt_No ซ้ำข้ามสาขา
- **ของเดิม (fallback):** นับ Receipt_No ที่ไม่ซ้ำทั้งหมด → เกินจริง (นับบิลคืนด้วย)
- **KPI ที่ผูกกับนิยามใหม่นี้ทั้งหมด:** จำนวนบิล (Total/by Store/by Type/by Promo),
  `ATV = ยอดขาย/บิล`, `UPT = ชิ้น/บิล` (`ASP = ยอดขาย/ชิ้น` ไม่เปลี่ยนนิยาม)
- **ยังเคารพคอลัมน์ Bill Count ของไฟล์เอง:** ถ้าไฟล์มีคอลัมน์ Bill Count ยังใช้ค่านั้น (sum) เหมือนเดิม

### ✅ ผลตรวจสอบกับไฟล์จริง (Mer-Raw 16.09.2026)
| ตัวชี้วัด | ค่า |
|---|---|
| แถวขายทั้งหมด | 33,198 |
| ธุรกรรมไม่ซ้ำ (Store+Receipt) | 18,697 |
| ธุรกรรมมีขายจริง = **Bill Count** | **17,949** |
| บิลคืนล้วน (ไม่นับ) | 748 |
| บิล qty=0 (ไม่นับ) | 0 |
| ยอดขาย (Excl VAT) | ฿62,670,445 |
| จำนวนชิ้น | 40,202 |
| ATV | ฿3,492 |
| UPT | 2.24 |
| ASP | ฿1,559 |

- **KPI definition change:** จำนวนบิล/ATV/UPT เปลี่ยนวิธีนับตามที่ระบุข้างบน (บันทึกตามกฎ — ห้ามเปลี่ยนสูตรเงียบๆ)
- **Regression:** ไฟล์เก่า + test suite เดิม (regress/bills/upt) ผ่าน, errors NONE
- **Known issues:** ไฟล์นี้ไม่มีคอลัมน์ Store Type → กราฟ/ตาราง "ราย Store Type" จะรวมเป็นกลุ่มเดียว
  ("—") จนกว่าจะทำ Store Type Library (วางแผนเป็น v1.6.0)

---

## v1.4.0

- **Date:** 2026-09-16
- **Status:** 🗄️ DEPRECATED (เก็บไว้ rollback — v1.5.0 ขึ้นแทน)
- **Snapshot:** `versions/v1.4.0/` (index.html, sw.js, manifest.json, icon.svg)
- **Rollback:** v1.3.1 (`versions/v1.3.1/` หรือ commit `d0690c9`)
- **Files:** index.html, sw.js (cache `sales-dash-v15`), manifest.json, icon.svg (ใหม่)
- **รวม bug fix ของ v1.3.1 (tooltip Store Type) ไว้ด้วย**

### Rebrand — โลโก้ใหม่ (คอนเซ็ปต์ B: Modern Blue Monoline — ผู้ใช้เลือก)
- 🎨 เปลี่ยนโลโก้บนหัวเรื่องจาก emoji 📊 เป็น **โลโก้ SVG** (กรอบสี่เหลี่ยมมนเส้นบาง +
  แท่งกราฟไล่เฉด + เส้นเติบโต โทนน้ำเงินแบรนด์) — คมชัดทุกความละเอียด, ปรับตามธีมสว่าง/มืด
- เพิ่มไฟล์ `icon.svg` ใช้เป็น **favicon + ไอคอน PWA** (แทนไอคอนเดิมของแอป Golf)
- อัปเดต `<link rel="icon">`, apple-touch-icon และ `manifest.json` ให้ชี้ที่ icon.svg
- คงข้อความ "Sales Analyst Dashboard" ตามเดิม (ผู้ใช้เลือก)

### Notes
- ใช้ SVG icon เพราะสภาพแวดล้อมไม่มีเครื่องมือแปลง PNG — เบราว์เซอร์/แอนดรอยด์รุ่นใหม่
  รองรับไอคอน SVG ใน manifest แล้ว (purpose: any maskable)
- ไม่มีการเปลี่ยน logic/นิยาม KPI ใด ๆ (เป็นการเปลี่ยนแบรนด์/หน้าตาเท่านั้น)

### Testing
- ✅ หัวเรื่องแสดงโลโก้ SVG, favicon เป็น SVG, ไม่มี console error (เมื่อมี icon.svg วางคู่)

---

## v1.3.1

- **Date:** 2026-09-16
- **Status:** 🗄️ DEPRECATED (fix นี้ถูกรวมเข้า v1.4.0 แล้ว — เก็บไว้สำหรับ rollback)
- **Snapshot:** `versions/v1.3.1/`
- **Rollback:** v1.3.0 (`versions/v1.3.0/` หรือ commit `416fbe4`)
- **Files:** index.html, sw.js (cache `sales-dash-v14`), manifest.json

### Bug fix (PATCH)
- 🐛 **แก้ tooltip กราฟ "ยอดขายราย Store Type" แสดง ฿0** — กราฟเป็นแนวนอน (ค่าอยู่แกน X)
  แต่ tooltip เดิมอ่านค่าจากแกน Y (ดัชนีหมวด = 0) ทำให้แสดง ฿0 เสมอ
  แก้ให้ tooltip อ่านค่าจาก `parsed.x` เฉพาะกราฟแนวนอนนี้ (ไม่กระทบกราฟอื่น)

### Testing
- ✅ ยืนยัน tooltip แสดงค่าจริง (เช่น "Shop: ฿10.5M") • ไม่มี console error • ไม่มีการเปลี่ยนนิยาม KPI

---

## v1.3.0

- **Date:** 2026-09-16
- **Status:** 🗄️ DEPRECATED (ถูกแทนที่ด้วย v1.4.0 — ยังเก็บไว้สำหรับ rollback)
- **Snapshot:** `versions/v1.3.0/`
- **Rollback:** v1.2.0 (`versions/v1.2.0/` หรือ commit `a30a3d4`)
- **Files:** index.html, sw.js (cache `sales-dash-v13`), manifest.json

### New feature (แผน Stage 3 สุดท้าย — อยู่ในหน้า Sales, ไม่แตะ v1.2.0)
1. **📁 Subclass Performance** — ตารางสมรรถนะรายหมวด เพิ่มใต้กราฟโดนัท (คงกราฟโดนัทเดิมไว้)
   - คอลัมน์: หมวดสินค้า (Subclass), ยอดขาย ฿, สัดส่วน (Mix %), ชิ้น, YoY %, ST %, Stock, MOH
   - จัดเรียงได้ทุกคอลัมน์
   - มิติ brand-aware: เลือกเฉพาะ GOODR จะสลับเป็น **Model Performance** (ModelName) อัตโนมัติ ให้ตรงกับกราฟโดนัทด้านบน

### Metric definitions (ใช้ของเดิมทั้งหมด — ไม่มีนิยามใหม่)
- ยอดขาย / Mix% / ชิ้น / YoY = ตามช่วง Daily/MTD/YTD และตัวกรอง (Mix% = ยอดขายหมวด ÷ ยอดขายรวมในช่วง)
- ST% / Stock / MOH = สแนปช็อตสต็อก 90 วันล่าสุด (นิยาม ST%/MOH เดิม) — ต้องมีไฟล์สต็อก

### Existing functionality preserved
- **กราฟโดนัท (product mix) เดิมยังอยู่ครบ** — ตารางนี้เป็นส่วนเสริม ไม่ได้แทนที่ (ตามที่ผู้ใช้กำชับ)

### Testing (headless, ไฟล์จริง)
- ✅ ไม่มี console error
- ✅ ALL brands → "Subclass Performance" 42 หมวด, Mix% รวม = 100%, ยอดขายรวม ฿27,053,060 (ตรงกับ total เดิม)
- ✅ EYEWEAR แถวแรก: ฿11.16M · 41.3% · 12,108 ชิ้น · YoY −0.4% · ST 31.6% · Stock 8,804 · MOH 6.5
- ✅ GOODR-only → สลับเป็น "Model Performance" (ModelName) 23 รายการ อัตโนมัติ
- ✅ จัดเรียงได้ • กราฟโดนัทเดิมยังแสดง • ไม่มีสต็อก → ST%/Stock/MOH = "—"

### Notes
- ครบแผน 3 Stage แล้ว (v1.1.0 → v1.3.0) จาก 9 Features ที่ขอ:
  - ทำแล้ว: 1,2,5,6 (v1.1.0) · 3,4 (v1.2.0) · 8 (v1.3.0)
  - ข้ามตามที่ผู้ใช้เลือก (ข้อมูลไม่รองรับ): 7 (aging 30/60/90/180, no-sales windows), 9 (Size analysis)

---

## v1.2.0

- **Date:** 2026-09-16
- **Status:** 🗄️ DEPRECATED (ถูกแทนที่ด้วย v1.3.0 — ยังเก็บไว้สำหรับ rollback)
- **Snapshot:** `versions/v1.2.0/`
- **Rollback:** v1.1.0 (`versions/v1.1.0/` หรือ commit `a5101fa`)
- **Files:** index.html, sw.js (cache `sales-dash-v12`), manifest.json

### New features (แผน Stage 2 จาก 3 — อยู่ในหน้า Stock, ไม่แตะ v1.1.0)
1. **🎯 Product Action Matrix** — ตารางคำแนะนำเชิง merchandising ราย GDNO × สาขา
   - คอลัมน์: GDNO, รุ่น (Model), สาขา, ขาย 90 วัน (ชิ้น), สต็อก (ชิ้น), ST %, MOH, ขาย/วัน (ชิ้น), คำแนะนำ (Action)
   - การ์ดสรุปจำนวน + สต็อกต่อ Action • จัดเรียงได้ทุกคอลัมน์ • แสดง 300 อันดับแรก (เรียงตามความสำคัญ+สต็อก)
2. **📊 Sales vs Stock Matrix (Quadrant)** — กราफ scatter: แกน X = สต็อก, แกน Y = Sales Velocity (ชิ้น/วัน 90 วัน)
   - แบ่ง 4 ช่องด้วยค่ามัธยฐาน (median) • สีตามช่อง • แตะ/คลิกจุดเพื่อดู GDNO/รุ่น/สาขา/ยอดขาย/สต็อก/ST%/MOH

### Business rules — Product Action Matrix (ผู้ใช้อนุมัติ recommended defaults 2026-09-16)
ประเมินราย GDNO × สาขา ตามลำดับความสำคัญ **REPLENISH → TRANSFER → MARKDOWN → HOLD → MONITOR**
(ลำดับนี้กำหนดเพื่อให้สินค้าที่ค้างแต่ขายได้ที่สาขาอื่นถูกแนะนำให้ "ย้าย" ก่อน "ลดราคา")
| Action | เงื่อนไข |
|---|---|
| 🟢 REPLENISH | ST% ≥ 70 และ MOH < 1 (ขายดี สต็อกจะหมด) |
| 🔵 TRANSFER | มีสต็อก (>0) และ 90 วันไม่มีการขายที่สาขานี้ แต่รุ่นนี้ขายได้ที่สาขาอื่น |
| 🔴 MARKDOWN | Flag_O = O หรือ `<O>` **หรือ** (ST% < 20 และ MOH > 6) |
| ⚪ HOLD | ST% 40–70 และ MOH 1–3 (สุขภาพดี) |
| 🟡 MONITOR | กรณีอื่น / ข้อมูลไม่พอ |

- **ST% / MOH** ใช้นิยามเดิม (ST% = ขาย 90 วัน ÷ (ขาย 90 วัน + สต็อก); MOH = สต็อก ÷ (ขาย 90 วัน ÷ 3)) — หน้าต่าง 90 วันนับจากวันสแนปช็อตสต็อก
- **Quadrant split** ใช้ค่ามัธยฐานของจุดที่พล็อต (ผู้ใช้อนุมัติ)

### Data dependencies
- ทั้ง 2 ฟีเจอร์ **ต้องอัปโหลดไฟล์สต็อกก่อน** (ไม่มีจะแสดงข้อความให้อัปโหลด, ไม่มี error)
- MARKDOWN จะละเอียดขึ้นเมื่อมี Flag_O ในไฟล์ยอดขาย (Source=STOCK rows)

### Testing (headless, ไฟล์จริง 50,813 ขาย / 98,600 สต็อก / 22,649 aging → 3,056 รายการ GDNO×สาขา)
- ✅ ไม่มี console error (ทั้งมีสต็อก และไม่มีสต็อก)
- ✅ **ตรวจกฎอิสระ: rule-mismatches = 0** — ทุกคำแนะนำเป็นไปตามเกณฑ์ที่กำหนด
- ✅ สรุป: REPLENISH 74 · TRANSFER 1,561 · MARKDOWN 675 · HOLD 260 · MONITOR 486 (รวม 3,056)
- ✅ จัดเรียงตาราง, กราฟ 4 ช่อง (median stock 8 ชิ้น), แตะจุดแสดงรายละเอียด ทำงานครบ
- ✅ ไม่มีสต็อก → แสดงสถานะว่างถูกต้อง ไม่มี error • ฟีเจอร์ v1.1.0 เดิมไม่กระทบ

### Known issues / notes
- TRANSFER มีจำนวนมาก (1,561) เป็นเรื่องปกติของเครือข่ายหลายสาขา (สินค้ากระจายอยู่หลายที่แต่ขายเป็นบางสาขา) — จัดเรียง/กรองเพื่อโฟกัสได้
- Quadrant แสดง 800 จุดแรก (สต็อก+ยอดขายสูงสุด) เพื่อความลื่นไหล
- Feature 8 (Subclass Performance table) → วางแผนไว้ v1.3.0

---

## v1.1.0

- **Date:** 2026-09-16
- **Status:** 🗄️ DEPRECATED (ถูกแทนที่ด้วย v1.2.0 — ยังเก็บไว้สำหรับ rollback)
- **Snapshot:** `versions/v1.1.0/`
- **Rollback:** v1.0.0 (`versions/v1.0.0/` หรือ commit `4bed724`)
- **Files:** index.html, sw.js (cache `sales-dash-v11`), manifest.json

### New features (แผน Stage 1 จาก 3 — ไม่แตะ v1.0.0)
1. **🩺 Sales Health** — แถบสรุปสุขภาพยอดขาย 6 ตัวชี้วัด พร้อมสถานะ 🟢/🟡/🔴
   - ยอดขาย vs เป้าหมาย, ยอดขาย vs ปีก่อน (LY), พยากรณ์ vs เป้าหมาย, สัดส่วนยอดขายโปรโมชั่น (info), Stock Risk, Slow Moving GDNO
2. **🏬 Store Performance** — ตารางรายสาขา: ยอดขาย, ยอดขาย LY, YoY %, ชิ้น, ST %, Stock, MOH, Sales/Day, เป้าหมาย, % เป้าหมาย — จัดเรียงได้ทุกคอลัมน์ + สีตามเกณฑ์
3. **📈 Daily Sales Trend (ปรับปรุง)** — เพิ่มเส้น ปีก่อน (LY) และ เป้าหมาย (Target Pace) ในแท็บ Daily/MTD/YTD (คงสไตล์กราฟเดิม)
4. **🔮 Forecast (ปรับปรุง)** — การ์ดพยากรณ์แสดง ยอดปัจจุบัน, ยอด/วัน, วันที่เหลือ, Estimated Landing, เป้าหมาย, % สำเร็จ

### Business rules / thresholds (ผู้ใช้อนุมัติ recommended defaults 2026-09-16)
- **สถานะสี vs เป้า/พยากรณ์/LY:** ≥100% 🟢 · 90–99% 🟡 · <90% 🔴
- **Stock Risk:** <10% 🟢 · 10–25% 🟡 · >25% 🔴

### KPI / metric definitions (ใหม่ในเวอร์ชันนี้ — ไม่แก้ของเดิม)
| ตัวชี้วัดใหม่ | นิยาม |
|---|---|
| ยอดขาย vs เป้าหมาย | ยอดขายในช่วง ÷ เป้ารายเดือนที่ตรงกับมุมมอง (MTD=เดือนนี้, YTD=ม.ค.–เดือนปัจจุบัน; Daily/Custom = n/a) |
| ยอดขาย vs LY | ยอดขายช่วงปัจจุบัน ÷ ยอดขายช่วงเดียวกันปีก่อน (จาก `ranges().ly` เดิม) |
| พยากรณ์ vs เป้าหมาย | พยากรณ์สิ้นเดือน (สูตรเดิม) ÷ เป้ารายเดือนของ refMonth |
| Stock Risk | ต้นทุนสต็อกค้างรุนแรง (Flag_O = O หรือ `<O>`, 180+ วัน) ÷ ต้นทุนสต็อกค้าง (Flag_O) ทั้งหมด — คิดจากชุดข้อมูล aging ชุดเดียว ไม่ผสมกับไฟล์ stock card |
| Slow Moving GDNO | จำนวน GDNO ที่ Flag_O = O หรือ `<O>` |
| Store ST % (รายสาขา) | ขาย 90 วันของสาขา ÷ (ขาย 90 วัน + สต็อกของสาขา) — นิยาม ST เดิมที่ระดับสาขา |
| Store MOH (รายสาขา) | สต็อกสาขา ÷ (ขาย 90 วัน ÷ 3) — นิยาม MOH เดิมที่ระดับสาขา |
| Store Sales/Day | ยอดขายในช่วง ÷ จำนวนวันในช่วง (per-day เสมอ ต่างจากตาราง Retail KPI เดิมที่เป็น per-month ใน YTD) |

> **ไม่มีการเปลี่ยนนิยาม KPI เดิม** (Sales, Net, GP, GP%, ASP, ATV, UPT, Sell-Through 90 วัน, MOH, Forecast) — สูตร Forecast คงเดิม (MTD ÷ วันที่ผ่านมา × วันในเดือน)

### Data dependencies
- Health cards ที่อิงเป้าหมาย และคอลัมน์เป้าใน Store Performance จะแสดง "—"/⚪ จนกว่าจะอัปโหลดไฟล์เป้าหมาย
- ST %, Stock, MOH ใน Store Performance ต้องอัปโหลดไฟล์สต็อก มิฉะนั้นแสดง "—"
- Stock Risk / Slow Moving GDNO ต้องมี Flag_O ในไฟล์ยอดขาย (Source=STOCK rows)

### Testing (headless, ไฟล์จริง 50,813 แถวขาย / 98,600 แถวสต็อก / 22,649 aging)
- ✅ ไม่มี console error ทุกมุมมอง (Daily/MTD/YTD/Custom) และทั้งหน้า Sales/Stock
- ✅ ฟีเจอร์เดิมครบ: KPI 10 การ์ด, Promo 15 แถว, Store Type total ฿27,053,060, Store 77 แถว, Top 20, Stock page (4 KPI/63 สาขา/ABC/38 แถว)
- ✅ Store Performance จัดเรียงได้, สี %เป้า ทำงาน (เขียว/แดง), เส้น LY + Target Pace แสดงครบทุกมุมมอง
- ✅ แก้บั๊ก Stock Risk >100% (เดิมหารข้ามชุดข้อมูล) ให้ใช้ชุด aging ชุดเดียว

### Known issues / notes
- ค่า Sales-vs-Target ในแท็บ MTD ช่วงต้นเดือนจะดูต่ำโดยธรรมชาติ (เทียบยอดสะสมกับเป้าทั้งเดือน) — ใช้ "พยากรณ์ vs เป้าหมาย" เพื่อดูแนวโน้มที่ปรับตาม pace แล้ว
- Feature 3,4 (Product Action Matrix, Sales-vs-Stock quadrant) → วางแผนไว้ v1.2.0
- Feature 7,9 (aging 30/60/90/180, Size) → ผู้ใช้เลือก "Skip for now" (ข้อมูลไม่รองรับ)
- Feature 8 (Subclass Performance table) → วางแผนไว้ v1.3.0

---

## v1.0.0

- **Date:** 2026-09-16
- **Status:** 🗄️ DEPRECATED (ถูกแทนที่ด้วย v1.1.0 — ยังเก็บไว้สำหรับ rollback)
- **Commit ref:** `4bed724`
- **Rollback ref (ก่อน gzip compression):** `151eafe`
- **Snapshot:** `versions/v1.0.0/`
- **Files:** index.html, sw.js (cache `sales-dash-v10`), manifest.json

### Baseline features (working version ปัจจุบัน)
- **ตัวกรอง:** วันที่ (Daily / MTD / YTD / Custom from–to), แบรนด์, ช่องทาง (Source), ประเภทร้าน (Store Type), หมวดสินค้า (Subclass), สาขา (Store)
- **KPI:** ยอดขาย (Net), จำนวนชิ้น, GP, GP%, ASP, ATV, UPT, จำนวนบิล (SUM Bill Count, fallback = distinct Receipt_No), Sale/Day ↔ Sale/Month (ตาม view), พยากรณ์สิ้นเดือน
- **กราฟ:** แท่ง = ยอดขายราย Store Type · โดนัท = brand-aware product mix (GOODR → ModelName, NATGEO → Subclass)
- **ตาราง:** ยอดขายรายสาขา (เปลี่ยนตามช่วง Daily/MTD/YTD), sort ได้ทุก dimension
- **หน้า Stock:** Stock by store (Stock Net / Stock Cost / MOH), brand-aware grouping, honor store-type filter
- **ข้อมูล:** อ่าน .xlsx (SheetJS), Firestore + Google Sign-in, gzip compression (pako), service worker network-first
- **Aging pipeline (Flag_O):** parse + persist + cloud-sync ไว้แล้ว แต่ยังไม่แสดง UI (dormant)

### KPI definitions (นิยามที่ตรึงไว้ — ห้ามเปลี่ยนเงียบ ๆ)
| KPI | นิยาม |
|---|---|
| ยอดขาย (Net) | ผลรวม Netsale_ExcVAT |
| จำนวนชิ้น | ผลรวม Sold_Qty |
| GP | Net − COGS (Margin_Value) |
| GP% | GP / Net |
| ASP | Net / จำนวนชิ้น |
| ATV | Net / จำนวนบิล |
| UPT | จำนวนชิ้น / จำนวนบิล |
| จำนวนบิล | SUM(Bill Count column); ถ้าไม่มีคอลัมน์ → distinct Receipt_No |
| Sale/Day | Net / จำนวนวันที่มีการขาย |
| Sale/Month | Net / จำนวนเดือน (มุมมอง YTD) |
| พยากรณ์สิ้นเดือน | (ยอด MTD / วันที่ผ่านมา) × จำนวนวันในเดือน |
| Stock Cost | ผลรวม Cost Amount |
| MOH | Stock / ยอดขายเฉลี่ยต่อเดือน |

### Known issues
- git tag push ถูกบล็อก (403) ในสภาพแวดล้อมนี้ → ใช้ commit SHA + โฟลเดอร์ `versions/` เป็น rollback แทน
- Aging UI ถูกถอดออกชั่วคราว (pipeline ข้อมูลยังทำงานอยู่เบื้องหลัง)

### Rollback
- คืนค่าจาก `versions/v1.0.0/` หรือ `git checkout 4bed724 -- sales-dashboard/`
