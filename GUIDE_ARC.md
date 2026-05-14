# Panduan Lengkap — Autonomous Race Challenge (ARC)

**Untuk pemula sepenuhnya.** Ikut step-by-step dari bateri sampai habis race. Tiada pengetahuan terdahulu diperlukan.

---

## 0. Apa itu ARC?

ARC adalah **perlumbaan kereta autonomi di trek bujur** (outer loop). Kereta perlu:

1. Ikut lane (garisan putih di tepi trek)
2. Lepas semua **checkpoint** (titik pemeriksaan)
3. Elak **kon oren** yang diletak rawak (langgar = penalti 1 saat)
4. Sampai garisan penamat **dalam masa T_stage**

**Markah:**
```
S_AR = (100 × (1 − T_total / T_stage)) × (cp / Total_Checkpoints) − P
```
- `T_total` = masa awak guna (termasuk penalti)
- `cp` = bilangan checkpoint berjaya lepas
- `P` = jumlah penalti (1 saat per kon langgar)

**Strategi**: cepat habis + lepas semua checkpoint + jangan langgar kon = markah tinggi.

---

## 1. Hardware checklist

Sebelum mula, pastikan semua ini ada:

| Item | Bilangan | Catatan |
|---|---|---|
| Raspberry Pi 4 (4GB/8GB) | 1 | + microSD ≥32GB dengan Raspberry Pi OS |
| Kamera USB FOV ≥120° | 1 | Pasang di tengah hadapan, condong ~25° ke bawah |
| MPU6050 IMU | 1 | Untuk yaw rate (anti-zigzag) |
| PCA9685 PWM driver | 1 | I²C, kawal servo + ESC |
| HC-SR04 ultrasonik | **5** | FRONT, FL, FR, LEFT, RIGHT (lihat wiring §3.3) |
| Rintangan 1kΩ, 2kΩ | 5 + 5 | Voltage divider untuk ECHO pin |
| ESC + motor brushless | 1 set | Skala 1:10 |
| Servo Ackermann | 1 | Standard hobby |
| LiPo 2S/3S (motor) | 1 | + balance charger |
| Power bank USB 5V/3A (Pi) | 1 | **JANGAN** kongsi rail dengan motor |
| Velcro / cable ties | banyak | Rule 2.4 — bateri WAJIB diikat |

> **Nota:** Kereta kita **tidak guna LiDAR** untuk ARC. Sensor depan 100% kamera + ultrasonik (LiDAR 2D selalu miss kon yang tirus).

---

## 2. Setup software (sekali sahaja, sebelum hari pertandingan)

> Skip bahagian ini kalau system dah jalan. Bahagian ini untuk kereta yang masih kosong.

### 2.1 Flash microSD

1. Download **Raspberry Pi Imager** dari [raspberrypi.com/software](https://www.raspberrypi.com/software/)
2. Pilih **Raspberry Pi OS (64-bit) Lite**
3. Klik gear icon → set:
   - Hostname: `racepi`
   - Username/password awak sendiri
   - Enable SSH
   - Configure WiFi rumah
4. Flash, masukkan SD ke Pi, boot.

### 2.2 SSH & install

Dari laptop:
```bash
ssh <username>@racepi.local
```

Di Pi:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git python3-pip python3-venv i2c-tools \
    python3-smbus libatlas-base-dev libopencv-dev python3-opencv

# Enable I²C
sudo raspi-config nonint do_i2c 0

# Tambah user ke group hardware
sudo usermod -a -G dialout,i2c,video,gpio $USER

# Reboot untuk apply groups
sudo reboot
```

### 2.3 Project setup

```bash
mkdir -p ~/projects && cd ~/projects
python3 -m venv donkey-env
source donkey-env/bin/activate
echo 'source ~/projects/donkey-env/bin/activate' >> ~/.bashrc

pip install --upgrade pip
pip install donkeycar smbus2 flask pupil-apriltags numpy opencv-python RPi.GPIO

donkey createcar --path ~/mycar
cd ~/mycar
```

**Copy semua fail `.py` dari project ini ke `~/mycar/`** (guna `scp` dari laptop):
```bash
# Dari laptop, dalam folder project:
scp *.py <username>@racepi.local:~/mycar/
```

Fail yang wajib ada:
```
~/mycar/
├── manage.py
├── lane_detector_robust.py
├── arc_controller.py
├── urban_controller.py        (tak digunakan ARC tapi diimport oleh manage.py)
├── adr_controller.py          (sama)
├── apriltag_detector.py
├── imu_part.py
├── ultrasonic_sensors.py
├── gui_server.py
├── pca9685_driver.py
├── tuning_store.py
└── calibrate_perspective.py
```

---

## 3. Hardware wiring (sekali sahaja)

### 3.1 Power architecture — PENTING

```
┌─────────────────┐
│  LiPo 2S/3S     ├──► ESC ──► Motor + Servo (via BEC)
│  (motor power)  │
└─────────────────┘     └─► PCA9685 V+ (kalau BEC = 5V — verify dengan multimeter!)

┌─────────────────┐
│  Power bank     ├──► Pi 4 USB-C
│  USB 5V/3A      │
└─────────────────┘

GND BERSAMA antara semua source (Pi GND ↔ PCA GND ↔ ESC GND).
```

> **AMARAN:** Jangan kongsi rail Pi dengan motor. Voltage spike ESC akan reboot Pi.

### 3.2 I²C wiring (sensors)

| Pi pin (BCM) | Fungsi | Sambung ke |
|---|---|---|
| Pin 1 (3.3V) | Power | MPU6050 VCC |
| Pin 2 (5V) | Power | (servo/PCA jika perlu) |
| Pin 3 (GPIO2, SDA) | I²C data | MPU6050 SDA + PCA9685 SDA |
| Pin 5 (GPIO3, SCL) | I²C clock | MPU6050 SCL + PCA9685 SCL |
| Pin 6 (GND) | Ground | MPU6050 GND + PCA9685 GND |

### 3.3 Ultrasonic wiring (5× HC-SR04)

```
                FRONT (0°)
                  ●
           FL ●       ● FR
        (45°L)         (45°R)
              ╲   ╱
       LEFT ●─[CAR]─● RIGHT
       (90°L)        (90°R)
```

| Sensor | TRIG (BCM) | ECHO (BCM) | Pi pin (TRIG / ECHO) |
|---|---|---|---|
| FRONT | GPIO23 | GPIO24 | 16 / 18 |
| FL | GPIO5 | GPIO6 | 29 / 31 |
| FR | GPIO12 | GPIO13 | 32 / 33 |
| LEFT | GPIO17 | GPIO27 | 11 / 13 |
| RIGHT | GPIO22 | GPIO25 | 15 / 22 |

**WAJIB voltage divider per ECHO** (ECHO output 5V, GPIO Pi max 3.3V):

```
HC-SR04 ECHO ──[1kΩ]──┬── Pi GPIO (3.3V safe)
                      │
                     [2kΩ]
                      │
                     GND
```

VCC semua sensor: sambung ke Pi 5V (pin 2 atau 4) — beban ~50–80mA sahaja, OK. Letak satu kapasitor 470µF elektrolitik dekat junction VCC sensor untuk stabilkan.

### 3.4 PCA9685 ke ESC + servo

| PCA9685 | Sambung ke |
|---|---|
| Channel 0 PWM out | Servo signal (steering) |
| Channel 1 PWM out | ESC signal (throttle) |
| V+ rail | BEC 5V (dari ESC) atau battery 2S regulated |
| VCC (logic) | Pi 3.3V |

### 3.5 Camera mounting

- Tinggi: ~15–20 cm dari permukaan trek
- Sudut: condong **ke bawah ~20–30°** (kira-kira nampak trek 0.3m–2m di depan)
- Posisi: tengah-tengah hadapan kereta
- Stabilize: jangan goyang — pakai mounting tegar

### 3.6 Verify wiring sebelum power on

```bash
# Pastikan I²C devices muncul
sudo i2cdetect -y 1
# Patut nampak: 0x40 (PCA9685) dan 0x68 (MPU6050)
```

Kalau ada device address yang missing → check wiring/power.

---

## 4. PWM calibration (sekali sahaja, **WAJIB** sebelum first run)

> **Salah PWM = motor/servo rosak. Buat ini sekali dengan teliti.**

### 4.1 Steering servo (channel 0)

Pi power on, motor LiPo **CABUT**. Cuma PCA9685 + servo dipower.

```bash
donkey calibrate --channel 0 --bus=1
```

Akan prompt PWM value (default 333). Tekan `+` / `-` untuk adjust:

- Cuba `460` → servo turn fully ke satu hujung (mungkin kiri)
- Cuba `290` → servo turn fully ke hujung lain (kanan)
- `375` → patut center

**Catat values:**
- `left_pulse = ___`
- `right_pulse = ___`
- (center = (left + right) / 2)

### 4.2 Throttle ESC (channel 1)

```bash
donkey calibrate --channel 1 --bus=1
```

Procedure tipikal (ikut manual ESC awak):
1. Pi power on, ESC power **OFF**
2. Set PWM ke `370` (zero/idle)
3. Power on ESC — tunggu beep "armed"
4. Naikkan PWM perlahan-lahan: `380, 400, 420...` — motor patut mula spin di sekitar 400
5. Catat `zero_pulse` (motor diam) dan `max_pulse` **konservatif** (mula dengan `+50` dari zero)

**Catat values:**
- `zero_pulse = ___`
- `max_pulse = ___` (start safe, naikkan kemudian)
- `min_pulse = ___` (reverse, kalau ESC support)

### 4.3 Update `manage.py`

Buka `~/mycar/manage.py`, cari section PWMSteering / PWMThrottle, tukar nilai:

```python
steering = PWMSteering(controller=steering_controller,
                       left_pulse=460,   # ← awak punya
                       right_pulse=290)  # ← awak punya

throttle = PWMThrottle(controller=throttle_controller,
                       max_pulse=420,    # ← awak punya, conservative dulu
                       zero_pulse=370,   # ← awak punya
                       min_pulse=330)    # ← awak punya
```

### 4.4 Wheelbase

Ukur **dari pusat gandar depan ke pusat gandar belakang** dengan pembaris. Dalam `manage.py`:

```python
arc_ctrl = ArcController(
    base_speed=0.30,
    max_speed=0.50,
    wheelbase_m=0.30,   # ← ukuran sebenar awak (meter)
    ...
)
```

**Salah wheelbase = pure-pursuit overshoot / undershoot di corner.**

---

## 5. Test komponen individual (sekali sahaja sebelum first run penuh)

Test setiap komponen one-by-one untuk pastikan jalan.

### 5.1 Camera

```bash
cd ~/mycar
python3 -c "
import cv2
cap = cv2.VideoCapture(0)
cap.set(3, 320); cap.set(4, 240)
ok, img = cap.read()
print('Camera:', 'OK' if ok else 'FAIL', img.shape if ok else '')
cv2.imwrite('/tmp/cam_test.jpg', img)
cap.release()
"
scp <username>@racepi.local:/tmp/cam_test.jpg .   # buat di laptop
```

Buka `cam_test.jpg` — patut nampak gambar warna **betul** (merah = merah, biru = biru). Kalau warna terbalik, ada masalah BGR/RGB.

### 5.2 IMU

```bash
python3 imu_part.py
```

Putar kereta kiri → `yaw` patut increase. Kalau terbalik, edit `imu_part.py` set `MOUNTING_FLIP_Z = True`.

### 5.3 Ultrasonik

```bash
python3 ultrasonic_sensors.py
# Atau test cepat:
python3 -c "
from ultrasonic_sensors import UltrasonicSensors
import time, threading
us = UltrasonicSensors()
threading.Thread(target=us.update, daemon=True).start()
for _ in range(20):
    print(us.run_threaded())
    time.sleep(0.5)
us.shutdown()
"
```

Halangkan tangan di hadapan setiap sensor satu-persatu — bacaan masing-masing patut turun ke ~0.1–0.3m bila tangan dekat.

---

## 6. Hari pertandingan: pre-race (di pit area)

### 6.1 Charging (1–2 jam sebelum)

- [ ] LiPo motor: charge ke voltage penuh (4.20V/cell). Verify dengan multimeter.
- [ ] Power bank Pi: charge penuh.
- [ ] Spare LiPo kalau ada — charge semua.

### 6.2 Urutan power-on (HAFAL urutan ini)

**Setiap kali on kereta, ikut urutan sama:**

1. **Pi power-bank ON dulu** → tunggu Pi boot (~30 saat, LED kelip stabil)
2. SSH atau VNC ke Pi dari laptop. Verify boot OK:
   ```bash
   ssh <username>@racepi.local
   sudo i2cdetect -y 1     # 0x40 + 0x68 muncul?
   ls /dev/video0           # kamera detected?
   ```
3. **Cangkuk LiPo motor LAST** — ESC akan beep "armed". Pastikan kereta atas blok / roda angkat (jangan running).
4. Test servo direction manual sebelum letak atas trek (lihat §6.4).

### 6.3 Boot system

```bash
cd ~/mycar
python3 manage.py drive
```

Patut output:
```
INFO ... CvCam opened...
INFO ... UltrasonicSensors: GPIO ready — ['front', 'fl', 'fr', 'left', 'right']
INFO ... MPU6050 initialized on I2C bus 1
INFO ... Calibrating gyro: keep car STILL (300 samples)
INFO ... Gyro bias (rad/s): ...
INFO ... GuiServer listening on 0.0.0.0:8890
```

Kalau ada error, cuba troubleshoot ikut §10 di bawah.

### 6.4 Buka GUI dari laptop

```
http://<pi-ip>:8890
```

Pastikan boleh tengok:
- Kamera live preview (warna betul)
- Telemetry panel update setiap saat
- Hardware status: **Camera, Ultrasonic (5/5), IMU** semua hijau (OK)
- Loop Hz > 15

### 6.5 Manual mode check

**Sebelum bawa ke trek, test gerakan asas di pit:**

1. Mode = `MANUAL`, ENABLED = **OFF**
2. Angkat kereta supaya roda tak sentuh lantai
3. ENABLE
4. Guna joystick GUI (atau keyboard) untuk:
   - Steer kiri → roda hadapan turn kiri (verify direction!)
   - Steer kanan → roda turn kanan
   - Throttle perlahan ke depan → motor spin **forward** (kalau reverse, swap 2 wayar motor)
5. DISABLE selepas verify

**Kalau direction terbalik:** swap `left_pulse` & `right_pulse` dalam `manage.py`, atau swap polariti motor.

---

## 7. Di trek: calibration (5–15 minit di venue)

### 7.1 Perspective transform

Camera mounting di kereta awak unik. Kena calibrate IPM sekali di trek sebenar.

1. Tampal **rectangle tape kuning/biru** di permukaan trek: **1.2m panjang × 0.5m lebar** (jangan guna warna putih — clash dengan lane).
2. Park kereta dengan rectangle tepat di depan kamera, edge dekat = ~30cm dari bumper.
3. Capture frame:
   ```bash
   python3 -c "
   import cv2
   cap = cv2.VideoCapture(0)
   cap.set(3,320); cap.set(4,240); import time; time.sleep(1)
   ok, img = cap.read()
   cv2.imwrite('/tmp/calib_frame.jpg', img)
   "
   ```
4. Scp ke laptop, run `python calibrate_perspective.py calib_frame.jpg`
5. Click **4 corners rectangle** ikut prompt order
6. Output `SRC_POINTS = ...` → copy paste ke `lane_detector_robust.py` (cari `SRC_POINTS` di top file)
7. Restart `manage.py`

### 7.2 HSV auto-tune

Lighting trek berbeza dari pit. Auto-tune di trek:

1. Buka GUI, mode = MANUAL, ENABLED = OFF
2. Posisi kereta di trek dengan **lane line putih jelas dalam frame**
3. Klik **"Auto-tune Thresholds"** di tab Calibration
4. Verify debug overlay nampak lane line ditebalkan
5. Ulang di **2–3 lokasi trek** (straight + corner + near checkpoint)

### 7.3 Cone HSV auto-tune (untuk ARC!)

Kon oren detection critical untuk ARC. Tune di trek:

1. Letak kon **sebenar** ~1m di depan kamera
2. Mode = MANUAL, klik **"Cone Auto-tune"** di GUI (atau tab khas)
3. Verify GUI tunjuk cone overlay (mask oren) hanya cover kon, bukan background
4. Kalau tak detect — adjust HSV manual:
   - `cone_h_lo / h_hi` ~ 5–25 untuk oren
   - `cone_s_min` > 100 (warna pekat)
   - `cone_v_min` > 80

---

## 8. Sebelum start (di start line)

### 8.1 Final checklist

- [ ] Battery motor: voltage > 7.4V (2S) atau > 11.1V (3S)
- [ ] Power bank Pi: > 50%
- [ ] GUI accessible dari laptop
- [ ] Hardware status: semua hijau
- [ ] Loop Hz > 18
- [ ] Lane confidence > 0.7 bila kereta di trek
- [ ] All 5 ultrasonik bacaan masuk akal (US visualisation di GUI tunjuk garis)
- [ ] Mode = `MANUAL`, ENABLED = `OFF`

### 8.2 Park kereta di start line

1. Letak kereta dengan bumper depan di garisan mula
2. Hadapkan kereta arah trek (ikut arah lap)
3. **Pegang kereta** sehingga peserta diluar trek (rules 4.3)

### 8.3 Switch ke mode race

1. Mode → `ARC` (button di GUI)
2. **JANGAN ENABLE LAGI** — tunggu signal pengadil
3. Verify panel telemetry:
   - `state: STOP` atau `SLOW`
   - throttle output = 0
   - steering output = 0

### 8.4 Saat pengadil bagi signal

1. **Klik ENABLE**
2. Kereta akan mula gerak
3. **Tangan stay dekat E-STOP** sepanjang race

---

## 9. Semasa pertandingan

### 9.1 Apa awak monitor di GUI

| Telemetry | Normal | Tindakan kalau abnormal |
|---|---|---|
| `state` | DRIVE / SLOW | Stuck di BRAKE? → ada halangan, mungkin false positive US |
| `Lane confidence` | > 0.7 | < 0.3 selalu = lane detector hilang track |
| `Forward min` | > 0.5 m | < 0.35 = brake aktif |
| `Loop Hz` | > 18 | < 12 = sistem slow |
| `Lane status` | OK | "⚠ WRONG LANE" = kereta terkeluar lane |

### 9.2 Bila tekan E-STOP

**Tekan E-STOP segera** kalau:
- Kereta menuju ke arah penonton / objek bukan trek
- Sticking — terus pusing-pusing tak gerak
- Crash imminent dengan kereta lain (untuk ADR — bukan ARC, tapi prinsip sama)
- Kereta keluar trek lebih 2 saat dan tak balik

E-STOP = klik butang merah besar di GUI. `enabled` jadi `false`, semua output 0.

### 9.3 Bila kereta keluar trek

Rules ARC: tiada penalti automatik. Kereta yang balik sendiri = continue. Kereta yang stuck di luar trek = end of run (segmen terakhir dianggap final position untuk markah).

**Tip:** Default `recovery_offset_m = 0.30` dalam ARC akan paksa kereta kembali ke lane kanan kalau detect wrong-lane.

---

## 10. Selepas run

1. **DISABLE segera** bila kereta lepas garisan tamat (atau time-up)
2. Tangkap kereta, **cabut LiPo motor**
3. Catat:
   - `T_total` (masa)
   - `cp` (bilangan checkpoint cleared — pengadil akan inform)
   - `P` (penalti, e.g. kon langgar)
   - Kira `S_AR` guna formula
4. Tukar battery / charge spare
5. Adjust strategy untuk run kedua (lihat §11)

---

## 11. Mode-specific tuning (untuk menang ARC)

Default values dalam `manage.py` adalah **CONSERVATIVE**. Naikkan untuk masa lebih pantas, tapi hati-hati.

### 11.1 Speed (parameter paling impactful)

```python
arc_ctrl = ArcController(
    base_speed=0.30,    # cruising speed
    max_speed=0.50,     # cap
    min_speed=0.20,     # jangan crawl
    ...
)
```

**Strategi tuning:**
1. Run pertama: `base_speed=0.30` — pastikan boleh habis lap
2. Naikkan +0.05 setiap run
3. Bila kereta mula **zigzag** atau **wide turn** = dah terlalu laju
4. Production value = 0.05 di bawah nilai yang bermasalah

### 11.2 Lookahead (pure pursuit)

```python
arc_ctrl = ArcController(
    ...
    lookahead_m=0.45,   # default
)
```

| Symptom | Adjustment |
|---|---|
| Kereta cut corner (langgar batas dalam) | Naikkan ke 0.55–0.60 |
| Kereta wide turn (langgar batas luar) | Turunkan ke 0.35–0.40 |
| Speed tinggi → naikkan proportional | 0.50–0.60 untuk speed > 0.45 |

### 11.3 Yaw damping (anti-zigzag)

```python
yaw_damping_gain=0.15
```

- Zigzag bila straight → naikkan ke `0.20–0.25`
- Steering lambat respon → turunkan ke `0.10`

### 11.4 Cone avoidance distance

Dalam `arc_controller.py`:
```python
cone_avoid_m=0.80,    # mula react jika kon < 80cm
```

Naikkan = react lebih awal (lebih selamat). Turunkan = lebih agresif.

### 11.5 Forward brake threshold

Dalam `arc_controller.py`:
```python
obstacle_brake_m=0.50,   # full brake
obstacle_slow_m=1.00,    # mula slow down
```

US front kadang bagi reading false (echo dari lantai). Kalau kereta brake tanpa sebab di straight → naikkan `obstacle_brake_m` ke 0.40.

---

## 12. Troubleshooting

### Kereta tak gerak langsung
- Mode betul (ARC)? Enabled = true?
- Battery motor cangkuk dengan betul?
- ESC armed? (dengar beep)
- `manage.py` ada error di terminal?

### Kereta gerak tapi steering salah arah
- Swap `left_pulse` ↔ `right_pulse` dalam `manage.py`
- Atau dalam GUI tuning, set `steering_invert=True` (kalau ada)

### Kereta zigzag teruk
- Naikkan `yaw_damping_gain` ke 0.25
- Turunkan `base_speed`
- Check lane confidence — kalau < 0.5, IPM atau HSV salah calibrate

### Lane confidence sentiasa rendah
- Re-run auto-tune di GUI
- Check perspective transform — debug image patut nampak trapezoid kuning roughly cover lane
- Lighting trek terlalu pantul? Tutup tepi camera dengan tape hitam (anti-glare)

### Kereta brake kerap di straight (false positive)
- US front detect lantai sebagai obstacle. Naikkan mounting sensor sedikit (5–10° menghala atas)
- Naikkan `obstacle_brake_m` ke 0.40

### Cone tak detect
- Re-run cone auto-tune di trek dengan kon sebenar
- Tukar `cone_h_lo/hi` manual: oren biasanya hue 5–25 dalam OpenCV
- Kalau still tak detect — sila check warna kamera betul (BGR vs RGB)

### Loop Hz < 12
- Kamera resolution terlalu tinggi? Default 320×240 patut OK
- Banyak `print()` debug? Buang
- AprilTag detector hog CPU walaupun mode ARC — set `quad_decimate=3.0` dalam `manage.py`

### Hardware status "NO DATA" (Ultrasonic)
- `RPi.GPIO` tak install: `pip install RPi.GPIO`
- User tak dalam group `gpio`: `sudo usermod -a -G gpio $USER` + relogin
- Wiring salah — verify TRIG output 5V pulse dengan multimeter

---

## 13. Race day checklist (print this!)

### 1 jam sebelum
- [ ] Semua battery charged
- [ ] Spare battery ada
- [ ] Laptop siap dengan SSH access ke Pi
- [ ] Manual checklist (kertas ini) ada di tangan

### 30 minit sebelum di trek
- [ ] Power-on Pi (urutan §6.2)
- [ ] Buka GUI dari laptop, verify all green
- [ ] Manual test gerakan asas (steering + throttle direction)

### 15 minit sebelum (di trek)
- [ ] Perspective calibration di trek sebenar
- [ ] HSV auto-tune di 3 lokasi
- [ ] Cone auto-tune dengan kon real
- [ ] Practice run di `base_speed = 0.30`
- [ ] Adjust `base_speed` ikut perasaan

### Saat race
- [ ] Park di start line, arah betul
- [ ] Mode = ARC
- [ ] ENABLED = OFF
- [ ] Tunggu signal
- [ ] ENABLE → tangan stay dekat E-STOP
- [ ] Monitor GUI

### Selepas
- [ ] DISABLE
- [ ] Tangkap kereta, cabut LiPo
- [ ] Catat masa + checkpoint + penalti
- [ ] Adjust untuk run kedua

---

## Lampiran A — Quick commands

```bash
# Run vehicle (mesti dalam folder mycar)
cd ~/mycar && python3 manage.py drive

# Test sensors individually
python3 ultrasonic_sensors.py
python3 imu_part.py

# Calibrate PWM
donkey calibrate --channel 0 --bus=1   # steering
donkey calibrate --channel 1 --bus=1   # throttle

# Find Pi IP
hostname -I

# Tail log masa race
python3 manage.py drive 2>&1 | tee /tmp/race.log
```

## Lampiran B — Sambungan terbiasa

| Symptom | Lokasi check |
|---|---|
| Kereta tak start | Battery, ESC armed, mode/enable |
| Steering terbalik | `manage.py` pulse values |
| Zigzag | `yaw_damping_gain`, `lookahead_m` |
| Wide turn | `lookahead_m` ↓ |
| Cut corner | `lookahead_m` ↑ |
| Brake palsu | `obstacle_brake_m`, US mounting |
| Cone tak elak | Cone HSV tuning |
| Wrong lane | Lane detector calibration |

**Semoga berjaya!** Race goal: habis cepat, lepas semua checkpoint, jangan langgar kon.
