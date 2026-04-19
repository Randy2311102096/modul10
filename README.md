Markdown
# Laporan Praktikum Laravel + AJAX (Modul 10)

Laporan ini disusun untuk menjelaskan implementasi pengambilan data secara asinkron menggunakan Laravel dan AJAX tanpa menggunakan database (menggunakan file JSON lokal).

---

## 1. Persiapan Lingkungan Kerja
* **Framework**: Laravel 11
* **Server Lokal**: Laragon (Apache & PHP 8.x)
* **Editor**: Visual Studio Code
* **Library Tambahan**: jQuery 3.7.1 (via CDN) & Bootstrap 5 (via CDN)

---

## 2. Struktur Kode Program

### A. Route (`routes/web.php`)
Route digunakan untuk mendefinisikan URL yang dapat diakses oleh browser dan skrip AJAX.
```php
use App\Http\Controllers\MahasiswaController;

// Menampilkan halaman utama (Blade)
Route::get('/', [MahasiswaController::class, 'index']);

// Endpoint untuk mengambil data JSON (Data Source)
Route::get('/ambil-data', [MahasiswaController::class, 'getData']);
B. Controller (app/Http/Controllers/MahasiswaController.php)
Controller ini bertugas menangani permintaan data. Terdapat dua fungsi utama: index() untuk menampilkan halaman, dan getData() untuk menyediakan data dalam format JSON.

PHP
public function getData() {
    // Menentukan lokasi file JSON di dalam folder storage
    $path = storage_path('app/mahasiswa.json');

    // Cek keberadaan file untuk menghindari error
    if (!file_exists($path)) {
        return response()->json([]); 
    }

    // Membaca isi file dan mengirimkan kembali sebagai response JSON
    $json = file_get_contents($path);
    $data = json_decode($json, true);
    return response()->json($data);
}
C. Sumber Data (storage/app/mahasiswa.json)
Data mahasiswa disimpan secara statis dalam format array objek JSON.

JSON
[
    {
        "nama": "Rizky Pratama",
        "nim": "2201001",
        "kelas": "4A",
        "prodi": "Teknik Informatika"
    },
    ...
]
D. View & Script AJAX (resources/views/halaman_utama.blade.php)
Menggunakan AJAX untuk mengambil data sehingga halaman tidak perlu dimuat ulang (no-reload).

JavaScript
$('#btnTampil').on('click', function() {
    $.ajax({
        url: "{{ url('/ambil-data') }}",
        type: "GET",
        dataType: "json",
        success: function(response) {
            let tabel = `<table class="table">...</table>`;
            // Loop data menggunakan $.each
            $.each(response, function(i, item) {
                tabel += `<tr><td>${item.nama}</td>...</tr>`;
            });
            $('#areaData').html(tabel);
        }
    });
});
3. Tata Cara Penggunaan
Menjalankan Server:
Buka Laragon, klik Start All. Pastikan Apache berjalan di port 80.

Mengakses Halaman:
Buka browser dan akses alamat http://localhost/praktikummodul10/public/.

Interaksi:
Klik tombol "Tampilkan Data".

Proses Kerja:

Browser mengirim permintaan latar belakang ke /ambil-data.

Controller membaca file mahasiswa.json.

Controller mengirim balik data dalam format JSON.

jQuery menerima data dan menyusunnya ke dalam tabel HTML secara instan.

link drive folder dan file code program : https://drive.google.com/drive/folders/1YrtRCNOYl5CIrXctgv22ciZEVdkE4m2L?usp=sharing
