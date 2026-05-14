# Panduan Lengkap — Urban Driving

**Untuk pemula sepenuhnya.** Ikut step-by-step dari bateri sampai habis race. Tiada pengetahuan terdahulu diperlukan.

---

## 0. Apa itu Urban Driving?

Urban Driving adalah **pemanduan autonomi di trek bandar** (jalan dalam grid dengan persimpangan). Kereta perlu:

1. Ikut lane di **jalan kanan** (right-bias)
2. **Detect papan tanda jalan** (AprilTag 36h11) di setiap simpang
3. **Buat keputusan betul** ikut tanda:
   - ID 0 = Dilarang masuk (jangan masuk jalan ini)
   - ID 1 = Jalan mati (jangan masuk)
   - ID 2 = Masuk kanan (turn kanan di simpang)
   - ID 3 = Masuk kiri (turn kiri di simpang)
   - ID 4 = Terus ke depan (jangan turn)
   - ID 5 = **BERHENTI** (destinasi akhir — STOP)
4. Berhenti di destinasi (tanda STOP, ID 5)

**Markah:**
```
S_AUD = ((100 × (1 − T_total / T_stage)) × (cp / Total_Checkpoints) − P) × Ks
```
- `Ks` = 1.0 (AprilTag) atau 1.3 (Simbol). **Kita guna AprilTag = 1.0**
- `P` = jumlah penalti

**Penalti:**

| Kesalahan | Penalti |
|---|---|
| Tidak berhenti di persimpangan | −10 |
| Buat keputusan salah (turn salah arah) | −25 |
| Tukar lorong tak betul | −20 |

**Strategi**: jangan langgar peraturan signs > kelajuan. Better slow + correct daripada cepat + penalti.

---

## 1. Hardware checklist

Sama macam ARC. Pastikan ada:

| Item | Bilangan | Catatan |
|---|---|---|
| Raspberry Pi 4 (4GB/8GB) | 1 | + microSD ≥32GB |
| Kamera USB FOV ≥120° | 1 | Pasang condong sedikit ke atas supaya nampak papan tanda |
| MPU6050 IMU | 1 | **CRITICAL** untuk yaw integration semasa turn |
| PCA9685 PWM driver | 1 | I²C |
| HC-SR04 ultrasonik | **5** | FRONT, FL, FR, LEFT, RIGHT |
| ESC + motor | 1 set | |
| Servo Ackermann | 1 | |
| LiPo motor + Power bank Pi | masing-masing 1 | |
| **AprilTag printouts** | 0–5 | Tag36h11 family, ID 0–5 (diberi oleh urusetia di venue) |

> **Penting untuk Urban:** Camera mounting kena nampak papan tanda di **tinggi 160mm** (max). Condong kamera **lebih sedikit ke atas** berbanding ARC supaya papan tanda dalam FOV.

---

## 2. Setup software (sekali sahaja)

### 2.1 Flash & install (sama macam ARC)

Ikut `@/home/donkey/fac_acc/GUIDE_ARC.md` §2.1–2.3.

### 2.2 Verify AprilTag library

```bash
python3 -c "import pupil_apriltags; print('AprilTag OK')"
```

Kalau error → `pip install pupil-apriltags`.

### 2.3 Test AprilTag detector

Cetak AprilTag dari [github.com/ayozzet/ACC_signage](https://github.com/ayozzet/ACC_signage). Saiz **8cm × 8cm** ke **10cm × 10cm**.

```bash
cd ~/mycar
python3 apriltag_detector.py /path/to/tag_image.jpg
```

Output patut:
```
Found 1 tags:
  id=5  range=1.23m  bearing=2.3deg  px=85
Annotated image: /tmp/apriltag_debug.jpg
```

Pull `/tmp/apriltag_debug.jpg` ke laptop — verify bounding box di tag.

---

## 3. Hardware wiring

Sama macam ARC. Rujuk `@/home/donkey/fac_acc/GUIDE_ARC.md` §3 untuk lengkap. Ringkasan:

- I²C: MPU6050 + PCA9685 di pin 3 (SDA), 5 (SCL)
- 5× HC-SR04: FRONT(23/24), FL(5/6), FR(12/13), LEFT(17/27), RIGHT(22/25) — semua dengan voltage divider 1kΩ+2kΩ di ECHO
- ESC channel 1, Servo channel 0 di PCA9685

### Camera angle khas Urban

Untuk Urban, papan tanda berada di **paras tinggi 160mm** di tepi trek. Adjust camera:

- Tinggi: 15–20 cm
- **Condong ke bawah hanya ~15°** (lebih flat berbanding ARC)
- Trade-off: kena nampak lane line di lantai DAN papan tanda di tepi. Compromise ~15° biasanya OK.

Test selepas mounting:
```bash
python3 -c "
import cv2
cap = cv2.VideoCapture(0); cap.set(3,320); cap.set(4,240)
import time; time.sleep(1)
ok, img = cap.read()
cv2.imwrite('/tmp/cam_test.jpg', img)
"
```

Verify dalam preview: nampak **lane line di bawah** + **papan tanda di tepi** bila kereta hampiri simpang.

---

## 4. PWM calibration

Sama macam ARC. Rujuk `@/home/donkey/fac_acc/GUIDE_ARC.md` §4. Catat values dan update `manage.py`.

---

## 5. Test komponen (sekali sahaja)

### 5.1 Camera, IMU, US

Sama macam ARC §5.

### 5.2 AprilTag standalone test

**Kritikal untuk Urban.**

```bash
cd ~/mycar
python3 apriltag_detector.py     # webcam mode
```

Pegang AprilTag cetakan di depan kamera. Terminal akan stream detections:
```
[(5, 0.42, 1.2)]   # id=5, range 0.42m, bearing 1.2°
```

**Verify:**
- Detect ID betul (1–5 sesuai cetakan)
- `range` masuk akal (jauh ↔ besar number)
- `bearing` positive bila tag di kiri, negative bila di kanan

Kalau tag tak detect:
- Saiz cetakan terlalu kecil — minimum 8cm × 8cm
- Lighting tak cukup
- Tag bend / kertas kedut — cetak balik di kertas tebal

---

## 6. Hari pertandingan: pre-race (di pit area)

### 6.1 Charging

Sama macam ARC: LiPo, power bank, spare battery.

### 6.2 Urutan power-on

Sama macam ARC §6.2:
1. Power bank Pi ON → tunggu boot
2. SSH, verify `i2cdetect`, `/dev/video0`
3. LiPo motor cangkuk LAST → ESC armed beep
4. Test manual sebelum letak atas trek

### 6.3 Boot system

```bash
cd ~/mycar
python3 manage.py drive
```

Buka GUI: `http://<pi-ip>:8890`

Verify:
- All hardware green
- Loop Hz > 15
- AprilTag debug overlay aktif (di tab kamera, ada green bounding box bila tag ada di frame)

### 6.4 Manual gerakan test

Lihat `@/home/donkey/fac_acc/GUIDE_ARC.md` §6.5.

---

## 7. Di trek: calibration

### 7.1 Perspective transform

Sama macam ARC §7.1. Tampal rectangle 1.2m × 0.5m di trek, capture frame, run `calibrate_perspective.py`, paste values ke `lane_detector_robust.py`.

> **Nota:** Lebar trek Urban = **60cm ± 10%** (berbanding ARC 50cm). Lane detector akan adapt automatic, tapi `cruise_offset_m` boleh diadjust kalau perlu lane positioning lebih tepat.

### 7.2 HSV auto-tune

Sama macam ARC §7.2. Buat di 2–3 lokasi.

### 7.3 AprilTag test live di trek

Sangat penting untuk Urban:

1. Mode = MANUAL
2. Bawa kereta dekat papan tanda **sebenar** di trek (bukan cetakan latihan)
3. Tengok GUI tab kamera — papan tanda patut dapat green bounding box + ID label
4. Test setiap **6 jenis tag** (ID 0–5) yang akan muncul:
   - ID 0: Dilarang masuk
   - ID 1: Jalan mati
   - ID 2: Masuk kanan
   - ID 3: Masuk kiri
   - ID 4: Terus ke depan
   - ID 5: STOP
5. Catat **range typical** bila tag baru detect (untuk reference)

Kalau ada tag tak detect:
- Pergi lebih dekat (range turun)
- Adjust camera angle
- Cetak tag lebih besar (mintak izin urusetia kalau perlu)

### 7.4 Practice approach

Drive kereta manual perlahan ke arah satu tag. Tengok di GUI:
- Berapa jauh tag mula detect? (typical 2.5m max untuk 8cm tag)
- Pada `range ≤ 0.5m`, kereta patut commit ke action (turn left/right/straight/stop)

---

## 8. Sebelum start (di start line)

### 8.1 Final checklist

- [ ] Semua battery charged
- [ ] GUI accessible
- [ ] Hardware semua hijau
- [ ] AprilTag detection berfungsi
- [ ] **IMU calibrated dengan baik** — yaw drift < 5°/min bila kereta diam
- [ ] Lane confidence > 0.7 di start position
- [ ] Mode = MANUAL, ENABLED = OFF

### 8.2 Reset Yaw — WAJIB untuk Urban!

Urban mode guna **yaw integration** untuk detect bila turn selesai (target ~80°). Yaw drift = turn salah.

1. **Park kereta arah lurus** mengikut lane di start line
2. Di GUI, klik **"Reset Yaw"** button
3. Verify `yaw = 0.0°` di telemetry

**Buat ini setiap kali baru start race.**

### 8.3 Switch ke URBAN mode

1. Mode → `URBAN`
2. ENABLED = OFF (tunggu signal)
3. Verify telemetry:
   - State: `CRUISING`
   - Stable sign: null (atau tag tunjuk start zone)

### 8.4 Saat pengadil bagi signal

1. **Klik ENABLE**
2. Tangan dekat E-STOP
3. Wisel pengadil ditiup = race start

---

## 9. Semasa pertandingan

### 9.1 Apa yang awak monitor di GUI

| Telemetry | Maksud |
|---|---|
| `state` | CRUISING → APPROACHING → LANE_KEEP_RIGHT → EXECUTING_TURN → CRUISING |
| `stable_sign` | Nama tag yang stable detect (e.g. "TURN_RIGHT") |
| `committed` | Action yang dah commit (selepas approach) |
| `seen_counts` | Sign detection counter (debug) |
| `Yaw (°)` | Untuk debug turn execution |

**Flow normal:**
1. State = `CRUISING`: ikut lane kanan, base speed
2. Approach simpang: state = `APPROACHING`, slow down
3. Stable sign confirmed: state = `LANE_KEEP_RIGHT`, bias ke kanan
4. Masuk simpang: state = `EXECUTING_TURN`, dead-reckon yaw
5. Yaw cleared 80°: state = `CRUISING` semula

### 9.2 Sebelum sampai destinasi (STOP sign)

Bila detect ID 5 stable:
- State = `STOPPING`: motor ramp down
- State = `STOPPED`: kereta berhenti penuh

**Inilah destinasi. Race habis.**

### 9.3 Bila tekan E-STOP

- Kereta turn ke arah salah secara jelas → E-STOP dan reset
- Stuck masa turn (yaw tak cukup) → E-STOP, manual rectify, restart segment

### 9.4 Penalti automatic vs strategi

| Situasi | Apa berlaku |
|---|---|
| Lepas tag dengan turn betul | Tiada penalti |
| Lepas tag tanpa turn (ignore) | −25 (decision salah) |
| Tak stop di persimpangan | −10 |
| Tukar lorong tak betul | −20 |

**Strategi**: better slow + 100% comply daripada cepat + 1 penalti −25.

---

## 10. Selepas run

Sama macam ARC §10. Adjust untuk run kedua.

---

## 11. Mode-specific tuning (untuk menang Urban)

### 11.1 Sign detection stability

Dalam `urban_controller.py`:
```python
N_STABLE_FRAMES = 3   # default
```

| Symptom | Adjust |
|---|---|
| False positive (kereta react ke nothing) | Naikkan ke 5 |
| Approach lambat (tag dah dekat tapi tak react) | Turunkan ke 2 |

### 11.2 Approach range

```python
APPROACH_RANGE_M = 0.6   # bila tag dekat ni, mula approach
```

- Naikkan ke 0.8 = react lebih awal (lebih banyak masa untuk slow down + position)
- Turunkan ke 0.4 = react lebih lewat (pantas tapi risk miss)

### 11.3 Turn target yaw

```python
TURN_TARGET_YAW_RAD = radians(80)   # default
```

| Symptom | Adjust |
|---|---|
| Turn pendek sangat (tak masuk lane baru penuh) | Naikkan ke 85–90 |
| Turn over-rotate (overshoot lane baru) | Turunkan ke 70–75 |

### 11.4 Speed di Urban

Dalam `manage.py`:
```python
urban_ctrl = UrbanController(
    base_speed=0.28,   # lebih perlahan dari ARC sebab tight space
    wheelbase_m=0.30,
)
```

Mula `base_speed=0.25`, naikkan +0.03 setiap practice run.

### 11.5 Right-lane bias

```python
cruise_offset_m = 0.10   # bias ke kanan dalam lane (10cm)
```

Rule 5.2.2.5: di checkpoint, kereta mesti di lorong kanan. Bias ini pastikan kereta sentiasa di kanan. Naikkan ke 0.15 kalau lane lebar.

### 11.6 Wrong-lane recovery

```python
recovery_offset_m = 0.30   # bila wrong-lane detected, paksa ke kanan
```

---

## 12. Strategi khas Urban

### 12.1 Hafal layout trek

Sebelum race, gerakkan kereta manual seluruh trek dan **catat order tag**:
```
Tag 4 (straight) → simpang ke kiri → Tag 2 (right) → ... → Tag 5 (STOP)
```

Cetak peta route. Visualize sebelum race.

### 12.2 Practice setiap turn

Mode MANUAL drive ke setiap simpang, biar URBAN mode handle turn. Verify:
- Stable detect berlaku 1–2m sebelum simpang
- Turn execution clean
- Lane keeping selepas turn

Kalau ada simpang yang fail consistently → adjust `APPROACH_RANGE_M` atau `TURN_TARGET_YAW_RAD` untuk turn tersebut. (Walaupun ada satu nilai global — workaround: tune supaya worst-case OK.)

### 12.3 Time budget

Hitung anggaran masa untuk lap:
```
T_estimate = Distance / Speed + (N_turns × 2s for turn execution)
```

Pastikan `T_estimate < T_stage × 0.8` (margin keselamatan).

### 12.4 ID Reference

Cetak dan letak dekat laptop:

| ID | Tag | Action |
|---|---|---|
| 0 | Dilarang masuk | Skip jalan ini |
| 1 | Jalan mati | Skip jalan ini |
| 2 | Masuk kanan | Turn kanan |
| 3 | Masuk kiri | Turn kiri |
| 4 | Terus depan | Continue straight |
| 5 | STOP | Final destination — berhenti |

---

## 13. Troubleshooting

### Tag tak detect langsung
- Run `python3 apriltag_detector.py` standalone untuk isolate masalah
- Camera resolution? 320×240 detect range ~2.5m max
- Tag cetakan: matte tak glossy, tepi tajam
- `quad_decimate` dalam `manage.py` terlalu tinggi: turunkan dari 2.0 ke 1.5

### Tag detect tapi action salah
- Verify ID mapping di dalam `urban_controller.py` (rujuk Jadual 6 rules)
- Pastikan AprilTag family = `tag36h11`

### Kereta turn ke arah salah
- IMU mounting upside-down? Edit `imu_part.py`: `MOUNTING_FLIP_Z = True`
- Verify: putar kereta kiri manual, telemetry `yaw` patut **increase**

### Turn tak complete (kereta stuck mid-turn)
- `TURN_TARGET_YAW_RAD` terlalu tinggi → turunkan ke 70
- Lane confidence drop semasa turn (kamera tak nampak lane) — OK, dead-reckon ambil alih
- Speed too low semasa turn → `TURN_THROTTLE` dalam `urban_controller.py`

### Turn over-rotate
- `TURN_TARGET_YAW_RAD` terlalu rendah → naikkan ke 85
- IMU drift accumulated → reset yaw lebih kerap

### Lane lost selepas turn
- HSV calibration di lokasi straight, tak match lighting di simpang
- Auto-tune di **lokasi simpang** juga, bukan straight sahaja

### Kereta stop di simpang tapi tak teruskan
- State stuck di `STOPPING`? Cuba reset yaw, restart
- Kalau detect ID 5 tapi tak patut (false positive) → naikkan `N_STABLE_FRAMES` ke 5

### Sebelum start: yaw drift teruk
- IMU calibration salah. Pastikan kereta BENAR-BENAR DIAM masa boot.
- Reset yaw selepas boot complete (jangan masa Pi baru on, IMU belum stable)

---

## 14. Race day checklist (print this!)

### 1 jam sebelum
- [ ] Semua battery charged
- [ ] AprilTag cetakan ada (backup kalau urusetia rosak)
- [ ] Laptop + SSH ready
- [ ] Hafal layout trek (kalau available)

### 30 minit sebelum di trek
- [ ] Power-on Pi
- [ ] Verify GUI all green
- [ ] Manual test gerakan basic

### 15 minit sebelum (di trek)
- [ ] Perspective calibration
- [ ] HSV auto-tune di 2–3 lokasi
- [ ] AprilTag detection test live di tag sebenar
- [ ] Catat range typical detect
- [ ] Practice 1 lap di Mode URBAN slow speed
- [ ] **Reset Yaw** di start line

### Saat race
- [ ] Park lurus di start
- [ ] **Reset Yaw** sekali lagi
- [ ] Mode = URBAN
- [ ] ENABLED = OFF
- [ ] Tunggu signal
- [ ] ENABLE → tangan stay dekat E-STOP
- [ ] Monitor `state` transitions

### Selepas
- [ ] DISABLE bila stop di destinasi (ID 5)
- [ ] Tangkap kereta, cabut LiPo
- [ ] Catat checkpoint cleared + penalti
- [ ] Adjust untuk run kedua

---

## Lampiran A — Quick commands

```bash
# Run vehicle
cd ~/mycar && python3 manage.py drive

# Test AprilTag standalone
python3 apriltag_detector.py
python3 apriltag_detector.py path/to/tag.jpg

# Test sensors
python3 ultrasonic_sensors.py
python3 imu_part.py

# Unit tests state machine
python3 test_urban_controller.py

# Reset yaw via curl (kalau GUI tak access)
curl -X POST http://localhost:8890/reset_yaw
```

## Lampiran B — State machine reference

```
CRUISING ──[stable sign + range<0.6m]──► APPROACHING
APPROACHING ──[action committed]──► LANE_KEEP_RIGHT (turn)
                                 └──► CRUISING (straight/skip)
LANE_KEEP_RIGHT ──[passed tag]──► EXECUTING_TURN
EXECUTING_TURN ──[yaw>80°]──► CRUISING
CRUISING ──[ID 5 detected]──► STOPPING
STOPPING ──[motor ramped]──► STOPPED (END)
```

**Goal Urban**: navigate setiap simpang dengan keputusan betul, sampai destinasi STOP, jangan kena penalti.

**Semoga berjaya!**
