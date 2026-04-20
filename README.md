# Scam Mule Account Detection
Scam Mule Account Detection คือกระบวนการวิเคราะห์และตรวจจับบัญชีที่ถูกใช้เป็น “บัญชีม้า” (mule accounts) ซึ่งเป็นตัวกลางในการรับและโอนเงินที่เกี่ยวข้องกับธุรกรรมหลอกลวง (scam)

ในรูปแบบของ Scam-induced transactions ผู้เสียหายจะถูกหลอกให้โอนเงินด้วยความสมัครใจไปยังบัญชีปลายทาง ทำให้ธุรกรรมมีลักษณะคล้ายธุรกรรมปกติ และยากต่อการตรวจจับด้วยระบบ fraud detection แบบดั้งเดิมที่พิจารณาเพียงข้อมูลระดับธุรกรรม (transaction-level)

พฤติกรรมสำคัญของ mule accounts มักเกี่ยวข้องกับ “การไหลของเงิน” (money flow behavior) เช่น: 1.การรับเงินจากหลายบัญชีในช่วงเวลาสั้น (high fan-in) 2.การโอนเงินถี่ผิดปกติ (high transaction velocity) 3.การมีธุรกรรมที่เบี่ยงเบนจากพฤติกรรมปกติ (abnormal deviation) เป็นต้น

---

## 1. Introduction & Background

ในปัจจุบัน Online Scam มีแนวโน้มเพิ่มขึ้นอย่างต่อเนื่อง โดยเฉพาะในรูปแบบของ Scam-induced transactions ซึ่งผู้เสียหายถูกหลอกให้โอนเงินไปยังบัญชีปลายทางด้วยความสมัครใจ ส่งผลให้ธุรกรรมเหล่านี้มีลักษณะคล้ายธุรกรรมปกติ และยากต่อการตรวจจับด้วยระบบ fraud detection แบบดั้งเดิม

ปัญหาสำคัญคือระบบตรวจจับแบบ traditional มักพิจารณาเฉพาะคุณสมบัติของธุรกรรม (transaction-level properties) เช่น จำนวนเงิน หรือความถี่ในการทำธุรกรรม ซึ่งไม่สามารถสะท้อน “พฤติกรรมการไหลของเงิน” (money flow behavior) ที่เกิดขึ้นระหว่างหลายบัญชีได้อย่างมีประสิทธิภาพ

บัญชีม้า (mule accounts) มีบทบาทสำคัญในกระบวนการ fraud โดยทำหน้าที่เป็นตัวกลางในการรับและกระจายเงินไปยังบัญชีอื่น พฤติกรรมของบัญชีประเภทนี้มักมีลักษณะเฉพาะ เช่น:

การรับเงินจากหลายบัญชีในช่วงเวลาสั้น (high fan-in)
การทำธุรกรรมถี่ผิดปกติ (high transaction velocity)
การมีรูปแบบธุรกรรมที่เบี่ยงเบนจากพฤติกรรมปกติ (abnormal deviation)
การกลับมาใช้งานของบัญชีที่เคย inactive (dormant-to-active behavior)

ดังนั้น โครงการนี้จึงมุ่งเน้นการใช้แนวคิด Behavioral Analytics เพื่อวิเคราะห์ pattern ของธุรกรรมและพฤติกรรมของบัญชี แทนการพิจารณาเฉพาะข้อมูลระดับธุรกรรม เพื่อเพิ่มประสิทธิภาพในการตรวจจับ mule accounts และธุรกรรมที่มีความเสี่ยงสูง

การวิเคราะห์ดังกล่าวมีเป้าหมายเพื่อสนับสนุนการพัฒนาระบบ fraud detection แบบ real-time ที่สามารถลดความเสียหายทางการเงิน และเพิ่มความสามารถในการป้องกันการทุจริตในระบบการเงินได้อย่างมีประสิทธิภาพ

---

## 2. Objectives

วัตถุประสงค์ของโครงการนี้คือการพัฒนากรอบการวิเคราะห์เพื่อระบุและตรวจจับ mule accounts จากพฤติกรรมของธุรกรรม (behavioral patterns) แทนการพิจารณาเฉพาะข้อมูลระดับธุรกรรม

### โดยมีเป้าหมายหลักดังนี้:

ลดความเสียหายจาก Scam-induced transactions ลงอย่างน้อย 20% ภายใน 6 เดือน
วิเคราะห์พฤติกรรมของบัญชีที่มีความเสี่ยงผ่าน Behavioral KPIs ได้แก่
Transaction Velocity
Network Pattern (Fan-in)
Deviation from Normal Behavior
ระบุ pattern ของ mule accounts เช่น
การรับเงินจากหลายบัญชีในช่วงเวลาสั้น
การทำธุรกรรมถี่ผิดปกติ
การกลับมาใช้งานของบัญชีที่เคย inactive
พัฒนาวิธีการตรวจจับธุรกรรมที่มีความเสี่ยงสูงแบบ near real-time
สนับสนุนการสร้างระบบ risk scoring และ alert mechanism เพื่อใช้ในการป้องกัน fraud ในเชิงปฏิบัติการ

โครงการนี้มุ่งเน้นการเชื่อมโยงระหว่างข้อมูลเชิงพฤติกรรม (behavioral data) และการตัดสินใจเชิงธุรกิจ เพื่อให้สามารถนำผลลัพธ์ไปใช้งานได้จริงในระบบ fraud detection

---

## 3. Key Questions

* **Network Level:** Accounts ที่ได้รับเงินจากหลาย sender ภายในระยะเวลาสั้นๆ (เช่น 1 ชั่วโมง) มี fraud rate สูงกว่าบัญชีทั่วไปหรือไม่ และมี threshold เท่าใดที่สามารถใช้เป็นสัญญาณเตือน
 
* **Account Level:** บัญชีที่มีลักษณะแบบ dormant (ไม่มีการใช้งานในช่วงระยะเวลาหนึ่ง) แล้วกลับมา active มี transaction pattern ที่ผิดปกติ เช่น sudden spike (>3x ของค่าเฉลี่ยภายใน 1 ชั่วโมง) ในจำนวนเงินหรือความถี่ มากกว่าบัญชีทั่วไปหรือไม่
 
* **Behavior Level:** ธุรกรรมที่มี deviation จากพฤติกรรมปกติของลูกค้า (เช่น amount สูงกว่าค่าเฉลี่ย X เท่า) มีความสัมพันธ์เชื่อมโยงกับการเกิด fraud มากน้อยเพียงใด

---

## 4. Dataset & Features

โครงการนี้ใช้ข้อมูลธุรกรรมทางการเงิน (transaction-level data) เพื่อวิเคราะห์พฤติกรรมของบัญชีและตรวจจับ mule accounts โดย dataset ประกอบด้วยตัวแปรสำคัญดังนี้:

### 🔹 Transaction Data

* **sender_account**: บัญชีผู้โอน
* **receiver_account**: บัญชีผู้รับ
* **transaction_amount**: จำนวนเงิน
* **transaction_timestamp**: เวลาในการทำธุรกรรม

### 🔹 Behavioral Features (Engineered Features)

เพื่อสะท้อนพฤติกรรมของบัญชี ได้มีการสร้างตัวแปรเพิ่มเติม (feature engineering) ได้แก่:

* **txn_count_last_1hr (Transaction Velocity)**
  จำนวนธุรกรรมที่เกิดขึ้นภายใน 1 ชั่วโมง
  → ใช้ตรวจจับบัญชีที่มีความถี่ผิดปกติ

* **txn_count_per_sender (Network / Fan-in)**
  จำนวนผู้โอน (unique senders) ต่อบัญชีปลายทาง
  → ใช้ตรวจจับการรับเงินจากหลายแหล่ง

* **is_abnormal_amount (Deviation Indicator)**
  ระบุว่าจำนวนเงินของธุรกรรมเบี่ยงเบนจากพฤติกรรมปกติหรือไม่
  → ใช้ตรวจจับ anomaly

* **dormant_status (Derived Feature)**
  สถานะของบัญชีที่ไม่มีการเคลื่อนไหวในช่วงระยะเวลาหนึ่ง (inactive >30 วัน)
  → ใช้วิเคราะห์พฤติกรรม dormant-to-active

### 🔹 Target Variable

* **is_mule_transaction**
  ตัวแปร label ที่ระบุว่าธุรกรรมนั้นเกี่ยวข้องกับ mule account หรือไม่ (1 = fraud, 0 = normal)

---

## 📈 Key Metrics (KPIs)

จาก feature ข้างต้น ได้กำหนดตัวชี้วัดสำคัญ (Key Performance Indicators) สำหรับการตรวจจับ fraud ดังนี้:

* **Transaction Velocity**
  วัดความถี่ของธุรกรรมในช่วงเวลาสั้น

* **Network Pattern (Fan-in)**
  วัดจำนวน sender ต่อบัญชีปลายทาง

* **Deviation Score**
  วัดระดับความผิดปกติของจำนวนเงิน

* **Dormant-to-Active Behavior**
  วัดการกลับมาใช้งานของบัญชีที่เคย inactive

KPI เหล่านี้ถูกนำไปใช้เป็นพื้นฐานในการวิเคราะห์ (Section 5) และสร้าง insight (Section 6) เพื่อระบุพฤติกรรมของ mule accounts อย่างเป็นระบบ


📌 **(ยังไม่ต้องใส่รูป — optional)**
👉 ถ้ามี ER diagram หรือ schema → ใส่ได้ตรงนี้

---

## ⚙️ Methodology

### 🔹 Data Preparation

* Clean และจัดรูปแบบข้อมูล
* สร้าง features ได้แก่ velocity, deviation และ dormant status

---

### 🔹 Behavioral Analysis

* วิเคราะห์ **velocity** → ธุรกรรมถี่ผิดปกติ
* วิเคราะห์ **network (fan-in)** → รับเงินหลาย sender
* วิเคราะห์ **deviation** → amount ผิดปกติ
* วิเคราะห์ **dormant behavior** → inactive → spike

---

### 🔹 Risk Identification

* กำหนด threshold เช่น

  * velocity > X
  * deviation > 3x
* รวมหลายปัจจัยเพื่อระบุบัญชีเสี่ยง

📌 **(X = รอเพื่อน / Tableau)**

---

## 🔍 Findings & Insights (Draft)

### 🔹 Network Pattern

Accounts ที่มีจำนวน sender สูงในช่วงเวลาสั้น
มี fraud rate = **[X%]** ซึ่งสูงกว่าบัญชีทั่วไป **[X เท่า]**

สะท้อนพฤติกรรม mule accounts ที่รับเงินจากหลายแหล่งก่อนโอนออก

📌 **ใส่ภาพตรงนี้**

```markdown
![Network Pattern](images/network_pattern.png)
```

👉 รูปที่ควรใช้:

* bar chart (sender count)
* network graph

📌 **รอเพื่อน: ตัวเลข + รูป**

---

### 🔹 Dormant Account

บัญชีที่ inactive (>30 วัน) ก่อนกลับมา active
มีแนวโน้มเกี่ยวข้องกับ fraud สูง **[X%]**

สะท้อนการนำบัญชีเก่ากลับมาใช้

📌 **ใส่ภาพตรงนี้**

```markdown
![Dormant Behavior](images/dormant.png)
```

👉 รูป:

* before/after activity

📌 **รอเพื่อน: ตัวเลข + รูป**

---

### 🔹 Dormant-to-Active Spike

บัญชีที่กลับมา active และมี spike (>3x ภายใน 1 ชั่วโมง)
มีความเสี่ยงสูง **[X เท่า]**

สะท้อนพฤติกรรม hit-and-run

📌 **ใส่ภาพตรงนี้**

```markdown
![Spike Pattern](images/spike.png)
```

📌 **รอเพื่อน: ตัวเลข + รูป**

---

### 🔹 Deviation Behavior

ธุรกรรมที่มี abnormal amount (>3 เท่า)
มี fraud rate = **[X%]**

แสดงว่าการเบี่ยงเบนของจำนวนเงินเป็นสัญญาณสำคัญ

📌 **ใส่ภาพตรงนี้**

```markdown
![Deviation](images/deviation.png)
```

📌 **รอเพื่อน: ตัวเลข + รูป**

---

## 📈 Dashboard

📌 **ใส่ภาพ dashboard หลักตรงนี้**

```markdown
![Dashboard](images/dashboard_overview.png)
```

👉 ควรเป็น:

* dashboard รวม
* มี filter / highlight

📌 **รอเพื่อน: รูป dashboard**

---

## 🚀 Recommendation

### 🔹 Real-time Risk Scoring

ใช้ velocity + deviation + network
เพื่อสร้าง risk score และ flag ธุรกรรมต้องสงสัย

---

### 🔹 Rule-based Alert

ตั้ง threshold เช่น:

* velocity สูง
* deviation สูง

เพื่อ trigger alert

---

### 🔹 Fraud Prevention

* แจ้งเตือนลูกค้า
* ระงับธุรกรรม

เพื่อลดความเสียหาย

---

## 👥 Contributors

* Member 1: นายปัณณธร สุวรรณาศรัย รหัสนิสิต 66102010175
* Member 2: นายพลวัต พงศ์ทิพย์พนัส รหัสนิสิต 66102010249
* Member 3: นายพลวิชญ์ วนิดา รหัสนิสิต 66102010584

---

## 📌 Notes (สำคัญ)

* `[X]` = ใส่ตัวเลขจริงหลังจาก Tableau เสร็จ
* `/images` = สร้าง folder สำหรับรูป
* ภาพควรมีอย่างน้อย 4–5 รูป
