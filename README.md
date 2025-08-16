# Analisis Erasure Coding terhadap Replikasi pada Key-Value Store Database Terdistribusi

Tugas Akhir - Institut Teknologi Bandung  
Program Studi Teknik Informatika  

**Penulis:** Muhamad Aji Wibisono (13521095)  
**Pembimbing:** Achmad Imam Kistijantoro, S.T, M.Sc., Ph.D.  
**Tanggal:** 16 Agustus 2025  
**Repository Implementasi:** [DistKV-Erasure-Coding](https://github.com/MuhamadAjiW/DistKV-Erasure-Coding)

## Deskripsi

Penelitian ini menganalisis kinerja erasure coding dibandingkan replikasi pada sistem distributed key-value store database, khususnya pada response time sistem dalam operasi write dan read. Dengan berkembangnya kebutuhan penyimpanan data dan komputasi intensif, solusi redundansi data yang efisien menjadi semakin penting. Erasure coding menawarkan solusi untuk meningkatkan ketahanan sistem dengan efisiensi penyimpanan yang lebih baik dibandingkan replikasi tradisional.

**Temuan Utama:** Penelitian ini membuktikan adanya threshold performance dimana erasure coding dapat mengungguli replikasi dalam operasi write pada kondisi bandwidth terbatas dan payload besar, namun replikasi konsisten unggul dalam operasi read.

## Metodologi

Penelitian ini mengimplementasikan sistem database terdistribusi yang mendukung kedua mekanisme redundansi dan membuat sistem benchmark yang dapat memvariasikan parameter bandwidth jaringan dan ukuran payload. Sistem dikembangkan menggunakan:

- **Bahasa Pemrograman:** Rust
- **Persistent Storage:** RocksDB
- **Erasure Coding Algorithm:** Reed-Solomon
- **Consensus Protocol:** OmniPaxos
- **Benchmarking Tool:** k6 dan mpstat
- **Environment:** Virtual machine dengan traffic control untuk simulasi jaringan

### Arsitektur Sistem

Sistem terdiri dari komponen-komponen berikut:
1. **Node**: Unit dasar sistem terdistribusi yang mengelola data
2. **Data Collector**: Script eksternal untuk pengumpulan data eksperimen
3. **Benchmark Component**: Melakukan pengujian kinerja dengan variasi parameter
4. **In-memory Key-Value Store**: Cache untuk meningkatkan kinerja read operations
5. **Persistent Database**: RocksDB untuk penyimpanan data permanen

## Hasil Penelitian

### Temuan Utama

1. **Threshold Performance pada Write Operations**: Erasure coding memiliki threshold kondisi ketika response time lebih rendah dibandingkan replikasi. Kondisi ini terjadi ketika bandwidth jaringan terbatas dan ukuran payload cukup besar. Penelitian mengidentifikasi kurva matematis yang menentukan titik impas antara kedua sistem.

2. **Read Operations**: Replikasi konsisten mengungguli erasure coding dalam operasi read karena kompleksitas rekonstruksi data. Secara matematis: `L_EC = T_data + T_rekonstruksi + T_shard` vs `L_REP = T_data`, sehingga `ΔL = T_rekonstruksi + T_shard > 0`.

3. **Use Case Suitability**: Penggunaan erasure coding tidak sesuai untuk distributed key-value store database yang beroperasi dengan data kecil di data center berkapasitas tinggi.

### Analisis Performa Berdasarkan Skenario

#### Skenario 1: Internet Cepat + Payload Kecil
- **Bandwidth**: 10 Gbps
- **Hasil**: Replikasi unggul dalam write dan read operations
- **Penyebab**: Overhead komputasi erasure coding lebih dominan daripada penghematan transfer data

#### Skenario 2: Internet Lambat + Payload Besar  
- **Bandwidth**: 1 Mbps
- **Hasil**: Erasure coding unggul dalam write operations, replikasi tetap unggul dalam read
- **Penyebab**: Penghematan bandwidth transfer data lebih signifikan daripada overhead komputasi

#### Skenario 3: Internet Menengah + Payload Besar
- **Bandwidth**: 40 Mbps (rata-rata Indonesia)
- **Hasil**: Terdapat titik impas yang dapat dimodelkan secara matematis
- **Model**: Ridge regression digunakan untuk menentukan boundary curve

### Visualisasi Kinerja

#### Heatmap Analysis
Penelitian menghasilkan heatmap yang menunjukkan rasio kinerja erasure coding vs replikasi:
- **Warna biru (nilai negatif)**: Erasure coding unggul
- **Warna merah (nilai positif)**: Replikasi unggul
- **Transisi warna**: Menunjukkan threshold boundary

#### Boundary Curve Model
Model regresi menghasilkan kurva matematis yang dapat memprediksi:
- Kondisi optimal untuk erasure coding
- Kondisi optimal untuk replikasi
- Titik impas berdasarkan bandwidth dan payload size

### Analisis Matematis

#### Write Operations Latency
```
Erasure Coding: L_EC = T_encode + T_transfer_shards + T_consensus
Replikasi: L_REP = T_transfer_full + T_consensus

Threshold terjadi ketika: T_encode + T_transfer_shards < T_transfer_full
```

#### Read Operations Latency
```
Erasure Coding: L_EC = T_data + T_rekonstruksi + T_shard
Replikasi: L_REP = T_data

Selisih: ΔL = T_rekonstruksi + T_shard (selalu positif)
```

### Rekomendasi Implementasi

#### Gunakan Erasure Coding jika:
- Payload size > threshold (berdasarkan kurva boundary)
- Bandwidth terbatas (< 40 Mbps untuk payload besar)
- Prioritas efisiensi storage tinggi
- Rasio write:read tinggi
- Infrastruktur edge computing atau IoT

#### Gunakan Replikasi jika:
- Payload size kecil (< 1MB)
- Bandwidth tinggi (> 100 Mbps)
- Prioritas read performance
- Data center environment
- Aplikasi real-time dengan latensi rendah

## Implementasi

Implementasi lengkap sistem dapat ditemukan di: [DistKV-Erasure-Coding Repository](https://github.com/MuhamadAjiW/DistKV-Erasure-Coding)

### Fitur Sistem

- Operasi read dan write pada key-value store
- Dukungan erasure coding (Reed-Solomon) dan replikasi
- Benchmark otomatis dengan variasi parameter (bandwidth, payload size)
- Monitoring kinerja real-time dengan trace logging
- Simulasi kondisi jaringan menggunakan traffic control
- In-memory cache untuk optimasi read operations
- Data collection dan analisis regresi otomatis

### Batasan Implementasi

- Operasi terbatas pada read dan write (tidak ada delete atau transaksi kompleks)
- Testing dilakukan pada lingkungan lokal dengan simulasi jaringan
- Konfigurasi membership cluster bersifat statis
- Konfigurasi erasure coding (4 data shard, 2 parity shard) statis
- Model regresi hanya valid pada kondisi eksperimen yang dilakukan
- Tidak mempertimbangkan faktor hardware (CPU, memory, disk speed)

## Setup dan Penggunaan

Sistem menggunakan file konfigurasi `config.json` untuk pengaturan node dan parameter erasure coding. Benchmark dapat dijalankan menggunakan script `scripts.sh` dengan perintah:

```bash
# Menjalankan semua node
./scripts.sh run_all

# Menjalankan benchmark suite lengkap dengan variasi parameter
./scripts.sh run_bench_suite

# Menjalankan benchmark spesifik
./scripts.sh run_bench_write_bigload_avgnet
./scripts.sh run_bench_read_smload_fastnet

# Mengumpulkan dan menganalisis data
./scripts.sh collect_data
./scripts.sh analyze_regression

# Membersihkan data dan log
./scripts.sh clean
```

### Konfigurasi Benchmark

```json
{
  "scenarios": {
    "fastnet_smload": {"bandwidth": "10gbps", "payload": "1kb-10kb"},
    "slownet_bigload": {"bandwidth": "1mbps", "payload": "100kb-1mb"},
    "avgnet_bigload": {"bandwidth": "40mbps", "payload": "10kb-500kb"}
  },
  "erasure_coding": {"data_shards": 4, "parity_shards": 2},
  "replication_factor": 3
}
```

## Struktur Repository

```
├── src/                           # Source code LaTeX thesis
│   ├── chapters/                  # Bab-bab thesis
│   │   ├── chapter-4/            # Implementasi dan evaluasi
│   │   │   ├── analisis-benchmark/  # Analisis mendalam
│   │   │   │   ├── 02-analisis-write.tex    # Analisis write ops
│   │   │   │   ├── 03-analisis-read.tex     # Analisis read ops
│   │   │   │   └── 04-analisis-keseluruhan.tex
│   │   └── appendix/             # Grafik dan data tambahan
│   ├── resources/                # Gambar dan visualisasi
│   │   └── chapter-4/           # Grafik hasil benchmark
│   │       ├── write_*_heatmap.png      # Heatmap analysis
│   │       ├── write_*_regression.png   # Model regresi
│   │       ├── write_*_boundary.png     # Boundary curve
│   │       └── read_*_line.png          # Line charts
├── references/                    # Paper dan referensi penelitian
│   ├── erasure-coding/           # Paper tentang erasure coding
│   ├── distributed-system/       # Paper sistem terdistribusi
│   └── consensus/                # Paper algoritma konsensus
├── output/                       # Output final thesis
│   ├── thesis.pdf               # Thesis lengkap
│   ├── paper.pdf                # Paper publikasi
│   └── poster.pdf               # Poster presentasi
└── README.md                     # Dokumentasi ini
```

## Key Graphs dan Visualisasi

### 1. Threshold Performance (Write Operations)
- `write_bigload_avgnet_boundary.png`: Kurva matematis titik impas
- `write_bigload_avgnet_heatmap.png`: Heatmap rasio kinerja
- `write_bigload_avgnet_regression.png`: Model regresi 3D

### 2. Performance Comparison
- `write_smload_fastnet.png`: Kondisi menguntungkan replikasi
- `write_bigload_slownet.png`: Kondisi menguntungkan erasure coding
- `read_*_line.png`: Konsistensi keunggulan replikasi pada read

### 3. Trace Analysis
- `write_*_trace.png`: Breakdown waktu operasi per komponen
- Menunjukkan kontribusi encoding, transfer, dan consensus

## Kesimpulan

### Temuan Kunci

1. **Threshold Performance Terbukti**: Erasure coding memiliki threshold ketika response time lebih rendah dibandingkan replikasi untuk operasi write pada kondisi bandwidth terbatas dan payload besar. Threshold ini dapat dimodelkan secara matematis menggunakan boundary curve.

2. **Asymmetric Performance**: Tidak terdapat threshold serupa untuk operasi read - replikasi selalu mengungguli erasure coding karena overhead rekonstruksi data yang tidak dapat dihindari.

3. **Context-Dependent Optimization**: Untuk use case distributed key-value store dengan data kecil di data center berkapasitas tinggi, replikasi lebih sesuai. Erasure coding optimal untuk sistem dengan data besar dan infrastruktur jaringan terbatas.

4. **Trade-off Fundamental**: Keunggulan erasure coding dalam efisiensi storage dan write performance (kondisi tertentu) harus dibayar dengan penurunan read performance yang konsisten.

### Implikasi Praktis

- **Data Center Applications**: Replikasi lebih sesuai untuk aplikasi dengan data kecil dan bandwidth tinggi
- **Edge Computing**: Erasure coding dapat memberikan keuntungan pada lingkungan dengan bandwidth terbatas
- **Hybrid Approach**: Sistem dapat menggunakan kedua metode berdasarkan karakteristik data dan kondisi jaringan
- **Cost Optimization**: Erasure coding menawarkan penghematan storage cost yang signifikan untuk data besar

## Keywords

Distributed System, Erasure Coding, Replication, Database, Key-Value Store, Performance Analysis, Threshold Performance, Reed-Solomon, OmniPaxos, RocksDB, Rust

## Kontribusi Penelitian

1. **Model Matematis**: Pengembangan boundary curve untuk prediksi threshold performance
2. **Implementasi Sistem**: Distributed key-value store dengan dukungan erasure coding dan replikasi
3. **Metodologi Benchmark**: Framework pengujian dengan variasi bandwidth dan payload size
4. **Analisis Empiris**: Validasi threshold performance melalui eksperimen terkontrol

## Lisensi

Penelitian ini dilakukan sebagai tugas akhir di Institut Teknologi Bandung. Semua hak cipta dilindungi sesuai dengan ketentuan akademik yang berlaku.

## Publikasi dan Sitasi

```bibtex
@mastersthesis{wibisono2025erasure,
  title={Analisis Kinerja Erasure Coding terhadap Replikasi pada Key-Value Store Database Terdistribusi},
  author={Wibisono, Muhamad Aji},
  year={2025},
  school={Institut Teknologi Bandung},
  type={Tugas Akhir},
  advisor={Kistijantoro, Achmad Imam}
}
```

## Template Credits

LaTeX template disesuaikan dari [template thesis ITB](https://github.com/IloveNooodles/tugas-akhir-if-itb) yang tersedia.