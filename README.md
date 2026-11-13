# HEARTBEAT RUSH — Clinical Emergency Response & Telemetry Simulator (V5.0)

> **“ช่วยให้เป็น เห็นแล้วกล้าทำ”** — คณะแพทยศาสตร์ มหาวิทยาลัยบูรพา (Faculty of Medicine, Burapha University)  
> ระบบจำลองสถานการณ์กู้ชีพฉุกเฉินและการกู้ชีพขั้นพื้นฐาน (CPR & First Aid) แบบ Interactive สำหรับงาน MDBUU Open House

---

## 🫀 ภาพรวมโครงการ (Project Overview)

**HEARTBEAT RUSH Simulator (V5.0 Production)** เป็น Single-Page Interactive Web Application ที่ถูกออกแบบขึ้นเป็นพิเศษสำหรับการจำลองเหตุการณ์ฉุกเฉินทางการแพทย์ โดยจำลองมอนิเตอร์สัญญาณชีพทางการแพทย์ (ICU/Emergency Telemetry Monitor) ควบคู่กับโจทย์สถานการณ์ฉุกเฉิน 6 ด่าน สำหรับให้นักเรียนมัธยมและผู้เข้าร่วมกิจกรรมได้มีส่วนร่วมจริงทั้งห้อง (Audience Engagement)

---

## ✨ ไฮไลท์และฟีเจอร์เด่นในเวอร์ชัน V5.0

1. **Cohesive Dark Obsidian & Elevated Slate Console:**
   - ธีมหน้าจอมอนิเตอร์การแพทย์ระดับ ICU ด้วยโทนสี Dark Obsidian (`#090D14`) และการ์ด Slate (`#111827`)
   - ตัวอักษรขนาดใหญ่พิเศษ (`IBM Plex Sans Thai` + `JetBrains Mono`) ชัดเจนระดับโปรเจกเตอร์ห้องประชุม
   - เว้นระยะ Safe-Area ด้านล่าง `56px` ไม่ถูกบังด้วย macOS Dock หรือแถบโปรเจกเตอร์
2. **High-DPI Anatomical ECG Waveform Engine:**
   - คำนวณคลื่นไฟฟ้าหัวใจจริงด้วยสมการ Gaussian Curve (P, Q, needle R, S, T) 60 FPS
   - คลื่นเปลี่ยนสถานะและสีอัตโนมัติตามสภาวะผู้ป่วย:
     - **Lobby:** Normal Sinus `76 BPM` (สีเขียวมรกต `#10B981`)
     - **Stage 1–3:** Critical Bradycardia `38 BPM` (สีแดงวิกฤต `#FF3B5C`, R-R interval ไม่สม่ำเสมอ พร้อมเสียงเตือน `540Hz`)
     - **Stage 4:** Agonal Rhythm `22 BPM` (คลื่นกว้างช้า วิกฤตใกล้เสียชีวิต)
     - **Stage 5A:** Coarse V-Fib พร้อม Mechanical Compression Spikes ตามจังหวะการกดหน้าอก
     - **Stage 5B:** Defibrillator Discharge Spike
     - **Stage 6:** Return of Spontaneous Circulation (ROSC) Normal Sinus `88 BPM`
3. **Microphone & Speech Recognition (Zero-Deadlock Architecture):**
   - **Time-Domain RMS Audio Analyser:** คำนวณระดับเสียงเดซิเบลสดตลอดเวลา แสดงเสียงห้องจริง `45–55 dB` และเสียงตะโกน `82–95+ dB`
   - **Stage 2 Shout Meter:** วัดระดับเสียงตะโกนพร้อมกันทั้งห้อง (>82 dB ต่อเนื่อง ~1.5 วินาที)
   - **Stage 3 1669 Dispatch:** ระบบถอดเสียงพูดสดภาษาไทย (`webkitSpeechRecognition`) ตรวจจับ 4 ข้อมูลสำคัญ (พิกัด, เหตุการณ์, อาการ, รถกู้ชีพ+AED) พร้อมช่องพิมพ์ข้อความสำรอง และปุ่มลัด `[1]`, `[2]`, `[3]` โดยจะก้าวหน้าไปด่าน 4 อัตโนมัติหลังแจ้งครบ
   - **Stage 5A CPR Acoustic Clap:** ตรวจจับเสียงปรบมือของคนทั้งห้อง 30 ครั้ง เข้าจังหวะ Metronome `110 BPM` พร้อมระบบป้องกันการปรบมือรัวมั่ว (<350ms)
   - **Stage 5B AED Voice Clearance:** ตรวจจับคำสั่งความปลอดภัย 3 ขั้น: *"ฉันถอย! ➔ คุณถอย! ➔ ทุกคนถอย!"*
4. **Resilient Thai Voice Coach & Hospital Intercom Chime:**
   - ระบบตรวจจับเสียงภาษาไทยในเครื่องอัตโนมัติ หากไม่มีเสียงภาษาไทยใน OS จะสลับไปใช้ **Hospital Intercom Chime (660Hz ➔ 880Hz)** ทันทีโดยไม่ค้าง
5. **Photo-Ready Rank Scoreboard (Stage 6):**
   - สรุปคะแนน เหรียญรางวัล (`RANK S`, `RANK A+`, `RANK A`) อัตราความเร็ว CPR และการประเมิน 4 ด้าน ในส่วน 56% ด้านบน
   - พื้นที่ 44% ด้านล่างเป็น **Photo Clean Zone** ปลอดโปร่งสำหรับให้น้องๆ ยืนถ่ายรูปหมู่หน้าจอโปรเจกเตอร์

---

## ⌨️ ปุ่มลัดสำหรับพิธีกรและผู้ควบคุม (Keyboard Shortcuts)

| ปุ่ม | คำสั่ง | รายละเอียด |
| :--- | :--- | :--- |
| `[F]` | Fullscreen Toggle | สลับโหมดเต็มหน้าจอโปรเจกเตอร์ |
| `[M]` | Mute Audio Toggle | ปิด / เปิดเสียงระบบทั้งหมด |
| `[R]` | Quick Reset | รีเซ็ตระบบทันทีพร้อมเริ่มกลุ่มถัดไป |
| `[Spacebar]` | Action Trigger | กดหน้าอก CPR / ผ่านด่านเสียงตะโกน / ปลดล็อกขั้นตอน AED |
| `[1]`, `[2]`, `[3]`, `[4]` | Choice Selection | เลือกคำตอบหรือขั้นตอนของแต่ละด่าน |
| `[Shift + ➔]` | Skip Stage | ข้ามไปยังด่านถัดไปทันที (กรณีเร่งด่วน) |
| `[B]` | Previous Stage | ย้อนกลับไปด่านก่อนหน้า |

---

## 🚀 วิธีการเปิดใช้งาน (How to Run)

ตัวแอปพลิเคชันเป็นไฟล์ Standalone HTML เดี่ยว (`index.html`) ไม่มี External Dependencies (ยกเว้น Google Web Fonts):

### วิธีที่ 1: เปิดผ่านเบราว์เซอร์ Google Chrome โดยตรง
ดับเบิลคลิกไฟล์ `index.html` หรือลากไฟล์ใส่ Google Chrome บน macOS ได้ทันที

### วิธีที่ 2: รันผ่าน Local Web Server (แนะนำสำหรับการเปิดไมโครโฟน)
\`\`\`bash
cd heartbeat-rush-sim
npx serve -l 3000
# หรือ
python3 -m http.server 8080
\`\`\`
แล้วเปิด `http://localhost:3000` บน Google Chrome

### วิธีที่ 3: GitHub Pages Deployment
1. Push โค้ดขึ้น GitHub Repository
2. ไปที่ **Settings** > **Pages**
3. เลือก Source เป็น Branch `main` โฟลเดอร์ `/ (root)`
4. เข้าใช้งานผ่านลิงก์ `https://<username>.github.io/<repo>/` ได้ทุกอุปกรณ์ผ่าน HTTPS (ไมโครโฟนจะทำงานได้สมบูรณ์แบบ 100%)

---

## 🏥 ลิขสิทธิ์และผู้จัดทำ
- **ผู้พัฒนาและจัดทำ:** Asia & Open House Team
- **สังกัด:** คณะแพทยศาสตร์ มหาวิทยาลัยบูรพา (Faculty of Medicine, Burapha University)
- **กิจกรรม:** MDBUU Open House 2026 — ฐาน CPR & First Aid (Heartbeat Rush)
