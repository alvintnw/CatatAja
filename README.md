# CatatAja

> **"Catat jualan cukup ngobrol."**

CatatAja adalah asisten pencatat keuangan berbasis AI untuk pemilik warung dan UMKM kuliner kecil di Indonesia. Pemilik usaha cukup mengetik transaksi dengan bahasa sehari-hari, lalu AI memahami, mencatat ke Google Sheets, dan merangkum untung-rugi hari itu. Tidak perlu mengisi tabel atau belajar aplikasi akuntansi.

Proyek ini dibangun sebagai flow [Langflow](https://www.langflow.org/) (`CatatAja_Langflow_Flow.json`) dengan integrasi Google Workspace melalui [Composio](https://composio.dev/).

---

## Fitur

- **Memahami bahasa sehari-hari.** Mengerti singkatan seperti `rb`, `ribu`, `k` (ribuan), `@` (harga satuan), `x` (jumlah), `goceng` (5.000), `ceban` (10.000), dan `seceng` (1.000).
- **Pencatatan otomatis ke Google Sheets.** Satu baris per item di spreadsheet **"CatatAja - Buku Kas"** dengan kolom `Tanggal | Jenis | Item | Jumlah | Harga Satuan | Total`. Penjualan dicatat sebagai *Pemasukan*, pembelian bahan sebagai *Pengeluaran*.
- **Ringkasan harian.** Setelah mencatat, AI menampilkan ulang transaksi dan menghitung total pemasukan, total pengeluaran, serta selisih (untung/rugi).
- **Bertanya saat data tidak jelas.** Jika harga, barang, atau jumlah tidak jelas, AI tidak menebak dan mengajukan satu pertanyaan singkat.
- **Draft email restock (opsional).** Atas permintaan, AI membuat *draft* email ke supplier di Gmail. Email tidak pernah dikirim otomatis, pemilik yang mengirimnya.
- **Pengingat jadwal (opsional).** Atas permintaan, AI membuat event di Google Calendar, misalnya belanja mingguan atau bayar supplier.

## Contoh Penggunaan

**Mencatat penjualan**

```
Hari ini laku nasi uduk 15 porsi @12rb, es teh 20 gelas @goceng
```

AI akan mencatat ke Google Sheets dan membalas dengan rincian transaksi serta ringkasan hari ini.

**Data kurang jelas**

```
Beli telur 3 kilo
```

AI akan bertanya: *"Harga telurnya berapa per kilo?"* sebelum mencatat.

**Restock bahan**

```
Tolong buatkan email pesan beras 25 kg ke supplier@contoh.com
```

AI membuat draft di Gmail, lalu pemilik meninjau dan mengirimnya sendiri.

**Pengingat**

```
Ingatkan aku belanja ke pasar tiap Senin jam 5 pagi
```

## Arsitektur Flow

```
Chat Input ──► Agent ──► Chat Output
                 ▲
                 ├── Tool: Google Calendar (Composio)
                 ├── Tool: Gmail (Composio)
                 └── Tool: Google Sheets (Composio)
```

| Komponen | Keterangan |
| --- | --- |
| **Chat Input / Chat Output** | Antarmuka percakapan di Playground Langflow |
| **Agent** | Inti logika. Berisi *system prompt* berbahasa Indonesia untuk pencatatan, ringkasan, dan batasan tugas |
| **Model** | Google Generative AI, default `gemini-3.5-flash-lite` (dengan tool calling) |
| **Tools** | Integrasi Google Workspace via Composio (Sheets, Gmail, Calendar) |
| **Utilitas** | Tool tanggal saat ini aktif, sehingga transaksi tanpa tanggal otomatis memakai hari ini |

## Prasyarat

- [Langflow](https://docs.langflow.org/get-started-installation) (flow ini diekspor dari versi **1.11.5**)
- API key **Google AI (Gemini)**
- Akun **[Composio](https://composio.dev/)** yang sudah terhubung ke akun Google (Sheets, Gmail, Calendar)
- Spreadsheet Google Sheets bernama **`CatatAja - Buku Kas`** dengan header kolom:

  | Tanggal | Jenis | Item | Jumlah | Harga Satuan | Total |
  | --- | --- | --- | --- | --- | --- |

## Cara Menjalankan

1. **Clone repositori**

   ```bash
   git clone https://github.com/alvintnw/CatatAja.git
   cd CatatAja
   ```

2. **Jalankan Langflow**

   ```bash
   pip install langflow
   langflow run
   ```

   Lalu buka `http://localhost:7860`.

3. **Impor flow.** Di Langflow, pilih *Import* lalu unggah `CatatAja_Langflow_Flow.json`.

4. **Atur kredensial.**
   - Pada komponen **Agent**, isi Google API Key (atau set environment variable `GOOGLE_API_KEY`).
   - Pada komponen Composio (Google Sheets, Gmail, Google Calendar), isi API key Composio dan hubungkan akun Google Anda.

5. **Siapkan spreadsheet** `CatatAja - Buku Kas` seperti pada bagian Prasyarat.

6. **Coba di Playground.** Ketik transaksi, misalnya `jual nasi goreng 10 porsi @15rb`.

> Jangan pernah meng-commit API key atau token ke repositori ini.

## Batasan

- CatatAja **bukan akuntan atau konsultan pajak**. Untuk saran pajak atau pembukuan formal, tanyakan ke ahli.
- Hanya mencatat data transaksi usaha. Tidak meminta atau menyimpan data pribadi pelanggan.
- Tidak mengarang data. Jika informasi tidak ada di pesan, AI akan bertanya.
- Email restock hanya dibuat sebagai draft dan tidak pernah dikirim otomatis.

## Struktur Repositori

```
CatatAja/
├── CatatAja_Langflow_Flow.json   # Flow Langflow (agent, prompt, dan tools)
└── README.md
```

## Pengembangan Selanjutnya

- Laporan mingguan dan bulanan
- Integrasi dengan WhatsApp agar pencatatan bisa langsung dari chat
- Pencatatan stok bahan baku

## Pembuat

Dibuat oleh [alvintnw](https://github.com/alvintnw).
