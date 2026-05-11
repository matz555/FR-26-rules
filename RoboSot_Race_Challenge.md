# RoboSot Race Challenge 2026

**Source:** MALAYSIA FIRA ROBOWORLD CUP — Rules and Regulation for 2026 (Version 2026/210, 23 Jan 2026)

---

## Tujuan Cabaran

Cabaran robot **wheeled bersaiz kecil**. Memerlukan pembangunan keupayaan robot dalam:
- **Vision identification**
- **Path planning**

---

## Challenge Format

- Format: **one-by-one trial**.
- Setiap trial: robot mesti cari dan bawa **bola berwarna** ke kawasan goal (ditanda dengan patches **merah, biru, kuning**) dalam **masa tersingkat** untuk skor mata.
- Robot **TIDAK BOLEH**:
  - Menyentuh halangan
  - Membawa bola ke kawasan warna yang salah
- Selepas permainan bermula: robot mesti **cari bola berwarna** di padang dan **tolak ke kawasan warna sepadan** untuk skor.
- Pemenang = pasukan dengan **jumlah skor tertinggi**.
- Setiap pasukan main **3 trials**.
- **Corak bola dan halangan berubah setiap trial.**

---

## Testing Time

- Setiap pasukan dapat **5 minit testing individu** sebelum permainan.
- Selepas semua pasukan habis test individu → **5 minit testing common** untuk semua pasukan.

---

## Robot Specification

- Saiz maksimum: **(W) 24cm × (L) 24cm × (H) 40cm** dalam **resting stage**, **tanpa** memanjangkan bahagian ke saiz maksimum.
- Semua aksesori (gripper, kicker) mesti **dipasang penuh** semasa verifikasi dan pengukuran robot.
- **Tiada aksesori tambahan** boleh ditambah semasa gameplay — semua aksesori mesti dipasang semasa proses verifikasi saiz.

---

## Challenge Setup

- Boleh guna robot **self-developed** atau **komersial** (cth: TurtleBot 3 Burger, Bveeta Mini), asalkan memenuhi spesifikasi robot.
- **Mana-mana software** boleh digunakan untuk integrasi dengan sistem robot.
- Verifikasi robot dilakukan dalam keadaan **resting** (tidak memanjang ke saiz maksimum).
- Semasa gameplay, robot **DIBENARKAN** memanjang melebihi saiz maksimum (asalkan aksesori sudah dipasang semasa verifikasi).

---

## Gameplay

### Format Round
- Setiap pasukan: **3 rounds**, **3 minit per round**.
- Robot mula dari **start point** (ditanda **X** — lihat Figure 4 dalam dokumen asal).

### Persediaan Round
- Sebelum setiap round, referee letakkan:
  - **12 bola**: 4 merah, 4 kuning, 4 biru
  - **Halangan** (black matte cone)
- Corak peletakan **konsisten antara pasukan**.

### Bonus — Golden Ball
- **Bonus ball color** ditarik (drawn) oleh referee pada permulaan setiap round.
- Tolak warna ini ke kawasan sepadan = **mata berganda (double points)**.

### Tindakan Robot
- Robot mesti **autonomously** kesan dan tolak bola ke kawasan warna sepadan.
- **Bola yang berjaya diskor** akan dialihkan oleh referee untuk elak gangguan tidak sengaja.
- Robot boleh **ambil semula bola** yang keluar padang **secara autonomous**.
- **Pemain manusia ATAU referee TIDAK BOLEH** sentuh atau pindahkan bola.
- Jika robot tidak boleh kembali → pemain manusia boleh mohon **failed attempt** dari referee.

### Larangan Campur Tangan
- **Campur tangan manusia semasa gameplay dilarang.**
- Pelanggaran → **warning**, **tolak markah**, atau **disqualification** (pelanggaran berulang).

### Permulaan Round
- Round bermula **HANYA selepas whistle referee**.
- Robot mesti pegun sehingga whistle.
- Pergerakan awal → **warning**; berulang → **disqualification**.

### Larangan Lain
- Peserta **tidak boleh** lengahkan atau halang cabaran → **disqualification**.

### Padang
- Padang permainan **tahan lasak** — tidak akan rosak oleh berat/saiz robot.
- Pasukan bertanggungjawab untuk gangguan akibat keadaan venue (boleh rujuk jawatankuasa anjuran untuk penyelesaian).

### Hak Penganjur
- **Penganjur berhak mentafsir peraturan.**

### Mekanisme Robot
- Pasukan boleh reka **mekanisme tolak atau dribbling bola**, asalkan robot kekal dalam saiz had.

### Peraturan Skor
- **Bola mesti sentuh kawasan warna betul** untuk dapat mata.
- Jika bola **berguling ke kawasan warna salah**:
  - **Tolak 5 mata** (kecuali jika bola tidak menyentuh tanah).
- **Sentuh halangan** → tolak mata. **Halangan TIDAK akan diletak semula** selepas foul.

---

## Game Field Specification

| Spesifikasi | Nilai |
|---|---|
| Saiz padang | **400 (L) × 300 (W) cm** |
| Bilangan kawasan goal | **3 kawasan** (lihat Figure 4) |
| Saiz setiap goal | **50 (L) × 50 (W) cm** |

### Bola Race Challenge
- **Jenis**: Plastik untuk kolam mainan kanak-kanak (ball-pool)
- **Diameter**: **68–70 mm**
- **Warna**: Merah, Kuning, Biru

---

## Scoring System

### Jadual Mata

| Tindakan | Mata |
|---|---|
| Tolak/sepak **Golden Ball** (bonus color) ke kawasan betul | **+20 mata** |
| Tolak bola biasa ke kawasan betul | **+10 mata** |
| Tolak bola (yang sentuh tanah) ke kawasan **warna salah** | **−5 mata** |
| Sentuh halangan | **−3 mata** per kejadian |
| Campur tangan pemain manusia tanpa kebenaran referee | **−5 mata** |
| Gagal lengkapkan pengubahsuaian robot **15 minit sebelum perlawanan** | **−5 mata** |

### Peraturan Tambahan Penilaian
- Semua syarat dan potongan mata **dinilai oleh referee**.
- Referee kira skor round; pasukan dengan **skor tertinggi** menang.
- **Skor pemenang tidak boleh kurang dari 0 mata**.

---

## Tie-breaker

- **Tie-breaker utama**: pasukan dengan **jumlah masa terpendek** merentas 3 rounds dirank lebih tinggi.
- Jika 3 top scorer **tidak boleh ditentukan** selepas 3 rounds → **2 additional rounds** dimainkan **keesokan harinya**.

---

## Peraturan Umum (Berkaitan)

- **Melintasi garisan/sempadan** = sekurang-kurangnya **50% robot atau bola** melintasi garisan.
- **Race Challenge MEMBENARKAN** robot melintasi sempadan dan kembali ke padang (sama seperti Soccer).
- Verifikasi robot wajib. Gagal verifikasi → **24 jam** untuk pengubahsuaian + **15 minit window** akhir. Gagal lagi → **disqualified**.
- Pasukan mesti tiba **1 jam sebelum** cabaran untuk inspeksi robot.
