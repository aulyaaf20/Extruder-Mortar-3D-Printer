
# Catatan Pengaturan Motor Ekstruder

## Konfigurasi Pin
- **Step_pin**: Pin ini digunakan untuk mengirimkan sinyal langkah (step) ke driver motor stepper. Setiap kali sinyal ini berubah dari rendah (low) ke tinggi (high), motor stepper akan bergerak satu langkah.
- **Dir_pin**: Pin ini mengontrol arah rotasi motor stepper. Kondisi rendah (low) menggerakkan motor searah jarum jam (misalnya maju), sedangkan kondisi tinggi (high) menggerakkan motor ke arah berlawanan (mundur).
- **Enable_pin**: Pin ini mengaktifkan atau menonaktifkan driver motor stepper. Dalam kondisi aktif (biasanya logika rendah tergantung driver), motor siap menerima sinyal step dan dir. Jika tidak aktif, motor tidak menerima sinyal gerakan.

    - Tanda seru (!) menunjukkan bahwa logika pin tersebut terbalik. Misalnya, pin PB4 akan menonaktifkan driver dalam kondisi tinggi (high) dan mengaktifkannya dalam kondisi rendah (low).

## Microstepping
Microstepping digunakan untuk meningkatkan resolusi motor stepper dengan membagi langkah penuh menjadi beberapa langkah kecil. Driver **TMC2209** mendukung hingga 256 microsteps per langkah penuh, namun nilai umum yang digunakan adalah 16 microsteps.

- Setiap langkah penuh pada motor (1.8° per step = 200 langkah per rotasi) dipecah menjadi 16 microsteps, yang meningkatkan resolusi dan memberikan kontrol lebih halus terhadap gerakan motor.

## Rotation Distance
Untuk menentukan seberapa jauh filament didorong oleh ekstruder dalam satu putaran penuh dari motor stepper (setelah memperhitungkan gearbox). Rumus yang digunakan:

```
rotation_distance = diameter × 3.14
```

Contoh:
- Diameter keluaran ekstruder = 0.5 cm atau 5 mm
- Maka, `rotation_distance = 5 mm × 3.14 = 15.7 mm`

## Full Step Per Rotation
Motor stepper umum seperti Nema 17 memiliki **200 full steps per rotation**, artinya motor membutuhkan 200 langkah penuh (360 derajat). Setiap full step memutar motor sebesar 1.8 derajat.

## Gear Ratio
Rasio roda gigi dari planetary gearbox untuk motor ini adalah **5.18:1**. Rumus untuk menghitung **rotation distance** yang efektif setelah memperhitungkan gearbox:

```
rotation_distance = rotation_distance_awal ÷ gear_ratio
```

Contoh:
```
rotation_distance = 15.7 mm ÷ 5.18 = 3.03 mm
```

## Nozzle Diameter dan Filament Diameter
- **Nozzle_diameter**: 5 mm (WAJIB DIATUR)
- **Filament_diameter**: 5 mm (disesuaikan dengan nozzle)

## Max Extrude Cross Section
Untuk membatasi luas penampang ekstrusi maksimum selama gerakan XY. Rumusnya:

```
max_extrude_cross_section = 4.0 × nozzle_diameter²
```

Jika diameter nozzle 5 mm, maka max extrude cross section sebesar 100 mm².

## Max Extrude Only Distance
Membatasi panjang maksimum gerakan ekstrusi atau retraksi dalam satu perintah. Nilai yang diatur pada klipper:

```
max_extrude_only_distance: 100.0
```

## Max Extrude Only Velocity
Kecepatan maksimum yang dapat dicapai motor ekstruder selama gerakan retraksi atau ekstrusi. Disarankan memulai dengan kecepatan rendah, misalnya di bawah 5-10 mm/s:

```
max_extrude_only_velocity: 5.0
```

## Max Extrude Only Accel
Percepatan maksimum motor ekstruder saat memulai atau berhenti dari gerakan ekstrusi atau retraksi. Nilai akselerasi awal untuk mortar disarankan antara **100-300 mm/s²**:

```
max_extrude_only_accel: 200.0
```

## Heater Pin dan Sensor
- **Heater_pin**: WAJIB DIATUR untuk mengontrol pemanas ekstruder dengan sinyal PWM.
- **Sensor_Type**: WAJIB DIATUR sesuai jenis sensor suhu. Pada sistem mortar, hanya menggunakan resistor 56K untuk menggantikan sensor suhu.
- **Min_extrude_temp**: 30°C (disesuaikan dengan resistor yang digunakan).

## Konfigurasi Driver TMC2209
- **Uart_pin**: Menghubungkan UART interface dari driver stepper motor TMC2209 ke papan kontrol.
- **Tx_pin**: Untuk pengiriman data dari Klipper ke driver TMC2209.
- **Uart_address**: Setiap driver memiliki alamat UART unik. Contoh, ekstruder menggunakan `uart_address: 3`.

## Run Current
Arus maksimum untuk motor stepper selama operasi, dinyatakan dalam ampere. Arus maksimum pada motor ini adalah **1.7 Ampere**, dengan pengaturan awal arus `run_current` pada 1.2-1.4A:

```
run_current: 1.200
```

## StealthChop Threshold
Mode operasi pada TMC2209 yang mengurangi getaran dan kebisingan pada kecepatan rendah. Ekstruder menggunakan:

```
Stealthchop_threshold: 999999
```

Ini memastikan mode StealthChop selalu aktif, meskipun pada kecepatan ekstrusi tinggi.
