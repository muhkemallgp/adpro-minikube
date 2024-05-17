#### Nama : Muh. Kemal Lathif Galih Putra
#### NPM : 2206081225
#### Kelas : ADPRO - A
#### ASDOS : REN
# TUTORIAL - 11
## Refleksi MiniCube

1. Compare the application logs before and after you exposed it as a Service.
Try to open the app several times while the proxy into the Service is running.
What do you see in the logs? Does the number of logs increase each time you open the app?

Before Expose
![alt text](/images/image.png)

After Expose
![alt text](/images/image-1.png)

Ada perbedaan jumlah log antara sebelum dan sesudah aplikasi di-expose sebagai Service. Sebelum diekspos, aplikasi diakses langsung di dalam pod dan log mencatat pesan awal (Started HTTP server on port 8080 dan Started UDP server on port 8081).

Setelah aplikasi di-expose sebagai Service menggunakan perintah minikube service hello-node, log tetap mencatat pesan awal dan permintaan yang masuk, namun sekarang permintaan tersebut diteruskan melalui service yang memungkinkan akses eksternal ke aplikasi. Setiap kali aplikasi diakses atau di-refresh melalui browser, jumlah entri log bertambah karena browser mengirimkan GET request ke service. Ini terlihat pada gambar dengan waktu 06:43:46 untuk log pertama dan 06:59:27 untuk log kedua.

2. Notice that there are two versions of `kubectl get` invocation during this tutorial section.
The first does not have any option, while the latter has `-n` option with value set to
`kube-system`. What is the purpose of the `-n` option and why did the output not list the pods/services that you
explicitly created?

Jawaban:

Opsi -n pada perintah kubectl get memungkinkan kita untuk memilih namespace tertentu di Kubernetes. Ini sangat berguna jika ada banyak layanan dengan nama yang sama di berbagai namespace. Jika opsi -n tidak digunakan, kubectl get akan menampilkan sumber daya dari namespace default. Namespace digunakan untuk memisahkan sumber daya dalam cluster. Dengan menggunakan -n kube-system, kita dapat melihat sumber daya dari namespace yang berisi komponen inti Kubernetes, seperti DNS dan server API. Tanpa opsi -n, perintah tersebut akan menampilkan sumber daya yang secara eksplisit dibuat oleh pengguna.

## Refleksi Update & Kubernetes Manifest File

1. What is the difference between Rolling Update and Recreate deployment strategy?

    Strategi Rolling Update dan Recreate dalam deployment kubernetes memiliki perbedaan yang cukup signifikan. Rolling Update memungkinkan pembaruan bertahap pada instance-instance ke versi terbaru tanpa menghentikan instance dengan versi lama, sehingga aplikasi tetap berjalan tanpa downtime. Di sisi lain, strategi Recreate membutuhkan penghentian semua instance lama sebelum menggantinya dengan versi baru, yang menyebabkan downtime. 

    Dari segi kompleksitas, Rolling Update lebih rumit tetapi mengurangi downtime dan menjaga ketersediaan aplikasi. Sementara itu, Recreate lebih sederhana namun bisa menyebabkan downtime pada aplikasi. Kedua strategi ini harus dipilih berdasarkan kebutuhan spesifik dari deployment aplikasi.

2. Try deploying the Spring Petclinic REST using Recreate deployment strategy and document
your attempt.

    Langkah yang dilakukan dalam mengubah deployment strategy menjadi Recreate adalah:
    
    - `minikube start` untuk memulai cluster minikube yang baru, khusus deployment Recreate
    - `minikube addons enable metrics-server` untuk mengaktifkan add-ons metric-server sama seperti pada tutorial sebelumnya
    - `kubectl apply -f recreate-deployment` dan `kubectl apply -f service,yaml` untuk apply manifest file dengan strategy deployment type Recreate
    - `kubectl get pods` untuk mengecek status pods sampai sudah mulai running
    - `minikube service spring-petclinic-rest` untuk mengaktifkan service melalui localhost dan mengakses endpoint `http:127.0.0.1:64008/petclinic/`

    ![alt text](/images/image-2.png)
    ![alt text](/images/image-3.png)

3.  Prepare different manifest files for executing Recreate deployment strategy

    Terdapat perbedaan manifest file untuk Rolling Update deployment dengan Recreate deployment. Jadi, manifest file (.yaml) yang dihasilkan sebelumnya perlu diedit di bagian:
    ```
    ...
        strategy:
            type: Recreate
    ...
    ```

4. What do you think are the benefits of using Kubernetes manifest files? Recall your experience
in deploying the app manually and compare it to your experience when deploying the same app
by applying the manifest files (i.e., invoking `kubectl apply -f` command) to the cluster.

    File manifest kubernetes bersikap seperti resep masakan. Di dalamnya sudah terdapat deskripsi yang jelas tentang proses deployment dan interaksi komponen. Terdapat juga langkah-langkah konfigurasi yang jelas, service yang digunakan, dan resource lainnya yang otomatis dijalankan ketika file manifest tersebut diaplikasikan. Dengan menggunakan file manifest untuk melakukan deployment, deployment dapat menjadi lebih efisien dan lebih konsisten dilakukan di environment yang berbeda-beda.
    Hal ini menjadi perbedaan yang signifikan ketika deployment harus saya lakukan secara manual. Deployment yang saya lakukan secara manual seringkali mengalami kesalahan konfigurasi dalam prosesnya sehingga kurang efisien.


