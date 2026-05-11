# Urban Driving (AUD) – Perlumbaan Bandar Autonomi
### FIRA Autonomous Car Physical Competition (FAC) – Kategori 2

> **Konsep:** Kereta swapandu skala 1:10 perlu menavigasi persekitaran bandar dari titik mula ke titik destinasi, mematuhi semua papan tanda jalan dan penanda April Tag, serta berhenti di lokasi yang betul.

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
- **Sensor wajib:** Kamera (untuk pengecaman papan tanda/April Tag)
- **Sensor tambahan dibenarkan:** LiDAR, ultrasonic, ToF, IMU
- **DILARANG:** Infrared line follower sensor, jammer, strobe light, laser, EMF

---

## 2. Spesifikasi Trek Urban Driving

| Item | Spesifikasi |
|---|---|
| Saiz gelanggang | 7.5m × 6m (anggaran) |
| Material | **Banner** (permukaan tidak rata sepenuhnya) |
| Lebar trek | 60cm ± 10% |
| Penghadang gelanggang | Disediakan keliling trek (keselamatan) |
| Tinggi papan tanda & lampu trafik | Maksimum 160mm |

### April Tag (Penanda Visual)
| Item | Spesifikasi |
|---|---|
| Keluarga | 36h11 |
| Saiz | 8cm × 8cm hingga 10cm × 10cm |
| Lokasi | Diletakkan di **bawah** setiap papan tanda jalan |

📁 **Sumber rujukan (GitHub):**
- Layout simbol papan tanda: `github.com/ayozzet/ACC_signage`
- Fail STL untuk cetakan 3D: `github.com/ayozzet/ACC_signage/tree/main/STL%20files`
- Fail April Tag (36h11): `github.com/ayozzet/ACC_signage`

---

## 3. Senarai Papan Tanda & April Tag

| ID | Nama Tanda | Simbol Visual | Maksud |
|:---:|---|---|---|
| **0** | Dilarang Masuk | Bulat merah, garis putih | Tidak boleh memasuki jalan ini |
| **1** | Jalan Mati | Biru dengan "T" terbalik | Tidak boleh memasuki jalan ini |
| **2** | Masuk Kanan | Biru, panah kanan | Pilih jalan sebelah kanan simpang |
| **3** | Masuk Kiri | Biru, panah kiri | Pilih jalan sebelah kiri simpang |
| **4** | Terus ke Depan | Biru, panah atas | Teruskan ke hadapan |
| **5** | Berhenti | STOP merah oktagon | Berhenti (**destinasi akhir**) |

---

## 4. Peraturan Permainan Urban Driving

### Pelancaran
1. Kereta **mesti diletakkan di laluan kanan** pada titik permulaan
2. Peserta mesti **berada di belakang kereta** sebelum mula
3. Pertandingan bermula apabila **wisel ditiup**
4. Selepas mula, peserta mesti **keluar dari trek** dan **tidak boleh menyentuh kereta**

### Semasa Pemanduan
5. Kereta boleh **tukar lorong** ketika jalan sehala
6. Tetapi **mesti di lorong kanan** ketika melalui checkpoint (persimpangan 3 atau 4)
7. Kereta mesti mampu **mengenalpasti semua simpang**
8. Mata & masa dikira berdasarkan **segmen terakhir** yang dilalui
9. Kereta mesti **berhenti di papan tanda "Berhenti"** (ID 5) untuk tamatkan misi

### Had Masa
10. Setiap pasukan diberi had masa tertentu (T_stage) oleh urusetia
11. Kedudukan: mata tertinggi + masa terpantas

---

## 5. Sistem Pekali Khas Urban Driving

### Pekali Kaedah Pengecaman (Ks)

| Kaedah Pengecaman | Ks |
|---|---|
| April Tag | 1.0 |
| **Simbol (papan tanda visual)** | **1.3** |

> 💡 **Strategi:** Menggunakan simbol visual memberi **bonus 30%** kerana lebih mencabar dari segi computer vision (CV). April Tag lebih mudah dikesan tetapi memberi markah asas sahaja.

---

## 6. Penalti Markah (P)

| Pelanggaran | Penalti |
|---|---|
| Tidak berhenti di persimpangan | **−10** |
| Penukaran lorong yang tidak betul (antara dua simpang & mengelak halangan) | **−20** |
| **Menjalankan keputusan yang salah** | **−25** ⚠️ |

> ⚠️ **Penalti terbesar** adalah keputusan salah – contoh: belok kiri sedangkan papan tanda menunjukkan "Masuk Kanan", atau masuk jalan bertanda "Dilarang Masuk".

---

## 7. Formula Pemarkahan Urban Driving

$$S_{AUD} = \left(\left(100 \times \left(1 - \frac{T_{total}}{T_{stage}}\right) \times \frac{cp}{Total\ Checkpoints}\right) - P\right) \times K_s$$

### Pemboleh Ubah

| Simbol | Maksud |
|---|---|
| **S_AUD** | Markah kategori Urban Driving |
| **T_stage** | Masa keseluruhan yang diperuntukkan |
| **T_total** | Masa digunakan (termasuk penalti masa) |
| **cp** | Bilangan checkpoint berjaya dilepasi |
| **Total Checkpoints** | Jumlah checkpoint dalam litar |
| **P** | Jumlah markah penalti |
| **Ks** | Pekali kaedah pengecaman (1.0 atau 1.3) |

---

## 8. Penalti Masa (Ditambah pada T_total)

### Langkauan Checkpoint
$$T_{total} \mathrel{+}= 0.5 \times \frac{T_{stage}}{Number\ of\ all\ checkpoints}\ \text{(s)}$$

### Komponen Terjatuh Semasa Permainan
$$T_{total} \mathrel{+}= 0.2 \times \frac{T_{stage}}{Number\ of\ all\ checkpoints}\ \text{(s)}$$

---

## 9. Sumbangan AUD kepada Markah Keseluruhan

$$S_T = (S_{AR} + S_{AUD}) \times K_a$$

### Pekali Tahap Autonomi (Ka)

| Tahap Autonomi | Pekali | Penerangan |
|---|---|---|
| **Onboard** | **1.0** | Semua pemprosesan di SBC dalam kereta |
| Bercampur | 0.75 | Sebahagian onboard, sebahagian offboard |
| Offboard | 0.5 | Pemprosesan di komputer luar |

---

## 10. Contoh Pengiraan Urban Driving

**Andaian:**
- T_stage = 180 saat
- Jumlah checkpoint = 6
- Pasukan lepasi 5 checkpoint
- Masa asal digunakan = 150 saat
- Tiada langkau checkpoint, tiada komponen jatuh
- 1 kali tidak berhenti di persimpangan (−10)
- Menggunakan **simbol visual** (Ks = 1.3)
- Onboard processing penuh (Ka = 1.0)

**Pengiraan:**
$$S_{AUD} = \left(\left(100 \times \left(1 - \frac{150}{180}\right) \times \frac{5}{6}\right) - 10\right) \times 1.3$$
$$S_{AUD} = \left((100 \times 0.1667 \times 0.8333) - 10\right) \times 1.3$$
$$S_{AUD} = (13.89 - 10) \times 1.3 = 5.06\ \text{mata}$$

**Jika menggunakan April Tag (Ks = 1.0) dalam senario sama:**
$$S_{AUD} = 3.89 \times 1.0 = 3.89\ \text{mata}$$

> 📊 Perbezaan ~30% antara kedua-dua kaedah pengecaman.

---

## 11. Istilah Penting

| Istilah | Pengertian |
|---|---|
| **Checkpoint** | Kawasan di persimpangan 3 atau 4 |
| **Final Point** | Kawasan kereta perlu berhenti di papan tanda "Berhenti" |
| **Start Point** | Kawasan pemain meletakkan kereta dan kereta mula bergerak |
| **Onboard** | Pemprosesan & pengecaman dalam SBC kereta |
| **Offboard** | Pemprosesan & pengecaman di luar SBC (komputer luar) |
| **SBC** | Single Board Computer (Raspberry Pi, Jetson, Intel NUC, dll.) |

---

## 12. Pelanggaran Peraturan (Disingkir)

- Tidak hadir 3 minit selepas masa pertandingan
- Tidak mematuhi spesifikasi kereta
- Berkongsi kereta dengan pasukan lain
- Tingkah laku tiada semangat kesukanan
- Merosakkan permukaan gelanggang
- Kereta tidak berfungsi semasa pemeriksaan

> ⚠️ **Keputusan pengadil adalah muktamad. Tiada rayuan.**

---

## 13. Tips Strategi Urban Driving

1. **Gunakan simbol visual** (bukan April Tag) untuk bonus Ks = 1.3
2. **Onboard processing** untuk Ka = 1.0
3. **Elakkan penalti −25** – classifier untuk papan tanda mesti tepat
4. **Pastikan stop sign detection robust** – penalti −10 untuk setiap miss
5. **Algoritma lane-keeping** mesti pastikan kereta di lorong kanan ketika checkpoint
6. **Latih model CV** dengan dataset papan tanda dari GitHub yang disediakan
7. **Kombinasi sensor** – kamera (papan tanda) + LiDAR/ToF (halangan & lorong)
8. **Pertimbangkan trade-off:** April Tag lebih reliable tapi markah rendah; simbol berisiko tapi markah tinggi
9. **Permukaan tidak rata** – pertimbangkan suspension dan stabilization
10. **Test pada permukaan banner** sebelum hari pertandingan untuk biasakan friction & getaran
