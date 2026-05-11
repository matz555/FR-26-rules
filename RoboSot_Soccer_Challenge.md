# RoboSot Soccer Challenge 2026

**Source:** MALAYSIA FIRA ROBOWORLD CUP — Rules and Regulation for 2026 (Version 2026/210, 23 Jan 2026)

---

## Tujuan Cabaran

Memaparkan keupayaan platform RoboSot dalam:
- **Dynamic obstacles avoidance**
- **Passing capability**

---

## Challenge Format

- Format: **league**.
- Robot setiap pasukan mesti perform **penalty task** dalam permainan bola sepak.
- Setiap pasukan letak **2 hingga 3 robot** di padang.
- **Minimum 4 pasukan** diperlukan untuk perform cabaran ini.

---

## Testing Time

- **5 minit setup time** untuk setiap pasukan sebelum permainan.

---

## Team Specification

### Human Player
- Hanya **4 ahli pasukan** dibenarkan terlibat aktif dalam perlawanan.

### Team Size
- 2 pasukan, setiap pasukan **2 atau 3 robot**.
- Robot tambahan boleh digunakan sebagai **substitute** (mesti diverifikasi semasa robot verification session).

### Goalkeeper
- Hanya **SATU robot** boleh ditetapkan sebagai goalkeeper.
- Goalkeeper hanya boleh **tangkap/pegang bola** jika bola **dalam goal area sendiri** (goal box + goal post).

### Remote Computer
- Setiap pasukan dibenarkan **satu** remote computer/control untuk hantar maklumat prosedur semasa stoppage.

---

## Robot Specification

- Saiz maksimum: **24cm (W) × 24cm (L) × 40cm (H)** dalam resting state.
- **Goalkeeper** mesti **TIDAK melebihi saiz maksimum** apabila **fully extended** — untuk elakkan goalkeeper menutup penuh goalpost.
- Semua aksesori (gripper, kicker) mesti **dipasang penuh** semasa verifikasi.
- **Tiada aksesori tambahan** boleh ditambah semasa gameplay.
- **Color patch** (magenta atau cyan) dibenarkan untuk pengenalan robot.
- Pasukan boleh main dengan **1 atau 2 robot** dalam setiap perlawanan.
- **Bola mesti sekurang-kurangnya 50% kelihatan** dari pandangan atas pada bila-bila masa.
  - Robot yang menyembunyikan/menutup bola terlalu banyak → dihukum kerana **illegal possession**.

### Ball Handling Rules

- **TIADA robot** boleh **grip atau pegang** bola dengan mana-mana mekanisme (jari, suction, magnet, dll.) semasa permainan normal.
- Robot hanya boleh kawal bola melalui:
  - **Dribbling**: sentuhan lembut berterusan (cth. dengan permukaan melengkung).
  - **Kicking**: impak diskret untuk lonjak bola.
- **Goalkeeper exception**: goalkeeper boleh grip dan pegang bola **HANYA** jika **seluruh robot dalam goal area sendiri** (goal box + goal post).
- **Valid dribble**: dribble sah hanya jika pihak lawan boleh **dislodge** bola dengan **satu sepakan atau sentuhan**.

### Definisi Penting
- **Gripping**: tindakan di mana bola sepenuhnya tertutup atau dipegang sehingga menghalang pihak lain dari kawal.
- **Dribbling**: mesti membenarkan **fair contestability** — robot lawan mesti boleh cabar dan ambil bola.

### Peranan Defending Robots
- Peranan defending robots boleh **ditukar semasa permainan**.
- Tetapi **goalkeeper aktif** mesti **sentiasa patuh saiz maksimum** semasa gameplay.

---

## Autonomous System

- Setiap robot mesti **fully independent**:
  - Vision system, kuasa, mekanisme motor — kesemuanya **self-contained**.
  - Image processing **MESTI dilakukan onboard**.
  - **TIADA global vision system** dibenarkan.

---

## Robot Behavior

- Robot mesti **autonomously**:
  - Navigate di padang
  - Ikut bola
- Robot yang tidak boleh lakukan ini → **lose qualification** dalam RoboSot Soccer Competition.

---

## Communication

### Remote Computer Control
- Boleh digunakan untuk hantar maklumat prosedur semasa stoppage atau atas arahan referee.
- Arahan yang dibenarkan:
  - **Start/Stop Commands**
  - **Formation/Setup Commands**
- **TIDAK BOLEH** hantar **positional data** — positional data mesti disimpan dalam robot sahaja.
- Dilaksana melalui butang remote atau key-press/mouse click pada host computer.
- Contoh penggunaan: automasi positioning robot untuk kick-offs dan penalty kicks.
- Pasukan **mesti boleh** start/stop robot dari remote.
- Fungsi formation/setup digalakkan tapi **tidak wajib**.
- Repositioning boleh dilakukan oleh **human handler** sebagai alternatif.
- Fungsi ini transitional — akhirnya diharapkan automasi melalui cue dari manusia atau remote electronic referee.

### Inter-Robot Communication
- Robot bebas hantar maklumat antara satu sama lain.

---

## Challenge Setup

- Referee sediakan **game match table** sebelum cabaran.
- Pasukan mesti berjumpa **≥15 minit sebelum** perlawanan untuk selesaikan isu komunikasi dan color patch.
- **Communications**: konflik komunikasi mesti diselesaikan **SEBELUM coin toss** (terutamanya RF communication).
- **Color Patches**: konflik antara pasukan diselesaikan dengan **coin toss** oleh referee.
- **Coin Toss**: dijalankan sebelum permainan; pemenang pilih pihak menyerang separuh masa pertama.

### Game Duration (dari Section Challenge Setup)
- 2 separuh masa, **5 minit setiap satu**, dengan **5 minit interval separuh masa**.
- Timekeeper rasmi hentikan jam semasa substitution, transport robot tercedera, time-out, dll.

### Jika Pasukan Tidak Sedia Selepas Separuh Masa
- **5 minit tambahan** dibenarkan.
- Masih tidak sedia → **disqualified**.

### Jika Lebih 4 Pasukan
- Head Referee boleh lantik ahli pasukan sebagai referee dan timekeeper.
- Gagal melakukan tugas → **disqualified untuk perlawanan seterusnya**.

> ⚠️ **PERCANGGAHAN DALAM DOKUMEN ASAL**: Section "Challenge Setup" sebut **5 minit per half / 5 minit halftime**, tetapi Section "Gameplay i" sebut **15 minit per half / 10 minit halftime**. Sila sahkan dengan referee semasa briefing.

---

## Gameplay

### i. Game Duration (dari Section Gameplay)
- Perlawanan: **2 separuh masa sama**, setiap satu **15 minit**.
- **Halftime interval**: **10 minit** (rehat, strategi, substitution).
- Timekeeper rasmi dilantik untuk setiap perlawanan.
- Jam dihentikan semasa:
  - Substitution
  - Transport/bantuan robot tercedera/rosak
  - Time-out rasmi diisytiharkan
  - Situasi lain mengikut budi bicara referee/timekeeper
- Jika pasukan tidak sedia selepas halftime → **5 minit grace period**. Masih tidak sedia → **disqualified**, pasukan lawan menang.

### ii. Kick-Offs
- Permulaan perlawanan dan **selepas setiap gol** → restart dengan **kick-off**.
- **Pasukan kick-off**:
  - Boleh posisikan robot **bebas dalam separuh padang sendiri** ATAU **dalam centre circle**.
- **Pasukan bertahan**:
  - Boleh posisikan robot di mana-mana **dalam separuh padang sendiri**.
  - **TIADA robot bertahan** dibenarkan **dalam centre circle** sebelum kick-off.
- Robot boleh diposisikan **manual oleh human handler** atau **autonomous via remote computer**.
- Bola mesti diletak **tepat di pusat centre circle** sebelum permainan bermula.
- Selepas isyarat referee, kick-off mesti dibuat:
  - Bola mesti disepak/dihantar ke **separuh padang pasukan kick-off sendiri**.
  - Mesti dilakukan dalam **5 saat** dari isyarat referee.
  - Gagal → **possession diberi kepada pasukan lawan**.

### iii. Winning the Game
- **Gol sah**: seluruh bola melepasi goal line (di atas tanah atau udara) antara goalpost dan di bawah crossbar.
- Pemenang ditentukan oleh jumlah gol semasa regulation time.
- **Mercy Rule**: jika satu pasukan memimpin **lebih 10 gol** pada bila-bila masa → perlawanan tamat serta-merta, pasukan tersebut menang.
- Jika seri selepas separuh masa kedua → **extra time** selepas **5 minit rehat**:
  - Extra time = **10 minit**.
- Jika masih seri selepas extra time → **penalty shootout**:
  - Setiap pasukan ambil **3 penalty kicks** mengikut prosedur penalty khas.
  - Hanya **kicker (penyerang)** dan **goalkeeper (defending)** di padang semasa penalty.
- **Penalty kick lengkap** apabila salah satu syarat berikut dipenuhi:
  - Goalkeeper kawal bola dalam penalty area
  - Bola keluar dari penalty area
  - **30 saat** berlalu sejak penalty diambil
- Jika masih seri selepas 3 penalty kicks → **penalty kicks tambahan** bergilir-gilir sehingga pemenang ditentukan.

### iv. Interruptions
- Interruptions = peristiwa khusus yang memerlukan tindakan prosedur:
  - Substitution
  - Repair
  - Foul
  - Robot tumbang (fallen robots)
- Jam permainan **terus berjalan** semasa interruption.
- **Kecuali** team manager minta time-out dan referee meluluskan.

### v. Substitutions
- **Maksimum 3 substitutions** semasa permainan aktif.
- **Unlimited substitutions** semasa halftime.
- Semasa perlawanan: substitution hanya boleh dilakukan semasa **interruption atau foul**.
- Team manager mesti panggil **"Substitution"** untuk maklumkan referee.
- Substitution boleh segera atau ditangguhkan ke interruption/foul seterusnya.
- **Jam TIDAK dihentikan** semasa substitution.
- Selepas substitution, perlawanan restart dengan semua robot dan bola di **posisi tepat seperti semasa interruption**.

### vi. Repair and Stop
- **Maksimum 2 repair time-outs** setiap perlawanan.
- Repair hanya boleh dimohon semasa interruption atau foul.
- Team manager mesti panggil **"Time-out"** dan dapat kelulusan referee.
- Setelah diluluskan: pasukan diberi **maksimum 2 minit** untuk repair.
- **Jam DIHENTIKAN** semasa repair time-out rasmi.
- Selepas tempoh repair (sama ada selesai atau tidak):
  - Permainan disambung semula dengan robot dan bola di **posisi asal interruption**.

### vii. Fallen Robot
- Jika robot tumbang dan menghalang gol, menghalang permainan, atau memberi kesan signifikan → referee henti perlawanan segera.
- Semua robot lain mesti **berhenti bergerak**.
- Robot tumbang:
  - Dikembalikan ke kedudukan tegak, ATAU
  - Dialihkan dari padang jika rosak/tidak boleh teruskan dengan selamat.
- Bola diletak di **lokasi asal semasa permainan dihentikan**.
- Selepas isyarat referee, permainan disambung dan semua robot boleh bergerak bebas.

### viii. A Goal Is Scored
- Selepas setiap gol → semua robot diposisikan untuk **kick-off seterusnya** mengikut peraturan kick-off.
- **Jam TIDAK dihentikan** selepas gol.

### ix. Ball Outside the Field
- Bola sepenuhnya melepasi **goal line** selepas sentuhan terakhir robot **menyerang** → **goal kick** kepada pasukan bertahan.
- Bola melepasi goal line selepas sentuhan terakhir robot **bertahan** tanpa gol → **corner kick** kepada pasukan menyerang.
- Bola sepenuhnya melepasi **sideline** (di tanah atau udara) → **throw-in** kepada pasukan lawan.

### x. Stalemate
- **Stalemate** = bola statik **lebih 5 saat** dan tiada robot boleh mainkan.
- **Stalemate dalam penalty area**:
  - **Free kick** kepada pasukan bertahan.
  - Pasukan menyerang mesti kekal di **belakang halfway line**.
  - Referee boleh kurangkan waktu menunggu kepada **3 saat** untuk pantaskan permainan.
- **Stalemate di luar penalty area**:
  - **Free kick** kepada pasukan menyerang.
  - Peraturan free balls dan free kicks dikuatkuasakan.

---

## Fouls

### i. Handling
- **Handling** = situasi di mana robot (selain goalkeeper dalam penalty area sendiri) kawal bola dengan cara haram.
- **Semasa bergerak**: robot **TIDAK BOLEH** pegang/perangkap/kelilingi **lebih ⅓ diameter bola** dari mana-mana sudut.
- **Semasa pegun**: robot **TIDAK BOLEH** pegang **lebih ½ diameter bola**.
- Bola mesti boleh **berguling secara semula jadi** semasa dikawal.
- Pelanggaran:
  - Foul **di luar penalty area** → **free kick** kepada pasukan lawan.
  - Foul **di dalam penalty area** → **penalty kick**.
- **Goalkeeper exemption**: hanya semasa dalam penalty area sendiri.

### ii. Collision
- Apabila 2 robot bertentangan bersaing untuk bola (kedua-dua menyentuh bola) → **TIADA robot lain** boleh campur tangan. Robot ketiga yang membantu/menggganggu = **foul**.
- Jika **lebih dari 1 robot** cuba intercept/block robot lawan yang sedang dribble:
  - Jika menyebabkan kelebihan tidak adil atau obstruction → **foul** kepada pasukan yang dianiaya.
- **Sebarang perlanggaran** (sengaja atau tidak) boleh dihukum jika menjejaskan permainan atau berisiko kerosakan:
  - Perlanggaran **di luar penalty area** → **free kick**.
  - Perlanggaran **di dalam penalty area** → **penalty kick**.
- Robot **dibenarkan** tolak bola **DAN** robot lawan ke belakang **HANYA JIKA** robot menolak kekal dalam **sentuhan berterusan dengan bola**.

### iii. Kicking
- Semua robot mesti direka dan diprogram supaya tendangan boleh dilakukan dengan **selamat**, tanpa mengancam/merosakkan robot lain.
- Robot mesti elak menendang robot lain. Jika sentuhan tidak boleh dielak → mesti **lembut dan terkawal**.
- Jika mekanisme tendangan terlalu kuat/tidak selamat:
  - Foul dipanggil, robot **dikeluarkan sementara dari padang**.
  - Pasukan diberi **maksimum 10 minit** untuk repair/modify.
  - Tidak selesai dalam tempoh ini → robot **TIDAK dibenarkan masuk semula** perlawanan.
- **Nota**: Lebih kuat mekanisme kick → lebih halus dan tepat kawalan pengaktifannya perlu.

### iv. Goalkeeper Push
- Robot menyerang **boleh tolak goalkeeper** **HANYA JIKA** bola antara robot menyerang dan goalkeeper.
- **DILARANG** tolak goalkeeper **bersama bola** masuk ke gol.
- Selepas sentuhan awal di depan goalkeeper → robot menyerang mesti **SEGERA bergerak menjauh**.
- Jika goalkeeper terus ditolak/dipaksa masuk gol/penalty area secara haram → **foul** dan **free kick** kepada pasukan bertahan.

### v. Obstruction
- **HANYA goalkeeper** dibenarkan dalam goal area pasukan bertahan.
- Jika robot bertahan tambahan masuk/kekal dalam goal area semasa bola dalam penalty area sendiri:
  - **Obstruction foul** → **penalty kick** kepada pasukan menyerang.

### vi. Offside
- **HANYA 1 robot menyerang** dibenarkan dalam goal area lawan.
- Robot menyerang kedua masuk → **offside** → **free kick** kepada pasukan bertahan.

### vii. Touch
- Jika ahli pasukan manusia sentuh robot **tanpa kebenaran referee** → foul → **penalty kick** kepada pasukan lawan.
- Hanya **1 orang per pasukan** dibenarkan sentuh robot, dan hanya dengan kebenaran eksplisit referee.

### viii. Illegal Collision
- Perlanggaran yang menyebabkan robot:
  - Hilang possession bola
  - Tumbang
  - Terbalikkan robot lain
- → **Illegal collision** → dihukum oleh referee.

---

## Actions (Restart Procedures)

### Peraturan Umum Restart
- Repositioning boleh dilakukan secara manual oleh human handler atau autonomous via remote control.
- Semua robot tidak terlibat langsung dalam restart mesti **≥1.2 meter** dari bola.
- Permainan disambung **selepas whistle** referee.

### i. Free Kick
- Semua robot **berhenti bergerak**.
- Bola diletak di **free-ball mark terdekat**.
- Pasukan diberi free kick: 1 robot boleh diposisikan **berhampiran bola**.
- Robot lain: **kekal ≥1.2m** dari bola.
- Selepas whistle: permainan disambung, semua robot bebas bergerak.

### ii. Penalty Kick
- Semua robot **berhenti**.
- Bola diletak di **pusat penalty arc** sepadan kawasan penalty di mana foul berlaku.
- Pasukan menyerang: posisikan **1 robot kicker di belakang bola**.
- Robot lain (menyerang dan bertahan): mesti **di belakang halfway line**.
- Pasukan bertahan: posisikan **goalkeeper di mana-mana dalam penalty area**, asalkan **sekurang-kurangnya satu bahagian goalkeeper sentuh goal line**.
- Selepas whistle: permainan disambung.
- Jika gol → restart dengan **kick-off**.
- Prosedur ini memastikan kelebihan pasukan menyerang dan memantaskan proses penalty.

### iii. Free Ball
- Bola diposisikan secepat mungkin di **free-ball mark terdekat**.
- Semua robot kekal **≥1.2m** dari bola sehingga whistle.

### iv. Throw-In
- Cara restart bila bola sepenuhnya melepasi **sideline**.
- **Gol TIDAK boleh** dijaringkan terus dari throw-in.
- Referee/assistant ambil dan kembalikan bola **secepat mungkin**.
- **TIADA stoppage jam permainan.**
- Robot bertahan mesti:
  - **≥1.2m** dari **thrower**
  - **≥0.7m** dari **receiver**
- Thrower **TIDAK BOLEH** sentuh bola lagi sehingga bola disentuh robot lain.
- Bola dalam permainan sebaik sahaja masuk semula padang.

### v. Corner Kick
- Diberi apabila bola lepasi goal line selepas sentuhan terakhir robot **bertahan** tanpa gol.
- Bola diletak **dalam corner arc** di penjuru terdekat.
- Robot pasukan menyerang ambil tendangan.
- Semua robot bertahan: **≥1.2m** dari bola, kekal **di belakang boundary line** yang ditetapkan.
- Kicker **TIDAK BOLEH** sentuh bola lagi sehingga disentuh robot lain.
- Bola dalam permainan sebaik ditendang dan bergerak.
- **Gol BOLEH** dijaringkan terus dari corner kick.

### vi. Goal Kick
1. Diberi apabila bola lepasi goal line selepas sentuhan terakhir robot **menyerang** tanpa gol. Gol boleh dijaringkan terus dari goal kick **tetapi hanya melawan pasukan lawan**.
2. Pasukan bertahan ambil tendangan dari **mana-mana posisi dalam penalty area**. Robot lawan mesti **di luar penalty area** sehingga bola dalam permainan. Bola dalam permainan apabila ditendang dan bergerak **melepasi penalty area**.
3. Apabila goal kick diberi: semua robot **berhenti**. Semua robot kecuali goalkeeper bertahan mesti **tinggalkan goal area**. Pasukan bertahan boleh posisikan bola dan goalkeeper **bebas dalam goal area**. Selepas whistle: permainan disambung.

---

## Game Field Specification

| Spesifikasi | Nilai |
|---|---|
| Saiz padang | **8m × 6m** (tidak termasuk ketebalan garisan) |
| Material | 100% fiber synthetic needle punch carpet |
| Warna | Light green |

### Markings dari Figure 6 (Soccer Game Field)
- 50cm, 50cm
- R40cm (centre circle radius)
- 100cm
- R20cm (corner arc radius)
- 300cm, 130cm
- 50cm, 30cm
- 100cm, 130cm, 30cm
- 400cm

---

## Goal Post Specification

(Sama seperti Avoidance Challenge)

| Spesifikasi | Nilai |
|---|---|
| Saiz | **1.0m × 0.3m × 0.5m** (W × D × H) |
| Crossbar | 3cm × 3cm di atas goal box (putih) |
| Material rangka | Wooden (white) |
| Warna dalaman goal | **Navy blue dan Yellow** (1 set setiap satu) |

---

## Ball Specification

| Spesifikasi | Nilai |
|---|---|
| Jenis | Stress Therapy Foam Balls |
| Material | Rubber |
| Saiz | 6.5 mm – 6.6 mm *(dokumen rujukan menyatakan 6.5–6.6 mm dalam Section 5, dan 6.6 cm di Section 2.4 — kemungkinan typo; saiz sebenar ~6.6 cm/66mm — sahkan dengan referee)* |
| Warna | Oren |

---

## Scoring System

### Skor Asas
- **Gol sah**: sekurang-kurangnya **separuh bola** lepasi goal line (dinilai referee). **1 mata** setiap gol sah.
- **Gol tidak sah**: bola disepak **melebihi goal bar** (out of boundary).

### Foul + Gol Serentak
- Jika gol sah berlaku **serentak** dengan foul → **gol DIKIRA**, kemudian foul dilaksana.
- Jika gol sah berlaku **selepas** foul → **gol TIDAK dikira**.

### Bantahan Keputusan Referee
- Mesti dibuat **sebelum permainan disambung**.
- Mesti disertakan **bukti video jelas**.
- Bantahan selepas permainan disambung → **TIDAK dilayan**. Keputusan referee adalah **muktamad**.

### Mercy Rule
- Pasukan menang dengan **>10 mata** → terus diisytihar pemenang cabaran.

### Match Points (League Format)

| Keputusan | Mata |
|---|---|
| **Menang** | 3 mata |
| **Seri** | 1 mata setiap pasukan |
| **Kalah** | 0 mata |

- Pasukan dengan **mata tertinggi** = pemenang cabaran.

---

## Tie-breaker

1. **Most goals obtained** dalam cabaran.
2. Jika sama: **biggest score difference** dalam mana-mana perlawanan dimainkan.

---

## Peraturan Umum (Berkaitan)

- **Melintasi garisan/sempadan** = sekurang-kurangnya **50% robot atau bola** melintasi garisan.
- **Soccer Challenge MEMBENARKAN** robot melintasi sempadan dan kembali ke padang.
- Verifikasi robot wajib. Gagal verifikasi → **24 jam** untuk pengubahsuaian + **15 minit window** akhir. Gagal lagi → **disqualified**.
- Pasukan mesti tiba **1 jam sebelum** cabaran untuk inspeksi robot.
