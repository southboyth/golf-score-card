# Changelog — Sales Analyst Dashboard

รูปแบบเวอร์ชัน: **Semantic Versioning** `MAJOR.MINOR.PATCH`
- **MAJOR** = เปลี่ยนโครงสร้าง/ดีไซน์ใหญ่ (breaking)
- **MINOR** = ฟีเจอร์ใหม่ (ไม่ทำของเดิมพัง)
- **PATCH** = แก้บั๊ก / แก้สูตรคำนวณ / แก้ UI / performance

สถานะ: `DRAFT` → `TESTING` → `STABLE` → `DEPRECATED`
กติกา: **ห้ามมาร์ก STABLE เอง** ต้องรอผู้ใช้ยืนยัน "Approved" / "ใช้งานได้" / "Stable"

Snapshot สมบูรณ์ที่รันได้ของแต่ละเวอร์ชันเก็บไว้ใน `versions/vX.Y.Z/`

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
