# Review of docs/ai/l3-draft.md
Reviewers: ten101popo and bankjunjan. Checked against docs/intent/movie-filter.md and SMART.

| บรรทัดในร่าง | ปัญหา | ตัดสิน: แก้ ตัด หรือเก็บ |
|---|---|---|
| 14: "REQ-1: The system shall recognise exactly five ratings: G, PG, PG-13, R, NC-17." | ตรงกับ Constraints ใน intent และทดสอบได้ชัดเจน | เก็บ |
| 15: "A movie with any other rating value shall be rejected when it is added to the catalog." (REQ-2) | การเพิ่มหนังเป็นขอบเขตของ L2 (enum Rating บังคับอยู่แล้ว) ลูกค้าขอเฉพาะการกรองรายการ | ตัด |
| 16–17: "Given one rating, the filter operation shall return every catalog movie whose rating equals that rating" และ "contain no movie whose rating differs" (REQ-3, REQ-4) | ตรงกับ Proposed outcome ของ intent และวัดผลได้ (Measurable) | เก็บ |
| 18: "The filter operation shall accept exactly one rating per call." (REQ-5) | ตอบ Open question ข้อ 2 แทนลูกค้า และคำว่า per call เป็นคำระดับโค้ด | แก้: ถาม customer proxy (ยืนยันว่าเลือกครั้งละ 1 rating) |
| 19: "The unfiltered movie list shall be ordered by release date, newest first." (REQ-6) | ตรงกับความต้องการอยากเห็นหนังใหม่ก่อน แต่ต้องนิยามคำว่า newest ให้ชัด | แก้: แยกเป็นบัตรใบที่ 2 (Sort movies: newest first or alphabetically) |
| 20–21: "The filtered list shall be ordered by release date, newest first" และ "deterministic" (REQ-7, REQ-8) | ลูกค้าขอเรียงหนังใหม่ในรายการเต็ม ไม่ได้ขอในผลการกรอง และ deterministic เป็นคำทางเทคนิค | ตัด |
| 22: "shall return an empty list and shall not throw an exception" (REQ-9) | ตอบ open question ข้อ 1 แทนลูกค้า และ exception เป็นคำของโค้ดไม่ใช่สิ่งที่ผู้ใช้เห็น | แก้: ถาม customer proxy (ให้แสดงรายการว่างพร้อมข้อความ No movies with this rating) |
| 23: "shall reject the call with an error that names the five allowed ratings" (REQ-10) | ผู้ปกครองเลือกจากตัวเลือก 5 ค่าบนหน้าจอ ไม่ได้พิมพ์เอง ลูกค้าไม่ได้ขอ | ตัด |
| 41: "The selection takes one call" (AC-1.5) | one call วัดจากโค้ด ไม่ใช่สิ่งที่ผู้ใช้เห็น จึงไม่ผ่านเกณฑ์ Measurable ของ SMART | ตัด |
| 48–56: Scenario happy path ผสมการเรียงลำดับ "Bravo" ก่อน "Alpha" เข้ากับการกรอง | ผสมสองฟีเจอร์ใน scenario เดียว ทำให้ไม่ Specific เฉพาะการกรองตาม rating | แก้: เหลือเฉพาะตรวจว่าเห็นหนังที่มี rating ที่เลือก และไม่เห็นหนัง rating อื่น |
| 63–67: "Scenario: Selection is not one of the five ratings" เลือก "PG-14" | ผู้ใช้เลือกจาก dropdown ที่มีแค่ 5 ค่า จึงเลือก PG-14 ไม่ได้ ลูกค้าไม่ได้ขอ | ตัด |
| 70–74: "### Story 2: Newest first" รวมทั้ง full list และ filtered list | ลูกค้ายืนยันให้เรียงรายการหนังแบบ newest first หรือ alphabetically แยกจากการกรอง | แก้: ปรับเป็นบัตรใบที่ 2 (Sort movies) |
| 80: "### Story 3: Clear empty result" | เป็น sad path ของ Story 1 ไม่ใช่ user story ใหม่แยกต่างหาก | แก้: ย้ายเป็นบัตรใบที่ 3 (Sad path of card 1) |
| 98: "\"Newest\" means the release date, not the date the movie was added to the catalog." (§4 ข้อ 2) | แยกสมมติฐานที่ไม่ได้อยู่ใน intent ออกมาชัดเจนเพื่อนำไปถามยืนยันกับลูกค้า | เก็บ |
| 110: "**Suitability is not the same as rating.**" (§5 Areas of concern) | ข้อกังวลที่ถูกต้อง เพราะเรตติ้งไม่ได้รับประกันว่าเนื้อหาเหมาะกับเด็กทุกคน | เก็บ |

Kept: สมมติฐานในหัวข้อ 4 (เช่น ความหมายของ newest คือวันออกฉาย) และข้อกังวลในหัวข้อ 5 เรื่อง "**Suitability is not the same as rating.**" เพราะร่างแยกสิ่งที่คาดเดาและข้อจำกัดทางธุรกิจออกมาเตือนทีมได้ตรงจุดโดยไม่ปะปนกับข้อกำหนดหลัก
Counts: แก้ 6 ตัด 5 เก็บ 4
Customer proxy: ผู้สอน/ผู้ช่วยสอนในบทบาทผู้ปกครองของเด็ก 8 ขวบ ยืนยันบัตรทั้งสามใบเมื่อ 2026-11-25 (เลือกครั้งละหนึ่ง rating, ไม่มีหนังให้เห็นรายการว่างพร้อมข้อความ No movies with this rating, และขอเรียงตามชื่อเพิ่มเติมด้วย)
