# Kubernetes Engine: Qwik Start

1. Buka Cloud Shell Terminal - `Activate Cloud Shell`, pilih `Continue`
1. Authorize User dengan perintah:

   ```sh
   gcloud auth list -> muncul popup -> `Authorize`
   ```

1. Set Compute Region dan Zone dengan perintah:

   ```sh
   gcloud config set compute/region us-west4
   gcloud config set compute/zone us-west4-c
   ```

1. Membuat Google Kubernetes Engine (GKE) Cluster dengan perintah:

   ```sh
   gcloud container clusters create --machine-type=e2-medium --zone=us-west4-c lab-cluster
   ```

1. Tunggu sampai cluster selesai dibuat, cukup lama, kenapa? Karena ini akan membuat 3 buah Komputer yang akan dikonfigurasi supaya siap digunakan untuk menjalankan `kubernetes`. Harap sabar !

1. Setelah selesai, Cluster `kubernetes` tidak bisa langsung digunakan, harus ambil credential nya terlebih dahulu dengan perintah:

   ```sh
   gcloud container clusters get-credentials lab-cluster
   ```

1. Selanjutnya akan mencoba untuk deploy aplikasi sederhana (hello-world) pada cluster `kubernetes` dengan perintah:

   ```sh
   kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
   ```

1. Tunggu sebentar (30 detik), lalu centang `Create a new Deployment: hello-server`

1. Aplikasi yang dideploy ini belum bisa diakses, karena di dalam `kubernetes` ada perbedaan antara aplikasi yang dibuat `deployment` dengan pengakses aplikasi yang bernama `service`. Untuk membuat service yang bisa mengakses deployment hello-world, kita akan menggunakan perintah:

   ```sh
   kubectl expose deployment hello-server --type=LoadBalancer --port 8080
   ```

1. Untuk melihat list service, gunakan perintah:

   ```sh
   kubectl get service
   ```

1. Tunggu sampai `EXTERNAL-IP` untuk `hello-server` muncul, lalu buka pada browser dengan cara: **http://external-ip-hello-world:8080**, e.g. `http://123.123.123.123:8080`

   **Warning: http:// bukan https:// yah !**

1. Centang `Create a Kubernetes Service`

1. Delete cluster yang sudah tidak digunakan dengan perintah:

   ```sh
   gcloud container clusters delete lab-cluster
   ```

1. Tunggu sampai delete selesai, centang `Delete the cluster`

   **Warning: INI CUKUP LAMA NUNGGUNYA !**

1. ~ Sampai di sini, lab selesai ~
