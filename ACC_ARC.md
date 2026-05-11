# Autonomous Race Challenge (ARC)
### FIRA Autonomous Car Physical Competition (FAC) – Kategori 1

> **Konsep:** Kereta swapandu skala 1:10 perlu menamatkan litar perlumbaan secara autonomi, melepasi semua checkpoint dan mengelak halangan (kon) yang diletakkan secara rawak di dalam trek.

---

## 1. Spesifikasi Kereta (Umum FAC ACC)

| Item | Spesifikasi |
|---|---|
| Skala | 1:10 |
| Wheelbase | 200mm – 550mm |
| Panjang maksimum | 600mm |
| Lebar maksimum | 400mm |
| Tinggi maksimum | 450mm |
| Berat | Tiada had |
| Sistem kemudi | Ackermann (1 atau 2 paksi) |
| Jenis pacuan | 2WD atau 4WD (4 roda sahaja) |
| Bekalan kuasa | Bateri sahaja (bebas jenis, perlu diikat rapi) |
| Kawalan | Automatik sepenuhnya (Fully Autonomous) |

### Perkakasan Kawalan
- **Top-end:** Single Board Computer (SBC) – Raspberry Pi, Jetson, Intel NUC, dll.
- **Low-end:** Sebarang microcontroller
- **Sensor dibenarkan:** Kamera (utama), LiDAR, ultrasonic, ToF, IMU
- **DILARANG:** Infrared line follower sensor, jammer, strobe light, laser, EMF
- **DILARANG:** Tayar melekit, cawan sedutan, gam, atau apa-apa yang merosakkan arena

---

## 2. Spesifikasi Trek ARC

| Item | Spesifikasi |
|---|---|
| Saiz gelanggang | 7.5m × 6m (anggaran) |
| Material gelanggang | Banting dilapik papan plywood |
| Warna trek | Hitam |
| Warna garisan | Putih |
| Lebar trek | 50cm ± 10% (minimum 590mm) |
| Garisan mula/tamat | Warna berbeza daripada penanda jalan biasa |
| Checkpoint | Bertanda kecil, warna **selain** hitam dan putih |
| Penghadang gelanggang | Disediakan keliling trek (keselamatan) |

### Halangan (Kon)
| Item | Spesifikasi |
|---|---|
| Tinggi minimum | 8 cm |
| Panjang & lebar maksimum | 7.8 cm |
| Warna | Oren |
| Penempatan | Rawak di dalam trek |

---

## 3. Peraturan Permainan ARC

1. **Undian giliran** – Nombor giliran ditentukan melalui undian. Keputusan undian adalah muktamad.
2. **Sesi latihan** – Terbuka kepada semua pasukan secara *first-come, first-served*. Prop pertandingan mesti dikongsi.
3. **Quarantine** – Kereta dikuarantin sebelum perlawanan. Sebarang perubahan hanya boleh dibuat sebelum tempoh kuarantin.
4. **Pelancaran** – Tiada sentuhan manusia sebelum/semasa perlawanan. Roda mesti berhenti sepenuhnya sebelum mula.
5. **Checkpoint** – Kereta mesti melepasi **kesemua checkpoint**. Kegagalan = penalti masa.
6. **Perlanggaran** – Jika kereta melanggar **sebarang halangan** (dalam atau luar litar), **pertandingan dikira berakhir**. Mata & masa dikira berdasarkan **segmen terakhir** yang dilalui.
7. **Had masa** – Setiap pasukan diberi had masa tertentu (T_stage) oleh urusetia.
8. **Kedudukan** – Disusun mengikut mata tertinggi + masa terpantas.

---

## 4. Formula Pemarkahan ARC

$$S_{AR} = \left(100 \times \left(1 - \frac{T_{total}}{T_{stage}}\right) \times \frac{cp}{Total\ Checkpoints}\right) - P$$

### Pemboleh Ubah

| Simbol | Maksud |
|---|---|
| **S_AR** | Markah kategori ARC |
| **T_stage** | Masa keseluruhan yang diperuntukkan |
| **T_total** | Masa digunakan (termasuk penalti masa) |
| **cp** | Bilangan checkpoint berjaya dilepasi |
| **Total Checkpoints** | Jumlah checkpoint dalam litar |
| **P** | Jumlah markah penalti |

---

## 5. Penalti Masa (Ditambah pada T_total)

### Langkauan Checkpoint
$$T_{total} \mathrel{+}= 0.5 \times \frac{T_{stage}}{Number\ of\ all\ checkpoints}\ \text{(s)}$$

### Komponen Terjatuh Semasa Permainan
$$T_{total} \mathrel{+}= 0.2 \times \frac{T_{stage}}{Number\ of\ all\ checkpoints}\ \text{(s)}$$

---

## 6. Sumbangan ARC kepada Markah Keseluruhan

Markah ARC akan dicampur dengan markah Urban Driving, kemudian didarab dengan pekali tahap autonomi (Ka):

$$S_T = (S_{AR} + S_{AUD}) \times K_a$$

### Pekali Tahap Autonomi (Ka)
| Tahap Autonomi | Pekali |
|---|---|
| Onboard | 1.0 |
| Bercampur (mixed) | 0.75 |
| Offboard | 0.5 |

> 💡 **Strategi:** Gunakan onboard processing sepenuhnya untuk Ka = 1.0.

---

## 7. Contoh Pengiraan ARC

**Andaian:**
- T_stage = 120 saat
- Jumlah checkpoint = 10
- Pasukan lepasi 8 checkpoint (langkau 2)
- Masa asal digunakan = 90 saat
- Tiada komponen jatuh

**Pengiraan penalti masa langkau checkpoint:**
- Penalti per checkpoint = 0.5 × (120/10) = 6 saat
- 2 checkpoint dilangkau = 12 saat tambahan
- **T_total = 90 + 12 = 102 saat**

**Pengiraan markah:**
$$S_{AR} = \left(100 \times \left(1 - \frac{102}{120}\right) \times \frac{8}{10}\right) - 0$$
$$S_{AR} = 100 \times 0.15 \times 0.8 = 12.0\ \text{mata}$$

---

## 8. Pelanggaran Peraturan (Disingkir)

- Tidak hadir 3 minit selepas masa pertandingan
- Tidak mematuhi spesifikasi kereta
- Berkongsi kereta dengan pasukan lain
- Tingkah laku tiada semangat kesukanan
- Merosakkan permukaan gelanggang
- Kereta tidak berfungsi semasa pemeriksaan

> ⚠️ **Keputusan pengadil adalah muktamad. Tiada rayuan.**

---

## 9. Tips Strategi ARC

1. **Elak perlanggaran pada semua kos** – Satu sahaja perlanggaran tamatkan sesi terus
2. **Imbangkan kelajuan vs ketepatan** – Lebih pantas = T_total rendah, tapi risiko terlanggar tinggi
3. **Pastikan semua checkpoint dilalui** – Penalti masa langkau adalah signifikan
4. **Ikat komponen rapi** – Elakkan penalti komponen terjatuh
5. **Gunakan LiDAR atau ToF** untuk pengesanan kon yang tepat
6. **Onboard processing** untuk Ka maksimum
