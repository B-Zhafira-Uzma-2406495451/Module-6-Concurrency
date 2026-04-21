# Commit 1 Reflection Notes
Pada milestone pertama, saya mempelajari cara kerja dasar sebuah web server dengan menggunakan TcpListener dari
standard library Rust. Inti dari fungsionalitas ini terletak pada fungsi handle_connection yang bertugas
mengelola objek TcpStream untuk merepresentasikan koneksi TCP aktif antara client dan server. Di dalam fungsi
handle_connection, saya menggunakan BufReader untuk membungkus stream agar pembacaan data permintaan menjadi
jauh lebih efisien melalui mekanisme buffering. Proses pembacaan dilakukan baris demi baris menggunakan metode
.lines(). Satu hal krusial yang saya pelajari adalah penggunaan .take_while(|line| !line.is_empty()). Logika
ini sangat penting karena protokol HTTP menandakan akhir dari sebuah request dengan baris kosong dengan
instruksi tersebut, server akan berhenti membaca tepat setelah semua header diterima dan sebelum memasuki
bagian body atau menunggu data yang tidak ada. Sebagai verifikasi, program ini berhasil mencetak seluruh isi
permintaan HTTP ke konsol terminal seperti yang ada pada modul. Hasil tersebut mengonfirmasi bahwa browser
mengirimkan permintaan dalam format HTTP standar.  

# Commit 2 Reflection Notes  
Setelah server berhasil membaca request dari browser, langkah selanjutnya adalah membuat server mampu
memberikan respons yang sesungguhnya, yaitu mengirimkan halaman HTML yang bisa dirender oleh browser. Untuk
itu, fungsi handle_connection dimodifikasi dengan menambahkan logika untuk membangun dan mengirimkan HTTP
response. Pertama, konten file hello.html dibaca menggunakan fs::read_to_string, yang menghasilkan sebuah
String berisi markup HTML. Konten ini kemudian dirakit menjadi respons HTTP yang valid menggunakan format!,
dengan struktur di bawah ini :  
```http
HTTP/1.1 200 OK\r\n
Content-Length: {panjang_konten}\r\n
\r\n
{isi_html}
```  
Status line (HTTP/1.1 200 OK) memberi tahu browser bahwa request berhasil diproses. Header Content-Length
memberi tahu browser berapa byte yang harus dibaca sebagai body. Baris kosong (\r\n\r\n) berfungsi sebagai
pemisah antara header dan body. Tanpa salah satu dari elemen ini, browser tidak akan mampu memproses respons
dengan benar. Terakhir, respons yang telah dirakit dikirimkan kembali ke browser melalui stream.write_all().  
Hasil tampilan web akan terlihat seperti ini :
![Commit 2 screen capture](/assets/images/commit2.png)  

# Commit 3 Reflection Notes 
Pada milestone sebelumnya, server selalu mengembalikan hello.html untuk setiap request apapun tanpa terkecuali.
Tentu saja perilaku seperti ini tidak mencerminkan cara kerja web server yang sesungguhnya. Oleh karena itu,
pada milestone ini server ditingkatkan agar mampu memvalidasi request dan memberikan respons yang sesuai.
Caranya adalah dengan membaca request line pertama dari HTTP request, lalu mencocokkannya menggunakan kondisi
if-else. Jika request line adalah "GET / HTTP/1.1", server mengembalikan hello.html dengan status 200 OK. Untuk
request lainnya yang tidak dikenali, server mengembalikan 404.html dengan status 404 NOT FOUND. Dengan begitu,
mengakses http://127.0.0.1:7878/bad akan menampilkan halaman "Oops!", sedangkan http://127.0.0.1:7878 tetap
menampilkan halaman "Hello!". Selain validasi, pada milestone ini juga dilakukan refactoring terhadap kode.
Tanpa refactoring, blok if dan else masing-masing berisi logika yang hampir identik untuk membaca file dan
memformat respons, sehingga terjadi duplikasi kode yang tidak perlu. Dengan refactoring, percabangan hanya
menentukan dua variabel yaitu status_line dan filename sementara proses membaca file dan membangun respons
cukup ditulis satu kali di luar blok kondisional. Pendekatan ini membuat kode menjadi lebih ringkas, mudah
dibaca, dan di-maintain ke depannya sesuai prinsip DRY (Don't Repeat Yourself). Berikut ada contoh tampilan
web apabila request yang dikirim ke server tidak valid :
![Commit 3 screen capture](/assets/images/commit3.png) 