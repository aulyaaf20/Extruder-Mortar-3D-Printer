# Panduan Menggunakan Klipper dengan Host Komputer Windows dan SKR Mini E3 V3

## Pendahuluan
Klipper adalah firmware open-source yang dirancang untuk meningkatkan kinerja 3D printer dengan menggunakan kekuatan pemrosesan yang terbagi dua, yaitu host komputer dan mikrokontroler. Panduan ini menjelaskan bagaimana cara menjalankan Klipper di komputer Windows menggunakan WSL (Windows Subsystem for Linux) dan mengonfigurasi SKR Mini E3 V3 sebagai mikrokontroler 3D printer Anda.

---

## 1. Instalasi WSL dan Klipper di Host Windows

### 1.1. Instalasi WSL (Windows Subsystem for Linux)
WSL memungkinkan Anda untuk menjalankan Linux di dalam Windows tanpa perlu mesin virtual. Berikut adalah langkah-langkah untuk menginstal WSL dan Ubuntu:

1. **Aktifkan WSL**:
    - Buka **PowerShell** sebagai Administrator.
    - Jalankan perintah berikut untuk mengaktifkan WSL dan menginstal distribusi Ubuntu:
      ```bash
      wsl --install
      ```
    - Setelah proses selesai, komputer akan diminta untuk di-restart.

2. **Jalankan Ubuntu**:
    - Setelah restart, buka Ubuntu dari **Start Menu** untuk menyelesaikan instalasi.
    - Atur nama pengguna dan kata sandi yang akan digunakan di lingkungan WSL.

3. **Update Ubuntu**:
    - Setelah masuk ke terminal Ubuntu, jalankan perintah berikut untuk memperbarui paket:
      ```bash
      sudo apt update && sudo apt upgrade
      ```

Untuk panduan lebih lengkap tentang instalasi WSL, Anda bisa merujuk ke [Tutorial Running Klipper on a Windows PC Using WSL](https://3dpandme.com/2022/08/25/tutorial-running-klipper-on-a-windows-pc-using-wsl/) (3D Print and Me, 2022).

### 1.2. Instalasi Klipper di WSL
Setelah WSL dan Ubuntu terinstal, langkah selanjutnya adalah instalasi Klipper:


1. **Clone repository Klipper**:
    - Unduh kode sumber Klipper dari GitHub:
      ```bash
      git clone https://github.com/Klipper3d/klipper.git
      cd klipper
      ```

2. **Install seluruh kebutuhan pada KIAUH**:
    - install 3 hal, pertama adalah Klipper, Moonraker, dan Mainsail.

3. **Konfigurasi Klipper**:
    - Jalankan perintah untuk membuka menu konfigurasi Klipper:
      ```bash
      make menuconfig
      ```

    - Pada menu ini, pilih pengaturan sesuai dengan mikrokontroler yang digunakan (lihat bagian selanjutnya untuk detail konfigurasi SKR Mini E3 V3).

---

## 2. Konfigurasi dan Flashing Firmware untuk SKR Mini E3 V3

### 2.1. Perbedaan Mikrokontroler SKR Mini E3 V3
SKR Mini E3 V3 adalah papan mikrokontroler berbasis STM32G0B1 atau STM32G0B0, yang mendukung koneksi USB untuk flashing dan komunikasi dengan Klipper. Penting untuk memastikan bahwa konfigurasi sesuai dengan spesifikasi mikrokontroler ini.

### 2.2. Konfigurasi Firmware untuk SKR Mini E3 V3
1. **Buka menu konfigurasi**:
    - Pada direktori Klipper, jalankan perintah berikut:
      ```bash
      make menuconfig
      ```

2. **Pilih pengaturan berikut**:
   - **Microcontroller Architecture**: `STMicroelectronics STM32`
   - **Processor model**: `STM32G0B0 or STM32G0B1`
   - **Bootloader offset**: `8KiB`
   - **Clock Reference**: `8 MHz crystal`
   - **Communication interface**: `USB (on PA11/PA12)`

3. **Simpan dan keluar** dari menu konfigurasi.

4. **Kompilasi firmware**:
    - Setelah konfigurasi selesai, jalankan perintah berikut untuk mengompilasi firmware:
      ```bash
      make clean
      make -j4
      ```

5. **Temukan file hasil kompilasi**:
    - Setelah kompilasi selesai, file firmware yang dihasilkan bernama `klipper.bin`. File ini akan digunakan untuk mem-flash SKR Mini E3 V3.

### 2.3. Flashing Firmware ke SKR Mini E3 V3
Untuk mem-flash firmware Klipper ke SKR Mini E3 V3, ikuti langkah-langkah berikut:

1. **Salin file firmware ke SD card**:
    - Hubungkan komputer dengan SD card yang akan digunakan pada SKR Mini E3 V3.
    - Salin file `klipper.bin` yang telah dihasilkan ke root directory dari SD card yang sudah diformat.

2. **Masukkan SD card ke SKR Mini E3 V3**:
    - Setelah file disalin, masukkan kartu SD ke slot SD card pada mikrokontroler SKR Mini E3 V3.
    - Pastikan jumper untuk USB pada mikrokontroler sudah terhubung karena daya yang digunakan disini dari laptop. Jika sudah ada power supply maka tidak memerlukan menghubungkan jumper USB pada mikrokontroler. Hal ini dilakukan agar tegangan 5V dari laptop dapat terhubung dengan mikrokontroler.

3. **Restart mikrokontroler**:
    - Matikan dan nyalakan kembali SKR Mini E3 V3. Mikrokontroler akan memulai proses flashing otomatis. Indikator LED pada papan akan memberikan sinyal jika proses flashing berhasil dengan menunggu biasanya 10 detik.
    - Untuk memastikan lebih lanjut bisa dengan melihat kembali file dalam SD card sudah berbentuk Cur atau belum. Jika sudah Cur tandanya berhasil.
4. **Melihat alamat MCU**:
    Untuk melihat jika sudah berhasil bisa dengan menjalankan kode dibawah ini pada WSL
    ```bash
    ls /dev/serial/serial/by-id
    ```
    jika berhasil akan mirip dengan di bawah ini
    
    ```bash
    usb-Klipper_stm32g0b1xx_27000D000150414235363020-if00
    ```

Untuk referensi lebih lanjut, Anda bisa merujuk ke [Klipper Flash Guide for SKR Mini E3 V3.0](https://www.drklipper.de/doku.php?id=klipper_faq%3Aflash_guide%3Astm32g0b1%3Abtt_skr_mini_e3_v3.0) (Dr. Klipper, n.d.) dan [How to Install Klipper on the SKR Mini E3 V3](https://www.youmaketech.com/how-to-install-klipper-on-the-skr-mini-e3-v3/) (You Make Tech, 2022).

---

## 3. Konfigurasi Tambahan

### 3.1. Konfigurasi File `printer.cfg`
Setelah firmware di-flash ke SKR Mini E3 V3, konfigurasi file `printer.cfg` perlu dilakukan untuk menghubungkan Klipper ke mikrokontroler. File ini berisi parameter penting seperti pinout, baudrate, dan pengaturan motor.

- Pastikan untuk menyetel **port USB** yang digunakan mikrokontroler dengan benar di `printer.cfg`.
- Sesuaikan **parameter akselerasi**, **jerk**, dan **pengaturan motor stepper** sesuai dengan printer Anda.

---

## Referensi

3D Print and Me. (2022, August 25). *Tutorial running Klipper on a Windows PC using WSL*. 3D Print and Me. Retrieved from https://3dpandme.com/2022/08/25/tutorial-running-klipper-on-a-windows-pc-using-wsl/

Dr. Klipper. (n.d.). *Klipper flash guide for SKR Mini E3 V3.0*. Dr. Klipper. Retrieved from https://www.drklipper.de/doku.php?id=klipper_faq%3Aflash_guide%3Astm32g0b1%3Abtt_skr_mini_e3_v3.0

You Make Tech. (2022). *How to install Klipper on the SKR Mini E3 V3*. You Make Tech. Retrieved from https://www.youmaketech.com/how-to-install-klipper-on-the-skr-mini-e3-v3/
