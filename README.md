# JMeter Load Test

โปรเจกต์นี้เก็บแผนทดสอบโหลดด้วย Apache JMeter สำหรับ API บน `127.0.0.1:8000` พร้อมผลการทดสอบและ Dashboard ที่สร้างไว้แล้ว โดยแบ่งการทดสอบเป็น 3 ระดับ ได้แก่ Basic, Increased และ Higher Load

## สิ่งที่ต้องเตรียม

- Java ที่รองรับ Apache JMeter
- Apache JMeter 5.6.3 หรือเวอร์ชันที่รองรับไฟล์แผนทดสอบนี้
- API ที่กำลังทำงานและเข้าถึงได้ที่ `http://127.0.0.1:8000`

> Source code ของ API ไม่ได้อยู่ใน repository นี้ โปรดเริ่ม API แยกต่างหากก่อนรันทดสอบ

## สถานการณ์ทดสอบ

| สถานการณ์ | ไฟล์แผนทดสอบ | Virtual users | Ramp-up | Loops ต่อ user | จำนวน requests ที่คาดหมาย |
|---|---|---:|---:|---:|---:|
| A - Basic Load | `A/Test Scenario A- Basic Load.jmx` | 5 | 10 วินาที | 2 | 30 |
| B - Increased Load | `B/Test Scenario B- Increased Load.jmx` | 10 | 20 วินาที | 3 | 90 |
| C - Higher Load | `C/Test Scenario C-Higher Load.jmx` | 20 | 40 วินาที | 5 | 300 |

แต่ละ loop เรียก API 3 รายการ:

- `GET /api/health` — ตรวจสอบสถานะ โดยคาดหวัง HTTP 200 และ JSON ที่มี `status` เป็น `ok`
- `GET /api/products` — ตรวจสอบรายการสินค้า โดยคาดหวัง HTTP 200 และมีข้อมูลสินค้า
- `GET /api/products/1` — ตรวจสอบข้อมูลสินค้ารายการหนึ่ง โดยคาดหวัง HTTP 200

แผนทดสอบกำหนดให้ตรวจสอบคำตอบตามเงื่อนไขข้างต้น หาก API ใช้ host หรือ port อื่น ให้แก้ค่าในไฟล์ `.jmx` ก่อนเริ่มทดสอบ

## โครงสร้างไฟล์

```text
.
├── A/
│   ├── Test Scenario A- Basic Load.jmx
│   ├── results.jtl
│   └── dashboard/
├── B/
│   ├── Test Scenario B- Increased Load.jmx
│   ├── results.jtl
│   └── dashboard/
├── C/
│   ├── Test Scenario C-Higher Load.jmx
│   ├── results.jtl
│   └── dashboard/
└── README.md
```

- ไฟล์ `.jmx` คือแผนทดสอบที่เปิดหรือแก้ไขได้ด้วย JMeter
- ไฟล์ `results.jtl` คือผลการทดสอบที่บันทึกไว้
- โฟลเดอร์ `dashboard/` มีรายงาน HTML ที่สร้างไว้แล้ว เปิด `dashboard/index.html` ในเว็บเบราว์เซอร์เพื่อดูสรุปและกราฟ

## วิธีรันทดสอบ

1. เริ่ม API และตรวจสอบว่าเข้าถึง `http://127.0.0.1:8000/api/health` ได้
2. ติดตั้ง JMeter และเพิ่มโฟลเดอร์ `bin` ของ JMeter ลงใน `PATH` เพื่อเรียกคำสั่ง `jmeter` ได้
3. เปิด PowerShell ที่โฟลเดอร์หลักของ repository แล้วรันสถานการณ์ที่ต้องการ เช่น:

```powershell
jmeter -n -t "A\Test Scenario A- Basic Load.jmx" -l "A\results-run.jtl"
```

ตัวอย่างคำสั่งสำหรับสถานการณ์อื่น:

```powershell
jmeter -n -t "B\Test Scenario B- Increased Load.jmx" -l "B\results-run.jtl"
jmeter -n -t "C\Test Scenario C-Higher Load.jmx" -l "C\results-run.jtl"
```

คำสั่ง `-n` ใช้รันแบบ non-GUI, `-t` ระบุไฟล์แผนทดสอบ และ `-l` ระบุไฟล์สำหรับบันทึกผล ใช้ชื่อไฟล์ผลลัพธ์ใหม่เพื่อเก็บ `results.jtl` ที่มีอยู่ไว้เปรียบเทียบ

## สร้าง Dashboard จากผลการทดสอบ

ใช้ไฟล์ `.jtl` จากการรันเพื่อสร้างรายงาน HTML โดยระบุโฟลเดอร์รายงานใหม่ที่ยังไม่มีอยู่:

```powershell
jmeter -g "A\results-run.jtl" -o "A\dashboard-run"
```

เปลี่ยน `A` เป็น `B` หรือ `C` เพื่อสร้างรายงานของสถานการณ์นั้น จากนั้นเปิด `dashboard-run\index.html` ในเว็บเบราว์เซอร์ โฟลเดอร์ปลายทางของ `-o` ต้องยังไม่มีอยู่หรือเป็นโฟลเดอร์ว่าง และไม่ควรใช้ชื่อ `dashboard` หากต้องการเก็บรายงานเดิมไว้

## เปิดผลลัพธ์ที่มีอยู่

เปิดไฟล์ต่อไปนี้ในเว็บเบราว์เซอร์เพื่อดู Dashboard ที่เตรียมไว้:

- `A/dashboard/index.html`
- `B/dashboard/index.html`
- `C/dashboard/index.html`

ผลการทดสอบดิบที่เกี่ยวข้องอยู่ใน `A/results.jtl`, `B/results.jtl` และ `C/results.jtl`



## นายกิตติศักดิ์ ขันเเข็ง 673450031-4