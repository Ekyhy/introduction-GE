
# ALGORITMA GENETIKA (GENETIC ALGORITHM) - PANDUAN DASAR


## 1) Gambaran Umum (Overview)

Dalam algoritma genetika, kita akan menentukan sebuah variabel target (dalam kasus ini, sebuah string). 
Kita harus membuat daftar tebakan acak (populasi) dan menghitung seberapa dekat tebakan tersebut dengan string target (fungsi fitness).

Setelah kita mendapatkan nilai fitness dari semua tebakan acak tersebut, kita akan memilih tebakan terbaik dari populasi kita (seleksi) berdasarkan kriteria tertentu (dalam kasus ini, jumlah huruf yang cocok). 
Dari tebakan yang terpilih, kita memilih beberapa tebakan secara acak (2 tebakan dalam kasus ini) dan menukar beberapa informasi di antara mereka (crossover / pindah silang).

Kemudian, berdasarkan tingkat mutasi (mutation rate) kita, kita bisa mengubah atau tidak mengubah huruf yang dipilih secara acak dari daftar crossover. 
Terakhir, populasi lama kita akan digantikan oleh populasi baru.

## 2) Membedah Algoritma Genetika
### 2.1 Mendefinisikan Istilah

* **Random**: Pustaka (Library) bawaan Python.
* **Chromosome**: Sebuah string di dalam populasi.
* **POP_SIZE**: Jumlah Kromosom di dalam daftar kita.
* **MUT_RATE**: Tingkat probabilitas di mana string kita akan diubah.
* **TARGET**: Tujuan akhir kita.
* **GENES**: Pilihan karakter dasar dari mana populasi kita akan dibentuk.

```python
import random

POP_SIZE = 500
MUT_RATE = 0.1
TARGET = 'rayan ali'
GENES = ' abcdefghijklmnopqrstuvwxyz'
```

Catatan: Kita akan mencoba mencapai string target ('rayan ali') menggunakan gen dari huruf a-z ditambah dengan karakter spasi.

### 2.2 Inisialisasi (Initialization)

```python
def initialize_pop(TARGET):
  population = list()
  tar_len = len(TARGET)

  for i in range(POP_SIZE):
      temp = list()
      for j in range(tar_len):
          temp.append(random.choice(GENES))
      population.append(temp)

  return population

  ```

Menghasilkan populasi dengan ukuran yang sama dengan panjang TARGET. Setiap string di dalam populasi akan disebut "Kromosom" dan setiap Kromosom hanya terdiri dari huruf-huruf yang telah didefinisikan dalam GEN.
Fungsi ini mengembalikan populasi awal.

### 2.3 Perhitungan Fitness (Fitness Calculation)

```python
def fitness_cal(TARGET, chromo_from_pop):
  difference = 0
  for tar_char, chromo_char in zip(TARGET, chromo_from_pop):
      if tar_char != chromo_char:
          difference += 1 
  return [chromo_from_pop, difference]

```

Kita menghitung fitness dengan membandingkan jumlah huruf yang cocok dengan target. Metode kita adalah menghitung perbedaan antara setiap kromosom dan target. Nilai fitness yang lebih besar berarti semakin jauh dari target (nilai fitness 0 berarti target telah ditemukan).
Fungsi ini mengembalikan list kromosom beserta tingkat fitness.

### 2.4 Seleksi (Selection)

```python
def selection(population, TARGET):
  sorted_chromo_pop = sorted(population, key=lambda x: x[1])
  return sorted_chromo_pop[:int(0.5*POP_SIZE)]

```

Untuk memilih kromosom terbaik, kita perlu mengurutkannya secara bertahap (ascending) berdasarkan definisi fitness kita. Kita hanya mengembalikan 50% teratas dari populasi untuk mencegah masuknya kromosom yang buruk ke dalam populasi generasi selanjutnya.
Fungsi ini mengembalikan 50% populasi teratas yang sudah diurutkan berdasarkan skor fitness.

### 2.5 Pindah Silang (Crossover)

```python
def crossover(selected_chromo, CHROMO_LEN, population):
  offspring_cross = []
  for i in range(int(POP_SIZE)):
    parent1 = random.choice(selected_chromo)
    parent2 = random.choice(population[:int(POP_SIZE*50)]) # Catatan: Pastikan logika indeksing disesuaikan

    p1 = parent1[0]
    p2 = parent2[0]

    crossover_point = random.randint(1, CHROMO_LEN-1)
    child =  p1[:crossover_point] + p2[crossover_point:]
    offspring_cross.extend([child])
  return offspring_cross

```

Fungsi ini akan memilih secara acak satu induk dari kumpulan kromosom terbaik dan satu induk dari populasi awal. Titik crossover menentukan titik batas di mana informasi akan ditukar di antara kedua induk untuk menghasilkan keturunan (offspring). Proses ini bertujuan untuk menambah keragaman (diversitas) pada populasi kita.
Fungsi ini akan mengembalikan daftar keturunan dengan panjang yang sama dengan populasi awal.

### 2.6 Mutasi (Mutation)

```python
def mutate(offspring, MUT_RATE):
  mutated_offspring = []

  for arr in offspring:
      for i in range(len(arr)):
          if random.random() < MUT_RATE:
              arr[i] = random.choice(GENES)
      mutated_offspring.append(arr)
  return mutated_offspring
```

Sekarang kita akan melakukan mutasi pada populasi yang telah melalui proses crossover. Mutasi berarti kita akan memilih sebuah karakter secara acak dari setiap kromosom dan menggantinya dengan karakter lain yang ada di dalam GENES. Probabilitas penggantian ini bergantung pada MUT_RATE dan angka acak yang dihasilkan pada setiap iterasi.
Fungsi ini akan mengembalikan daftar populasi yang telah bermutasi.

### 2.7 Penggantian (Replacement)

```python
def replace(new_gen, population):
  for _ in range(len(population)):
      if population[_][1] > new_gen[_][1]:
        population[_][0] = new_gen[_][0]
        population[_][1] = new_gen[_][1]
  return population
```

Sekarang kita memiliki 2 jenis populasi. Pertama adalah populasi awal, dan yang kedua adalah generasi baru. Generasi baru adalah populasi bermutasi yang telah dievaluasi skor fitness-nya.

Fungsi replace melakukan iterasi pada setiap kromosom dalam populasi awal dan membandingkannya dengan generasi baru. Jika generasi baru memiliki skor fitness yang lebih baik (nilainya lebih kecil), maka ia akan menggantikan kromosom yang ada di populasi awal; jika tidak, kromosom lama akan tetap dipertahankan.
Fungsi ini akan mengembalikan kromosom-kromosom terbaik dari gabungan populasi awal dan generasi baru.
