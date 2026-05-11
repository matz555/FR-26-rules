# RoboSot Avoidance Challenge 2026

**Source:** MALAYSIA FIRA ROBOWORLD CUP — Rules and Regulation for 2026 (Version 2026/210, 23 Jan 2026)

---

## Tujuan Cabaran

Memaparkan keupayaan platform RoboSot dalam **obstacles avoidance** menggunakan aplikasi **vision** dan **sonar**.

---

## Challenge Format

- Dijalankan secara **one-by-one trial**.
- Setiap trial: robot mesti maneuver ke **goal line** (ditanda dengan goalpost biru) dalam **masa tersingkat** untuk skor mata.
- Robot **tidak boleh**:
  - Menyentuh mana-mana halangan
  - Melintasi garisan sempadan
- Setiap pasukan main **3 trials**.
- Pemenang ditentukan berdasarkan **jumlah skor semua trials**.
- **Corak halangan berubah setiap trial.**

---

## Testing Time

- Setiap pasukan dapat **5 minit testing individu** sebelum permainan.
- Selepas semua pasukan habis test individu → **5 minit testing common** untuk semua pasukan.

**Nota Penting:**
- Semasa perlawanan: **TIADA masa testing**.
- **TIDAK DIBENARKAN** mengukur jarak antara halangan secara fizikal oleh pemain manusia.
- Lokasi halangan dan goalpost **kekal sama** untuk setiap trial (dalam trial yang sama untuk semua pasukan).

---

## Robot Specification

- Saiz maksimum: **(W) 24cm × (L) 24cm × (H) 40cm** dalam **resting stage**.
- Semua aksesori (gripper, kicker, dll.) **mesti dipasang** semasa proses verifikasi robot.

---

## Challenge Setup

- **Tiada penanda (markers)** dibenarkan diletak oleh pasukan di padang.
- **Tiada testing time** selepas robot diletakkan di start point.
- Setelah diposisikan, robot **tidak boleh digerakkan** sebelum isyarat mula.
- Setiap pasukan mesti lengkapkan **3 trials**.
- Pasukan boleh **withdraw** dari trial (sebelum atau semasa run) jika tidak boleh perform → **skor sifar (0)** untuk trial tersebut.
- Untuk setiap trial, referee letakkan **15 halangan** di padang mengikut corak yang ditetapkan.
- Semasa permainan, pemain manusia **HANYA** boleh:
  - Menekan butang start pada robot, ATAU
  - Menggunakan mekanisme remote start
- Penggunaan komputer/remote-control untuk **gerakkan atau pandu robot** → **disqualification untuk trial tersebut**.
- Robot boleh diorientasikan dalam **mana-mana arah** ketika diletakkan di start marker.
- Referee akan isyaratkan permulaan dan tamat trial dengan **whistle**.
- Semua pasukan menggunakan **corak halangan yang sama** dalam trial yang sama (kesaksamaan).
- **Corak halangan berubah** antara trials.

---

## Gameplay

- Robot maneuver ke goal line di depan goalpost biru sambil mengelak **semua halangan**.
- **Sentuhan dengan halangan = foul** → trial tamat serta-merta.
- Robot mesti kekal dalam **boundary lines** → melintasi sempadan = trial tamat.
- Referee rekod masa apabila:
  - Robot tamat tugasan, ATAU
  - Foul/stop dipanggil
- Jika robot **gagal melintasi goal line dalam 2 minit**:
  - Jarak diukur **berserenjang** (perpendicular) dari start line ke titik terjauh robot yang menyentuh tanah pada whistle.
  - **Tidak kira arah** atau status sempadan.
- Jika robot **tidak bergerak pada permulaan**:
  - Pasukan mesti maklumkan referee dan withdraw → **skor 0** untuk trial.
- Jika robot **melepasi end boundary line di belakang goalpost** tanpa melintasi goal line → **out of bounds**, trial tamat.
- Pasukan boleh **request tamatkan trial** sebelum had 2 minit:
  - Trial hanya berhenti selepas referee tiup whistle.
  - Jarak diukur pada titik tersebut.
- Pemenang = **jumlah skor tertinggi** merentas semua trials.

---

## Scoring System

### Kategori 1: Gagal capai goal line dalam 2 minit
(Jarak maksimum 400cm)

> **Skor = jarak (cm) × 0.2**
> Maksimum: **80 mata**

### Kategori 2: Berjaya lengkapkan tugasan dalam 2 minit
(Dinilai oleh referee)

> **Skor = 80 (jarak) + [masa baki (saat) × 0.2] + 20 (bonus completion)**
> Maksimum: **124 mata**

---

## Tie-breaker

1. Jika pasukan ada skor sama → pemenang = **masa tersingkat** menyelesaikan cabaran dalam mana-mana trial.
2. Jika **tiada pasukan berjaya selesaikan** dalam mana-mana trial → tie-breaker = **jumlah jarak dilalui dalam 3 trials**.

---

## Obstacles and Placement Rules

- Padang permainan disempadani **garisan putih**: **400 cm × 300 cm**.
- Halangan: **black matte cone**
  - Saiz: **13.5 cm × 13.5 cm × 23 cm (H)**
- Halangan diletak dalam padang, **KECUALI** kawasan goal box.
- Sebelum cabaran: **5 minit common testing** dengan **3 halangan** untuk kalibrasi.
- Selepas testing: referee letakkan **15 halangan** secara random (sendiri atau dengan bantuan peserta/penonton).
- **Jarak minimum antara halangan: 45 cm** (diukur dari tapak).
- Lokasi halangan ditanda supaya **corak sama untuk semua pasukan dalam trial yang sama**.
- **Tiada jarak minimum** antara bola dan halangan, asalkan boleh dicapai robot tanpa foul.
- Corak halangan **berubah setiap trial**.

---

## Field Specification

| Spesifikasi | Nilai |
|---|---|
| Saiz padang total | **5m × 6m** |
| Saiz padang permainan | **3m × 4m** (tidak termasuk ketebalan garisan; saiz berkesan diukur antara pusat garisan sempadan) |
| Material | 100% fiber synthetic needle punch carpet |
| Warna | Light green (hijau muda) |

---

## Goalpost Specification

| Spesifikasi | Nilai |
|---|---|
| Dimensi | **1.0m × 0.3m × 0.5m** (Width × Depth × Height) |
| Material rangka | Wooden (white colour) |
| Crossbar | 3cm × 3cm di atas goal box (putih) |
| Warna dalaman goal | **Navy blue** |

---

## Peraturan Umum (Berkaitan)

- **Melintasi garisan/sempadan** = sekurang-kurangnya **50% robot atau bola** melintasi garisan.
- **Avoidance Challenge TIDAK membenarkan** robot melintasi sempadan dan kembali ke padang (berbeza dengan Race & Soccer).
- Verifikasi robot wajib. Gagal verifikasi → **24 jam** untuk pengubahsuaian + **15 minit window** akhir. Gagal lagi → **disqualified**.
- Pasukan mesti tiba **1 jam sebelum** cabaran untuk inspeksi robot.
- Pengubahsuaian wajib siap **≥15 minit** sebelum bermula — jika tidak, **markah ditolak**.
