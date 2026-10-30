# Belajar Instal Kubectl

Perintah yang diberikan kepada Anda adalah perintah untuk menginstal `kubectl` di Debian 13 (Trixie).

`kubectl` adalah salah satu tools utama dalam ekosistem Kubernetes. Fungsinya adalah untuk mengelola dan berkomunikasi dengan Kubernetes Cluster melalui terminal Linux.

Saya jelaskan dari dasar supaya Anda memahami bukan hanya cara menginstalnya, tetapi juga apa yang sebenarnya dilakukan setiap perintah, mengapa diperlukan, dan apa risikonya.

# 1. Apa itu kubectl?

Enable autocompletion for kubectl on Windows

kubectl

Command Line Interface (CLI)Tool resmi Kubernetes untuk berkomunikasi dengan Kubernetes API Server.

Sederhananya:

- Kubernetes (K8s): Platform untuk mengelola dan menjalankan aplikasi dalam container.
- kubectl: Alat yang digunakan administrator atau developer untuk mengontrol Kubernetes.
- Kubernetes Cluster: Sekumpulan mesin (server/node) yang bekerja bersama untuk menjalankan aplikasi container.

Contohnya, jika perusahaan Anda memiliki Kubernetes Cluster, Anda bisa menggunakan `kubectl` untuk:

- Melihat server/node yang tergabung dalam cluster.
- Melihat aplikasi atau Pod yang sedang berjalan.
- Menjalankan, menghentikan, dan menghapus aplikasi.
- Melihat log aplikasi.
- Melakukan deployment aplikasi.
- Memeriksa kondisi cluster.
- Mengelola konfigurasi Kubernetes.

Penting: Menginstal `kubectl` tidak berarti Anda otomatis menginstal Kubernetes Server atau membuat Kubernetes Cluster. Anda baru memasang alat untuk mengakses dan mengelola cluster.

# 2. Penjelasan setiap perintah

Mari kita bedah satu per satu.

## Perintah pertama

bash

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Perintah ini digunakan untuk mengunduh file binary `kubectl` dari server resmi Kubernetes.

Ada beberapa bagian di dalamnya.

A. `curl`

`curl` adalah program Linux untuk melakukan komunikasi melalui jaringan, termasuk mengunduh file dari URL.

B. `-L`

bash

```
-L
```

Artinya mengikuti HTTP redirect.

Jika server mengarahkan permintaan ke alamat lain, `curl` akan mengikuti pengalihan tersebut.

C. `-O`

bash

```
-O
```

Artinya menyimpan file dengan nama asli dari server.

Dalam kasus ini, nama file yang diunduh adalah:

text

```
kubectl
```

D. `$(...)`

Ini adalah fitur command substitution di Bash/Linux.

Artinya, perintah yang ada di dalam tanda kurung akan dijalankan terlebih dahulu dan hasilnya dimasukkan ke perintah utama.

Contohnya:

bash

```
$(curl -L -s https://dl.k8s.io/release/stable.txt)
```

Bagian tersebut menjalankan perintah:

bash

```
curl -L -s https://dl.k8s.io/release/stable.txt
```

Fungsinya mengambil informasi versi stabil terbaru Kubernetes yang tersedia.

Misalnya hasilnya:

text

```
v1.34.2
```

Maka URL lengkap yang digunakan menjadi:

text

```
https://dl.k8s.io/release/v1.34.2/bin/linux/amd64/kubectl
```

Versi di atas hanya contoh, bukan klaim versi stabil saat ini.

E. `linux/amd64`

Bagian ini menunjukkan platform binary yang diunduh:

- `linux`: Sistem operasi Linux.
- `amd64`: Arsitektur prosesor 64-bit x86 (Intel/AMD).

Jadi, perintah tersebut mengunduh executable `kubectl` yang sesuai untuk Linux dengan arsitektur AMD64.

## Perintah kedua

bash

```
chmod +x kubectl
```

Perintah ini memberikan izin eksekusi kepada file `kubectl`.

Penjelasan:

- `chmod`: Change Mode, untuk mengubah permission file.
- `+x`: Menambahkan permission execute.
- `kubectl`: File yang akan diberikan izin eksekusi.

Mengapa diperlukan?

Karena file yang diunduh belum tentu memiliki permission untuk dijalankan sebagai program.

Setelah perintah tersebut, file bisa dieksekusi sebagai program Linux.

## Perintah ketiga

bash

```
sudo mv kubectl /usr/local/bin/
```

Perintah ini memindahkan file `kubectl` ke direktori:

text

```
/usr/local/bin/
```

Penjelasan:

- `sudo`: Menjalankan perintah dengan hak administrator (root).
- `mv`: Memindahkan atau mengganti nama file.
- `kubectl`: File yang dipindahkan.
- `/usr/local/bin/`: Direktori untuk program tambahan yang diinstal secara lokal.

Mengapa harus ke `/usr/local/bin/`?

Karena direktori tersebut biasanya sudah termasuk dalam environment variable `PATH` Linux.

Dengan begitu, Anda bisa menjalankan:

bash

```
kubectl
```

Dari direktori mana pun tanpa harus mengetik lokasi lengkap:

bash

```
/usr/local/bin/kubectl
```

# 3. Apakah perintah tersebut aman untuk Debian 13?

Secara umum, ya. Perintah tersebut merupakan metode instalasi resmi yang didokumentasikan oleh Kubernetes. Jadi bukan perintah aneh atau script tidak dikenal.

Kubernetes

+1

Namun ada beberapa hal yang perlu diperhatikan.

### A. Pastikan arsitektur CPU

Perintah yang diberikan menggunakan `amd64`.

Cek terlebih dahulu:

bash

```
dpkg --print-architecture
```

Jika hasilnya:

text

```
amd64
```

Maka perintah tersebut cocok.

Jika hasilnya:

text

```
arm64
```

Anda harus menggunakan URL dengan `arm64`, bukan `amd64`.

### B. Verifikasi file yang diunduh

Saya menyarankan menambahkan pemeriksaan checksum sebelum menginstal binary.

Checksum digunakan untuk memastikan file yang diunduh sesuai dengan file resmi yang dipublikasikan.

Gunakan urutan berikut:

bash

```
# Download kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Download checksum
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

# Verifikasi checksum
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

Jika berhasil, hasilnya:

text

```
kubectl: OK
```

Jika hasilnya `FAILED`, jangan lanjutkan instalasi.

### C. Instalasi ke direktori sistem

Untuk bagian instalasi, saya menyarankan menggunakan:

bash

```
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Ini merupakan alternatif yang lebih rapi dibandingkan `chmod` kemudian `mv`, karena sekaligus mengatur:

- Owner: `root`
- Group: `root`
- Permission: `755`

Artinya hanya root yang dapat mengubah file tersebut, sedangkan pengguna lain dapat membaca dan menjalankannya.

## 4. Versi instalasi yang saya rekomendasikan

Jika Debian 13 Anda menggunakan arsitektur AMD64, berikut urutan lengkap yang dapat digunakan.

bash

```
# 1. Download kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# 2. Download checksum
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

# 3. Verifikasi file
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

# 4. Install kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# 5. Periksa versi
kubectl version --client

# 6. Hapus file download
rm -f kubectl kubectl.sha256
```

Perintah ini tidak menginstal service atau daemon yang berjalan di background. Anda hanya memasang satu executable bernama `kubectl`.

# 5. Bagaimana cara menggunakan kubectl setelah instalasi?

Setelah selesai, coba beberapa perintah berikut.

1. Memastikan instalasi berhasil

bash

```
kubectl version --client
```

Contoh output:

text

```
Client Version: v1.xx.x
Kustomize Version: vX.X.X
```

2. Melihat bantuan perintah

bash

```
kubectl help
```

3. Melihat konfigurasi Kubernetes

bash

```
kubectl config view
```

4. Melihat daftar cluster yang dikonfigurasi

bash

```
kubectl config get-contexts
```

5. Melihat node Kubernetes

bash

```
kubectl get nodes
```

6. Melihat aplikasi yang berjalan

bash

```
kubectl get pods -A
```

Perlu diketahui, perintah nomor 5 dan 6 membutuhkan koneksi ke Kubernetes Cluster yang sudah dikonfigurasi. Jika belum ada cluster, kemungkinan Anda mendapatkan pesan seperti:

text

```
The connection to the server localhost:8080 was refused
```

Itu tidak berarti instalasi `kubectl` gagal. Bisa jadi memang belum ada konfigurasi koneksi ke cluster.

# 6. Bagaimana kubectl terhubung ke server Kubernetes?

Ini bagian yang penting untuk dipahami jika Anda bekerja sebagai SysAdmin, DevOps, atau Infrastructure Engineer.

Gambaran sederhananya:

Debian 13

Server / Laptop administrator

kubectl terinstal di sini

HTTPS + Kubernetes API

Kubernetes Control Plane

API Server, Scheduler, Controller Manager

Worker Node 1

Container / Pods

Worker Node 2

Container / Pods

Agar `kubectl` bisa terhubung ke cluster, biasanya Anda membutuhkan file konfigurasi bernama:

text

```
~/.kube/config
```

File ini disebut kubeconfig.

Isinya biasanya mencakup:

- Alamat Kubernetes API Server.
- Informasi cluster.
- Credential atau sertifikat autentikasi.
- Context yang menentukan cluster dan user yang digunakan.

Jangan sembarangan membagikan file ini karena bisa mengandung credential yang memberikan akses ke infrastruktur perusahaan.

# 7. Apakah kubectl sama dengan Docker?

Tidak. Keduanya memiliki fungsi berbeda.

| Tool | Fungsi |
| --- | --- |
| Docker | Membuat dan menjalankan container |
| kubectl | Mengelola Kubernetes Cluster |
| Kubernetes | Mengorkestrasi container dalam cluster |
| containerd | Container runtime |
| Helm | Mengelola instalasi aplikasi Kubernetes melalui chart |

Contoh hubungan penggunaannya:

- Docker digunakan untuk membuat image aplikasi.
- Kubernetes digunakan untuk mengatur aplikasi tersebut agar berjalan di beberapa server.
- kubectl digunakan administrator untuk memberikan instruksi kepada Kubernetes.

# 8. Satu hal penting mengenai versi

Jangan selalu berasumsi bahwa versi `kubectl` terbaru otomatis cocok dengan cluster perusahaan.

Dokumentasi Kubernetes menetapkan aturan kompatibilitas: versi minor `kubectl` sebaiknya maksimal berbeda satu tingkat dari versi minor Kubernetes Control Plane.

Misalnya:

| kubectl | Control Plane | Kompatibel |
| --- | --- | --- |
| v1.34 | v1.33 | Ya |
| v1.34 | v1.34 | Ya |
| v1.34 | v1.35 | Ya |
| v1.34 | v1.32 | Tidak dijamin |

Kubernetes

+1

Jika perusahaan sudah memiliki cluster Kubernetes, sebaiknya tanyakan versi cluster kepada administrator sebelum menginstal versi terbaru.

# 9. Kesimpulan

Dari tiga perintah yang diberikan kepada Anda:

bash

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl

sudo mv kubectl /usr/local/bin/
```

Artinya:

1. Mengunduh program `kubectl` dari server resmi Kubernetes.
2. Memberikan izin agar program dapat dijalankan.
3. Memindahkan program ke direktori sistem agar dapat dipanggil dari terminal mana pun.

Instalasi ini tidak akan langsung mengubah konfigurasi jaringan, menjalankan container, menginstal Kubernetes Cluster, atau mengubah service Debian 13 Anda.

Referensi resmi:

- [Kubernetes – Install and Set Up kubectl on Linux](<https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/?source=post_page-----f79bc4da132d--------------------------------&utm_source=chatgpt.com>)
- [Dokumentasi Kubernetes Bahasa Indonesia](<https://v1-36.docs.kubernetes.io/id/docs/tasks/tools/install-kubectl/?utm_source=chatgpt.com>)

## Saran saya sebelum Anda melanjutkan

Ada satu hal yang ingin saya pastikan karena ini akan menentukan langkah berikutnya.

Anda diminta menginstal `kubectl` di Debian 13 tersebut untuk keperluan apa?

Apakah Debian 13 Anda:

- Digunakan sebagai server administrator untuk mengakses Kubernetes Cluster milik perusahaan?
- Akan digunakan untuk membangun Kubernetes Cluster sendiri?
- Atau sedang dipersiapkan untuk deployment aplikasi menggunakan Kubernetes?

Ketiganya memiliki tahapan konfigurasi yang berbeda. Saya bisa bantu susun langkah berikutnya supaya tidak salah instalasi atau mengubah konfigurasi server yang sudah berjalan.
