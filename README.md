# AgriFlow — Smart Irrigation Dashboard

เว็บแอปต้นแบบสำหรับระบบให้น้ำอัจฉริยะ พัฒนาด้วย React + Vite โดยแบ่งเป็น 2 ส่วนหลัก

- **Control** — แสดงค่าความชื้นดิน อุณหภูมิ ความชื้นสัมพัทธ์ กราฟแนวโน้ม และส่วนควบคุมปั๊มน้ำ Manual / Auto
- **Docs & Learning** — คู่มือและองค์ความรู้เรื่องภาพรวมระบบ ฮาร์ดแวร์ MQTT หลักการควบคุม และความปลอดภัย

> เวอร์ชันนี้เป็น Frontend Prototype ใช้ข้อมูลจำลอง (Mock Data) ที่อัปเดตทุก 3 วินาที ปุ่มควบคุมปั๊มและโหมด Auto ยังไม่สั่งงาน ESP32 หรือปั๊มน้ำจริง

## สิ่งที่ต้องติดตั้ง

1. Node.js เวอร์ชัน LTS
2. Visual Studio Code (แนะนำ)
3. แตกไฟล์ ZIP นี้ไว้ในโฟลเดอร์ที่ต้องการ

## วิธีเปิดใช้งาน

เปิด Terminal ในโฟลเดอร์ `smart-irrigation-web` แล้วรันคำสั่งตามลำดับ

```bash
npm install
npm run dev
```

จากนั้นเปิด URL ที่ Vite แสดงใน Terminal โดยปกติจะเป็น `http://localhost:5173`

## สร้างไฟล์สำหรับ Deploy

```bash
npm run build
npm run preview
```

ไฟล์เว็บไซต์ที่ Build แล้วจะอยู่ในโฟลเดอร์ `dist`

## โครงสร้างโปรเจค

```text
smart-irrigation-web/
├── index.html
├── package.json
├── README.md
└── src/
    ├── components/
    │   ├── LineChart.jsx
    │   ├── MetricCard.jsx
    │   ├── PumpPanel.jsx
    │   ├── Sidebar.jsx
    │   └── Topbar.jsx
    ├── data/
    │   └── mockData.js
    ├── pages/
    │   ├── Control.jsx
    │   └── Docs.jsx
    ├── App.jsx
    ├── main.jsx
    └── styles.css
```

## วิธีเปลี่ยนจากข้อมูลจำลองเป็น ESP32 จริงในอนาคต

แนะนำโครงสร้างการสื่อสารดังนี้

```text
ESP32  -- MQTT -->  MQTT Broker  -->  Node.js Backend
                                           |
                                      Socket.IO
                                           |
                                      React Web App
```

ตัวอย่าง MQTT Topic ที่วางแผนไว้

| Topic | ทิศทาง | หน้าที่ |
|---|---|---|
| `farm/field01/telemetry` | ESP32 → Backend | ส่งค่าความชื้น อุณหภูมิ และความชื้นอากาศ |
| `farm/field01/cmd` | Backend → ESP32 | ส่งคำสั่งควบคุมปั๊ม / เปลี่ยนโหมด |
| `farm/field01/status` | ESP32 → Backend | รายงานสถานะปั๊มและอุปกรณ์ |

ตัวอย่าง JSON สำหรับ Telemetry

```json
{
  "deviceId": "farm-01",
  "timestamp": "2026-11-11T22:00:00+07:00",
  "soilMoisture": 45.5,
  "temperature": 29.5,
  "humidity": 70,
  "pumpState": "OFF",
  "mode": "AUTO"
}
```

**ข้อควรระวัง:** ห้ามใส่ MQTT username/password หรือ secret key ไว้ใน Frontend ควรให้ Backend เป็นตัวกลาง และให้ ESP32 มี logic ความปลอดภัยในตัวเอง เช่น ปิดปั๊มเมื่อข้อมูลเซนเซอร์ผิดปกติหรือการสื่อสารขาดหาย

## ไฟล์สำคัญที่แก้ไขบ่อย

- `src/App.jsx` — state หลักและการจำลองข้อมูล Real-time
- `src/pages/Control.jsx` — layout หน้า Dashboard
- `src/pages/Docs.jsx` — บทเรียนและเนื้อหาในหน้า Docs
- `src/components/PumpPanel.jsx` — ปุ่มควบคุม Manual / Auto
- `src/styles.css` — สี ขนาด และ Responsive UI
