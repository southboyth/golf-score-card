# Changelog — Sales Analyst Dashboard

รูปแบบเวอร์ชัน: **Semantic Versioning** `MAJOR.MINOR.PATCH`
- **MAJOR** = เปลี่ยนโครงสร้าง/ดีไซน์ใหญ่ (breaking)
- **MINOR** = ฟีเจอร์ใหม่ (ไม่ทำของเดิมพัง)
- **PATCH** = แก้บั๊ก / แก้สูตรคำนวณ / แก้ UI / performance

สถานะ: `DRAFT` → `TESTING` → `STABLE` → `DEPRECATED`
กติกา: **ห้ามมาร์ก STABLE เอง** ต้องรอผู้ใช้ยืนยัน "Approved" / "ใช้งานได้" / "Stable"

Snapshot สมบูรณ์ที่รันได้ของแต่ละเวอร์ชันเก็บไว้ใน `versions/vX.Y.Z/`

---

## v1.3.1

- **Date:** 2026-09-16
- **Status:** 🧪 TESTING (รอผู้ใช้ยืนยัน)
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
- **Status:** ✅ STABLE (ผู้ใช้ยืนยัน "ใช้งานได้" 2026-09-16)
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
