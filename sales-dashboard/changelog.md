# Changelog — Sales Analyst Dashboard

รูปแบบเวอร์ชัน: **Semantic Versioning** `MAJOR.MINOR.PATCH`
- **MAJOR** = เปลี่ยนโครงสร้าง/ดีไซน์ใหญ่ (breaking)
- **MINOR** = ฟีเจอร์ใหม่ (ไม่ทำของเดิมพัง)
- **PATCH** = แก้บั๊ก / แก้สูตรคำนวณ / แก้ UI / performance

สถานะ: `DRAFT` → `TESTING` → `STABLE` → `DEPRECATED`
กติกา: **ห้ามมาร์ก STABLE เอง** ต้องรอผู้ใช้ยืนยัน "Approved" / "ใช้งานได้" / "Stable"

Snapshot สมบูรณ์ที่รันได้ของแต่ละเวอร์ชันเก็บไว้ใน `versions/vX.Y.Z/`

---

## v1.1.0

- **Date:** 2026-09-16
- **Status:** 🧪 TESTING (รอผู้ใช้ทดสอบและ Approve)
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
- **Status:** ✅ STABLE (ผู้ใช้ยืนยัน "ยังใช้ได้" 2026-09-16)
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
