# Rangkaian Simulasi
<img width="851" height="419" alt="image" src="https://github.com/user-attachments/assets/b85e6b73-4442-47fe-9bf0-a210ed16b245" />

## Input Ud dan Uq

Dalam kerangka sistem kontrol untuk skema open loop two level inverter, Ud dan Uq merupakan representasi komponen tegangan pada kerangka referensi DQ ( rotating reference frame), secara lebih lanjutnya komponen tegangan Ud akan berokelasi dengan komponen direct axis dan komponen Uq akan berkorelasi dengan quadratur axis, sehingga dari korelasi tersebut memiliki arti bahwa, keluaran amplitudo dan sudut dari sinyal ABC akan bergantung terhadap nilai DQ yang di berikan, atau secara matemasi dapat direpresentasikan sebagai berikut: 

**Amplitudo vektor tegangan:**

```math
V_{amp} = \sqrt{U_d^2 + U_q^2}
```

**Sudut fasa vektor tegangan:**

```math
\phi = \arctan2(U_q, U_d)
```
 Dalam hal simulasi ini, nilai Ud yang digunakan adalah 1 dan nilai Uq yang digunakan adalah 0 sehingga amplitudo vektor tegangan nya akan bernilai 1 dan sudut fasa vektor tegangan nya bernilai 0, ini memiliki arti bahwa gelombang abc akan "in-phase" dengan sumbu d dan tidak akan ada pergeseran fasa tambahan.

 ## Transformasi DQ-ABC
 Melansir dari [Transformasi Clarke–Park by nicho ](https://github.com/dc-ac-ugm/Basic-Inverter-Model/tree/main/CLARKE-PARK%20TRANSFORMATION), Clarke-Park Transformation dapat diuraikan sebaga berikut:

### CLARKE-PARK TRANSFORMATION

Clarke-Park transformation (atau bisa disebut abc-dq transformation) adalah transformasi reference frame pada sistem 3 fasa. Keluaran dari transformasi ini adalah value yang konstan agar memudahkan kontrol pada sistem. Clarke-Park transformation terdiri dari 2 transformasi yaitu `Clarke Transformation` dan `Park Transformation`.

#### CLARKE TRANSFORMATION

Clarke transformation adalah perubahan reference frame dari 3 komponen menjadi 2 komponen.

```math
abc \rightarrow \alpha \beta
```

Clarke Transformation memiliki visualisasi sebagai berikut:

![clarke transformation](https://github.com/user-attachments/assets/19258b71-b913-4698-a49f-47f21b15131a)

Clarke transformation menggubah referensi ke $`\alpha \beta`$ frame. Tetapi frame ini adalah frame statis yang akan mengubah 3 gelombang sinusoidal menjadi 2 gelombang sinusoidal.

Perubahan reference frame tersebut menggunakan perhitungan matriks:

```math
\begin{bmatrix}
X_\alpha \\
X_\beta
\end{bmatrix}

=

\frac{2}{3}

\begin{bmatrix}
1 & -\frac{1}{2} & -\frac{1}{2}\\
0 & \frac{\sqrt{3}}{2} & -\frac{\sqrt{3}}{2}
\end{bmatrix}

\begin{bmatrix}
y_a \\
y_b \\
y_c
\end{bmatrix}

```


#### PARK TRANSFORMATION

Park transformation adalah tranformasi yang mengubah 2 komponen dari `Clarke Transformation` yang merupakan 2 gelombang sinusiodal, menjadi 2 gelombang konstan (DC). Park transformation memiliki 2 sumbu reference frame yaitu `d (direct)` dan `q (quadrature)` di mana d adalah komponen real dan q adalah komponen imajiner.

```math

\alpha \beta \rightarrow dq

```

Park transformation dapat divisualisasikan sebagai berikut:

![image](https://github.com/user-attachments/assets/6c357d80-8e74-4eb4-9c5f-fa984204eba7)

Reference frame dari calrke park transformation adalah rotating reference frame, di mana reference frame berputar dengan kecepatan sesuai frekuensi gelombang sinusoidal. Sudut perbedaan antara $`\alpha \beta`$ frame dan $`dq`$ frame direpresentasikan menjadi $`\theta`$.

Transformasi Park dapat dikalkulasi menggunakan formula:

```math
\begin{bmatrix}
X_d \\
X_q
\end{bmatrix}

=

\begin{bmatrix}
cos(\theta) & sin(\theta)\\
-sin(\theta) & cos(\theta)
\end{bmatrix}

\begin{bmatrix}
y_\alpha \\
y_\beta
\end{bmatrix}

```

### CLARKE PARK TRANSFORMATION

Dari 2 transformasi tersebut didapatkan formula kombinasi `Clarke-Park Transformation`:

```math
\begin{bmatrix}
X_d \\
X_q
\end{bmatrix}

=

\frac{2}{3}

\begin{bmatrix}
cos(\theta) & -sin(\theta)\\
cos(\theta -120\textdegree) & -sin(\theta -120\textdegree\\
cos(\theta +120\textdegree) & -sin(\theta +120\textdegree
\end{bmatrix}^T

\begin{bmatrix}
y_a \\
y_b \\
y_c
\end{bmatrix}

```
## Theta Transfromasi
Theta merupakan informasi mengenai sudut posisi yang berfungsi untuk memutar sistem koordinat, dalam hal ini dari kerangka DQ (Park) ker kerangka abc (three phase), theta berada dalam nilai kisaran 0-2pi, dalam simulasi ini untuk melakukan pengujian, bisa dibangkitkan menggunakan gelombang segitiga atau ramp, sehingga dapat digunakan untuk mewakili sudut yang berulang dari 0→2π→0 secara periodik.

| Parameter             | Nilai   |
|-----------------------|---------|
| Minimum signal value  | 0       |
| Maximum signal value  | 2 * π   |
| Frequency [Hz]        | 50      |
| Duty cycle [p.u.]     | 1       |
| Phase delay [s]       | 0       |

Nilai frequensi pada theta akan mempengaruhi niali frekuensi gelombang abc, atau secara matematis dapat diuraikan sebagai berikut:
```math
V_a = V_{amp} \cdot \cos(\theta - \phi),
```

kemudian jika sudut θ berubah **linear** terhadap waktu, maka

```math
\theta(t) = \omega \cdot t,
```

sehingga

```math
V_a(t) = V_{amp} \cdot \cos(2 \pi f t - \phi),
```
yang artinya bahwa,  θ yang dihasilkan oleh gelombang segitiga atau ramp dari 0 ke 2π, dalam simulasi ini pada frekueensi 50 Hz, akan menghasilkan frekuensi sinusoidal yang sama yaitu 50 Hz.

## Sinyal Carrier

Dalam hal PWM, sinyal carrier merupakan sinyal pembawa yang memiliki frekuensi tinggi( dalam hal ini karena menggunakan IM818 berada di kisaran 10-20 kHz) dengan bentuk gelombang segitiga yang berfungsi untuk menjadi pembanding dengan sinyal referensi ( dalam hal ini abc) agar menghasilkan PWM, dengan cara menentukan lebar pulsa PWM. Dengan representasi matematis sebagai berikut: 
```math
m_a = \frac{V_{\text{ref peak}}}{V_{\text{carrier peak}}}.
```
Dalam hal ini, agar tidak terjadi overmodulation maka peak dari sinyal carrier harus sama dengan peak dari sinyal referensi, untuk simulasi ini menjadi sebagai berikut:
| Parameter              | Nilai     |
|------------------------|-----------|
| Minimum signal value   | -1        |
| Maximum signal value   | 1         |
| Frequency [Hz]         | 200000    |
| Duty cycle [p.u.]      | 1         |
| Phase delay [s]        | 0         |

dengan demikian, karena sinyal dari segitiga mendekati sinyal referensi maka setiap kali sinyal referensi > sinyal carrier  akan menghasilkan output PWM HIGH, begitu pula setiap kali sinyal referensi < sinyal carrier akan menghasilkan output PWM LOW, sehingga menghasilkan variasi lebar sinyal pulsa yang mengikuti bentuk sinyal referensinya.

## Dokumentasi Output
### Output keluaran sinyal ABC
<img width="1919" height="1030" alt="image" src="https://github.com/user-attachments/assets/cefa32d7-31dd-4aaf-a342-d6b3ef684eb9" />

### Output keluaran PWM
bagian atas secara berurutan High side dan bagian bawah secara berurutan  Low side 

<img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/8cca1d8c-bb09-4c0e-bafc-260e08393ec1" />

## Uji Validitas 
Jika dilakukan pengurangan antara SPWM High-side dan Low-side dengan contoh gambar rangkaian sebagai berikut:
<img width="899" height="346" alt="image" src="https://github.com/user-attachments/assets/32e44971-b33f-46c7-ae06-6d832827d6d1" />

Output keluarannya menjadi:
<img width="1919" height="1030" alt="image" src="https://github.com/user-attachments/assets/ee16470c-b6dc-4c34-b3db-d1668a55b634" />

kemudian apabila dilakukan filter, maka:
<img width="929" height="338" alt="image" src="https://github.com/user-attachments/assets/3273d495-0444-4857-9628-f0c4ac3c1835" />

akan menghasilkan keluaran sebagai berikut:
<img width="1864" height="922" alt="image" src="https://github.com/user-attachments/assets/5bf8123d-cedc-4d82-b81c-d00bb044b54c" />

Dari gambar tersebut dapat kita ketahui, bahwa hasil keluaran pengurangan PWM High side dan Low side yang apabila difilter, akan menghasilkan keluaran sinusoidal murni, yang mana ini merupakan sinyal tegangan diferensial fasa A dari inverter.
