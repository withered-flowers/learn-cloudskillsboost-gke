# Orchestrating the Cloud with Kubernetes

1.  Buka Cloud Shell Terminal - `Activate Cloud Shell`, pilih `Continue`
1.  Authorize User dengan perintah:

    ```sh
    gcloud auth list -> muncul popup -> `Authorize`
    ```

1.  Set Compute Zone dengan perintah:

    ```sh
    gcloud config set compute/zone us-central1-b
    ```

1.  Membuat Google Kubernetes Engine (GKE) Cluster dengan perintah:

    ```sh
    gcloud container clusters create io
    ```

1.  Tunggu sampai cluster selesai dibuat, cukup lama, kenapa? Karena ini akan membuat 3 buah Komputer yang akan dikonfigurasi supaya siap digunakan untuk menjalankan `kubernetes`. Harap sabar !

1.  Sebenarnya secara otomatis, perintah mengambil credentials dari kubernetes cluster akan dijalankan, namun apabila ingin lebih yakin, setelah ini jalankan perintah:

    ```sh
    gcloud container clusters get-credentials io
    ```

1.  Copy source code yang akan digunakan dari Bucket (Cloud Storage) yang sudah disiapkan dengan perintah:

    ```sh
    gsutil cp -r gs://spls/gsp021/* .
    ```

1.  Pindah ke folder tersebut dengan perintah:

    ```sh
    cd orchestrate-with-kubernetes/kubernetes
    ```

1.  Mari lihat foldernya ada apa saja dengan perintah:

    ```sh
    ls -l
    ```

1.  Membuat sebuah deployment untuk nginx dengan perintah:

    ```sh
    kubectl create deployment nginx --image=nginx:1.10.0
    ```

1.  Kita harus mengetahui bahwa dalam kubernetes, semua container itu berjalan dalam satuan terkecil kubernetes: `pod`

1.  Listing pod yang ada dengan perintah:

    ```sh
    kubectl get pods
    ```

1.  Ingat bahwa deployment tidak bisa diakses langsung dari luar, untuk bisa diakses, kita harus membuat `service` dengan perintah:

    ```sh
    kubectl expose deployment nginx --port 80 --type LoadBalancer
    ```

1.  Untuk melihat `service` yang sudah dibuat, gunakan perintah:

    ```sh
    kubectl get services
    ```

    **WARNING: Tunggu sampai EXTERNAL-IP untuk nginx dari `pending` menjadi `ip address`**, jalankan ulang perintah di atas, sampai mendapatkan ip addressnya

1.  Coba untuk mengecek hasilnya dengan perintah:

    ```sh
    curl http://external-ip-dari-nginx:8080
    ```

1.  Centang `Create a Kubernetes cluster and launch Nginx container`

1.  Pindah ke directory yang ada dengan perintah

    ```sh
    cd ~/orchestrate-with-kubernetes/kubernetes
    ```

1.  Lihat isi dari file `monolith.yaml` dengan perintah:

    ```sh
    cat pods/monolith.yaml
    ```

1.  Membuat pods dengan perintah:

    ```sh
    kubectl create -f pods/monolith.yaml
    ```

1.  Untuk melihat pod yang sudah tebuat kita bisa menggunakan perintah:

    ```sh
    kubectl get pods
    ```

1.  Untuk melihat detil pods monolith yang dibuat, gunakan perintah:

    ```sh
    kubectl describe pods monolith
    ```

1.  Buka Cloud Shell yang satu lagi, kemudian kita akan mencoba untuk melakukan port forwarding, yaitu cara untuk memetakan port local yang ada di komputer sendiri (terminal cloud shell), terhadap port di dalam pod (monolith pod)

1.  Pada terminal yang kedua ini, gunakan perintah:

    ```sh
    kubectl port-forward monolith 10080:80
    ```

1.  Pada terminal pertama, gunakan perintah:

    ```sh
    curl http://127.0.0.1:10080
    ```

    Muncul hasilnya !

1.  Pada terminal pertama, gunakan perintah:

    ```sh
    curl http://127.0.0.1:10080/secure
    ```

    Hasilnya `authorization failed`

1.  Coba untuk login terlebih dahulu dengan perintah:

    ```sh
    curl -u user http://127.0.0.1:10080/login
    ```

1.  Masukkan password `password`

1.  Copy `token` yang keluar yah !

1.  Simpan token pada variabel dengan cara:

    ```sh
    TOKEN=$(curl http://127.0.0.1:10080/login -u user|jq -r '.token')
    ```

    Masukkan password `password`

1.  Coba lakukan dengan cara:

    ```sh
    curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure
    ```

1.  Bagian lainnya silahkan dicoba sendiri yah, kita akan langsung masuk ke Task 7 (Creating a Service)

1.  Pindah ke directory dengan perintah:

    ```sh
    cd ~/orchestrate-with-kubernetes/kubernetes
    ```

1.  Kita sekarang ingin membuat monolith yang secure (https), yang memiliki sertifikat TLS, untuk membuatnya, kita akan menggunakan perintah:

    ```sh
    kubectl create secret generic tls-certs --from-file tls/
    kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf
    kubectl create -f pods/secure-monolith.yaml
    ```

1.  Membuat service monolith yang biasa dengan perintah:

    ```sh
    kubectl create -f services/monolith.yaml
    ```

1.  Centang: `Create Monolith pods and service`

1.  Jalankan perintah berikut:

    ```sh
    gcloud compute firewall-rules create allow-monolith-nodeport \
    --allow=tcp:31000
    ```

1.  Centang `Allow traffic to the monolith service on the exposed nodeport`

1.  Supaya cepat, kita akan langsung Task 8 (Adding labels to pods)

1.  Kita akan mencoba untuk melihat pods yang memiliki label `monolith` dengan perintah:

    ```sh
    kubectl get pods -l "app=monolith"
    ```

1.  Eksperimen dengan perintah berikut:

    ```sh
    kubectl get pods -l "app=monolith,secure=enabled"
    ```

1.  Tambahkan label ke `secure-monolith`, kemudian lihat label yang dibuat, dengan perintah:

    ```sh
    kubectl label pods secure-monolith 'secure=enabled'
    kubectl get pods secure-monolith --show-labels
    ```

1.  Centang `Adding Labels to Pods`

1.  Masuk ke Task 9

1.  Membuat deployment dan service untuk `auth` dengan menggunakan perintah:

    ```sh
    kubectl create -f deployments/auth.yaml
    kubectl create -f services/auth.yaml
    ```

1.  Membuat deployment dan service untuk `hello` dengan menggunakan perintah:

    ```sh
    kubectl create -f deployments/hello.yaml
    kubectl create -f services/hello.yaml
    ```

1.  Membuat configmap, dan deployment untuk frontend dengan menggunakan perintah:

    ```sh
    kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
    kubectl create -f deployments/frontend.yaml
    kubectl create -f services/frontend.yaml
    ```

1.  Cek apakah FrontEnd sudah selesai dan sudah mendapatkan ip dengan perintah:

    ```sh
    kubectl get services frontend
    ```

1.  Apabila External Ip dari frontend sudah ada, centang `Creating Deployments (Auth, Hello and Frontend)`

1.  ~ Lab Selesai di sini ~
