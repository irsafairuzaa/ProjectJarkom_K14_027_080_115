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

## 5. Soal 4: DHCP

### 5.1 Rancangan

- Semua interface router dan server memakai IP statis. Perangkat pengguna (PC dan smartphone) mendapat IP, gateway, dan DNS otomatis.
- DNS yang dibagikan: `10.112.160.2` (SRV-DC, LAN Server dan Storage DC).
- Setiap pool mengecualikan `.1` sampai `.10` (gateway dan cadangan alamat statis). Alamat pertama yang dibagikan `.11`, jadi tidak bentrok dengan IP statis.
- LAN Server (Balai Kota dan DC) tidak memakai DHCP karena isinya server berIP statis.
- LAN CCTV Kota dilayani server DHCP di SRV-DC, bukan router. R-DC meneruskan permintaan dengan `ip helper-address`.

IP statis yang dipakai:

| Perangkat | IP | Gateway |
|---|---|---|
| SRV-DC | `10.112.160.2/24` | `10.112.160.1` |
| SRV-BALAIKOTA | `10.112.200.2/27` | `10.112.200.1` |
| SRV-GOOGLE | `8.8.8.8/24` | `8.8.8.1` |

SRV-BALAIKOTA sebelumnya memakai `10.112.192.2/21`, bentrok dengan PC Pegawai. IP-nya diperbaiki ke tabel di atas.

### 5.2 Tabel pool

| Dilayani oleh | Nama pool | Network | Gateway |
|---|---|---|---|
| R-PUSAT | PEGAWAI | 10.112.192.0/21 | 10.112.192.1 |
| R-UTARA | WIFI-UTARA | 10.112.0.0/18 | 10.112.0.1 |
| R-UTARA | SENSOR-UTARA | 10.112.64.0/23 | 10.112.64.1 |
| R-BARAT | WIFI-BARAT | 10.113.0.0/19 | 10.113.0.1 |
| R-BARAT | PUSKESMAS-BARAT | 10.113.32.0/24 | 10.113.32.1 |
| R-TIMUR | WIFI-TIMUR | 10.113.64.0/19 | 10.113.64.1 |
| R-TIMUR | BANJIR-TIMUR | 10.113.96.0/22 | 10.113.96.1 |
| R-SELATAN | WIFI-SELATAN | 10.113.128.0/19 | 10.113.128.1 |
| R-SELATAN | PJU-SELATAN | 10.113.160.0/25 | 10.113.160.1 |
| R-PELABUHAN | SCADA-PELABUHAN | 10.113.192.0/19 | 10.113.192.1 |
| R-PELABUHAN | KANTOR-PELABUHAN | 10.113.224.0/25 | 10.113.224.1 |
| SRV-DC | CCTV | 10.112.128.0/19 | 10.112.128.1 |

### 5.3 Konfigurasi

Pola pool di router (contoh R-UTARA, LAN Sensor):

```
ip dhcp excluded-address 10.112.64.1 10.112.64.10
ip dhcp pool SENSOR-UTARA
 network 10.112.64.0 255.255.254.0
 default-router 10.112.64.1
 dns-server 10.112.160.2
```

LAN CCTV, di SRV-DC (Services → DHCP): pool `CCTV`, gateway `10.112.128.1`, DNS `10.112.160.2`, start IP `10.112.128.11`, mask `255.255.224.0`, maksimum 500 pengguna.

Relay di R-DC, sisi LAN CCTV:

```
interface FastEthernet0/0
 ip helper-address 10.112.160.2
```

Permintaan DHCP dari PC CCTV berupa broadcast, yang tidak diteruskan router. `ip helper-address` mengubahnya menjadi unicast ke SRV-DC, yang ada di LAN lain pada R-DC yang sama.

### 5.4 Bukti pengujian

Setiap PC dan smartphone diatur ke DHCP, lalu `ipconfig /all` menampilkan alamat dari pool.

Pegawai Balai Kota mendapat IP dari R-PUSAT
<img width="1157" height="547" alt="4a_dhcp_pegawai" src="https://github.com/user-attachments/assets/c72b1942-d617-4b33-9308-594991a3840d" />


CCTV Kota mendapat IP dari SRV-DC lewat relay (DHCP Servers: 10.112.160.2)
<img width="1448" height="641" alt="4b_dhcp_cctv_relay" src="https://github.com/user-attachments/assets/b0a3e1ba-b35e-496d-bee1-7024bc86e8e7" />


Smartphone WiFi mendapat IP dari pool wilayahnya
<img width="1907" height="1072" alt="4c_dhcp_wifi" src="https://github.com/user-attachments/assets/48cb33bd-6444-4dc5-be6e-dc61ba6e8d42" />

---

## 6. Soal 5: NAT

### 6.1 Rancangan

Semua wilayah mengakses `8.8.8.8` memakai satu IP publik milik R-PUSAT (`203.0.113.57`). R-ISP tidak punya rute ke blok privat, jadi balasan hanya bisa kembali lewat terjemahan NAT di R-PUSAT.

### 6.2 Konfigurasi (R-PUSAT)

```
ip access-list standard NAT-INSIDE
 permit 10.112.0.0 0.1.255.255
interface GigabitEthernet0/0
 ip nat outside
interface GigabitEthernet0/1
 ip nat inside
interface GigabitEthernet0/2
 ip nat inside
interface Vlan11
 ip nat inside
interface Vlan13
 ip nat inside
interface Vlan14
 ip nat inside
interface Vlan15
 ip nat inside
interface Vlan20
 ip nat inside
ip nat inside source list NAT-INSIDE interface GigabitEthernet0/0 overload
```

- `10.112.0.0/15` mencakup seluruh blok privat kelompok (hasil CIDR soal 2).
- `overload` membuat banyak host berbagi satu IP publik dengan membedakan nomor port.
- `Gi0/0` (arah R-ISP) adalah `outside`, tujuh interface lainnya `inside`.

### 6.3 Bukti pengujian

Sebelum NAT, `ping 8.8.8.8` dari PC Pegawai gagal 100%, sedangkan ping dari R-PUSAT sendiri berhasil. Sesudah NAT, ping dari semua wilayah berhasil.

Ping 8.8.8.8 gagal sebelum NAT
<img width="492" height="278" alt="5a_ping_sebelum_nat" src="https://github.com/user-attachments/assets/784a27bc-13d4-4a6e-bf37-5484b2868861" />


Ping 8.8.8.8 berhasil sesudah NAT dan tabel terjemahan (show ip nat translations)
<img width="1037" height="257" alt="5b_nat_translations" src="https://github.com/user-attachments/assets/c5935876-a1db-4a74-977a-2eb48b466045" />


Tabel routing R-ISP: hanya dua rute connected, tanpa blok 10.x
<img width="597" height="267" alt="5c_risp_route" src="https://github.com/user-attachments/assets/fff4c87f-a1d1-4f35-8008-e4d75af57a98" />

---

## 7. Soal 6: Tunnel GRE

### 7.1 Perubahan perangkat

Router-PT tidak mendukung tunnel (`interface tunnel 0` ditolak). Karena tunnel butuh dua ujung, R-PUSAT dan R-PELABUHAN diganti ke Cisco 2911, yang menerima perintah tunnel. Router lain tidak berubah.

- **R-PELABUHAN**: 2911 tanpa modul (3 port Gigabit bawaan cukup).
- **R-PUSAT**: butuh 8 port routed, sedangkan 2911 hanya punya 3. Ditambah dua modul `HWIC-4ESW`, dan lima link/LAN memakai port switch dengan SVI (`interface vlan`).

Pemetaan port R-PELABUHAN:

| Fungsi | Port | IP |
|---|---|---|
| L6 ke R-UTARA | Gi0/0 | 10.113.224.130/30 |
| LAN SCADA | Gi0/1 | 10.113.192.1/19 |
| LAN Kantor | Gi0/2 | 10.113.224.1/25 |

Pemetaan port R-PUSAT:

| Fungsi | Port fisik | Interface IP | IP |
|---|---|---|---|
| ke R-ISP | Gi0/0 | Gi0/0 | 203.0.113.57/30 |
| L2 ke R-UTARA | Gi0/1 | Gi0/1 | 10.112.66.1/30 |
| LAN Pegawai | Gi0/2 | Gi0/2 | 10.112.192.1/21 |
| L1 ke R-DC | Fa0/2/0 | Vlan11 | 10.112.161.1/30 |
| L3 ke R-SELATAN | Fa0/2/1 | Vlan13 | 10.113.160.129/30 |
| L4 ke R-TIMUR | Fa0/2/2 | Vlan14 | 10.113.100.1/30 |
| L5 ke R-BARAT | Fa0/2/3 | Vlan15 | 10.113.33.1/30 |
| LAN Server Balai Kota | Fa0/3/0 | Vlan20 | 10.112.200.1/27 |

Konfigurasi lama (IP, rute static, DHCP, NAT) dipasang ulang di router baru dengan nama port yang baru.

### 7.2 Konfigurasi tunnel

| | R-PUSAT | R-PELABUHAN |
|---|---|---|
| IP tunnel | 192.168.14.1/30 | 192.168.14.2/30 |
| Sumber | Gi0/1 (10.112.66.1) | Gi0/0 (10.113.224.130) |
| Tujuan | 10.113.224.130 | 10.112.66.1 |

```
! R-PUSAT
interface Tunnel0
 ip address 192.168.14.1 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 10.113.224.130
 tunnel mode gre ip
ip route 10.113.192.0 255.255.224.0 192.168.14.2
ip route 10.113.224.0 255.255.255.128 192.168.14.2

! R-PELABUHAN
interface Tunnel0
 ip address 192.168.14.2 255.255.255.252
 tunnel source GigabitEthernet0/0
 tunnel destination 10.112.66.1
 tunnel mode gre ip
ip route 10.112.192.0 255.255.248.0 192.168.14.1
ip route 10.112.200.0 255.255.255.224 192.168.14.1
```

`tunnel source` harus berupa nama interface. Jika ditulis sebagai alamat IP, Packet Tracer menampilkan `Tunnel source UNKNOWN` dan protokol tunnel tetap `down`.

### 7.3 Mengapa router memilih tunnel

Tabel routing R-PUSAT punya dua rute ke Pelabuhan:

| Rute | Next hop | Prefix |
|---|---|---|
| 10.113.192.0/18 | R-UTARA (10.112.66.2) | /18 |
| 10.113.192.0/19 dan 10.113.224.0/25 | tunnel (192.168.14.2) | lebih panjang |

Router memilih rute dengan prefix paling panjang (paling spesifik), jadi lalu lintas ke LAN SCADA dan Kantor lewat tunnel. Rute `/18` lewat R-UTARA tetap ada, tapi hanya dipakai jika tidak ada yang lebih spesifik. Di R-PELABUHAN, dua rute ke Balai Kota (`/21` dan `/27`) lebih spesifik daripada default route, jadi arah baliknya juga lewat tunnel.

Alamat tujuan tunnel (`10.113.224.130` dan `10.112.66.1`) tidak tercakup rute tunnel, jadi tunnel tidak membungkus dirinya sendiri.

### 7.4 Mengapa GRE tidak mengenkripsi

GRE hanya membungkus paket asli dengan header IP baru. Isi paket tetap utuh dan terbaca oleh siapa pun di jalur, termasuk R-UTARA yang dilewati. Enkripsi butuh protokol lain, misalnya IPsec. Bonus IPsec tidak dikerjakan.

### 7.5 Bukti pengujian

Jalur Balai Kota ke Pelabuhan sebelum tunnel melewati R-UTARA (`10.112.66.2`). Sesudah tunnel, hop R-UTARA hilang dan hop berganti menjadi alamat tunnel.

Tracert sebelum tunnel: melewati R-UTARA
<img width="415" height="225" alt="6a_tracert_sebelum png" src="https://github.com/user-attachments/assets/3cce174c-19a3-444b-9d37-fd031b07912b" />

Konfigurasi tunnel dan status up/up di R-PUSAT dan R-PELABUHAN
<img width="567" height="237" alt="6b_tunnel_up" src="https://github.com/user-attachments/assets/3818658a-11be-4436-a775-9cdd815766ac" />

Tracert Balai Kota ke Pelabuhan lewat 192.168.14.2
<img width="422" height="123" alt="6c_tracert_balaikota_pelabuhan" src="https://github.com/user-attachments/assets/0fa2287a-0dde-44da-85c1-f68647fcb04b" />

Tracert Pelabuhan ke Balai Kota lewat 192.168.14.1
<img width="427" height="131" alt="6d_tracert_pelabuhan_balaikota" src="https://github.com/user-attachments/assets/7261bfbf-8dd7-4b46-8a16-fc5e3080781b" />


### 7.6 Catatan

- Lalu lintas dari Pelabuhan ke internet tidak simetris: permintaan lewat R-UTARA (default route), tetapi balasannya pulang lewat tunnel karena R-PUSAT punya rute spesifik ke Pelabuhan. Ini tetap berfungsi karena NAT di R-PUSAT bekerja dua arah (TTL ping dari LAN SCADA ke 8.8.8.8 menjadi 125, bukan 124).
- Konfigurasi semua router sudah disimpan (`copy running-config startup-config`). Setelah R-PUSAT di-restart, semua interface, tunnel, dan rute kembali dari NVRAM.

---

## 8. Catatan untuk anggota yang melanjutkan (soal 7 dan 8)

### Pembaruan catatan sebelumnya

Catatan "soal 4 sampai 8" di akhir bagian 4 sebagian sudah tidak berlaku:

- **Tabel port (bagian 2.5)** tidak lagi berlaku untuk **R-PUSAT** dan **R-PELABUHAN**, karena keduanya diganti ke Cisco 2911. Pemetaan baru ada di bagian 7.1. Router lain tidak berubah.
- **Perangkat pengguna** (PC dan smartphone) sekarang memakai **DHCP**. Hanya server yang statis.
- **LAN CCTV** sudah dilayani DHCP dari SRV-DC lewat relay di R-DC (bagian 5.3).
- **Port Gig9/0 di R-UTARA** tetap dikosongkan untuk link L7 di file OSPF (soal 7).

### Untuk soal 7 (OSPF)

- Rute static ke Pelabuhan lewat tunnel (`10.113.192.0/19` dan `10.113.224.0/25` via `192.168.14.2`) dan alamat tujuan tunnel (`10.113.224.130` dan `10.112.66.1`) harus tetap terjangkau setelah rute static diganti OSPF. Jangan sampai OSPF mengiklankan alamat tunnel lewat tunnel itu sendiri.
- R-PUSAT sekarang 2911 dengan interface `Vlan11`, `Vlan13`, `Vlan14`, `Vlan15`, dan `Vlan20`. Gunakan nama itu di perintah `network` dan `passive-interface`.

### Untuk soal 8 (layanan aplikasi)

- Server yang tersedia: **SRV-DC** `10.112.160.2/24` (gateway `10.112.160.1`) dan **SRV-BALAIKOTA** `10.112.200.2/27` (gateway `10.112.200.1`).
- DHCP sudah membagikan DNS `10.112.160.2` ke semua PC dan smartphone. Service DNS, HTTP, FTP, dan Email di server belum dikonfigurasi.
- Pembagian server yang disarankan: DNS dan FTP di SRV-DC (sesuai soal), web di SRV-BALAIKOTA.
- **Jangan matikan service DHCP di SRV-DC**, karena melayani LAN CCTV.
- Jangan ubah IP server tanpa menyesuaikan pool DHCP dan record DNS.

## 7. Soal 7: OSPF dan Redundansi Link

### 7.1 Aturan

1. Menambahkan link cadangan L7 antara R-DC dan R-UTARA.
2. Mengganti seluruh static route antar-router dengan OSPF.
3. Router tidak mengirim pesan OSPF ke arah LAN pengguna.
4. Default route internet hanya dikonfigurasi di R-PUSAT. Router lain mendapatkannya secara otomatis melalui OSPF.
5. Jika salah satu link backbone terputus, konektivitas harus pulih secara otomatis melalui jalur alternatif.

### 7.2 Alamat Link L7

| Link | Menghubungkan | Network | Router pertama | Router kedua |
|---|---|---|---|---|
| L7 | R-DC dan R-UTARA | 10.112.161.4/30 | 10.112.161.5 (R-DC) | 10.112.161.6 (R-UTARA) |

Subnet mask: `255.255.255.252`.

### 7.3 Konfigurasi Link L7

**R-DC**

```cisco
enable
configure terminal
interface FastEthernet8/0
 ip address 10.112.161.5 255.255.255.252
 no shutdown
end
```

**R-UTARA**

```cisco
enable
configure terminal
interface FastEthernet6/0
 ip address 10.112.161.6 255.255.255.252
 no shutdown
end
```

### 7.4 Konfigurasi OSPF

Seluruh router menggunakan OSPF process ID 1 dan area 0. Perintah `passive-interface default` mencegah pengiriman pesan OSPF ke LAN pengguna. Hanya interface yang menghubungkan antar-router yang diaktifkan kembali.

**R-PUSAT**

```cisco
configure terminal
router ospf 1
 router-id 1.1.1.1
 passive-interface default
 no passive-interface GigabitEthernet0/1
 no passive-interface Vlan11
 no passive-interface Vlan13
 no passive-interface Vlan14
 no passive-interface Vlan15
 network 10.0.0.0 0.255.255.255 area 0
 default-information originate
end
```

**R-DC**

```cisco
configure terminal
router ospf 1
 router-id 2.2.2.2
 passive-interface default
 no passive-interface GigabitEthernet9/0
 no passive-interface FastEthernet8/0
 network 10.0.0.0 0.255.255.255 area 0
end
```

**R-UTARA**

```cisco
configure terminal
router ospf 1
 router-id 3.3.3.3
 passive-interface default
 no passive-interface GigabitEthernet7/0
 no passive-interface FastEthernet0/0
 no passive-interface FastEthernet6/0
 network 10.0.0.0 0.255.255.255 area 0
end
```

**R-BARAT**

```cisco
configure terminal
router ospf 1
 router-id 4.4.4.4
 passive-interface default
 no passive-interface FastEthernet0/0
 network 10.0.0.0 0.255.255.255 area 0
end
```

**R-TIMUR**

```cisco
configure terminal
router ospf 1
 router-id 5.5.5.5
 passive-interface default
 no passive-interface FastEthernet0/0
 network 10.0.0.0 0.255.255.255 area 0
end
```

**R-SELATAN**

```cisco
configure terminal
router ospf 1
 router-id 6.6.6.6
 passive-interface default
 no passive-interface FastEthernet0/0
 network 10.0.0.0 0.255.255.255 area 0
end
```

**R-PELABUHAN**

```cisco
configure terminal
router ospf 1
 router-id 7.7.7.7
 passive-interface default
 no passive-interface GigabitEthernet0/0
 network 10.0.0.0 0.255.255.255 area 0
end
```

### 7.5 Pengaturan Default Route

R-PUSAT tetap menggunakan static default route menuju ISP, kemudian mendistribusikannya melalui OSPF.

**R-PUSAT**

```cisco
ip route 0.0.0.0 0.0.0.0 203.0.113.58

router ospf 1
 default-information originate
```

Static default route pada router lainnya dihapus agar menggunakan default route yang dipelajari melalui OSPF.

| Router | Perintah |
|---|---|
| R-DC | `no ip route 0.0.0.0 0.0.0.0 10.112.161.1` |
| R-UTARA | `no ip route 0.0.0.0 0.0.0.0 10.112.66.1` |
| R-BARAT | `no ip route 0.0.0.0 0.0.0.0 10.113.33.1` |
| R-TIMUR | `no ip route 0.0.0.0 0.0.0.0 10.113.100.1` |
| R-SELATAN | `no ip route 0.0.0.0 0.0.0.0 10.113.160.129` |
| R-PELABUHAN | `no ip route 0.0.0.0 0.0.0.0 10.113.224.129` |

Static route antar-router lama juga dihapus agar seluruh routing internal menggunakan OSPF. Static default route R-PUSAT menuju ISP tetap dipertahankan.

### 7.6 Verifikasi OSPF

Periksa adjacency antar-router:

```cisco
show ip ospf neighbor
```

Status neighbor yang berhasil terbentuk adalah `FULL`.

Periksa interface OSPF:

```cisco
show ip ospf interface brief
```

Periksa routing table:

```cisco
show ip route
```

Router selain R-PUSAT harus menerima default route OSPF dengan kode:

```text
O*E2 0.0.0.0/0
```

### 7.7 Pengujian Redundansi Link (Failover)

Pengujian dilakukan dengan memutus link utama L1 antara R-DC dan R-PUSAT. Jalur alternatif yang digunakan adalah:

`R-DC → R-UTARA → R-PUSAT`

**1. Pastikan konektivitas awal**

Dari R-DC:

```cisco
ping 10.112.66.1
```

**2. Putuskan link utama L1**

Pada R-DC:

```cisco
configure terminal
interface GigabitEthernet9/0
 shutdown
end
```

**3. Tunggu OSPF melakukan reconvergence**

Periksa routing table:

```cisco
show ip route
```

Kemudian lakukan ping kembali dari R-DC ke interface R-PUSAT yang terhubung dengan R-UTARA:

```cisco
ping 10.112.66.1
```

Ping harus tetap berhasil meskipun link langsung R-DC ke R-PUSAT terputus, karena trafik dialihkan melalui L7 dan R-UTARA.

**4. Pulihkan link utama**

Pada R-DC:

```cisco
configure terminal
interface GigabitEthernet9/0
 no shutdown
end
```

### 7.8 Kesimpulan

Dengan OSPF, seluruh router dapat mempelajari rute jaringan secara otomatis tanpa static route antar-router. Link L7 menyediakan jalur cadangan antara R-DC dan R-UTARA. Ketika link utama L1 terputus, OSPF melakukan reconvergence sehingga komunikasi antara R-DC dan R-PUSAT tetap dapat berlangsung melalui jalur alternatif.

---

## 9. Soal 8: Layanan Aplikasi

### 9.1 Rancangan

Domain yang dipakai adalah `smartcity.sby`. Semua perangkat pengguna sudah mendapat DNS `10.112.160.2` dari DHCP (soal 4), sehingga tidak ada konfigurasi tambahan di router maupun di sisi client.

| Layanan | Server | IP |
|---|---|---|
| DNS dan FTP | SRV-DC (LAN Server dan Storage DC) | `10.112.160.2` |
| Web dan Email | SRV-BALAIKOTA (LAN Server Balai Kota) | `10.112.200.2` |

Service DHCP di SRV-DC tetap menyala karena melayani LAN CCTV Kota.

### 9.2 Konfigurasi

**a. DNS (SRV-DC → Services → DNS → On)**

| Name | Type | Address |
|---|---|---|
| `www.smartcity.sby` | A | `10.112.200.2` |
| `ftp.smartcity.sby` | A | `10.112.160.2` |
| `mail.smartcity.sby` | A | `10.112.200.2` |

**b. Web (SRV-BALAIKOTA → Services → HTTP → On)**

Isi `index.html` diganti dengan:

```html
<html>
<head><title>Portal SmartCity Surabaya</title></head>
<body>
<h1>Portal SmartCity Surabaya</h1>
<p>Kelompok K14</p>
</body>
</html>
```

**c. FTP (SRV-DC → Services → FTP → On)**

Akun `cctv` (password `cctv14`) dibuat dengan permission Write, Read, Delete, Rename, dan List, supaya perangkat CCTV dapat mengunggah dan mengubah file. Akun bawaan `cisco` dihapus.

**d. Email (SRV-BALAIKOTA → Services → EMAIL)**

SMTP dan POP3 dinyalakan, domain `smartcity.sby`, lalu dibuat dua user:

| User | Password | Dipakai di |
|---|---|---|
| `pegawai` | `pegawai14` | PC Pegawai Balai Kota |
| `pelabuhan` | `pelabuhan14` | PC Kantor Pelabuhan |

Di tiap PC (Desktop → Email → Configure Mail), alamat diisi `user@smartcity.sby`, sedangkan incoming dan outgoing server diisi `mail.smartcity.sby`.

### 9.3 Masalah yang ditemui

PC LAN CCTV awalnya tidak bisa ping maupun FTP ke SRV-DC. `ipconfig /all` menunjukkan alamat `169.254.x.x`, artinya DHCP gagal, bukan masalah OSPF. Tabel routing R-DC sudah benar, karena kedua LAN terhubung langsung ke R-DC. Setelah pool CCTV di SRV-DC dan `ip helper-address` di R-DC diperiksa, PC mendapat IP `10.112.128.x` dan FTP berhasil.

### 9.4 Bukti pengujian

DNS dari PC: `nslookup www.smartcity.sby` menghasilkan `10.112.200.2`
<!-- tempel screenshot nslookup -->

Web Browser membuka `http://www.smartcity.sby` dan menampilkan "Portal SmartCity Surabaya" dengan Kelompok K14
<!-- tempel screenshot web -->

FTP dari PC CCTV: login akun `cctv`, `put rekaman.txt`, lalu `dir`
<!-- tempel screenshot ftp -->

Email terkirim dan diterima dua arah antara PC Pegawai Balai Kota dan PC Kantor Pelabuhan
<!-- tempel screenshot email -->