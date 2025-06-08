**PROYEK UAS (CHAPTER 7)**
```
Nama : Gede Rama Pradnya Widadga
NPM : 2306161914
Chapter : 7
```
**Metode Muller untuk Mencari Akar Fungsi Polinomial**

Program yang saya buat ini bertujuan untuk menyelesaikan masalah pencarian akar dari suatu fungsi nonlinier, khususnya fungsi polinomial dengan menggunakan Metode Muller yang diimplementasikan dalam bahasa pemrograman C. Metode Muller sendiri merupakan salah satu teknik numerik berbasis interpolasi kuadratik yang mampu menemukan akar-akar fungsi, baik real maupun kompleks, dengan memanfaatkan tiga titik pendekatan awal. Dibandingkan metode lain seperti Newton-Raphson atau metode secant  metode Muller memiliki keunggulan karena tidak membutuhkan turunan eksplisit dan dapat mengakomodasi akar kompleks tanpa perlu cara khusus.

Fungsi yang saya gunakan dalam studi kasus ini adalah :

**f(x) = x^3 − 13x − 12**

Fungsi ini saya pilih karena memiliki tiga akar real yang tidak mudah ditemukan secara eksak tanpa metode numerik. 
Dimana program ini meminta tiga nilai tebakan awal (x₀, x₁, dan x₂), lalu menghitung pendekatan akar dari fungsi tersebut dengan membentuk parabola yang melalui ketiga titik tersebut, dan mencari titik potong parabola dengan sumbu-x sebagai akar baru. Proses ini diulang hingga perubahan akar antar iterasi lebih kecil dari nilai toleransi yang ditentukan (misalnya 0.0001) 
atau hingga jumlah iterasi maksimum tercapai.

**Code**
```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>

// Fungsi polinomial f(x) = x^3 - 13x - 12
double f(double x) {
    return x * x * x - 13 * x - 12;
}

// Fungsi untuk mencatat hasil iterasi ke file CSV
void catat_iterasi(int iterasi, double akar, double error) {
    FILE *file = fopen("hasil_iterasi.csv", "a");
    if (file != NULL) {
        fprintf(file, "%d,%.10f,%.6f\n", iterasi, akar, error);
        fclose(file);
    }
}

// Fungsi untuk membuat grafik menggunakan Gnuplot
void tampilkan_grafik() {
    FILE *gnuplot = popen("gnuplot -persistent", "w");
    if (gnuplot) {
        fprintf(gnuplot,
            "set title 'Visualisasi Metode Muller'\n"
            "set xlabel 'x'\n"
            "set ylabel 'f(x)'\n"
            "set grid\n"
            "f(x) = x**3 - 13*x - 12\n"
            "plot f(x) title 'f(x)' with lines lw 2 lc rgb 'blue', \\\n"
            "     'hasil_iterasi.csv' using 2:(0) title 'Akar hasil iterasi' with points pt 7 ps 1.5 lc rgb 'red'\n"
        );
        pclose(gnuplot);
    }
}

// Implementasi Metode Muller
void metode_muller(double x0, double x1, double x2, double toleransi, int max_iterasi) {
    double h0, h1, d0, d1, a, b, c, radikal, penyebut, delta, x3, galat;
    int iterasi = 0;

    FILE *file = fopen("hasil_iterasi.csv", "w");
    fprintf(file, "Iterasi,Akar,Galat\n");
    fclose(file);

    printf("Iterasi\tAkar Perkiraan\t\tGalat (%%)\n");

    do {
        h0 = x1 - x0;
        h1 = x2 - x1;

        d0 = (f(x1) - f(x0)) / h0;
        d1 = (f(x2) - f(x1)) / h1;

        a = (d1 - d0) / (h1 + h0);
        b = a * h1 + d1;
        c = f(x2);

        radikal = sqrt(b * b - 4 * a * c);
        penyebut = (fabs(b + radikal) > fabs(b - radikal)) ? b + radikal : b - radikal;

        delta = -2 * c / penyebut;
        x3 = x2 + delta;
        galat = fabs(delta / x3) * 100;

        printf("%d\t%.10f\t%.6f\n", iterasi + 1, x3, galat);
        catat_iterasi(iterasi + 1, x3, galat);

        // Update tebakan untuk iterasi berikutnya
        x0 = x1;
        x1 = x2;
        x2 = x3;

        iterasi++;
    } while (galat > toleransi && iterasi < max_iterasi);

    printf("\nAkar ditemukan mendekati: %.10f\n", x3);
    tampilkan_grafik();
}

// Program utama
int main() {
    // Tebakan awal
    double x0 = 4.5, x1 = 5.5, x2 = 5.0;
    double toleransi = 0.0001;
    int max_iterasi = 100;

    printf("=== Pencarian Akar dengan Metode Muller ===\n");
    printf("Fungsi: f(x) = x^3 - 13x - 12\n\n");

    metode_muller(x0, x1, x2, toleransi, max_iterasi);

    return 0;
}

```
**Dokumentasi Hasil**

Link : https://imgur.com/a/pVU2Qoj
