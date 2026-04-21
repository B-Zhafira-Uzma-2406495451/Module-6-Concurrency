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
