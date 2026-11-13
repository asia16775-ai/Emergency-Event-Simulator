# HEARTBEAT RUSH — Clinical Emergency Response & Telemetry Simulator (V6.0 Safari & Chrome Perfected)

> **“ช่วยให้เป็น เห็นแล้วกล้าทำ”** — คณะแพทยศาสตร์ มหาวิทยาลัยบูรพา (Faculty of Medicine, Burapha University)  
> ระบบจำลองสถานการณ์กู้ชีพฉุกเฉินและการกู้ชีพขั้นพื้นฐาน (CPR & First Aid) แบบ Interactive สำหรับงาน MDBUU Open House  
> **Live Deployment:** `https://asia16775-ai.github.io/Emergency-Event-Simulator/`

---

## 🫀 ภาพรวมโครงการ (Project Overview)

**HEARTBEAT RUSH Simulator (V6.0 Safari & Chrome Perfected Release)** เป็น Single-Page Standalone Web Application ที่ได้รับการออกแบบทางวิศวกรรมเพื่อแก้ปัญหาและยกระดับประสิทธิภาพการใช้งานจริงบนเบราว์เซอร์หลักทั้ง **macOS Safari** และ **Google Chrome** ผสานหน้าจอมอนิเตอร์สัญญาณชีพทางการแพทย์ (ICU/Emergency Telemetry Monitor) เข้ากับโจทย์สถานการณ์ฉุกเฉิน 6 ด่าน สำหรับนักเรียนมัธยมและผู้เข้าร่วมกิจกรรมทั้งห้อง 20–30 คน

---

## 🛡️ สถาปัตยกรรมแก้ปัญหาหน้างาน V6.0 (Safari WebKit & Chrome Perfected)

### 1. Web Audio Graph แก้ไขระบบไมโครโฟนบน Safari WebKit
* **ปัญหาเดิม:** บน macOS/iOS Safari หาก `AnalyserNode` ไม่ได้เชื่อมต่อกับ `audioCtx.destination` ตัว WebKit Audio Graph Optimizer จะมองว่าเป็น Branch ที่ไม่มีการใช้งาน และส่งค่าสัญญาณเสียงเป็น 0 (Flat zero) ทำให้หลอดวัดเสียงและตัวจับเสียงตบมือไม่ขยับ
* **วิธีแก้:**
  1. เชื่อมต่อ `micSource -> analyserNode -> zeroGainNode (gain = 0.0001) -> audioCtx.destination`
  2. จัดเก็บ `window._micStream`, `window._micSource`, และ `window._analyser` ไว้ที่ระดับ `window` เพื่อป้องกัน Safari Garbage Collector เข้ามาตัดการเชื่อมต่อเธรดเสียง

### 2. แถบสั่งการด้วยเสียงสองระบบ (Voice + Click Dual Input) ในด่านที่ 1 และ 4
* แสดงแถบ **Live Voice Answer Bar** ด้านบนคำถาม:  
  `🎙️ ระบบสั่งการด้วยเสียงทำงานอยู่: พูดว่า "เลือกข้อ 1–4" หรือพูดเนื้อหาคำตอบใส่ไมค์ได้เลย (หรือคลิกปุ่มด้านล่าง)`
* แสดงข้อความที่ระบบถอดเสียงได้แบบ Real-time
* ระบบ **Smart Voice-to-Choice Matcher (`matchVoiceToChoice`)** ตรวจจับคีย์เวิร์ดคำตอบภาษาไทยอัตโนมัติ
* ระบบจะรอให้ AI พูดสรุปความรู้ทางการแพทย์จนจบ (`await speakAndWait(...)`) ก่อนจะเปลี่ยนไปด่านถัดไป

### 3. ด่านที่ 2 (ขอความช่วยเหลือ): หลอดวัดเสียงคู่ & บังคับความดัง 100% (No Quiet Bypass)
* แสดง **หลอดวัดเสียง 2 หลอด (Dual-Bar UI):**
  1. **หลอดวัดความดังเสียงสด (Live Acoustic Volume dB):** ขยับขึ้นลงตามเสียงจริงแบบ 60fps แบ่งเป็น 3 โซนสีชัดเจน (<65 dB สีฟ้า, 65–79 dB สีส้ม, >=80 dB สีเขียว) พร้อมเส้นเป้าหมาย `[ 80 dB TARGET ]`
  2. **หลอดสะสมพลังเสียงกู้ชีพ (0% ➔ 100%):** จะเพิ่มขึ้น **เฉพาะเมื่อเสียงดังเกิน 80 dB เท่านั้น** (ต้องตะโกนดังต่อเนื่อง 1.4 วินาที)
* หากพูดข้อความเบาๆ ระบบจะแสดงป้ายเตือน: `"✓ ได้ยินคำพูดแล้ว แต่ต้องตะโกนให้ดังเกินขีด 80 dB ด้วย!"` โดยไม่ปล่อยให้ผ่านด่านจนกว่าจะตะโกนดังจริง

### 4. ระบบ Promise-Based `speakAndWait()` ไม่ตัดเสียง AI
* จัดการเสียงพูดสังเคราะห์ผ่าน Promise ที่รออีเวนต์ `utterance.onend` + หน่วงเวลาผ่อนคลายเสียง 650ms อย่างแม่นยำ
* ในด่าน 3 (1669 Dispatch) เมื่อแจ้งข้อมูลครบ 4 ข้อ ระบบจะขึ้นแถบเขียวและรอให้เจ้าหน้าที่ 1669 ยืนยันข้อมูลจนจบประโยค ก่อนก้าวหน้าไปด่าน 4

### 5. ด่านที่ 5A (CPR 110 BPM): นาฬิกา Zero-Drift Clock & หลอดวัดแรงปรบมือ
* AI พูดแนะนำจนจบก่อน ➔ พักระบบ STT ชั่วคราวเพื่อให้เบราว์เซอร์ทุ่มทรัพยากรไมค์ให้กับการจับเสียง ➔ นับถอยหลัง **3... 2... 1... เริ่ม!**
* คำนวณจังหวะ Metronome และวงไฟกะพริบจากความต่างเวลาจริง `(performance.now() - cprStartTime) % 545.4545` ไร้ปัญหาจังหวะหน่วงหรือเลื่อนลอยสะสม
* **หลอดวัดแรงปรบมือสด (Clap Spike Meter):** แสดงกราฟ Transient Spike พุ่งขึ้นตามเสียงปรบมือจริง พร้อมเส้น Threshold (0.08) ช่วยให้เห็นการทำงานของไมค์ทันที
* รองรับทั้งเสียงตบมือ, การคลิกวงกลมกลางจอ และปุ่ม `[Spacebar]`

### 6. ด่านที่ 6: ออกแบบใหม่เป็น "HERO MISSION SCOREBOARD" สไตล์ Esports
* กรอบดีไซน์แบบ Glass-Slate Hero Container อยู่ในพื้นที่ 62% ด้านบน เพื่อให้พื้นที่ด้านล่าง 38% เป็น **Unobstructed Photo Safe Viewport** สำหรับยืนถ่ายรูปหมู่หน้าจอโปรเจกเตอร์โดยไม่บังคะแนน
* **ฝั่งซ้าย:** ตราสัญลักษณ์ Rank S / A+ / A เวกเตอร์ SVG พร้อมวงแหวนความก้าวหน้าแบบ Radial Progress Animation และตัวเลขอัปเดตคะแนนแบบไดนามิก พร้อมป้ายแสดงกลุ่มตัวแทน (`RESCUE SQUAD TEAM XX`)
* **ฝั่งขวา:** แถบคลื่นไฟฟ้าหัวใจจำลองปกติ (ROSC Normal Sinus 88 BPM) พร้อมแถบประเมิน 4 ด้าน (Scene Safety, 1669 Dispatch, CPR Cadence, AED Defibrillation)
* **แถบล่าง:** ข้อมูลงาน Open House 2026 คณะแพทยศาสตร์ มหาวิทยาลัยบูรพา และปุ่ม `[ 🔄 เริ่มรอบถัดไป (Quick Reset - R) ]`

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

## 🚀 วิธีการเปิดใช้งาน (Deployment & Running)

### 1. ใช้งานผ่าน GitHub Pages (แนะนำสูงสุดสำหรับหน้างาน)
เข้าสู่ระบบผ่าน HTTPS:  
👉 **`https://asia16775-ai.github.io/Emergency-Event-Simulator/`**

### 2. ใช้งานแบบ Local บนเครื่อง Mac
```bash
cd heartbeat-rush-sim
npx serve -l 3000
# แล้วเปิด Chrome หรือ Safari ไปที่ http://localhost:3000
```

---

## 🏥 ข้อมูลโครงการ
- **ทีมผู้จัดทำ:** Asia & Heartbeat Rush Crew
- **หน่วยงาน:** คณะแพทยศาสตร์ มหาวิทยาลัยบูรพา (Faculty of Medicine, Burapha University)
- **กิจกรรม:** MDBUU Open House 2026 — ฐาน CPR & First Aid (Heartbeat Rush)
