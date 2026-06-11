# Prompts สำหรับสร้างสื่อการสอน
## หัวข้อ: "แนวทางวิเคราะห์ข้อมูลกับทะเบียน Project"

> **กลุ่มเป้าหมาย:** เพื่อนพนักงานที่มีพื้นฐาน technical ไม่สูง  
> **Output ต่อ prompt:** แผนหลักสูตร + สไลด์ (.pptx) + ใบงาน (.docx) + คู่มือ (.docx)  
> **วิธีใช้:** Copy prompt → เปิด chat ใหม่ **ใน Project เดียวกัน** (เพื่อให้ search past chats ใช้ได้) → วาง prompt → รัน

---

## Prompt A — Concept-Focused Edition (1–2 ชั่วโมง / Lunch & Learn)

เน้นสร้างความเข้าใจ concept หลัก เหมาะกับการสอนครั้งเดียว ผู้เรียนได้ภาพรวมและไอเดียไปต่อยอดเอง

```xml
<role>
คุณคือ Learning Experience Designer (นักออกแบบประสบการณ์การเรียนรู้) ที่เชี่ยวชาญการสอนพนักงานที่ไม่มีพื้นฐาน technical ให้เข้าใจการวิเคราะห์ข้อมูล (data analysis) ผ่านการทำงานจริง คุณทำงานร่วมกับ data analyst และ HR/Learning team มา 10+ ปี
</role>

<task>
ออกแบบและสร้างชุดสื่อการสอน "lunch & learn" ความยาว 1–2 ชั่วโมง หัวข้อ "แนวทางวิเคราะห์ข้อมูลกับทะเบียน project" สำหรับเพื่อนพนักงานในองค์กร เน้น concept หลัก ผู้เรียนกลับไปต่อยอดเองได้
</task>

<audience>
- พนักงานทั่วไปในองค์กร พื้นฐาน technical ไม่สูง
- รู้จัก Excel ระดับใช้งานพื้นฐาน (กรอกข้อมูล, sum, ฟิลเตอร์)
- ไม่ถนัด pivot table, formula ซับซ้อน, หรือเครื่องมือ BI
- ไม่เคยเรียน statistics หรือ data analysis อย่างเป็นทางการ
- เป้าหมาย: เข้าใจว่า "ทะเบียน project" ที่ทำอยู่ทุกวันมีคุณค่ายังไง และเริ่มดึง insight ออกมาได้
</audience>

<step_1_gather_context>
ก่อนออกแบบ ต้องดึง context จาก chat เก่าใน project นี้:

1. เรียก `conversation_search` ด้วย keywords ต่อไปนี้ (ทำหลายครั้ง):
   - "ทะเบียน project วิเคราะห์"
   - "แนวทางวิเคราะห์ข้อมูล project"
   - "project registry analysis"
   - "KPI ทะเบียนโครงการ"

2. สกัดจากผลลัพธ์:
   - แนวทางวิเคราะห์ที่เคยแนะนำไว้
   - ตัวอย่าง metric / KPI ที่ควรดู
   - คอลัมน์/field ในทะเบียน project ที่อ้างถึง
   - เครื่องมือที่แนะนำ (Excel, Power BI, ฯลฯ)
   - ตัวอย่าง use case หรือ pain point ที่เคยคุย

3. ถ้า search ไม่เจอเนื้อหาเพียงพอ → หยุดและแจ้งผู้ใช้:
   "หา context จาก chat เก่าได้แค่ [...] ขอ paste เนื้อหาเพิ่มเติมเกี่ยวกับ [...] ก่อนเริ่ม"

ห้ามข้ามขั้นนี้ และห้ามใช้ generic content แทน — สื่อต้องอิงบริบทจริงของผู้ใช้
</step_1_gather_context>

<step_2_plan>
ส่ง outline ให้ผู้ใช้ confirm ก่อนสร้างสื่อจริง:

โครงสร้าง outline:
1. Learning Objectives 3 ข้อ (รูปแบบ: "หลังเรียนจบ ผู้เรียนจะสามารถ [verb ที่วัดได้] เช่น ระบุ / อธิบาย / เลือกใช้ / สร้าง")
2. Agenda แบ่งเป็น 4–5 ช่วง (รวม ~90 นาที):
   - Opening + Why this matters (10 นาที)
   - Concept หลัก 2–3 อัน (30–40 นาที)
   - ตัวอย่างจริงจากองค์กร (20 นาที)
   - Hands-on สั้นๆ บน worksheet (20 นาที)
   - Wrap-up + Q&A (10 นาที)
3. Concept หลักที่จะสอน ระบุชื่อแต่ละอัน + ทำไมเลือก
4. Real-world example ที่จะใช้ (อิงจาก context ที่หามาได้)

รอ user confirm หรือปรับ ก่อนไป step 3
</step_2_plan>

<step_3_create_materials>
หลัง confirm outline แล้ว สร้าง 3 deliverables:

<deliverable_A name="สไลด์ presentation">
- Format: .pptx (เรียก skill `/mnt/skills/public/pptx/SKILL.md` ก่อนสร้าง)
- ปริมาณ: 15–20 slides
- ภาษา: ไทย, font Sarabun หรือ IBM Plex Sans Thai
- โทน: minimal, ไม่อัดตัวหนังสือ, ใช้ visual / icon / diagram
- ทุก slide มี **speaker note** บอกว่าจะพูดอะไรประมาณ 1–2 ย่อหน้า
- โครงสร้าง:
  1. Title + agenda
  2. Why: ทำไมพนักงานทุกคนควรรู้
  3. Concept หลัก แยก slide ต่อ concept
  4. Real example จากองค์กร (ใช้ข้อมูลจาก context ที่หามา)
  5. Hands-on intro
  6. Key takeaways 3 ข้อ
  7. Resources + Q&A
</deliverable_A>

<deliverable_B name="ใบงาน worksheet">
- Format: .docx (เรียก skill `/mnt/skills/public/docx/SKILL.md` ก่อนสร้าง)
- ขนาด: 2–4 หน้า
- เนื้อหา:
  - หัวกระดาษ: ชื่อ-แผนก, วันที่
  - Section 1: คำถาม warm-up 2–3 ข้อ เกี่ยวกับทะเบียน project ที่ทำอยู่
  - Section 2: โจทย์หลัก — ให้ตัวอย่างทะเบียน project (mock data 8–12 rows) แล้วถามให้ดึง insight 3 ข้อ
  - Section 3: reflection — "จะนำไปใช้กับงานของตัวเองยังไง"
- ห้ามใช้ศัพท์ technical โดยไม่อธิบาย
- มีเฉลยแยก (สร้างเป็นไฟล์ .docx ที่ 2)
</deliverable_B>

<deliverable_C name="คู่มือ handbook">
- Format: .docx (เรียก skill `/mnt/skills/public/docx/SKILL.md` ก่อนสร้าง)
- ขนาด: 6–10 หน้า
- โครงสร้าง:
  1. ปก + สารบัญ
  2. บทนำ: เป้าหมายและกลุ่มผู้อ่าน
  3. Concept หลัก 2–3 อัน (แต่ละอัน 1–2 หน้า: นิยาม + ตัวอย่าง + เมื่อไรใช้)
  4. Step-by-step: เริ่มวิเคราะห์ทะเบียน project ของตัวเองยังไง 5 ขั้น
  5. Common pitfalls (กับดักที่เจอบ่อย) 3–5 ข้อ
  6. Glossary: ศัพท์สำคัญ 10–15 คำ พร้อมคำอธิบายภาษาคน
  7. Resources: ลิงก์/หนังสือ/คลิป เรียนต่อ
</deliverable_C>
</step_3_create_materials>

<style_rules>
- ภาษาไทยทั้งหมด
- ศัพท์ technical ครั้งแรก: EN ตามด้วยไทยในวงเล็บ เช่น "metric (ตัววัด)" ครั้งถัดไปใช้ EN ได้
- ห้ามใช้ศัพท์ที่ไม่ได้อธิบาย เช่น "aggregation", "dimension", "granularity"
- ใช้ analogy / metaphor ที่คนทั่วไปเข้าใจ เช่น "ทะเบียน project เหมือนสมุดบัญชี — ถ้าไม่เคยสรุปท้ายเดือน ก็ไม่รู้ว่าใช้เงินไปกับอะไรเยอะสุด"
- ทุกตัวอย่างต้องเป็นบริบทการทำงานจริง ไม่ใช่ตัวอย่างวิชาการลอยๆ
- โทนเป็นกันเอง ชวนคุย ไม่เป็นทางการเกินไป
</style_rules>

<self_check>
ก่อนส่งให้ผู้ใช้ ตรวจสอบ:
- [ ] ทำ step 1 (search past chats) จริง ไม่ได้ข้าม
- [ ] context ที่ใช้ออกแบบมาจาก chat เก่าจริง อ้างอิงได้
- [ ] รอ confirm outline จากผู้ใช้ก่อนสร้างสื่อจริง
- [ ] สื่อ 3 ชุดครบ: pptx + worksheet docx + handbook docx (และเฉลย)
- [ ] เรียก skill ที่เกี่ยวข้องก่อนสร้างแต่ละไฟล์
- [ ] ทุก slide มี speaker note
- [ ] ไม่มีศัพท์ technical ที่ไม่ได้อธิบาย
- [ ] มี real-world example อย่างน้อย 2 ตัวอย่าง ที่อิง context จริง
- [ ] worksheet มีเฉลย
- [ ] handbook มี glossary
- [ ] ผู้เรียนที่ไม่มี background สามารถตามได้
</self_check>

<start>
เริ่มจาก step 1 — รายงานผลที่ search ได้จาก chat เก่าก่อน แล้วค่อยไป step 2
</start>
```

**ทำอะไร:** สั่งให้ AI ดึง context จาก chat เก่าใน project → ส่ง outline ให้ confirm → สร้างสื่อ 3 ชุด (slide + worksheet + handbook) แบบ concept-focused 1–2 ชม.

**Placeholder:** ไม่มี — copy-paste ใช้ได้ทันที (AI จะหา context และถามต่อเอง)

---

## Prompt B — Full Course Edition (Multi-Session Course)

เน้นเป็นหลักสูตรเต็มรูปแบบ 4–6 modules มี hands-on จริง ผู้เรียนสามารถนำไปใช้กับงานได้

```xml
<role>
คุณคือ Learning Experience Designer (นักออกแบบประสบการณ์การเรียนรู้) + Data Analyst (นักวิเคราะห์ข้อมูล) ที่ออกแบบ corporate training (อบรมในองค์กร) ด้าน data literacy (การรู้เท่าทันข้อมูล) สำหรับ non-technical staff มา 10+ ปี เข้าใจ adult learning principles และ scaffolding (การวางโครงค่อยๆ ขยับขึ้น)
</role>

<task>
ออกแบบและสร้างชุดสื่อ "หลักสูตรการวิเคราะห์ข้อมูลกับทะเบียน project" แบบ multi-session สำหรับเพื่อนพนักงาน โดยผู้เรียนจบหลักสูตรแล้วต้องวิเคราะห์ทะเบียน project ของตัวเองได้จริง
</task>

<audience>
- พนักงานในองค์กร พื้นฐาน technical ไม่สูง
- รู้จัก Excel ระดับพื้นฐาน
- ไม่ถนัด pivot, formula ซับซ้อน, เครื่องมือ BI
- ไม่เคยเรียน statistics หรือ analytics
- มี "ทะเบียน project" ที่ดูแลอยู่จริง (โครงการ/งาน/task ที่บันทึกไว้)
- เป้าหมายปลายทาง: ใช้ทะเบียนของตัวเองตอบคำถามทางธุรกิจได้ ไม่ใช่แค่ "บันทึกเพื่อบันทึก"
</audience>

<step_1_gather_context>
ก่อนออกแบบ ต้องดึง context จาก chat เก่าใน project นี้:

1. เรียก `conversation_search` ด้วย keywords (ทำหลายครั้งให้ครอบคลุม):
   - "ทะเบียน project วิเคราะห์"
   - "แนวทางวิเคราะห์ข้อมูล project"
   - "project registry analysis approach"
   - "KPI metric ทะเบียนโครงการ"
   - "data analysis project tracking"

2. สกัดจากผลลัพธ์:
   - แนวทาง / framework ที่เคยแนะนำไว้
   - ตัวอย่าง metric / KPI ที่ควรดูในทะเบียน project
   - คอลัมน์/field สำคัญที่อ้างถึง
   - เครื่องมือที่แนะนำ
   - ตัวอย่าง use case / pain point
   - ความซับซ้อนของ data ที่ผู้ใช้มี

3. ถ้า context ไม่พอ → หยุดและถาม:
   "หาเนื้อหาจาก chat เก่าได้แค่ [...] ก่อนสร้าง course ขอข้อมูลเพิ่ม:
   - ทะเบียน project ที่จะใช้สอนมีคอลัมน์อะไรบ้าง
   - ขนาด dataset โดยประมาณ
   - คำถามทางธุรกิจที่อยากให้ตอบได้"

ห้ามใช้ generic content — ทุก example, exercise, case study ต้องอิงบริบทจริง
</step_1_gather_context>

<step_2_plan_curriculum>
ออกแบบโครงสร้างหลักสูตรและส่งให้ user confirm ก่อนสร้างสื่อ

โครงสร้างที่ต้องส่ง:

1. **Course Learning Objectives** (terminal objectives) 4–5 ข้อ
   - รูปแบบ: "หลังจบหลักสูตร ผู้เรียนจะ [วัดผลได้]"

2. **โครงสร้าง 4–6 Modules** แต่ละ module:
   - ชื่อ + ระยะเวลา (ประมาณ 2–3 ชม.)
   - Module objectives 2–3 ข้อ
   - หัวข้อหลัก
   - กิจกรรม hands-on
   - ความเชื่อมโยงกับ module ก่อนหน้า

3. **Suggested Module Flow** (แนะนำ — ปรับได้):
   - **Module 1: ทำไมต้องวิเคราะห์ทะเบียน project**
     - ปัญหาของการบันทึกแล้วไม่ใช้
     - คำถามทางธุรกิจที่ทะเบียนตอบได้
   - **Module 2: รู้จักข้อมูลของตัวเอง**
     - อ่านคอลัมน์ในทะเบียน, ประเภทข้อมูล, data quality
   - **Module 3: คำถามพื้นฐาน 5 แบบที่ตอบได้ทันที**
     - นับ, รวม, เฉลี่ย, เปรียบเทียบ, จัดอันดับ
   - **Module 4: หาความสัมพันธ์และ pattern**
     - แนวโน้ม, ความสัมพันธ์ระหว่างคอลัมน์, anomaly
   - **Module 5: สร้าง dashboard / report ง่ายๆ**
     - เลือก chart ที่เหมาะ, สื่อสาร insight
   - **Module 6: นำไปใช้กับงานจริง (capstone)**
     - workshop ใช้ทะเบียนตัวเอง

4. **Assessment plan:** วัดผลยังไงตอนจบแต่ละ module + ตอนจบ course

ส่งให้ user ดู — รอ confirm หรือปรับ
</step_2_plan_curriculum>

<step_3_create_materials_per_module>
หลัง confirm หลักสูตรแล้ว สร้างสื่อ **per module** (ทำทีละ module เพื่อไม่ overload):

ถามผู้ใช้: "เริ่มสร้างสื่อ module ไหนก่อน หรือสร้างทั้งหมดทีเดียว?"

แต่ละ module ส่ง 3 deliverables:

<deliverable_A name="สไลด์ presentation per module">
- Format: .pptx (เรียก skill `/mnt/skills/public/pptx/SKILL.md` ก่อนสร้าง)
- ปริมาณ: 20–30 slides ต่อ module
- ภาษา: ไทย, font Sarabun หรือ IBM Plex Sans Thai
- ทุก slide มี speaker note
- โครงสร้าง:
  1. Module title + agenda + objectives
  2. Hook / scenario เปิด module
  3. Content (concept → example → mini-exercise) ทำ pattern นี้ซ้ำ
  4. Hands-on intro
  5. Module wrap-up + เชื่อมต่อ module ถัดไป
</deliverable_A>

<deliverable_B name="ใบงาน workbook per module">
- Format: .docx (เรียก skill `/mnt/skills/public/docx/SKILL.md` ก่อนสร้าง)
- ขนาด: 4–8 หน้า
- เนื้อหา:
  - Warm-up 2–3 ข้อ
  - Exercise หลัก 3–5 ข้อ ระดับง่าย → ยาก
  - Capstone activity ของ module
  - Reflection: "จะใช้กับงานของฉันยังไง"
  - Self-check rubric
- ห้ามใช้ศัพท์ที่ไม่ได้อธิบาย
- มีเฉลยแยก (.docx ที่ 2)
</deliverable_B>

<deliverable_C name="Handbook (สร้างครั้งเดียว ครอบคลุมทุก module)">
- Format: .docx (เรียก skill `/mnt/skills/public/docx/SKILL.md` ก่อนสร้าง)
- ขนาด: 30–50 หน้า
- โครงสร้าง:
  1. ปก, คำนำ, สารบัญ
  2. How to use this handbook
  3. Module 1–N: แต่ละ module 4–8 หน้า
     - Concept summary
     - Step-by-step instruction
     - Worked example (ตัวอย่างทำเสร็จ)
     - Common pitfalls
     - Checklist
  4. Master glossary 30–50 คำ
  5. Templates: ตารางวิเคราะห์, checklist, dashboard layout
  6. Further resources
  7. ภาคผนวก: cheat sheet 2 หน้าสรุปทั้งหมด
</deliverable_C>

<deliverable_D name="Facilitator Guide (สำหรับผู้สอน)">
- Format: .docx
- ขนาด: 15–25 หน้า
- เนื้อหา:
  - Overview หลักสูตร, prerequisites, equipment
  - Timing breakdown per module
  - Facilitation tips ต่อ section
  - Discussion questions
  - Trouble-shooting: ถ้าผู้เรียนติด common issues จะแก้ยังไง
  - Assessment guide + scoring rubric
</deliverable_D>
</step_3_create_materials_per_module>

<style_rules>
- ภาษาไทยทั้งหมด
- ศัพท์ technical ครั้งแรก: EN + ไทยในวงเล็บ ครั้งถัดไปใช้ EN
- ห้ามใช้ศัพท์ที่ไม่ได้อธิบาย เช่น dimension, aggregation, normalization, ETL
- ใช้ analogy ที่ relate กับงาน office ทั่วไป
- ทุก example ต้องเป็นบริบทการทำงานจริงในองค์กร อิง context จาก chat เก่า
- Scaffolding: เริ่มจาก concept ง่ายมาก ค่อยๆ ขยับ — ห้ามกระโดด
- ทุก module ต้อง link กลับไปยังคำถาม "แล้วเอาไปใช้กับงานเรายังไง"
- มี "moment of relevance" ทุก 15–20 นาทีเพื่อรักษา engagement
</style_rules>

<self_check>
ก่อนส่งแต่ละ deliverable ตรวจสอบ:
- [ ] ทำ step 1 (search past chats) จริง ไม่ใช่เดา
- [ ] รอ confirm curriculum ก่อนสร้างสื่อ
- [ ] ทุก example อิง context จริง ไม่ใช่ generic
- [ ] เรียก skill ที่เกี่ยวข้องก่อนสร้างไฟล์
- [ ] Module objectives เชื่อมกลับ course objectives
- [ ] Slide มี speaker note ครบทุกหน้า
- [ ] Workbook มีเฉลย
- [ ] ไม่มีศัพท์ที่ไม่ได้อธิบาย
- [ ] Scaffolding ชัด: ง่าย → ยาก
- [ ] Hands-on activity จริง ไม่ใช่อ่านอย่างเดียว
- [ ] Facilitator guide ครอบคลุม trouble-shooting
- [ ] ผู้เรียน non-technical ตามได้
</self_check>

<start>
เริ่มจาก step 1 — รายงานผลที่ search ได้จาก chat เก่าก่อน แล้วค่อยไป step 2 (curriculum plan)
</start>
```

**ทำอะไร:** สั่งให้ AI ดึง context จาก chat เก่า → ออกแบบหลักสูตร 4–6 modules → confirm → ทยอยสร้างสื่อต่อ module (slide + workbook + handbook + facilitator guide)

**Placeholder:** ไม่มี — copy-paste ใช้ได้ทันที

---

## ตารางเปรียบเทียบ 2 Versions

| มิติ | Prompt A (Concept-Focused) | Prompt B (Full Course) |
|------|---------------------------|------------------------|
| ระยะเวลา | 1–2 ชม. ครั้งเดียว | 4–6 modules × 2–3 ชม. |
| Deliverables | 3 ไฟล์ (slide + worksheet + handbook) | 13+ ไฟล์ (slide×N + workbook×N + handbook + facilitator guide) |
| ความลึก | เข้าใจ concept พอเอาไปต่อยอด | ทำได้จริงกับงานของตัวเอง |
| Hands-on | สั้นๆ บน worksheet | จริงจัง มี capstone |
| เหมาะกับ | Awareness session, kickoff | Skill-building program |
| เวลาที่ AI ใช้สร้าง | ~5–10 นาที | ~30–60 นาที (ทยอยสร้าง) |

---

## คำแนะนำการใช้งาน

1. **ต้องอยู่ใน Project เดียวกัน** — เพราะ prompt ใช้ `conversation_search` ดึง chat เก่า ถ้าไม่อยู่ใน project จะหาไม่เจอ
2. **ใช้ Claude.ai ที่ enable Create File / File Skills** — เพื่อสร้าง .pptx และ .docx ได้จริง
3. **เริ่มจาก Prompt A ก่อน** ถ้าไม่แน่ใจ — ใช้เวลาน้อย, ดูคุณภาพก่อน decide ทำ full course
4. **ตอบคำถามที่ AI ถาม** — ถ้า context ไม่พอ AI จะถาม ไม่ต้องเดา
5. **Confirm outline ก่อนสร้างสื่อจริง** — แก้ outline ง่ายกว่าแก้สื่อที่สร้างแล้ว

ลองใช้แล้วผลลัพธ์เป็นอย่างไรครับ? มีจุดไหนอยากปรับ — เช่น เพิ่ม module video, ปรับ tone, เปลี่ยน deliverable format?
