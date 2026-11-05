# Perhitungan Subnetting CIDR — Cisco Packet Tracer

Topologi: **Serial Experiments Lain, Layer:04 — The Wired Expansion** (`problems/topo-modul4-cpt.png`).
Metode: **CIDR** di CPT, sehingga GNS3 memakai VLSM.

Prefix IP kelompok K-46: `192.234`.

## Langkah 1 — Labelling subnet dan netmask

Aturan soal: **Jumlah IP = jumlah host + jumlah interface router pada subnet tersebut.**

Netmask dipilih paling kecil yang host usable-nya (`2^(32-n) - 2`) masih cukup untuk Jumlah IP.
Link Cloud–Cyberia tidak dihitung sebagai subnet, sesuai template yang memulai A1 dari LAN Cyberia dan A2 dari Cyberia > Wired.

| Subnet | Rute | Host | Iface router | Jumlah IP | Netmask | IP usable |
|---|---|--:|--:|--:|:-:|--:|
| A1 | Cyberia > Switch1 > JJ, MyuMyu | 125 | 1 | 126 | /25 | 126 |
| A2 | Cyberia > Wired | 0 | 2 | 2 | /30 | 2 |
| A3 | Wired > Switch2 > Psyche, Copland, Schumann | 0 | 4 | 4 | /29 | 6 |
| A4 | Psyche > Switch3 > Karl, Lin | 880 | 1 | 881 | /22 | 1022 |
| A5 | Copland > Juri | 31 | 1 | 32 | /26 | 62 |
| A6 | Copland > Switch4 > Masayuki | 300 | 1 | 301 | /23 | 510 |
| A7 | Schumann > Akashic | 3 | 1 | 4 | /29 | 6 |
| A8 | Schumann > Accela | 0 | 2 | 2 | /30 | 2 |
| A9 | Accela > Switch5 > Omnipresence, Denpa | 513 | 1 | 514 | /22 | 1022 |
| A10 | Wired > Navi | 0 | 2 | 2 | /30 | 2 |
| A11 | Navi > Switch6 > Alice, Reika | 253 | 1 | 254 | /24 | 254 |
| A12 | Navi > Switch7 > Taro | 57 | 1 | 58 | /26 | 62 |
| A13 | Navi > Switch8 > Yasuo, Miho, Kuma | 142 | 1 | 143 | /24 | 254 |
| A14 | Wired > Tachibana | 0 | 2 | 2 | /30 | 2 |
| A15 | Tachibana > Switch9 > Collective, Ghost | 266 | 1 | 267 | /23 | 510 |
| A16 | Tachibana > Deus | 0 | 2 | 2 | /30 | 2 |
| A17 | Deus > Switch10 > Lilith, Specter | 98 | 1 | 99 | /25 | 126 |
| A18 | Deus > Protocol7 | 0 | 2 | 2 | /30 | 2 |
| A19 | Protocol7 > Switch11 > Chisa, Hodgeson | 32 | 1 | 33 | /26 | 62 |
| A20 | Protocol7 > Knights | 0 | 2 | 2 | /30 | 2 |
| A21 | Knights > Eiri | 2 | 1 | 3 | /29 | 6 |
| A22 | Knights > Phantoma | 0 | 2 | 2 | /30 | 2 |
| A23 | Phantoma > Switch12 > Semi | 14 | 1 | 15 | /27 | 30 |
| A24 | Phantoma > Shibuya | 0 | 2 | 2 | /30 | 2 |
| A25 | Shibuya > Switch13 > Lain, Mika | 61 | 1 | 62 | /26 | 62 |
| **Total** | | | | **2814** | **/19** | |

Subnet yang rawan salah hitung:

- **A5**: 31 + 1 = 32 IP. /27 hanya punya 30 usable, jadi harus /26.
- **A9**: 513 + 1 = 514 IP. /23 hanya punya 510 usable, jadi harus /22.
- **A21**: 2 + 1 = 3 IP. /30 hanya punya 2 usable, jadi harus /29.
- **A23**: 14 + 1 = 15 IP. /28 hanya punya 14 usable, jadi harus /27.
- **A3**: 4 interface router tanpa host, jadi /29.
- **A1, A11, A25**: pas penuh (126/126, 254/254, 62/62).

**Batas bawah root:** total ukuran blok seluruh subnet = 4188 alamat. Nilai ini melebihi /20 (4096) dan muat di /19 (8192).
Jadi /19 adalah root terkecil yang mungkin, dan penggabungan di bawah mencapainya.

## Langkah 2 — Penggabungan subnet

Aturan modul: **netmask gabungan = 1 tingkat di atas subnet terbesar yang digabungkan.**
Contoh: A23 (/27) + A24 (/30) menjadi /26.

Penggabungan dimulai dari subnet terjauh dari Cloud, lalu naik sampai mencakup seluruh topologi.
Agar root tetap /19, subnet kecil yang bersebelahan digabung dulu sebelum masuk ke subnet besar.
Alasannya: setiap kali subnet besar digabung, ukurannya berlipat dua, jadi jumlah penggabungan yang melibatkan subnet besar harus sesedikit mungkin.

### Penggabungan I

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| B1 | A23 | /27 | A24 | /30 | **/26** | Phantoma |
| B2 | A21 | /29 | A22 | /30 | **/28** | Knights |
| B3 | A18 | /30 | A16 | /30 | **/29** | link Tachibana–Deus–Protocol7 |
| B4 | A7 | /29 | A8 | /30 | **/28** | Schumann |
| B5 | A6 | /23 | A5 | /26 | **/22** | Copland |
| B6 | A11 | /24 | A13 | /24 | **/23** | LAN besar Navi |
| B7 | A12 | /26 | A10 | /30 | **/25** | Navi |
| B8 | A1 | /25 | A2 | /30 | **/24** | Cyberia |

### Penggabungan II

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| C1 | A25 | /26 | B1 | /26 | **/25** | Phantoma + Shibuya |
| C2 | B2 | /28 | A20 | /30 | **/27** | Knights + link ke Protocol7 |
| C3 | B3 | /29 | A14 | /30 | **/28** | link Wired–Tachibana–Deus–Protocol7 |
| C4 | B4 | /28 | A3 | /29 | **/27** | Schumann + Switch2 |
| C5 | A4 | /22 | B5 | /22 | **/21** | Psyche + Copland |
| C6 | B6 | /23 | B7 | /25 | **/22** | seluruh Navi |

### Penggabungan III

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| D1 | A19 | /26 | C2 | /27 | **/25** | Protocol7 + Knights |
| D2 | A17 | /25 | C3 | /28 | **/24** | Deus + link |
| D3 | A9 | /22 | C4 | /27 | **/21** | Schumann + Accela |

### Penggabungan IV

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| E1 | C1 | /25 | D1 | /25 | **/24** | Protocol7 ke bawah |
| E2 | C5 | /21 | D3 | /21 | **/20** | seluruh sisi kiri Wired |

### Penggabungan V

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| F1 | D2 | /24 | E1 | /24 | **/23** | Deus ke bawah |

### Penggabungan VI

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| G1 | A15 | /23 | F1 | /23 | **/22** | Tachibana ke bawah |

### Penggabungan VII

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| H1 | C6 | /22 | G1 | /22 | **/21** | Navi + Tachibana |

### Penggabungan VIII

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| I1 | H1 | /21 | B8 | /24 | **/20** | seluruh sisi kanan + Cyberia |

### Penggabungan IX

| Subnet | Gabungan dari | Netmask | Gabungan dari | Netmask | Netmask akhir | Cakupan |
|---|---|:-:|---|:-:|:-:|---|
| J1 | E2 | /20 | I1 | /20 | **/19** | seluruh topologi |

Hasil akhir: **J1 = /19**, dengan NID `192.234.0.0` dan netmask `255.255.224.0`.

## Langkah 3 — Pohon pembagian IP

Cara menurunkan pohon, dari root ke bawah:

1. Anak pertama (blok lebih besar) memakai NID induknya.
2. Anak kedua memakai NID di **tengah** blok induk, yaitu NID induk + setengah ukuran induk.
3. Netmask tiap anak mengikuti hasil penggabungan, bukan selalu induk + 1. Inilah pembeda pohon CIDR dari VLSM.

Contoh: J1 `192.234.0.0/19` berukuran 8192 alamat, setengahnya 4096 = 16 pada oktet ketiga.
Maka E2 mendapat `192.234.0.0/20` dan I1 mendapat `192.234.16.0/20`.

```
J1  192.234.0.0/19
├─ E2  192.234.0.0/20
│  ├─ C5  192.234.0.0/21
│  │  ├─ A4  192.234.0.0/22
│  │  └─ B5  192.234.4.0/22
│  │     ├─ A6  192.234.4.0/23
│  │     └─ A5  192.234.6.0/26
│  └─ D3  192.234.8.0/21
│     ├─ A9  192.234.8.0/22
│     └─ C4  192.234.12.0/27
│        ├─ B4  192.234.12.0/28
│        │  ├─ A7  192.234.12.0/29
│        │  └─ A8  192.234.12.8/30
│        └─ A3  192.234.12.16/29
└─ I1  192.234.16.0/20
   ├─ H1  192.234.16.0/21
   │  ├─ C6  192.234.16.0/22
   │  │  ├─ B6  192.234.16.0/23
   │  │  │  ├─ A11  192.234.16.0/24
   │  │  │  └─ A13  192.234.17.0/24
   │  │  └─ B7  192.234.18.0/25
   │  │     ├─ A12  192.234.18.0/26
   │  │     └─ A10  192.234.18.64/30
   │  └─ G1  192.234.20.0/22
   │     ├─ A15  192.234.20.0/23
   │     └─ F1  192.234.22.0/23
   │        ├─ D2  192.234.22.0/24
   │        │  ├─ A17  192.234.22.0/25
   │        │  └─ C3  192.234.22.128/28
   │        │     ├─ B3  192.234.22.128/29
   │        │     │  ├─ A18  192.234.22.128/30
   │        │     │  └─ A16  192.234.22.132/30
   │        │     └─ A14  192.234.22.136/30
   │        └─ E1  192.234.23.0/24
   │           ├─ C1  192.234.23.0/25
   │           │  ├─ A25  192.234.23.0/26
   │           │  └─ B1  192.234.23.64/26
   │           │     ├─ A23  192.234.23.64/27
   │           │     └─ A24  192.234.23.96/30
   │           └─ D1  192.234.23.128/25
   │              ├─ A19  192.234.23.128/26
   │              └─ C2  192.234.23.192/27
   │                 ├─ B2  192.234.23.192/28
   │                 │  ├─ A21  192.234.23.192/29
   │                 │  └─ A22  192.234.23.200/30
   │                 └─ A20  192.234.23.208/30
   └─ B8  192.234.24.0/24
      ├─ A1  192.234.24.0/25
      └─ A2  192.234.24.128/30
```

## Langkah 4 — Hasil pembagian IP

| Subnet | Network ID | Netmask | Broadcast | Range IP |
|---|---|---|---|---|
| A1 (/25) | 192.234.24.0 | 255.255.255.128 | 192.234.24.127 | 192.234.24.1 – 192.234.24.126 |
| A2 (/30) | 192.234.24.128 | 255.255.255.252 | 192.234.24.131 | 192.234.24.129 – 192.234.24.130 |
| A3 (/29) | 192.234.12.16 | 255.255.255.248 | 192.234.12.23 | 192.234.12.17 – 192.234.12.22 |
| A4 (/22) | 192.234.0.0 | 255.255.252.0 | 192.234.3.255 | 192.234.0.1 – 192.234.3.254 |
| A5 (/26) | 192.234.6.0 | 255.255.255.192 | 192.234.6.63 | 192.234.6.1 – 192.234.6.62 |
| A6 (/23) | 192.234.4.0 | 255.255.254.0 | 192.234.5.255 | 192.234.4.1 – 192.234.5.254 |
| A7 (/29) | 192.234.12.0 | 255.255.255.248 | 192.234.12.7 | 192.234.12.1 – 192.234.12.6 |
| A8 (/30) | 192.234.12.8 | 255.255.255.252 | 192.234.12.11 | 192.234.12.9 – 192.234.12.10 |
| A9 (/22) | 192.234.8.0 | 255.255.252.0 | 192.234.11.255 | 192.234.8.1 – 192.234.11.254 |
| A10 (/30) | 192.234.18.64 | 255.255.255.252 | 192.234.18.67 | 192.234.18.65 – 192.234.18.66 |
| A11 (/24) | 192.234.16.0 | 255.255.255.0 | 192.234.16.255 | 192.234.16.1 – 192.234.16.254 |
| A12 (/26) | 192.234.18.0 | 255.255.255.192 | 192.234.18.63 | 192.234.18.1 – 192.234.18.62 |
| A13 (/24) | 192.234.17.0 | 255.255.255.0 | 192.234.17.255 | 192.234.17.1 – 192.234.17.254 |
| A14 (/30) | 192.234.22.136 | 255.255.255.252 | 192.234.22.139 | 192.234.22.137 – 192.234.22.138 |
| A15 (/23) | 192.234.20.0 | 255.255.254.0 | 192.234.21.255 | 192.234.20.1 – 192.234.21.254 |
| A16 (/30) | 192.234.22.132 | 255.255.255.252 | 192.234.22.135 | 192.234.22.133 – 192.234.22.134 |
| A17 (/25) | 192.234.22.0 | 255.255.255.128 | 192.234.22.127 | 192.234.22.1 – 192.234.22.126 |
| A18 (/30) | 192.234.22.128 | 255.255.255.252 | 192.234.22.131 | 192.234.22.129 – 192.234.22.130 |
| A19 (/26) | 192.234.23.128 | 255.255.255.192 | 192.234.23.191 | 192.234.23.129 – 192.234.23.190 |
| A20 (/30) | 192.234.23.208 | 255.255.255.252 | 192.234.23.211 | 192.234.23.209 – 192.234.23.210 |
| A21 (/29) | 192.234.23.192 | 255.255.255.248 | 192.234.23.199 | 192.234.23.193 – 192.234.23.198 |
| A22 (/30) | 192.234.23.200 | 255.255.255.252 | 192.234.23.203 | 192.234.23.201 – 192.234.23.202 |
| A23 (/27) | 192.234.23.64 | 255.255.255.224 | 192.234.23.95 | 192.234.23.65 – 192.234.23.94 |
| A24 (/30) | 192.234.23.96 | 255.255.255.252 | 192.234.23.99 | 192.234.23.97 – 192.234.23.98 |
| A25 (/26) | 192.234.23.0 | 255.255.255.192 | 192.234.23.63 | 192.234.23.1 – 192.234.23.62 |

## Langkah 5 — Alokasi IP per interface

Router memakai IP usable pertama pada subnetnya. Host memakai IP setelahnya dan menjadikan router sebagai gateway.
Tiap node mewakili seluruh host yang tertulis pada topologi.

Link `Cyberia Fa0/0` ke Cloud sengaja tanpa IP dari prefix, karena tidak termasuk subnet yang dihitung.

| Subnet | Node | Interface | IP | Netmask |
|---|---|---|---|---|
| A1 | Cyberia | Fa1/0 | 192.234.24.1 | 255.255.255.128 |
| A1 | JJ | Fa0 | 192.234.24.2 | 255.255.255.128 |
| A1 | MyuMyu | Fa0 | 192.234.24.3 | 255.255.255.128 |
| A2 | Cyberia | Fa0/1 | 192.234.24.129 | 255.255.255.252 |
| A2 | Wired | Fa0/0 | 192.234.24.130 | 255.255.255.252 |
| A3 | Wired | Fa0/1 | 192.234.12.17 | 255.255.255.248 |
| A3 | Psyche | Fa0/0 | 192.234.12.18 | 255.255.255.248 |
| A3 | Copland | Fa0/0 | 192.234.12.19 | 255.255.255.248 |
| A3 | Schumann | Fa0/0 | 192.234.12.20 | 255.255.255.248 |
| A4 | Psyche | Fa0/1 | 192.234.0.1 | 255.255.252.0 |
| A4 | Karl | Fa0 | 192.234.0.2 | 255.255.252.0 |
| A4 | Lin | Fa0 | 192.234.0.3 | 255.255.252.0 |
| A5 | Copland | Fa0/1 | 192.234.6.1 | 255.255.255.192 |
| A5 | Juri | Fa0 | 192.234.6.2 | 255.255.255.192 |
| A6 | Copland | Fa1/0 | 192.234.4.1 | 255.255.254.0 |
| A6 | Masayuki | Fa0 | 192.234.4.2 | 255.255.254.0 |
| A7 | Schumann | Fa0/1 | 192.234.12.1 | 255.255.255.248 |
| A7 | Akashic | Fa0 | 192.234.12.2 | 255.255.255.248 |
| A8 | Schumann | Fa1/0 | 192.234.12.9 | 255.255.255.252 |
| A8 | Accela | Fa0/0 | 192.234.12.10 | 255.255.255.252 |
| A9 | Accela | Fa0/1 | 192.234.8.1 | 255.255.252.0 |
| A9 | Omnipresence | Fa0 | 192.234.8.2 | 255.255.252.0 |
| A9 | Denpa | Fa0 | 192.234.8.3 | 255.255.252.0 |
| A10 | Wired | Fa1/0 | 192.234.18.65 | 255.255.255.252 |
| A10 | Navi | Fa0/0 | 192.234.18.66 | 255.255.255.252 |
| A11 | Navi | Fa0/1 | 192.234.16.1 | 255.255.255.0 |
| A11 | Alice | Fa0 | 192.234.16.2 | 255.255.255.0 |
| A11 | Reika | Fa0 | 192.234.16.3 | 255.255.255.0 |
| A12 | Navi | Fa1/0 | 192.234.18.1 | 255.255.255.192 |
| A12 | Taro | Fa0 | 192.234.18.2 | 255.255.255.192 |
| A13 | Navi | Fa1/1 | 192.234.17.1 | 255.255.255.0 |
| A13 | Yasuo | Fa0 | 192.234.17.2 | 255.255.255.0 |
| A13 | Miho | Fa0 | 192.234.17.3 | 255.255.255.0 |
| A13 | Kuma | Fa0 | 192.234.17.4 | 255.255.255.0 |
| A14 | Wired | Fa1/1 | 192.234.22.137 | 255.255.255.252 |
| A14 | Tachibana | Fa0/0 | 192.234.22.138 | 255.255.255.252 |
| A15 | Tachibana | Fa0/1 | 192.234.20.1 | 255.255.254.0 |
| A15 | Collective | Fa0 | 192.234.20.2 | 255.255.254.0 |
| A15 | Ghost | Fa0 | 192.234.20.3 | 255.255.254.0 |
| A16 | Tachibana | Fa1/0 | 192.234.22.133 | 255.255.255.252 |
| A16 | Deus | Fa0/0 | 192.234.22.134 | 255.255.255.252 |
| A17 | Deus | Fa0/1 | 192.234.22.1 | 255.255.255.128 |
| A17 | Lilith | Fa0 | 192.234.22.2 | 255.255.255.128 |
| A17 | Specter | Fa0 | 192.234.22.3 | 255.255.255.128 |
| A18 | Deus | Fa1/0 | 192.234.22.129 | 255.255.255.252 |
| A18 | Protocol7 | Fa0/0 | 192.234.22.130 | 255.255.255.252 |
| A19 | Protocol7 | Fa0/1 | 192.234.23.129 | 255.255.255.192 |
| A19 | Chisa | Fa0 | 192.234.23.130 | 255.255.255.192 |
| A19 | Hodgeson | Fa0 | 192.234.23.131 | 255.255.255.192 |
| A20 | Protocol7 | Fa1/0 | 192.234.23.209 | 255.255.255.252 |
| A20 | Knights | Fa0/0 | 192.234.23.210 | 255.255.255.252 |
| A21 | Knights | Fa0/1 | 192.234.23.193 | 255.255.255.248 |
| A21 | Eiri | Fa0 | 192.234.23.194 | 255.255.255.248 |
| A22 | Knights | Fa1/0 | 192.234.23.201 | 255.255.255.252 |
| A22 | Phantoma | Fa0/0 | 192.234.23.202 | 255.255.255.252 |
| A23 | Phantoma | Fa0/1 | 192.234.23.65 | 255.255.255.224 |
| A23 | Semi | Fa0 | 192.234.23.66 | 255.255.255.224 |
| A24 | Phantoma | Fa1/0 | 192.234.23.97 | 255.255.255.252 |
| A24 | Shibuya | Fa0/0 | 192.234.23.98 | 255.255.255.252 |
| A25 | Shibuya | Fa0/1 | 192.234.23.1 | 255.255.255.192 |
| A25 | Lain | Fa0 | 192.234.23.2 | 255.255.255.192 |
| A25 | Mika | Fa0 | 192.234.23.3 | 255.255.255.192 |

## Langkah 6 — Static route

Ada 24 route. Setiap router selain Cyberia punya satu default route ke arah Cyberia, ditambah satu route gabungan untuk tiap router tetangga di arah bawah.

| Router | Tujuan | Blok | Next hop |
|---|---|---|---|
| Cyberia | 192.234.0.0/19 | J1 | 192.234.24.130 |
| Wired | 0.0.0.0/0 | default | 192.234.24.129 |
| Wired | 192.234.0.0/22 | A4 | 192.234.12.18 |
| Wired | 192.234.4.0/22 | B5 | 192.234.12.19 |
| Wired | 192.234.8.0/21 | D3 | 192.234.12.20 |
| Wired | 192.234.16.0/22 | C6 | 192.234.18.66 |
| Wired | 192.234.20.0/22 | G1 | 192.234.22.138 |
| Psyche | 0.0.0.0/0 | default | 192.234.12.17 |
| Copland | 0.0.0.0/0 | default | 192.234.12.17 |
| Schumann | 0.0.0.0/0 | default | 192.234.12.17 |
| Schumann | 192.234.8.0/22 | A9 | 192.234.12.10 |
| Accela | 0.0.0.0/0 | default | 192.234.12.9 |
| Navi | 0.0.0.0/0 | default | 192.234.18.65 |
| Tachibana | 0.0.0.0/0 | default | 192.234.22.137 |
| Tachibana | 192.234.22.0/23 | F1 | 192.234.22.134 |
| Deus | 0.0.0.0/0 | default | 192.234.22.133 |
| Deus | 192.234.23.0/24 | E1 | 192.234.22.130 |
| Protocol7 | 0.0.0.0/0 | default | 192.234.22.129 |
| Protocol7 | 192.234.23.0/24 | E1 | 192.234.23.210 |
| Knights | 0.0.0.0/0 | default | 192.234.23.209 |
| Knights | 192.234.23.0/25 | C1 | 192.234.23.202 |
| Phantoma | 0.0.0.0/0 | default | 192.234.23.201 |
| Phantoma | 192.234.23.0/26 | A25 | 192.234.23.98 |
| Shibuya | 0.0.0.0/0 | default | 192.234.23.97 |

## Pemeriksaan

Sudah diperiksa dengan skrip:

- Ke-25 subnet tidak saling tumpang tindih dan semuanya berada di dalam `192.234.0.0/19`.
- Setiap subnet punya IP usable yang cukup untuk Jumlah IP-nya.
- Simulasi longest-prefix-match dari ke-13 router ke ke-25 subnet: semua sampai tujuan, tanpa loop.

Sudah diuji di Packet Tracer pada `cisco.pkt`:

- `pt_health_check`: tidak ada link down dan tidak ada IP duplikat.
- Ping ring ke-25 host (tiap host mengirim dan menerima): semua mendapat reply, 3/4 sampai 4/4 paket. Paket pertama yang hilang adalah ARP.
- Lain ke Karl setelah ARP terisi: 4/4, TTL 120.

Simulasi skrip memakai 24 static route, yaitu 12 default route dan 12 route ke arah bawah (belum termasuk default route Cyberia ke Cloud).
Jumlah ini minimum: satu route untuk setiap router tetangga di arah bawah.
Pengelompokan di atas memungkinkannya karena tiap arah cukup diwakili satu blok gabungan.
Blok boleh memuat subnet yang terhubung langsung ke router itu (misalnya A3 di dalam D3 pada Wired), sebab route connected lebih spesifik dan menang.
