# Introduction to Docker

1. Buka Cloud Shell Terminal - `Activate Cloud Shell`, pilih `Continue`
1. Authorize User dengan perintah:

   ```sh
   gcloud auth list -> muncul popup -> `Authorize`
   ```

1. Coba untuk menjalankan docker image `hello-world` dengan perintah:

   ```sh
   docker run hello-world
   ```

1. Untuk melihat image docker yang sudah terdownload di cloud-shell, gunakan perintah:

   ```sh
   docker images
   ```

1. Coba untuk menjalankan docker image `hello-world` dan membuat sebuah container lagi dengan perintah:

   ```sh
   docker run hello-world
   ```

1. Lihat docker container apa saja yang sedang berjalan dengan perintah:

   ```sh
   docker ps
   ```

1. Lihat docker container apa saja yang pernah berjalan dengan perintah:

   ```sh
   docker ps -a
   ```

1. Mari kita coba untuk membuat sebuah docker image sendiri dengan perintah:

   ```sh
   # Membuat sebuah folder dengan nama test dan pindah ke folder tersebut
   mkdir test && cd test

   # Membuat sebuah file dengan nama Dockerfile yang isinya seperti di bawah
   cat > Dockerfile <<EOF
   # Menggunakan image dari official node, dengan tag lts
   FROM node:lts
   # Set Working directory untuk image yang akan dibuat di /app
   WORKDIR /app
   # Copy seluruh file yang ada di posisi sekarang (.) ke /app yang ada di image
   ADD . /app
   # Mengekspose port 80 yang ada di dalam image untuk bisa digunakan
   EXPOSE 80
   # Menjalankan perintah "node app.js" ketika image ini dibuat jadi container
   CMD ["node", "app.js"]
   EOF
   ```

1. Sekarang mari kita membuat file `app.js` yang akan dijalankan oleh si container, dengan perintah:

   ```sh
   cat > app.js <<EOF
   const http = require('http');
   const hostname = '0.0.0.0';
   const port = 80;
   const server = http.createServer((req, res) => {
      res.statusCode = 200;
      res.setHeader('Content-Type', 'text/plain');
      res.end('Hello World\n');
   });
   server.listen(port, hostname, () => {
       console.log('Server running at http://%s:%s/', hostname, port);
   });
   process.on('SIGINT', function() {
       console.log('Caught interrupt signal and will exit');
       process.exit();
   });
   EOF
   ```

1. Sekarang kita akan membuat image berdasarkan `Dockerfile` yang sudah dibuat dengan perintah:

   ```sh
   docker build -t node-app:0.1 .
   ```

1. Mari kita lihat kembali apakah image sudah dibuat dengan perintah:

   ```sh
   docker images
   ```

1. Kalau sudah dibuat, pastinya mau dijalankan donk? mari kita jalankan dengan perintah:

   ```sh
   # -p = ikat port
   # 4000:80 = ikat port 4000 yang ada komputer dengan port 80 yang ada di dalam image
   # Ingat, tadi kita expose port 80 di dalam Dockerfile
   docker run -p 4000:80 --name my-app node-app:0.1
   ```

1. Mari kita coba mengecek apakah program berjalan dengan **membuka sebuah terminal yang baru lagi** (kita sebut dengan `2nd terminal` yah setelah ini), lalu jalankan perintah:

   ```sh
   curl http://localhost:4000
   ```

1. Stop aplikasi yang dibuat dan hapus containernya dengan perintah (pada `2nd terminal`):

   ```sh
   docker stop my-app && docker rm my-app
   ```

1. Pastinya tidak enak kan kalau menjalankan image docker harus buka 2 terminal terus? Mari kita coba jalankan image pada background dengan perintah (pada `terminal pertama`):

   ```sh
   # Di sini ada -d yang menyatakan (detach) = jalankan di background
   docker run -p 4000:80 --name my-app -d node-app:0.1
   docker ps
   ```

1. Sekarang kalau ada code yang salah dan ingin diperbaiki bagaimana? Mari kita buka kembali file `app.js` dengan cara mengetik `nano app.js` dan modifikasi kode menjadi seperti berikut

   ```js
   // Sebelumnya ini
   // res.end('Hello World\n');

   // Ini yang barunya
   res.end("Welcome to Cloud\n");
   ```

   Kemudian tekan `CTRL + X` untuk keluar dari nano kemudian tekan `Y` untuk menyimpan perubahan (`save`)

1. Selanjutnya kita akan kembali ke terminal lagi.

1. Buat image yang baru berdasarkan kode yang sudah dimodif dengan perintah:

   ```sh
   docker build -t node-app:0.2 .
   ```

   Perhatikan di sini kita menaikkan tag versionin dari `0.1` menjadi `0.2`

1. Kemudian kita bisa mencoba untuk menjalankan aplikasi dengan perintah:

   ```sh
   docker run -p 8080:80 --name my-app-2 -d node-app:0.2
   docker ps
   ```

1. Dan kita bisa mencoba untuk menjalankan keduanya secara satu per satu dengan menggunakan perintah:

   ```sh
   # Versi 0.1 (Hello World)
   curl http://localhost:4000

   # Versi 0.2 (Welcome to Cloud)
   curl http://localhost:8080
   ```

1. Task 4 Debug di skip

1. Task 5 dimulai

1. Kembali pada Dashboard di Console GCP, tekan garis 3 (Hamburger Menu) untuk membuka `Navigation Menu`, cari di bawah _CI/CD_, pilih `Artifact Registries` -> `Repositories`

1. Pilih tombol `Create Repository`

   - nama repository harus bernama `my-repository`
   - format harus `Docker`
   - region harus `us-central1 (Iowa)

1. Tekan tombol `Create`

1. Tunggu sampai repository selesai dibuat, kemudian kembali ke terminal

1. Sekarang kita harus set docker repository kita ke us-central1 dengan perintah:

   ```sh
   gcloud auth configure-docker us-central1-docker.pkg.dev
   ```

1. Sekarang kita akan mem-push (memindahkan dari lokal ke repo) image yang sudah dibuat ke repo yang dibuat dengan perintah:

   ```sh
   # Ambil Project ID GCP
   export PROJECT_ID=$(gcloud config get-value project)

   # Pindah ke folder ~/test
   cd ~/test

   # build image node-app:0.2 dengan nama yang baru
   docker build -t us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2 .

   # Push image ke repo
   docker push us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2
   ```

1. Tunggu sampai push nya selesai, sisanya sudah boleh di-skip, kemudian centang (`Check My Progress`) `Publish your container image to Artifact Registry`

1. ~ Lab selesai di sini ~
