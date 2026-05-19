# Senarai Hardware Tambahan — Bveeta Race Challenge (FINAL)

Status semasa Bveeta R007 (sudah ada):

✅ Raspberry Pi · Motor MCU (`/dev/ttyUSB1`) · 2× DC motor **dengan encoder built-in** · RPLidar A1 (`/dev/ttyUSB0`) · USB camera · MPU6050

❌ Belum ada: **dribbler · magnetometer · ToF · bumper · wide-angle lens**

---

## Ringkasan Beli

| # | Item | Mandatory? | Anggaran (RM) | Tujuan |
|---|---|---|---|---|
| 1 | **Continuous-rotation servo** (FS90R / SG90-360) | ✅ Wajib | 15-25 | Dribbler roller — hisap & pegang bola |
| 2 | Foam roller Ø35 mm × 80 mm + shaft coupler | ✅ Wajib | 10-20 | Mekanikal dribbler |
| 3 | **GY-271 QMC5883L** magnetometer | ✅ Wajib | 10 | Heading drift-free |
| 4 | **VL53L1X** ToF (Adafruit / Pololu / GY-53L1X) | 🟡 Sangat disyorkan | 30 | Confirm bola dah dalam dribbler |
| 5 | Microswitch bumper × 2-3 (Cherry SS-5GL / Omron) | 🟡 Disyorkan | 6 total | Anti-foul cone touch |
| 6 | Lens M12 wide-angle 2.8 mm 120° | 🟢 Optional | 30 | Field-of-view lebih luas |
| 7 | DC-DC buck 5V/3A (MP1584 / LM2596S) | 🟢 Optional | 10 | Pi tidak reset masa servo + motor full load |

**Bajet minimum (1+2+3): ~RM45**
**Bajet recommended (1-5): ~RM85**

---

## 1. Dribbler — Continuous-Rotation Servo (WAJIB)

**Kenapa servo CR**: firmware Bveeta `ros_arduino_bridge` sudah ada handler `s <id> <pos>\r\n` — tiada perlu flash Arduino. Servo CR berputar laju ikut "position" (90 = stop, 0 = full reverse, 180 = full forward).

### Pilihan servo

| Model | Stall torque | Catatan |
|---|---|---|
| **FS90R** | 1.5 kg·cm | Paling murah & cukup untuk bola pool plastik 70 mm |
| **MG996R-CR** (modified) | 9.4 kg·cm | Power penuh, agak besar |
| **DSSERVO DS04-NFC** | 5.5 kg·cm | Mid-range bagus |

### Wiring (servo → Bveeta MCU aux servo header)

Bveeta MCU biasanya ada 4 servo header (3-pin: Signal / +5V / GND).

```
Continuous-rotation servo:
  Brown/Black  (GND) ─────► Servo header GND
  Red          (+5V) ─────► Servo header +5V (atau external 5V/2A kalau servo besar)
  Orange/Yellow(SIG) ─────► Servo header SIG channel 0
                            (default DRIBBLER_SERVO_ID=0 dalam robot_control.py)
```

⚠️ **Untuk MG996 / servo torque besar**: rail 5V Pi tidak boleh handle stall current. Sediakan **5V external BEC 3A** untuk servo, dan **gandingkan GND** dengan MCU.

### Mekanikal mounting

```
       ┌──────────────────┐
       │  Robot body      │
       │                  │
       │  ┌────┐          │   Servo dengan roller di hadapan robot,
       │  │SVO │═══[ROLLER]═> tinggi axle ~3.5 cm dari lantai (separuh
       │  └────┘          │   tinggi bola 70 mm), spin ke arah dalam
       │                  │   (suck-in).
       └──────────────────┘
            ▲ depan robot
```

Roller foam + cable-tie ke shaft servo sudah cukup untuk prototype.

### Kalau perlu DC motor + H-bridge (upgrade nanti)

Tukar `DRIBBLER_MODE='custom'` di `robot_control.py`. Perlu flash firmware Arduino tambah handler `k <speed>` → PWM pin spare.

---

## 2. Magnetometer GY-271 QMC5883L (WAJIB)

**Kenapa**: MPU6050 yaw drift ~1-2°/min sebab integrate gyro Z. Atas padang 4 m, drift 5° = bola tersasar 35 cm — boleh miss goal zone 50 cm. Magnetometer fix heading absolute.

Software dah auto-detect QMC5883L (0x0D) dan HMC5883L (0x1E). Plug-and-play.

### Wiring (I²C bus 1, share dengan MPU6050)

```
GY-271 (QMC5883L)         Raspberry Pi 40-pin
   ┌────┐
   │VCC │ ────────────► Pin 1  (3.3V)
   │GND │ ────────────► Pin 6  (GND)
   │SCL │ ────────────► Pin 5  (GPIO3 / SCL1)
   │SDA │ ────────────► Pin 3  (GPIO2 / SDA1)
   │DRDY│ ──── (NC, biarkan)
   └────┘
```

### Mounting penting

- Letak **paling tinggi** dalam robot, sekurang-kurangnya **8 cm dari motor & driver** (medan magnet)
- **Jauh dari LiDAR motor** (juga ada motor stepper)
- Mount **horizontal** (XY plane sejajar lantai)
- Jangan dekat aluminium frame yang besar

### Calibration (sekali per venue)

1. Buka GUI → tab **IMU/Mag**
2. Putar robot 360° perlahan-lahan 2-3 kali atas lantai padang
3. Catit min/max X dan Y dari telemetry
4. Kira: `x_off = (Xmax+Xmin)/2`, `y_off = (Ymax+Ymin)/2`
5. `x_scale = ((Xmax-Xmin)+(Ymax-Ymin))/2 / (Xmax-Xmin)` (sama untuk y)
6. Masukkan dalam GUI → klik **Apply**

### Verify cek I²C

```bash
sudo i2cdetect -y 1
# Sepatutnya nampak: 0x0D (QMC) dan 0x68 (MPU)
```

---

## 3. VL53L1X Time-of-Flight (SANGAT DISYORKAN)

**Kenapa**:

- LiDAR A1 ada blind cone 23 cm. Bola 70 mm dalam mulut dribbler (jarak ~5 cm) → lidar **tak nampak**
- Tanpa ToF, state machine `CAPTURE` guna **timeout fallback 600 ms** — boleh false-positive (assume captured padahal belum) atau false-negative (release awal sebab lidar nampak shaft servo)
- Dengan ToF: capture confirmed bila `tof_distance < 8 cm`

### Module pilihan

| Module | Range | Catatan |
|---|---|---|
| **GY-53L1X** | 4 m | Paling murah |
| **Adafruit VL53L1X** | 4 m | Lebih reliable, ada level-shifter built-in |
| Pololu VL53L1X | 4 m | Mid-range |

### Wiring (I²C bus 1, share lagi)

```
VL53L1X                   Raspberry Pi
   ┌────┐
   │VIN │ ────────────► Pin 17 (3.3V)
   │GND │ ────────────► Pin 9  (GND)
   │SCL │ ────────────► Pin 5  (GPIO3 / SCL1)   [share]
   │SDA │ ────────────► Pin 3  (GPIO2 / SDA1)   [share]
   │XSHUT│ ─────────── (NC)
   │GPIO1│ ─────────── (NC)
   └────┘
```

I²C address default = **0x29** — tiada konflik dengan QMC (0x0D) atau MPU (0x68).

### Mounting

Pasang **dalam mulut dribbler**, hadap depan, ~3-4 cm dari lantai (paras tengah bola). Sudut tilt 0° (lurus depan).

```
     ┌─[Robot front view]─┐
     │                    │
     │   ┌──┐ ToF aim →   │
     │   │TF│════════════ │  ← bola disyorkan akan trigger ToF <8 cm
     │   └──┘ [Roller]    │
     │ ──axis──           │
     └────────────────────┘
            ▼ lantai
```

### Software (akan ditambah selepas hardware sampai)

Saya akan create:

- `tof_sensor.py` — driver background thread, expose `get_distance_mm()`
- Update `race_engine.py` `CAPTURE` state: `if tof.get_distance_mm() < 80: → SEARCH_GOAL`

Library: `pip install adafruit-circuitpython-vl53l1x` atau `pip install VL53L1X`.

---

## 4. Bumper Microswitch ×2-3 (DISYORKAN)

**Kenapa**: Tiap kali sentuh cone = **−3 mata**. 2 sentuhan = 1 bola dah hilang scoring. Visual avoidance tak 100% — bumper switch beri confirmation fizikal & trigger reverse dalam <50 ms.

### Layout

```
   ┌──┐ ┌──────────────┐ ┌──┐
   │L │ │              │ │R │   ← Microswitch L & R di pinggir depan
   └┬─┘ └──────┬───────┘ └─┬┘
    │       ┌──┴──┐        │
    │       │  C  │        │   ← Optional center bumper
    │       └─────┘        │
    └──────── Robot ───────┘
```

### Wiring (terus ke Pi GPIO)

```
Switch L (Cherry SS-5GL or similar):
   COM ──────────► GND (Pi pin 6 / 9)
   NO  ──────────► GPIO17 (Pin 11)
                   [enable internal pull-up dalam Python]

Switch R:
   COM ──────────► GND
   NO  ──────────► GPIO27 (Pin 13)

Switch C (optional):
   COM ──────────► GND
   NO  ──────────► GPIO22 (Pin 15)
```

Logik: `pressed = GPIO.input(pin) == GPIO.LOW` (sebab pull-up + COM-to-GND).

### Software (akan ditambah)

- `bumper.py` — polling 100 Hz thread, expose flags `left/right/center`
- `race_engine.py` — kalau mana-mana TRUE: terus pergi ke RECOVER (reverse + arc opposite side)

---

## 5. Wide-Angle Lens (OPTIONAL)

Kalau kamera USB jenis **modul terpisah dengan M12 mount** (banyak kamera ELP/Arducam), tukar lens dari standard ~60° ke **120° M12 2.8 mm**.

**Pros**: Detect goal patch dari jauh, kurang spin-search.
**Cons**: Distortion barrel — perlu adjust `focal_px` dalam `camera_vision.py` ke ~160 untuk 320 px width.

Tiada wiring change.

---

## 6. Power Audit (OPTIONAL tapi recommended)

Beban baru pada 5V Pi rail:

- Servo CR: 200-500 mA biasa, 1 A peak (stall)
- VL53L1X: ~20 mA
- QMC5883L: ~100 µA
- 3× microswitch: 0 mA (pull-up)

Pi 4 sendiri ~700 mA + USB devices (lidar 200 mA, camera 250 mA).

Total ~1.5-2.5 A peak. Kalau buck converter sekarang <3 A atau Pi kerap reset, tambah:

- **DC-DC buck 5V/3A** (MP1584 / LM2596S) khas untuk Pi
- Servo guna power supply berasingan (BEC 5V/3A) dengan **GND tied** ke Pi

---

## Bus I²C selepas semua upgrade

```
$ sudo i2cdetect -y 1
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                                  -- 0d -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- 29 -- -- -- -- -- --
...
60: -- -- -- -- -- -- -- -- 68 -- -- -- -- -- -- --
```

| Address | Device |
|---|---|
| `0x0D` | QMC5883L magnetometer |
| `0x29` | VL53L1X ToF |
| `0x68` | MPU6050 IMU |

3 device pada bus yang sama — tiada masalah pada 100 kHz default.

---

## Senarai pin Pi 40-pin (final)

| Pin | Function | Connected to |
|---|---|---|
| 1 | 3.3V | QMC5883L VCC |
| 3 | GPIO2 SDA1 | QMC + MPU + VL53L1X SDA |
| 5 | GPIO3 SCL1 | QMC + MPU + VL53L1X SCL |
| 6 | GND | QMC + bumper switch GND |
| 9 | GND | MPU + VL53L1X GND |
| 11 | GPIO17 | Bumper Left NO |
| 13 | GPIO27 | Bumper Right NO |
| 15 | GPIO22 | Bumper Center NO (optional) |
| 17 | 3.3V | VL53L1X VIN |
| USB | `/dev/ttyUSB0` | RPLidar A1 |
| USB | `/dev/ttyUSB1` | Bveeta Motor MCU |
| USB | `/dev/video0` | Kamera |

MCU servo header ch.0 → dribbler servo.

---

## Urutan pemasangan disyorkan

1. **Servo dribbler** dulu — paling impact, plug-and-play. Test via GUI tab **Manual Drive** → button "Dribbler IN/OUT".
2. **Magnetometer** — solder 4 wire, kalibrasi via GUI. Drift gyro hilang.
3. **VL53L1X** — solder 4 wire, biar saya tambah modul `tof_sensor.py` dan integrate ke `CAPTURE` state.
4. **Bumper switches** — paling akhir, biar saya tambah `bumper.py` + integrasi RECOVER state.

Bila item (3) atau (4) dah sampai, bagitahu saya — saya akan extend `race_engine.py` untuk pakai sensor tersebut.
