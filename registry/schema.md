# Schema ระบบทะเบียนงบประมาณ (8 ชั้น) — ร่างสำหรับพัฒนา

อ้างอิงจากหน้าจอจริงในเอกสารอบรม 11 มิ.ย. 2569 (โปรแกรม Bis65xxxx / Lao66b101)
DDL เป็น PostgreSQL-style generic — ปรับชนิดข้อมูล/ชื่อให้เข้ากับ DB ที่ทีมใช้ได้

---

## 1. หลักการออกแบบ (Design decisions)

| # | หลักการ | เหตุผลจากเอกสาร |
|---|---|---|
| 1 | **ผูกปีงบประมาณทุกตารางทะเบียน** (`fiscal_year`) | ทุกจอกรองด้วย "ปี"; มีปุ่ม "ค้นหาทะเบียนตามปี" = ยกยอด/คัดลอกข้ามปี |
| 2 | **Effective dating** `start_date`/`end_date` (NULL หรือ 9999-12-31 = เปิดอยู่) | จอแสดง วันที่เริ่มต้น–สิ้นสุด, 99/99/99 = ไม่มีวันสิ้นสุด |
| 3 | **Soft-close ก่อน hard-delete** (`row_status` ACTIVE/CLOSED) | จอมีปุ่ม ปิด / เปิด-ปิดตามสถานะ แยกจาก ลบ; ลบ "ตามสิทธิ์ที่กำหนด" |
| 4 | **ห้ามลบเมื่อมี child** (FK RESTRICT) | รักษา referential integrity ของสายโซ่ทะเบียน |
| 5 | **แยกสิทธิ์ตามระบบ** (`system_type` eB/BBL) | e-Budgeting หน่วยงานแก้ทะเบียนเอง / BBL อ่านอย่างเดียว |
| 6 | **รายการเป็นต้นไม้** (`parent_item_id` + `item_level` 1–3) | จอ "ทะเบียนรายการ (ระดับชั้นของรายการ)" |

---

## 2. ลำดับชั้น (Hierarchy chain)

```
ref_fiscal_year ─┐ (scope ทุกตารางทะเบียน)
ministry ──< agency ──< output_project ──< activity ──< item >── ref_expense_category
   │                         ^   ^                          │
   └──< ministry_service_target ─< agency_service_target ───┘ (FK เข้า output_project)
program ───────────────────────────< output_project
ref_area ──< item   (gps สำหรับ e-BGIS)
item ──< item  (self: ระดับชั้น 1/2/3)
```

หมายเหตุ: `>—<` = ตารางอ้างอิง (reference/master) ทางขวาเป็นฝั่ง "หนึ่ง"

---

## 3. คอลัมน์ร่วม (ใส่ในตารางทะเบียนชั้น 3–8 ทุกตาราง)

```sql
fiscal_year   smallint     NOT NULL REFERENCES ref_fiscal_year(fiscal_year),
code          varchar(20)  NOT NULL,           -- รหัสตามรูปแบบของแต่ละชั้น
name_th       varchar(500) NOT NULL,
start_date    date         NOT NULL,
end_date      date         NULL,               -- NULL = เปิดอยู่ (จอแสดง 99/99/99)
row_status    varchar(10)  NOT NULL DEFAULT 'ACTIVE',  -- ACTIVE | CLOSED
created_by    varchar(50)  NOT NULL,
created_at    timestamptz  NOT NULL DEFAULT now(),
updated_by    varchar(50)  NULL,
updated_at    timestamptz  NULL
-- UNIQUE (fiscal_year, code) ภายในขอบเขตหน่วยงานที่เกี่ยวข้อง
```

---

## 4. นิยามตาราง (DDL ร่าง)

```sql
-- ===== ตารางอ้างอิง (master/reference) =====
CREATE TABLE ref_fiscal_year (
  fiscal_year   smallint PRIMARY KEY,           -- 70
  period_status varchar(10) NOT NULL,           -- OPEN | CLOSED (รอบทำงบ)
  note          varchar(200)
);

CREATE TABLE ref_expense_category (             -- 12 หมวด
  expense_cat_id smallint PRIMARY KEY,
  code           varchar(10) NOT NULL UNIQUE,
  name_th        varchar(100) NOT NULL          -- เงินเดือน, ค่าจ้างประจำ, ... , รายจ่ายอื่น
);

CREATE TABLE ref_area (
  area_id   integer PRIMARY KEY,
  code      varchar(10) NOT NULL UNIQUE,        -- รหัสจังหวัด/อปท.
  name_th   varchar(150) NOT NULL,
  gps_lat   numeric(10,7),                      -- e-BGIS
  gps_long  numeric(10,7)
);

-- ===== ชั้น 1–2: หน่วยงาน =====
CREATE TABLE ministry (
  ministry_id integer PRIMARY KEY,
  code        varchar(5) NOT NULL UNIQUE,       -- 01000
  name_th     varchar(255) NOT NULL
);

CREATE TABLE agency (
  agency_id   integer PRIMARY KEY,
  ministry_id integer NOT NULL REFERENCES ministry(ministry_id),
  code        varchar(5) NOT NULL UNIQUE,       -- 01007
  name_th     varchar(255) NOT NULL,
  system_type varchar(5) NOT NULL DEFAULT 'eB'  -- eB | BBL
);

-- ===== ชั้น 4: เป้าหมายบริการกระทรวง (Bis65I004) =====
CREATE TABLE ministry_service_target (
  min_target_id bigserial PRIMARY KEY,
  ministry_id   integer NOT NULL REFERENCES ministry(ministry_id),
  /* + คอลัมน์ร่วม: code(2 ตัวอักษร เช่น BC), name_th, fiscal_year, start/end, row_status, audit */
  fiscal_year smallint NOT NULL REFERENCES ref_fiscal_year(fiscal_year),
  code varchar(2) NOT NULL, name_th varchar(500) NOT NULL,
  start_date date NOT NULL, end_date date, row_status varchar(10) NOT NULL DEFAULT 'ACTIVE',
  created_by varchar(50) NOT NULL, created_at timestamptz NOT NULL DEFAULT now(),
  updated_by varchar(50), updated_at timestamptz,
  UNIQUE (fiscal_year, ministry_id, code)
);

-- ===== ชั้น 5: เป้าหมายบริการหน่วยงาน (Bis65I005) =====
CREATE TABLE agency_service_target (
  agy_target_id bigserial PRIMARY KEY,
  agency_id     integer NOT NULL REFERENCES agency(agency_id),
  min_target_id bigint  NOT NULL REFERENCES ministry_service_target(min_target_id), -- M:1
  fiscal_year smallint NOT NULL REFERENCES ref_fiscal_year(fiscal_year),
  code varchar(2) NOT NULL, name_th varchar(500) NOT NULL,                          -- 07
  start_date date NOT NULL, end_date date, row_status varchar(10) NOT NULL DEFAULT 'ACTIVE',
  created_by varchar(50) NOT NULL, created_at timestamptz NOT NULL DEFAULT now(),
  updated_by varchar(50), updated_at timestamptz,
  UNIQUE (fiscal_year, agency_id, code)
);

-- ===== ชั้น 3: แผนงาน =====
CREATE TABLE program (
  program_id bigserial PRIMARY KEY,
  prog_type  varchar(20) NOT NULL,    -- บุคลากรภาครัฐ | พื้นฐาน | ยุทธศาสตร์ | บูรณาการ
  fiscal_year smallint NOT NULL REFERENCES ref_fiscal_year(fiscal_year),
  code varchar(20) NOT NULL, name_th varchar(500) NOT NULL,
  start_date date NOT NULL, end_date date, row_status varchar(10) NOT NULL DEFAULT 'ACTIVE',
  created_by varchar(50) NOT NULL, created_at timestamptz NOT NULL DEFAULT now(),
  updated_by varchar(50), updated_at timestamptz,
  UNIQUE (fiscal_year, code)
);

-- ===== ชั้น 6: ผลผลิต/โครงการ (Bis65I009) ★ =====
CREATE TABLE output_project (
  output_id     bigserial PRIMARY KEY,
  agency_id     integer NOT NULL REFERENCES agency(agency_id),
  agy_target_id bigint  NOT NULL REFERENCES agency_service_target(agy_target_id),
  program_id    bigint  NOT NULL REFERENCES program(program_id),
  acct_code     varchar(7) NOT NULL,   -- รหัสบัญชี XX-XXXX เช่น 00-0244
  op_type       varchar(2) NOT NULL,   -- 00=โครงการ | 07=บุคลากร | 08=กองทุน (prefix ของ acct_code)
  fiscal_year smallint NOT NULL REFERENCES ref_fiscal_year(fiscal_year),
  code varchar(20) NOT NULL, name_th varchar(500) NOT NULL,
  start_date date NOT NULL, end_date date, row_status varchar(10) NOT NULL DEFAULT 'ACTIVE',
  created_by varchar(50) NOT NULL, created_at timestamptz NOT NULL DEFAULT now(),
  updated_by varchar(50), updated_at timestamptz,
  UNIQUE (fiscal_year, agency_id, acct_code)
);

-- ===== ชั้น 7: กิจกรรม (Bis65a001) =====
CREATE TABLE activity (
  activity_id bigserial PRIMARY KEY,
  output_id   bigint NOT NULL REFERENCES output_project(output_id),
  seq_code    varchar(10) NOT NULL,   -- ลำดับ 1,2,3
  fiscal_year smallint NOT NULL REFERENCES ref_fiscal_year(fiscal_year),
  name_th varchar(500) NOT NULL,
  start_date date NOT NULL, end_date date, row_status varchar(10) NOT NULL DEFAULT 'ACTIVE',
  created_by varchar(50) NOT NULL, created_at timestamptz NOT NULL DEFAULT now(),
  updated_by varchar(50), updated_at timestamptz,
  UNIQUE (fiscal_year, output_id, seq_code)
);

-- ===== ชั้น 8: รายการ (Bis65a002) =====
CREATE TABLE item (
  item_id        bigserial PRIMARY KEY,
  activity_id    bigint NOT NULL REFERENCES activity(activity_id),
  parent_item_id bigint NULL REFERENCES item(item_id),      -- self: ระดับชั้น
  expense_cat_id smallint NOT NULL REFERENCES ref_expense_category(expense_cat_id),
  area_id        integer NULL REFERENCES ref_area(area_id),
  item_level     smallint NOT NULL DEFAULT 1,               -- 1 | 2 | 3
  code varchar(6) NOT NULL,                                 -- 000337
  fiscal_year smallint NOT NULL REFERENCES ref_fiscal_year(fiscal_year),
  name_th varchar(500) NOT NULL,
  start_date date NOT NULL, end_date date, row_status varchar(10) NOT NULL DEFAULT 'ACTIVE',
  created_by varchar(50) NOT NULL, created_at timestamptz NOT NULL DEFAULT now(),
  updated_by varchar(50), updated_at timestamptz,
  UNIQUE (fiscal_year, code),
  CHECK (item_level BETWEEN 1 AND 3)
);
```

---

## 5. กฎสำคัญ (Business rules / constraints)

1. **บังคับลำดับการสร้าง (parent ต้องมาก่อน):** สร้าง output_project ได้ต่อเมื่อมี agency_service_target; สร้าง activity ต่อเมื่อมี output; สร้าง item ต่อเมื่อมี activity
2. **op_type ต้องสอดคล้องกับ prefix ของ acct_code** (00- → โครงการ, 07- → บุคลากร, 08- → กองทุน)
3. **ลบ (hard-delete):** อนุญาตเฉพาะผู้มีสิทธิ์ + เมื่อไม่มีแถวลูกอ้างอิง (ON DELETE RESTRICT) — กรณีปกติให้ใช้ปิด (row_status=CLOSED)
4. **ยกยอดข้ามปี:** ฟังก์ชัน copy ทั้งสายโซ่จาก fiscal_year เดิม → ปีใหม่ (อ้างอิงปุ่ม "ค้นหาทะเบียนตามปี")
5. **area_id (พิกัด GPS)** บังคับเฉพาะรายการประเภท ครุภัณฑ์ / ที่ดิน / สิ่งก่อสร้าง (ตาม e-BGIS)
6. **BBL:** ตารางเดียวกันได้ แต่ control การแก้ไขที่ application layer ตาม `agency.system_type`

---

## 6. จุดที่ต้องยืนยันกับทีม/ผู้ใช้งานก่อน finalize

- เป้าหมายบริการหน่วยงาน → กระทรวง เป็น **M:1 หรือ M:N**? (ร่างนี้ใช้ M:1 — ถ้า M:N ต้องเพิ่มตาราง junction)
- ผลผลิต/โครงการ ผูกกับ **แผนงาน** โดยตรง หรือผ่านเป้าหมายบริการ? (ร่างนี้ผูกตรงทั้งคู่)
- รหัส (`code`) ให้ระบบ **gen อัตโนมัติ** ตาม prefix หรือผู้ใช้กรอกเอง?
- ต้องเก็บ **ประวัติการแก้ไข (audit trail/version)** แบบเต็ม table หรือแค่ created/updated?
- รายการ (item) มีงบประมาณ/ตัวเงินผูกอยู่ (จอ Bis65b003 MTEF) — จะแยกเป็นตาราง `budget_amount` ต่างหากในเฟสถัดไป
