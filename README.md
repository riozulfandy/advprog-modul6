## Muhammad Mariozulfandy 2206041404

### Commit 1 Reflection
```rust
fn handle_connection(mut stream: TcpStream) { 
    let buf_reader = BufReader::new(&mut stream);
    let http_request: Vec<_> = buf_reader 
        .lines() 
        .map(|result| result.unwrap())
        .take_while(|line| !line.is_empty()) 
        .collect();
    println!("Request: {:#?}", http_request);
}
```

Dalam fungsi handle_connection, terdapat suatu instance BufReader yang melingkupi mutable reference ke stream. BufReader menambahkan buffering dengan mengelola panggilan ke method std::io::Read.

Variabel http_request dibuat untuk mengumpulkan baris-baris permintaan yang dikirim oleh browser ke server, dan mengumpulkannya dalam sebuah vektor dengan tipe annotation Vec<_>.

BufReader mengimplementasikan std::io::BufRead, yang menyediakan method lines. Method lines mengembalikan iterator dari Result<String, std::io::Error> dengan membagi stream setiap kali byte baris baru terdeteksi. Untuk mendapatkan setiap String, method ini melakukan pemetaan dan membuka setiap result. Result mungkin menghasilkan kesalahan jika data tidak valid dalam format UTF-8 atau jika terjadi masalah saat membaca dari stream.

Browser menandai akhir dari permintaan HTTP dengan mengirim dua karakter baris baru secara berurutan, sehingga untuk menangkap satu permintaan dari stream, kita mengambil baris-baris sampai kita menemukan baris yang kosong. Kemudian, proses dilanjutkan dengan mengumpulkan baris-baris tersebut ke dalam vektor dan mencetaknya menggunakan format debug yang mencukupi agar instruksi yang dikirim oleh browser web ke server terlihat jelas.

### Commit 2 Reflection

![Commit 2 screen capture](/assets/images/commit2.png)