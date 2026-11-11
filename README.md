# Dokumentasi Mid-Project Jarkom

**Kelompok:** K14
**Skema yang dipakai:** CIDR
**Base network:** `10.112.0.0` (oktet ke-2 = 8 × 14 = 112)

---

## 1. Pemilihan skema: CIDR

### Perbandingan

| | VLSM | CIDR |
|---|---|---|
| Cara alokasi | Subnet diurutkan dari blok terbesar, ditempel berurutan | Tiap router mendapat satu blok utuh yang berisi semua LAN dan link miliknya |
| Pemakaian alamat | Sekitar 94,2% (blok induk `/16`) | Sekitar 47,1% (blok induk `/15`) |
| Static route di R-PUSAT | 1 route per LAN (sekitar 12 route) | 1 route per router (6 route) |
| Static route R-UTARA ke R-PELABUHAN | 2 route | 1 route |


Kita memilih CIDR agar tiap router mendapat satu blok utuh yang berisi semua LAN dan link miliknya. Maka dari itu, route jadi lebih ringkas dan lebih mudah ditelusuri/ mempercepat pencarian kesalahan. Pilihan CIDR ini mengorbankan efisiensi alamatnya untuk routing yang ringkas.
---

## 2. Soal 1: Perancangan topologi

### 2.1 Screenshot topologi

<img width="3313" height="1605" alt="image" src="https://github.com/user-attachments/assets/edc51f0c-745a-4999-819a-c3987319541a" />

### 2.2 Langkah pembuatan

1. **Menaruh router.** Ada 8 router, R-ISP, R-PUSAT, R-DC, R-BARAT, R-UTARA, R-TIMUR, R-SELATAN, R-PELABUHAN. Menggunakan router-PT dan menambah modul `PT-ROUTER-NM-1CGE` menyesuaikan kebutuhan port di tiap router.
2. **Mengganti nama.** Display Name dan Hostname diganti sesuai soal.
3. **Menaruh switch dan perangkat.** Satu switch per LAN (14 switch), dengan satu perangkat per LAN.
4. **Memasang kabel.** Jenis kabel dipilih manual sesuai kebutuhan.
5. **Memberi label LAN** memakai Place Note dan Draw Ellipse.

### 2.3 Pertimbangan desain

#### Jenis router

| Router | Jenis | Alasan |
|---|---|---|
| R-PUSAT | Router-PT | Butuh 8 port Ethernet tembaga (ISP, 5 link, 2 LAN) |
| R-UTARA | Router-PT | Butuh 4 port, dan 5 port saat link L7 ditambahkan di file OSPF |
| R-DC | Router-PT | Butuh 3 port, dan 4 port saat link L7 ditambahkan |
| R-BARAT, R-TIMUR, R-SELATAN, R-PELABUHAN | Router-PT dengan 1 modul 1CGE | Butuh 3 port (1 uplink dan 2 LAN) |
| R-ISP | Router-PT | Butuh 2 port (ke R-PUSAT dan ke server internet) |

#### Perangkat tiap LAN

| Router | LAN | Isi LAN | Media | Alasan |
|---|---|---|---|---|
| R-PUSAT | Pegawai Balai Kota | SW-PEGAWAI-BALAIKOTA + PC | Kabel | Perangkat tetap, butuh koneksi stabil |
| R-PUSAT | Server Balai Kota | SW-SERVER-BALAIKOTA + Server | Kabel | Server butuh bandwidth dan keandalan |
| R-DC | CCTV Kota | SW-CCTV-KOTA + PC | Kabel | Streaming CCTV butuh bandwidth stabil |
| R-DC | Server dan Storage DC | SW-SERVER-DC + Server | Kabel | Penyimpanan butuh keandalan |
| R-BARAT | WiFi Publik Barat | SW + Access Point + Smartphone | Wireless | Pengguna publik bergerak |
| R-BARAT | Puskesmas dan Sekolah Barat | SW + PC | Kabel | Layanan publik butuh koneksi stabil |
| R-UTARA | WiFi Publik Utara | SW + Access Point + Smartphone | Wireless | Pengguna publik bergerak |
| R-UTARA | Sensor Lalu Lintas Utara | SW + PC perwakilan | Kabel | Sensor butuh koneksi stabil dan terus menyala |
| R-TIMUR | WiFi Publik Timur | SW + Access Point + Smartphone | Wireless | Pengguna publik bergerak |
| R-TIMUR | Sensor Banjir Timur | SW + PC perwakilan | Kabel | Peringatan banjir butuh keandalan |
| R-SELATAN | WiFi Publik Selatan | SW + Access Point + Smartphone | Wireless | Pengguna publik bergerak |
| R-SELATAN | PJU Pintar Selatan | SW + PC perwakilan | Kabel | Perangkat tetap |
| R-PELABUHAN | Sensor dan SCADA Pelabuhan | SW + PC perwakilan | Kabel | SCADA butuh koneksi stabil |
| R-PELABUHAN | Kantor Pelabuhan | SW + PC | Kabel | Perangkat kantor tetap |

#### Pengaturan WiFi

Setiap Access Point memakai SSID berbeda (`WIFI-BARAT`, `WIFI-UTARA`, `WIFI-TIMUR`, `WIFI-SELATAN`) tanpa password, karena merupakan WiFi publik. Smartphone di tiap wilayah memakai SSID yang sama dengan AP-nya. SSID dibedakan supaya smartphone tidak tersambung ke AP wilayah lain.

### 2.4 Jenis kabel

| Menghubungkan | Kabel |
|---|---|
| Router ke router | Copper Cross-Over |
| Router ke server | Copper Cross-Over |
| Router ke switch | Copper Straight-Through |
| Switch ke PC, server, atau AP | Copper Straight-Through |


### 2.5 Tabel port dan kabel

| Router | Port | Disambung ke |
|---|---|---|
| R-ISP | Fa0/0 | R-PUSAT |
| R-ISP | Fa1/0 | SRV-GOOGLE (8.8.8.8) |
| R-PUSAT | Gig4/0 | R-ISP |
| R-PUSAT | Fa0/0 | L1: R-DC |
| R-PUSAT | Fa1/0 | L2: R-UTARA |
| R-PUSAT | Gig5/0 | L3: R-SELATAN |
| R-PUSAT | Gig6/0 | L4: R-TIMUR |
| R-PUSAT | Gig7/0 | L5: R-BARAT |
| R-PUSAT | Gig8/0 | SW-PEGAWAI-BALAIKOTA |
| R-PUSAT | Gig9/0 | SW-SERVER-BALAIKOTA |
| R-DC | Gig9/0 | L1: R-PUSAT |
| R-DC | Fa0/0 | SW-CCTV-KOTA |
| R-DC | Fa1/0 | SW-SERVER-DC |
| R-UTARA | Gig7/0 | L2: R-PUSAT |
| R-UTARA | Fa0/0 | L6: R-PELABUHAN |
| R-UTARA | Fa1/0 | SW-WIFI-UTARA |
| R-UTARA | Gig8/0 | SW-SENSOR-LALULINTAS-UTARA |
| R-UTARA | Gig9/0 | Dikosongkan (L7 untuk file OSPF) |
| R-BARAT | Fa0/0 | L5: R-PUSAT |
| R-BARAT | Fa1/0 | SW-WIFI-BARAT |
| R-BARAT | Gig6/0 | SW-PUSKESMAS-SEKOLAH-BARAT |
| R-TIMUR | Fa0/0 | L4: R-PUSAT |
| R-TIMUR | Fa1/0 | SW-WIFI-TIMUR |
| R-TIMUR | Gig6/0 | SW-SENSOR-BANJIR-TIMUR |
| R-SELATAN | Fa0/0 | L3: R-PUSAT |
| R-SELATAN | Fa1/0 | SW-WIFI-SELATAN |
| R-SELATAN | Gig6/0 | SW-PJU-SELATAN |
| R-PELABUHAN | Fa0/0 | L6: R-UTARA |
| R-PELABUHAN | Fa1/0 | SW-SCADA-PELABUHAN |
| R-PELABUHAN | Gig6/0 | SW-KANTOR-PELABUHAN |

---

## 3. Soal 2: Tabel subnetting

### 3.1 Dasar perhitungan

**Alamat dasar (soal 1a).** Oktet ke-2 = 8 × K = 8 × 14 = 112, jadi base network `10.112.0.0/8`.

**Rumus kebutuhan alamat:**

- LAN: kebutuhan = host + 1 (gateway) + 2 (network dan broadcast)
- Link antar-router: kebutuhan = 2 + 2 = 4 (tanpa gateway)
- Bit host = n terkecil, 2ⁿ ≥ kebutuhan
- Prefix = 32 − bit host, ukuran blok = 2ⁿ

### 3.2 Tabel kebutuhan

<img width="900" height="481" alt="image" src="https://github.com/user-attachments/assets/720b6832-e265-44fd-b4c1-5124f7435e05" />

### 3.3 Tabel alokasi CIDR

Subnet dikelompokkan per router dan diurutkan berdasarkan alamat. Gateway tiap LAN adalah alamat host pertama (network + 1). Link tidak memiliki gateway.

<img width="987" height="465" alt="image" src="https://github.com/user-attachments/assets/e8277f94-689f-4e45-8783-baa278c50911" />

### 3.4 Pohon penggabungan (supernetting)

Aturan penggabungan:

- Prefix sama → prefix dikurangi 1
- Prefix berbeda → ambil prefix terkecil (blok terbesar), lalu kurangi 1

```
Root /15 (131.072)
├── P1 /16
│   ├── R-UTARA /17 = WiFi Utara /18 + [Sensor LL /23 + L2 /30 → /22]
│   └── A /17
│       ├── R-DC /18 = CCTV /19 + [Server DC /24 + L1 /30 → /23]
│       └── R-PUSAT /20 = Pegawai /21 + Server Balai Kota /27
└── P2 /16
    ├── B /17
    │   ├── R-BARAT /18 = WiFi Barat /19 + [Puskesmas /24 + L5 /30 → /23]
    │   └── R-TIMUR /18 = WiFi Timur /19 + [Sensor Banjir /22 + L4 /30 → /21]
    └── C /17
        ├── R-SELATAN /18 = WiFi Selatan /19 + [PJU /25 + L3 /30 → /24]
        └── R-PELABUHAN /18 = SCADA /19 + [Kantor /25 + L6 /30 → /24]
```

### 3.5 Supernet per router

| Router | Supernet | Mask | Rentang |
|---|---|---|---|
| R-UTARA | 10.112.0.0/17 | 255.255.128.0 | 10.112.0.0 – 10.112.127.255 |
| R-DC | 10.112.128.0/18 | 255.255.192.0 | 10.112.128.0 – 10.112.191.255 |
| R-PUSAT | 10.112.192.0/20 | 255.255.240.0 | 10.112.192.0 – 10.112.207.255 |
| R-BARAT | 10.113.0.0/18 | 255.255.192.0 | 10.113.0.0 – 10.113.63.255 |
| R-TIMUR | 10.113.64.0/18 | 255.255.192.0 | 10.113.64.0 – 10.113.127.255 |
| R-SELATAN | 10.113.128.0/18 | 255.255.192.0 | 10.113.128.0 – 10.113.191.255 |
| R-PELABUHAN | 10.113.192.0/18 | 255.255.192.0 | 10.113.192.0 – 10.113.255.255 |

### 3.6 Tabel VLSM

<img width="868" height="439" alt="image" src="https://github.com/user-attachments/assets/8c920a89-cece-4a8f-b9dc-d59c0680c9e3" />

---

## 4. Soal 3: Static routing

### 4.1 Aturan

1. Seluruh LAN saling berkomunikasi hanya dengan static route dan default route untuk arah keluar.
2. Default route hanya boleh mengarah ke router di atasnya.
3. R-PUSAT wajib memiliki static route ke semua LAN milik R-DC, R-UTARA, R-PELABUHAN, R-SELATAN, R-TIMUR, dan R-BARAT.
4. R-UTARA wajib memiliki static route ke semua LAN milik R-PELABUHAN.

### 4.2 Alamat link

| Link | Menghubungkan | Network | Router atas | Router bawah |
|---|---|---|---|---|
| L1 | R-PUSAT dan R-DC | 10.112.161.0/30 | 10.112.161.1 | 10.112.161.2 |
| L2 | R-PUSAT dan R-UTARA | 10.112.66.0/30 | 10.112.66.1 | 10.112.66.2 |
| L3 | R-PUSAT dan R-SELATAN | 10.113.160.128/30 | 10.113.160.129 | 10.113.160.130 |
| L4 | R-PUSAT dan R-TIMUR | 10.113.100.0/30 | 10.113.100.1 | 10.113.100.2 |
| L5 | R-PUSAT dan R-BARAT | 10.113.33.0/30 | 10.113.33.1 | 10.113.33.2 |
| L6 | R-UTARA dan R-PELABUHAN | 10.113.224.128/30 | 10.113.224.129 | 10.113.224.130 |
| R-PUSAT ke R-ISP | | 203.0.113.56/30 | 203.0.113.57 (R-PUSAT) | 203.0.113.58 (R-ISP) |

Server internet: `8.8.8.0/24`, SRV-GOOGLE `8.8.8.8`, gateway `8.8.8.1` (interface R-ISP).

### 4.3 Konfigurasi per router

#### Catatan

- Setiap interface yang dipakai diberi IP, baik sisi LAN maupun sisi link.
- Sisi LAN memakai alamat gateway (network + 1), karena interface router yang menghadap LAN adalah gateway bagi perangkat di LAN itu.
- Dua ujung sebuah link harus berada di subnet yang sama, dan IP ujung tetangga menjadi next hop pada route.

#### R-ISP

R-ISP tidak memiliki route apa pun.

```
enable
configure terminal
hostname R-ISP
interface FastEthernet0/0
 ip address 203.0.113.58 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/0
 ip address 8.8.8.1 255.255.255.0
 no shutdown
 exit
end
copy running-config startup-config
```

SRV-GOOGLE: IP `8.8.8.8`, mask `255.255.255.0`, gateway `8.8.8.1`.

#### R-PUSAT

```
enable
configure terminal
hostname R-PUSAT
interface GigabitEthernet4/0
 ip address 203.0.113.57 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/0
 ip address 10.112.161.1 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/0
 ip address 10.112.66.1 255.255.255.252
 no shutdown
 exit
interface GigabitEthernet5/0
 ip address 10.113.160.129 255.255.255.252
 no shutdown
 exit
interface GigabitEthernet6/0
 ip address 10.113.100.1 255.255.255.252
 no shutdown
 exit
interface GigabitEthernet7/0
 ip address 10.113.33.1 255.255.255.252
 no shutdown
 exit
interface GigabitEthernet8/0
 ip address 10.112.192.1 255.255.248.0
 no shutdown
 exit
interface GigabitEthernet9/0
 ip address 10.112.200.1 255.255.255.224
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 203.0.113.58
ip route 10.112.128.0 255.255.192.0 10.112.161.2
ip route 10.112.0.0 255.255.128.0 10.112.66.2
ip route 10.113.192.0 255.255.192.0 10.112.66.2
ip route 10.113.128.0 255.255.192.0 10.113.160.130
ip route 10.113.64.0 255.255.192.0 10.113.100.2
ip route 10.113.0.0 255.255.192.0 10.113.33.2
end
copy running-config startup-config
```

| Route | Tujuan | Next hop |
|---|---|---|
| default (0.0.0.0/0) | Keluar ke internet | R-ISP (203.0.113.58) |
| 10.112.128.0/18 | Semua LAN R-DC | R-DC (di L1) |
| 10.112.0.0/17 | Semua LAN R-UTARA | R-UTARA (di L2) |
| 10.113.192.0/18 | Semua LAN R-PELABUHAN | **R-UTARA** (di L2) |
| 10.113.128.0/18 | Semua LAN R-SELATAN | R-SELATAN (di L3) |
| 10.113.64.0/18 | Semua LAN R-TIMUR | R-TIMUR (di L4) |
| 10.113.0.0/18 | Semua LAN R-BARAT | R-BARAT (di L5) |

R-PELABUHAN dijangkau lewat R-UTARA karena kabel L6 menghubungkannya ke R-UTARA.

#### R-UTARA

```
enable
configure terminal
hostname R-UTARA
interface GigabitEthernet7/0
 ip address 10.112.66.2 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/0
 ip address 10.113.224.129 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/0
 ip address 10.112.0.1 255.255.192.0
 no shutdown
 exit
interface GigabitEthernet8/0
 ip address 10.112.64.1 255.255.254.0
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 10.112.66.1
ip route 10.113.192.0 255.255.192.0 10.113.224.130
end
copy running-config startup-config
```

| Route | Tujuan | Next hop |
|---|---|---|
| default (0.0.0.0/0) | Ke atas | R-PUSAT (10.112.66.1) |
| 10.113.192.0/18 | Semua LAN R-PELABUHAN | R-PELABUHAN (10.113.224.130) |

#### R-DC

```
enable
configure terminal
hostname R-DC
interface GigabitEthernet9/0
 ip address 10.112.161.2 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/0
 ip address 10.112.128.1 255.255.224.0
 no shutdown
 exit
interface FastEthernet1/0
 ip address 10.112.160.1 255.255.255.0
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 10.112.161.1
end
copy running-config startup-config
```

#### R-BARAT, R-TIMUR, R-SELATAN, R-PELABUHAN

Keempat router ini hanya memiliki default route ke atas, karena static route ke bawah hanya untuk R-PUSAT dan R-UTARA.

**R-BARAT**

```
hostname R-BARAT
interface FastEthernet0/0
 ip address 10.113.33.2 255.255.255.252
 no shutdown
interface FastEthernet1/0
 ip address 10.113.0.1 255.255.224.0
 no shutdown
interface GigabitEthernet6/0
 ip address 10.113.32.1 255.255.255.0
 no shutdown
ip route 0.0.0.0 0.0.0.0 10.113.33.1
```

**R-TIMUR**

```
hostname R-TIMUR
interface FastEthernet0/0
 ip address 10.113.100.2 255.255.255.252
 no shutdown
interface FastEthernet1/0
 ip address 10.113.64.1 255.255.224.0
 no shutdown
interface GigabitEthernet6/0
 ip address 10.113.96.1 255.255.252.0
 no shutdown
ip route 0.0.0.0 0.0.0.0 10.113.100.1
```

**R-SELATAN**

```
hostname R-SELATAN
interface FastEthernet0/0
 ip address 10.113.160.130 255.255.255.252
 no shutdown
interface FastEthernet1/0
 ip address 10.113.128.1 255.255.224.0
 no shutdown
interface GigabitEthernet6/0
 ip address 10.113.160.1 255.255.255.128
 no shutdown
ip route 0.0.0.0 0.0.0.0 10.113.160.129
```

**R-PELABUHAN**

```
hostname R-PELABUHAN
interface FastEthernet0/0
 ip address 10.113.224.130 255.255.255.252
 no shutdown
interface FastEthernet1/0
 ip address 10.113.192.1 255.255.224.0
 no shutdown
interface GigabitEthernet6/0
 ip address 10.113.224.1 255.255.255.128
 no shutdown
ip route 0.0.0.0 0.0.0.0 10.113.224.129
```

### Catatan untuk anggota yang melanjutkan (soal 4 sampai 8)

1. Gunakan file `.pkt` terbaru dan simpan salinan sebelum mengubah apa pun.
2. Gunakan tabel port di bagian 2.5, karena nama interface yang sama dipakai untuk DHCP, NAT, dan GRE.
3. Perangkat pengguna (PC dan smartphone) masih memakai IP statis sementara, dan akan diganti ke DHCP pada soal 4. Server tetap statis.
4. Port Gig9/0 di R-UTARA dikosongkan untuk link L7 pada file OSPF (soal 7).
5. LAN CCTV Kota mendapat DHCP dari server di LAN Server dan Storage DC, bukan dari router.
