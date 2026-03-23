# Drawing using HTML Canvas Graphics

**Gambar Hasil:** ![Hasil Render Canvas](image\Hasil.png) 
---

## Deskripsi
Pada karya ini, saya merancang dan mengodekan ilustrasi burung bergaya *low-poly* dengan latar belakang pancaran sinar (*sunburst*) secara murni menggunakan HTML Canvas. Gambar ini dibentuk dari gabungan banyak bidang bersudut (poligon) yang masing-masing diisi dengan satu warna penuh. 

Dalam proses pengerjaannya, posisi setiap bentuk ditentukan secara presisi melalui titik koordinat absolut di atas kanvas. Agar basis kode tetap rapi dan *maintainable*, proses penggambarannya saya pecah menjadi beberapa fungsi modular (seperti ekor, badan, sayap, dan kepala). Lapisan-lapisan tersebut kemudian dirender secara berurutan, ditumpuk dari objek yang paling belakang hingga yang terdepan.

## Konsep Transformasi dan *State Management*
Meskipun penyusunan struktur utama burung mengandalkan koordinat absolut demi presisi bentuk, proyek ini juga mengimplementasikan konsep fundamental dari Canvas Graphics, yaitu Transformasi dan *State Isolation*:

* **Translasi (Translation)**
  Digunakan secara spesifik pada fungsi `drawEye()`. Untuk menggambar bola mata, titik acuan atau titik pusat (*origin*) kanvas dipindahkan sementara menggunakan fungsi `g.translate(490, 215)`. Dengan memodifikasi *origin* ini, objek lingkaran simetris dapat digambar cukup dengan perintah `circle(8)` tepat di titik pusat koordinat `(0,0)` yang baru, tanpa harus menghitung titik tengah busur secara manual.

* **Isolasi State (Save & Restore)**
  Untuk mencegah transformasi (seperti perpindahan `translate` pada mata) dan perubahan properti (seperti warna `fillStyle`) merusak pengaturan global kanvas, saya menerapkan metode `g.save()` di awal setiap fungsi objek dan `g.restore()` di akhir fungsi. Hal ini menjamin bahwa pengaturan *style* pada setiap bagian gambar terisolasi dengan aman tanpa bocor ke elemen lain.

## *Rendering Pipeline* (Proses Penggambaran)
Proses *rendering* pada Canvas dieksekusi secara prosedural dengan menerapkan *Painter's Algorithm* (metode melukis di mana lapisan paling belakang digambar lebih dulu, lalu ditumpuk bertahap hingga ke lapisan terdepan). Berikut adalah tahapan pembuatannya:

1. **Fungsi Pembantu (*Helper*)**
   Mengembangkan fungsi modular `poly(pts)` untuk mengeksekusi *looping path* yang secara otomatis menghubungkan deretan titik koordinat (x, y) menjadi sebuah poligon utuh, serta `circle(r)` untuk menyederhanakan pembuatan lingkaran penuh.

2. **Latar Belakang (*Background*)**
   Membangun latar belakang *sunburst* menggunakan 9 poligon segitiga/segiempat dengan warna kontras, di mana seluruh titik pusarannya memancar dari satu titik fokus di koordinat `(800, 620)`.

3. **Pelapisan Belakang (*Background Objects*)**
   Mulai merender burung dari lapisan terdalam, yaitu ekor (`drawTail`), dilanjutkan dengan menggambar tenggeran hitam melintang (`drawPerch`), dan menyusun poligon sayap belakang yang berwarna hijau tua (`drawWing`).

4. **Pelapisan Tengah (*Midground Objects*)**
   Menyusun anatomi utama tubuh burung, dimulai dari area perut hijau-kuning (`drawBody`), area transisi leher dan dada atas (`drawNeckChest`), lalu bagian kepala (`drawHead`) yang tersusun dari poligon oranye dan mahkota ungu.

5. **Detail dan Lapisan Depan (*Foreground*)**
   Sebagai sentuhan akhir, detail anatomi diselesaikan dengan menggambar paruh berlapis (`drawBeak`), menggambar mata menggunakan transformasi koordinat (`drawEye`), dan memposisikan paha serta jari-jari kaki (`drawLegs`) di lapisan paling depan agar natural menutupi area tenggeran.