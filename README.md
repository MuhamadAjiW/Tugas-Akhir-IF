# Analisis Erasure Coding terhadap Replikasi pada Key-Value Store Database Terdistribusi

Tugas Akhir - Institut Teknologi Bandung  
Program Studi Teknik Informatika  

**Penulis:** Muhamad Aji Wibisono (13521095)  
**Pembimbing:** Achmad Imam Kistijantoro, S.T, M.Sc., Ph.D.  
**Tanggal:** 17 Juli 2025

## Deskripsi

Penelitian ini menganalisis kinerja erasure coding dibandingkan replikasi pada sistem distributed key-value store database, khususnya pada response time sistem dalam operasi write dan read. Dengan berkembangnya kebutuhan penyimpanan data dan komputasi intensif, solusi redundansi data yang efisien menjadi semakin penting. Erasure coding menawarkan solusi untuk meningkatkan ketahanan sistem dengan efisiensi penyimpanan yang lebih baik dibandingkan replikasi tradisional.

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

1. **Threshold Performance**: Erasure coding memiliki threshold kondisi ketika response time lebih rendah dibandingkan replikasi pada operasi write. Kondisi ini terjadi ketika bandwidth jaringan terbatas dan ukuran payload cukup besar.

2. **Read Operations**: Replikasi konsisten mengungguli erasure coding dalam operasi read karena kompleksitas rekonstruksi data.

3. **Use Case Suitability**: Penggunaan erasure coding tidak sesuai untuk distributed key-value store database yang beroperasi dengan data kecil di data center berkapasitas tinggi.

### Analisis Performa

- **Erasure Coding**: Unggul dalam efisiensi penyimpanan dan latensi write operations pada beban kerja dengan data besar dan bandwidth terbatas
- **Replikasi**: Lebih baik untuk operasi read dan write pada data kecil dengan bandwidth tinggi, tetapi membutuhkan biaya penyimpanan yang lebih tinggi

### Rekomendasi

Pemilihan antara erasure coding dan replikasi harus mempertimbangkan:
- Karakteristik beban kerja
- Rasio operasi baca dan tulis
- Ukuran data
- Infrastruktur jaringan yang tersedia

## Implementasi

Implementasi lengkap sistem dapat ditemukan di: [DistKV-Erasure-Coding Repository](https://github.com/MuhamadAjiW/DistKV-Erasure-Coding)

### Fitur Sistem

- Operasi read dan write pada key-value store
- Dukungan erasure coding dan replikasi
- Benchmark otomatis dengan variasi parameter
- Monitoring kinerja real-time
- Simulasi kondisi jaringan yang berbeda

### Batasan Implementasi

- Operasi terbatas pada read dan write (tidak ada delete atau transaksi kompleks)
- Testing dilakukan pada lingkungan lokal dengan simulasi
- Konfigurasi membership cluster bersifat statis
- Konfigurasi erasure coding (data shard dan parity shard) statis

## Setup dan Penggunaan

Sistem menggunakan file konfigurasi `config.json` untuk pengaturan node dan parameter erasure coding. Benchmark dapat dijalankan menggunakan script `scripts.sh` dengan perintah:

```bash
# Menjalankan semua node
./scripts.sh run_all

# Menjalankan benchmark suite lengkap
./scripts.sh run_bench_suite

# Membersihkan data dan log
./scripts.sh clean
```

## Struktur Repository

```
├── src/                    # Source code LaTeX thesis
├── references/            # Paper dan referensi penelitian
├── build/                 # Build output
├── chapters/              # Bab-bab thesis
├── config/                # Konfigurasi LaTeX
└── output/               # Output final thesis
```

## Kesimpulan

1. Erasure coding memiliki threshold ketika response time lebih rendah dibandingkan replikasi untuk operasi write pada kondisi bandwidth terbatas dan payload besar
2. Tidak terdapat threshold serupa untuk operasi read, dimana replikasi selalu mengungguli erasure coding
3. Untuk use case distributed key-value store dengan data kecil, replikasi lebih sesuai
4. Erasure coding tetap dapat dipertimbangkan untuk sistem dengan data besar dan infrastruktur jaringan terbatas

## Keywords

Distributed System, Erasure Coding, Replication, Database, Key-Value Store

## Lisensi

Penelitian ini dilakukan sebagai tugas akhir di Institut Teknologi Bandung. Semua hak cipta dilindungi sesuai dengan ketentuan akademik yang berlaku.

## Template Credits

LaTeX template disesuaikan dari [template thesis ITB](https://github.com/IloveNooodles/tugas-akhir-if-itb) yang tersedia.