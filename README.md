# CHECO / มคอ.2 Dashboard

Static dashboard สำหรับ publish บน GitHub Pages โดยอ่านข้อมูลจาก Google Sheet ผ่าน Google Apps Script endpoint.

## วิธีใช้

1. สร้าง GitHub repository ใหม่
2. อัปโหลดไฟล์ `index.html` ไปไว้ที่ root ของ repository
3. ไปที่ `Settings > Pages`
4. เลือก `Deploy from a branch`
5. เลือก branch `main` และ folder `/root`
6. รอ GitHub Pages สร้าง URL

## Data endpoint

Dashboard ตั้งค่า endpoint ไว้ในไฟล์ `index.html` ที่ตัวแปร:

```js
const API_URL = 'https://script.google.com/macros/s/AKfycbyhrRlTbBZMvv398xXG1QCothkee4G-zPvS4Ma6BUi0_sxfZuH4g1fxygmBAu7GtLWg/exec';
```

## รูปแบบ JSON ที่ dashboard รองรับ

```json
{
  "status": "success",
  "count": 298,
  "data": [
    {
      "หน่วยงาน": "...",
      "คณะ": "...",
      "วิทยาเขต": "...",
      "รหัสติดตาม": "...",
      "รหัสหลักสูตร": "...",
      "ชื่อหลักสูตร": "...",
      "ระดับการศึกษา": "...",
      "วันที่รับทราบ": "...",
      "ประเภทการดำเนินการ": "...",
      "เกณฑ์ประเมิน": "..."
    }
  ]
}
```

Dashboard ยังรองรับกรณี response เป็น array โดยตรง หรือใช้ key `rows` แทน `data`.

## Logic หลัก

- จัดกลุ่มหลักตาม `เกณฑ์ประเมิน`
- `ประเภทการดำเนินการ` ที่นับเป็น มคอ.2 ได้แก่ `หลักสูตรใหม่`, `ปรับปรุงตามกำหนดรอบปรับปรุง`, `ปรับปรุงแบบแยก`, `ปรับปรุงแบบรวม`
- ถ้า `วันที่รับทราบ` มีวันที่ = ส่ง มคอ.2 สำเร็จ
- ถ้า `วันที่รับทราบ` เป็น `อยู่ระหว่างการพิจารณา` หรือว่าง = ยังไม่สำเร็จ
- `ปรับปรุงเล็กน้อย ครั้งที่ ...` นับเป็นจำนวนครั้งการปรับแก้ ไม่เอาไปรวมกับการส่ง มคอ.2 สำเร็จ
