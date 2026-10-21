# Portal Peluang dan Potensi Investasi Kota Tegal

Paket awal lengkap: Google Sheets sebagai database, Google Apps Script sebagai backend/API, dan `index.html` sebagai frontend yang dapat dihosting di GitHub Pages.

## Isi paket
- `Template_Sheet_Portal_Investasi_Tegal.xlsx` — template spreadsheet dengan tab proyek, potensi, profil daerah, berita, minat investor, log, pengaturan, pengguna admin, dan petunjuk.
- `Code.gs` — backend Apps Script.
- `index.html` — website responsif dengan katalog, filter, detail proyek, statistik, peta, formulir minat, berita, dan panel admin.
- `README.md` — panduan pemasangan.

## Penting sebelum produksi
1. Data contoh di sheet `PROYEK`, `POTENSI`, dan `BERITA` berstatus `DRAFT`; tidak ditampilkan di website publik. Ganti dengan data resmi dan verifikasi.
2. Ubah teks profil, kontak, gambar, dan URL agar sesuai sumber resmi.
3. Kunci admin jangan ditulis di `index.html`, GitHub, atau sheet publik.
4. Integrasi WhatsApp/reminder tidak diaktifkan pada paket ini.
5. Peta menampilkan proyek yang memiliki koordinat; pastikan koordinat dan hak penggunaan data valid.
6. Sebelum website digunakan publik, instansi perlu menetapkan kebijakan privasi, petugas yang berwenang, retensi data, prosedur backup, dan proses persetujuan publikasi proyek.

## A. Buat Google Sheet
1. Upload `Template_Sheet_Portal_Investasi_Tegal.xlsx` ke Google Drive.
2. Klik kanan > Buka dengan Google Spreadsheet.
3. Pastikan tab sheet tidak diganti namanya.
4. Salin ID spreadsheet dari URL Google Sheets: bagian di antara `/d/` dan `/edit`.

## B. Siapkan Apps Script
1. Buka `script.google.com` dan buat project baru.
2. Tempel isi `Code.gs` ke file `Code.gs`.
3. Di **Project Settings > Script Properties**, tambahkan:
   - `SPREADSHEET_ID` = ID Google Sheet
   - `ADMIN_API_KEY` = kunci acak panjang minimal 32 karakter. Contoh buat sendiri, jangan gunakan contoh publik.
4. Simpan.
5. Pilih fungsi `setupPortal` lalu klik **Run** dan berikan otorisasi. Fungsi ini akan menambahkan tab/header yang belum ada dan menyimpan ID spreadsheet bila perlu.
6. Deploy > New deployment > type **Web app**:
   - Execute as: **Me**
   - Who has access: **Anyone** (dibutuhkan untuk pengunjung GitHub Pages; konfirmasikan sesuai kebijakan keamanan instansi)
7. Deploy, salin URL yang berakhiran `/exec`.

## C. Hubungkan frontend
1. Buka `index.html`.
2. Cari:
   `API_URL: 'PASTE_URL_WEB_APP_APPS_SCRIPT_DI_SINI'`
3. Ganti dengan URL `/exec` dari deployment.
4. Upload `index.html` ke repository GitHub Pages.
5. Buka website, lalu uji data publik. Untuk uji admin, klik `Admin` di footer dan masukkan `ADMIN_API_KEY`.

## D. Publikasikan proyek
Di sheet `PROYEK`, ubah `STATUS_PUBLIKASI` menjadi `PUBLISHED` hanya setelah verifikasi. Isi minimum:
- `NAMA_PROYEK`, `SEKTOR`, `DESKRIPSI`, `STATUS_PUBLIKASI`
- nilai investasi harus angka rupiah (tanpa teks Rp/miliar); misalnya 5000000000
- URL gambar/dokumen harus HTTPS
- koordinat harus decimal latitude/longitude yang benar
- `SUMBER_DATA`, `TANGGAL_UPDATE`, dan `CATATAN_RISIKO`

Status publikasi yang didukung: `DRAFT`, `REVIEW`, `PUBLISHED`, `ARCHIVED`.

## E. Tes fungsi
- Buka URL Apps Script `/exec?action=health` untuk cek API.
- Buka `/exec?action=publicData` untuk cek data publik.
- Masuk admin pada website; buat proyek draft, edit, lalu ubah ke `PUBLISHED`.
- Kirim formulir minat investor dengan data uji, lalu cek tab `MINAT_INVESTOR`.
- Periksa `LOG_AKTIVITAS`.

## F. Catatan teknis
- Pembacaan lintas domain menggunakan JSONP agar frontend GitHub Pages dapat memanggil Apps Script.
- Perubahan data dan formulir menggunakan POST form-urlencoded melalui iframe tersembunyi untuk menghindari ketergantungan pada CORS response. Karena browser tidak dapat membaca balasan POST lintas domain, UI memberi konfirmasi pengiriman dan data perlu dimuat ulang untuk memastikan perubahan benar-benar tersimpan.
- Untuk instalasi produksi dengan kebutuhan keamanan/keandalan lebih tinggi, pertimbangkan frontend yang disajikan langsung oleh Apps Script atau backend/API dengan autentikasi dan CORS yang dirancang khusus. Jangan menganggap kunci admin sederhana sebagai pengganti audit keamanan.
- Cache data publik: 15 menit. Saat ada perubahan proyek/potensi/berita/profil, cache dihapus.
- Jangan masukkan data pribadi atau informasi rahasia yang tidak diperlukan.
