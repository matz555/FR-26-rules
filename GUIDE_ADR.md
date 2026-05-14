# Panduan Lengkap — Autonomous Duel Race (ADR)

**Untuk pemula sepenuhnya.** Ikut step-by-step dari bateri sampai habis race. Tiada pengetahuan terdahulu diperlukan.

---

## 0. Apa itu ADR?

ADR adalah **perlumbaan kepala-ke-kepala (2 kereta serentak)** di trek bujur. Format **kalah mati** (knockout). Kereta perlu:

1. Bermula serentak dengan kereta lawan (sebelah-menyebelah)
2. Ikut lane (lorong assigned: KIRI atau KANAN — undian)
3. **Detect kereta lawan** di depan dan **overtake** ikut peluang
4. **Return** ke lane asal selepas overtake
5. Elak kon yang diletak rawak (langgar = penalti 1 saat)
6. Sampai garisan penamat **dahulu** = menang round

**Cara menang:**
- Lepasi garisan penamat **terdahulu**, atau
- Markah tertinggi + masa terpantas (kalau tied)

**Strategi**: lane-following stabil + overtake dengan confident bila peluang ada + jangan crash.

---

## 1. Hardware checklist

Sama macam ARC. Pastikan:

| Item | Bilangan | Catatan |
|---|---|---|
| Raspberry Pi 4 (4GB/8GB) | 1 | + microSD ≥32GB |
| Kamera USB FOV ≥120° | 1 | |
| MPU6050 IMU | 1 | |
| PCA9685 PWM driver | 1 | |
| HC-SR04 ultrasonik | **5** | FRONT, FL, FR, LEFT, RIGHT — **CRITICAL untuk ADR** |
| Rintangan 1kΩ, 2kΩ | 5+5 | Voltage divider ECHO |
| ESC + motor brushless | 1 set | |
| Servo Ackermann | 1 | |
| LiPo motor + Power bank Pi | masing-masing 1 | |
| **Spare kereta** | 1 (optional) | Rule 5.7.1: dibenarkan register 2 kereta |

> **Khas untuk ADR:** Ultrasonik **CRITICAL**. Front+FL+FR untuk detect kereta lawan, LEFT+RIGHT untuk clearance semasa overtake/return.

---

## 2. Setup software (sekali sahaja)

Sama macam ARC §2. Rujuk `@/home/donkey/fac_acc/GUIDE_ARC.md` §2.1–2.3.

---

## 3. Hardware wiring

Sama macam ARC §3. Wiring 5× ultrasonik adalah **wajib untuk ADR** (tidak optional macam ARC).

Rujuk `@/home/donkey/fac_acc/GUIDE_ARC.md` §3 untuk:
- Power architecture (Pi separate dari motor!)
- I²C wiring
- Ultrasonik pin assignment + voltage divider
- PCA9685 ke ESC + servo
- Camera mounting

### 3.1 Camera khas ADR

- Tinggi: ~15–20 cm
- Condong ke bawah ~25° (kerana lawan kereta paling rendah di lantai)
- Pastikan **FOV penuh nampak depan** — sebab US bantu detect lawan tapi camera primary untuk lane following

---

## 4. PWM calibration

Sama macam ARC §4. Rujuk `@/home/donkey/fac_acc/GUIDE_ARC.md` §4.

---

## 5. Test komponen (sekali sahaja sebelum first run)

### 5.1 Camera, IMU

Sama macam ARC §5.

### 5.2 Ultrasonik — kena verify SEMUA 5 sensor

```bash
cd ~/mycar
python3 -c "
from ultrasonic_sensors import UltrasonicSensors
import time, threading
us = UltrasonicSensors()
threading.Thread(target=us.update, daemon=True).start()
time.sleep(0.5)
for _ in range(30):
    f, fl, fr, l, r = us.run_threaded()
    print(f'F={f}  FL={fl}  FR={fr}  L={l}  R={r}')
    time.sleep(0.3)
us.shutdown()
"
```

**Test setiap sensor satu-persatu** dengan tangan:
- Tangan di depan FRONT → `F` turun ke ~0.1–0.3
- Tangan serong kiri-depan → `FL` turun
- Tangan serong kanan-depan → `FR` turun
- Tangan tepat di kiri → `L` turun
- Tangan tepat di kanan → `R` turun

Kalau ada sensor yang tak respond → check wiring, voltage divider.

---

## 6. Hari pertandingan: pre-race (di pit area)

### 6.1 Charging

Sama macam ARC §6.1.

### 6.2 Urutan power-on

Sama macam ARC §6.2.

### 6.3 Boot system

```bash
cd ~/mycar
python3 manage.py drive
```

GUI: `http://<pi-ip>:8890`

Verify:
- All hardware green
- **Ultrasonic = 5/5** (penting untuk ADR!)
- Loop Hz > 15

### 6.4 Pilih lane assignment

**Ini step KHAS ADR.** Sebelum race, urusetia akan beritahu lorong awak (KIRI atau KANAN) via undian.

Edit `manage.py`, cari ADRController:

```python
adr_ctrl = ADRController(
    base_speed=0.32,
    wheelbase_m=0.30,
    lane_assignment='RIGHT',   # ← tukar 'LEFT' atau 'RIGHT' ikut undian
)
```

**Atau** via GUI tuning (live update kalau ada button "Lane Assignment"):
- Lorong kiri → `lane_assignment = 'LEFT'`
- Lorong kanan → `lane_assignment = 'RIGHT'`

Logik:
- `'RIGHT'`: kereta start di lorong kanan, overtake **ke kiri**, return **ke kanan**
- `'LEFT'`: start lorong kiri, overtake **ke kanan**, return **ke kiri**

### 6.5 Manual gerakan test

Sama macam ARC §6.5.

---

## 7. Di trek: calibration

### 7.1 Perspective transform

Sama macam ARC §7.1.

### 7.2 HSV auto-tune

Sama macam ARC §7.2. **Tambahan**: tune juga untuk warna kereta lawan kalau visual detection diperlukan (biasanya US dah cukup untuk ADR).

### 7.3 Test detection kereta lawan (US-based)

**Critical untuk ADR.**

1. Letak kereta lain (atau objek besar saiz kereta) ~1.5m di depan
2. Drive kereta awak slow ke arahnya (mode MANUAL)
3. Monitor GUI panel "US Visualization":
   - FRONT sensor ray turun bila approach (bacaan 1.5m → 1.0m → 0.5m...)
   - Pada `~1.0m`, GUI telemetry tunjuk `Opponent: ⚠ DETECTED 1.0m`
4. Verify detection consistent — tak flicker on/off

Kalau tak detect:
- Sensor mounting too high (US ray pass over the car) — turunkan sensor
- Tweak `opponent_max_range_m` dalam `adr_controller.py`

### 7.4 Practice overtake (di trek practice)

Letak kereta target diam di lane awak. Switch mode ADR, ENABLE.

**Expected behaviour:**
1. State `CRUISING`: ikut lane normal
2. Approach kereta lawan: state → `OPPONENT_DETECTED`
3. Confirmed stable: state → `OVERTAKING`, kereta **swing keluar** ke lane bersebelahan
4. Pass kereta lawan: state → `RETURNING`
5. Selepas hold time, state → `CRUISING` semula

Adjust kalau:
- Overtake terlalu cepat trigger (false positive): naikkan `opponent_stable_frames`
- Overtake terlalu lambat: turunkan `opponent_stable_frames`

---

## 8. Sebelum start (di start line)

### 8.1 Final checklist

- [ ] Battery semua charged
- [ ] GUI accessible
- [ ] Hardware semua hijau (especially Ultrasonic 5/5!)
- [ ] `lane_assignment` dah set betul ikut undian
- [ ] Loop Hz > 18
- [ ] Practice run dah confirm overtake function
- [ ] Mode = MANUAL, ENABLED = OFF

### 8.2 Reset Yaw

Walaupun ADR tak guna yaw integration sebanyak Urban, tetap reset untuk safety:

1. Park kereta lurus di start line, ikut arah lane
2. Klik **"Reset Yaw"** di GUI
3. Verify `yaw = 0.0°`

### 8.3 Park di start position

1. Letak kereta di **lorong assigned** (KIRI atau KANAN ikut undian)
2. Bumper depan di garisan mula
3. Hadap arah trek
4. Verify kamera tengok lane dengan baik (confidence > 0.7)

### 8.4 Switch ke ADR mode

1. Mode → `ADR`
2. ENABLED = OFF
3. Verify telemetry:
   - State: `CRUISING` (atau `STOP`)
   - throttle/steering output = 0

### 8.5 Wisel start

1. **Tunggu wisel** — jangan ENABLE awal
2. Saat wisel:
   - **Klik ENABLE secepat mungkin** (latency 0.1s pun penting)
   - Tangan stay dekat E-STOP

---

## 9. Semasa pertandingan

### 9.1 Apa yang awak monitor di GUI

| Telemetry | Maksud |
|---|---|
| `state` | CRUISING → OPPONENT_DETECTED → OVERTAKING → RETURNING → CRUISING |
| `Opponent` | "clear" atau "⚠ DETECTED 1.2m" |
| `US front/FL/FR` | Distance kereta lawan |
| `US left/right` | Clearance untuk overtake/return |
| `Forward min` | Closest forward obstacle |

**Flow normal overtake:**
```
1. CRUISING (lane kanan, base_speed)
   ↓ detect kereta lawan US front < 1.5m
2. OPPONENT_DETECTED (slow down, prep overtake)
   ↓ US LEFT clear > overtake_clear_m (i.e. 0.6m)
3. OVERTAKING (swing ke kiri, lane swap)
   ↓ pass kereta lawan, US RIGHT clear
4. RETURNING (swing balik ke kanan)
   ↓ hold time complete (0.8s)
5. CRUISING (sambung race)
```

### 9.2 Apa berlaku kalau lawan blok

Kalau US LEFT (overtake side) **tak clear**, state akan stay di `OPPONENT_DETECTED`:
- Kereta slow down di belakang lawan
- Tunggu peluang
- Bila clear, overtake

### 9.3 Bila tekan E-STOP

**Tekan SEGERA** kalau:
- Kereta menuju ke kereta lawan (frontal crash imminent)
- Stuck di overtake (separuh lane berhenti)
- Keluar trek dengan teruk
- Crash dah berlaku — biarkan pengadil handle

Rules 5.5: kalau crash, kereta dikembalikan satu kaki ke belakang dan perlawanan diteruskan. Awak boleh re-ENABLE selepas kereta di-reset.

### 9.4 Kon penalty

Rules 5.4: langgar kon = **+1 saat penalti**. ADR controller akan elakkan kon (sama macam ARC), tapi kadang impossible kalau lawan force awak ke arah kon.

**Strategi**: jangan over-prioritize kon avoidance sampai sacrifice posisi. 1 saat penalti < kalah race.

---

## 10. Selepas run

1. **DISABLE segera** bila kereta lepas garisan tamat
2. Tangkap kereta, cabut LiPo
3. Catat:
   - Menang / kalah round?
   - Masa
   - Kon yang langgar (penalti)
4. Tukar battery, charge
5. Adjust strategi (lihat §11)

**Kalau menang, awak teruskan ke round seterusnya (kalah mati).**

---

## 11. Mode-specific tuning (untuk menang ADR)

### 11.1 Lane assignment (paling penting!)

```python
lane_assignment='RIGHT'    # 'RIGHT' atau 'LEFT'
```

**WAJIB ikut undian.** Salah set = race start di lorong salah = disqualified.

### 11.2 Opponent detection range

Dalam `adr_controller.py`:
```python
opponent_min_range_m=0.3   # ignore lebih dekat dari ni (mungkin debris)
opponent_max_range_m=1.5   # detect dalam range ni
```

| Symptom | Adjust |
|---|---|
| Tak detect lawan dari jauh | Naikkan `max_range` ke 2.0 |
| False positive (detect lantai/dinding) | Turunkan `max_range` ke 1.2 |

### 11.3 Stable frames (anti-false-positive)

```python
opponent_stable_frames=2    # default lower untuk US (vs 8 untuk LiDAR)
```

- Naikkan ke 4 → less false positive tapi slower reaction
- Turunkan ke 1 → super reactive tapi might trigger pada noise

### 11.4 Overtake clearance

```python
overtake_clear_m=0.6   # US side > ni → safe to swing
```

- Naikkan ke 0.8 → conservative (lebih selamat)
- Turunkan ke 0.4 → agresif (lebih cepat overtake tapi risk crash)

### 11.5 Overtake offset (berapa jauh keluar lane)

```python
overtake_offset_m=0.30   # 30cm dari center lane
```

Lane width = 590mm minimum. Overtake offset 30cm = hampir penuh ke lane bersebelahan.

### 11.6 Return hold time

```python
return_hold_s=0.8   # tunggu sebelum balik
```

Naikkan = pastikan dah jauh dari lawan. Turunkan = balik cepat = save time.

### 11.7 Speed

```python
adr_ctrl = ADRController(
    base_speed=0.32,    # mula sini
    max_speed=0.55,     # cap
)
```

ADR speed boleh sikit lebih tinggi dari ARC sebab tiada cone-following overhead, tapi need balance dengan reaction time untuk overtake.

---

## 12. Strategi khas ADR

### 12.1 Start latency

Saat wisel → ENABLE → first move = ~0.2 saat latency. Latih klik ENABLE **secepat mungkin**.

### 12.2 Defensive vs Aggressive

| Style | Setting |
|---|---|
| **Defensive** (pastikan habis race tanpa crash) | `opponent_stable_frames=4`, `overtake_clear_m=0.8`, `base_speed=0.30` |
| **Balanced** | `opponent_stable_frames=2`, `overtake_clear_m=0.6`, `base_speed=0.35` |
| **Aggressive** (lebih laju, more risk) | `opponent_stable_frames=1`, `overtake_clear_m=0.4`, `base_speed=0.40` |

Sesuaikan ikut lawan. Defensive vs lawan yang dominant. Aggressive vs lawan yang slow.

### 12.3 Track awareness

Scout kereta lawan sebelum race:
- Speed dia? Kalau lambat → awak boleh balance/aggressive
- Style overtake dia? Kalau aggressive → defensive

### 12.4 Cone awareness

Walaupun ADR ada US untuk obstacle, **kon tetap perlu detect via camera** (warna oren). Pastikan cone HSV tuning di GUI dah betul (sama macam ARC).

### 12.5 Spare kereta

Kalau ada budget, register **2 kereta**. Rule 5.7.1: spare boleh substitute kalau kereta utama rosak (tapi tak boleh reprogram).

---

## 13. Troubleshooting

### Tak detect lawan langsung
- Run `python3 ultrasonic_sensors.py` standalone — semua 5 sensor reply?
- US mounting terlalu tinggi (over lawan) — turunkan ke ~5–10 cm dari lantai
- `opponent_max_range_m` terlalu rendah

### False positive (detect ghost opponent)
- Naikkan `opponent_stable_frames` ke 4
- US echo dari lantai? Naikkan mounting sedikit (5°)
- Tweak `opponent_min_range_m` ke 0.4

### Overtake fail (kereta swing tapi balik tanpa lepas)
- `overtake_max_s` terlalu pendek — biar overtake fully complete
- Lawan terlalu cepat → ADR tak boleh catch up. Naikkan `base_speed`.

### Overtake crash dengan lawan
- `overtake_clear_m` terlalu rendah — naikkan ke 0.8
- US LEFT/RIGHT bacaan tak betul — verify standalone test

### Return failure (stuck di lane salah selepas overtake)
- `return_hold_s` terlalu lama — kereta tak return
- `bk_clear` US tak detect lawan dah pass → tunggu time-out
- Manual reset: DISABLE → manual drive ke lane betul → re-ENABLE

### Kereta jam-pack di lane (tak pakai overtake)
- Verify `state` di GUI. Stuck di `CRUISING`? Mungkin US tak detect lawan.
- Kalau stuck di `OPPONENT_DETECTED` lama → US side tak clear, lawan blok terus.

### Crash dengan lawan dari sisi (T-bone)
- Lawan keluar dari blind spot. Tambah US tambahan di belakang? (Future improvement)
- Sementara: defensive setting + slow speed

---

## 14. Race day checklist (print this!)

### 1 jam sebelum
- [ ] Semua battery charged
- [ ] Spare kereta charged (kalau ada)
- [ ] Catat lane assignment dari undian
- [ ] Laptop + SSH ready

### 30 minit sebelum di trek
- [ ] Power-on Pi
- [ ] Verify GUI all green, **5/5 ultrasonic**
- [ ] Manual test gerakan
- [ ] Update `lane_assignment` dalam config

### 15 minit sebelum (di trek)
- [ ] Perspective calibration
- [ ] HSV auto-tune (lane + cone)
- [ ] Practice 1 lap solo
- [ ] Practice overtake dengan target object/kereta diam
- [ ] Adjust `opponent_stable_frames`, `overtake_clear_m` ikut feel

### Saat race
- [ ] Park di lorong assigned
- [ ] Mode = ADR
- [ ] **Reset Yaw**
- [ ] ENABLED = OFF
- [ ] Wisel → ENABLE secepat mungkin
- [ ] Tangan dekat E-STOP

### Selepas
- [ ] DISABLE
- [ ] Tangkap kereta, cabut LiPo
- [ ] Catat hasil (menang/kalah, masa, penalti)
- [ ] Charge battery untuk round seterusnya (kalau menang)

---

## Lampiran A — Quick commands

```bash
# Run vehicle
cd ~/mycar && python3 manage.py drive

# Test ultrasonik
python3 ultrasonic_sensors.py

# Test live di GUI
# Buka http://<pi-ip>:8890 di laptop

# Tukar lane assignment cepat
# Edit ~/mycar/manage.py, ubah 'lane_assignment' line, save, restart
```

## Lampiran B — State machine ADR reference

```
CRUISING ──[US front < max_range, stable]──► OPPONENT_DETECTED
OPPONENT_DETECTED ──[US side clear > 0.6m]──► OVERTAKING
OVERTAKING ──[passed opponent]──► RETURNING
RETURNING ──[hold time 0.8s]──► CRUISING
ANY ──[forward < 0.5m]──► BRAKE (emergency)
ANY ──[collision IMU]──► COLLISION (stop)
```

## Lampiran C — Critical numbers

| Parameter | Default | Range biasa |
|---|---|---|
| `lane_assignment` | RIGHT | LEFT / RIGHT |
| `base_speed` | 0.32 | 0.25–0.45 |
| `opponent_max_range_m` | 1.5 | 1.0–2.0 |
| `opponent_stable_frames` | 2 | 1–4 |
| `overtake_clear_m` | 0.6 | 0.4–0.8 |
| `overtake_offset_m` | 0.30 | 0.25–0.35 |
| `return_hold_s` | 0.8 | 0.5–1.2 |
| `obstacle_brake_m` | 0.35 | 0.25–0.45 |

**Goal ADR**: lepas garisan dahulu daripada lawan, dengan minimum penalti, tanpa crash.

**Semoga berjaya!**
