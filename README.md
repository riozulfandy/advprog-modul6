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

## Commit 3 Reflection

```rust
fn handle_connection(mut stream: TcpStream) { 
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();
    let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };

    let contents = fs::read_to_string(filename).unwrap();
    let response = format_response(status_line, &contents);

    stream.write_all(response.as_bytes()).unwrap();
}

fn format_response(status_line: &str, contents: &str) -> String {
    let length = contents.len();
    format!(
        "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
    )
}
```
Pada saat ini, pembuatan respons dilakukan di dalam struktur percabangan if dan else. Kedua bagian kode hampir identik, dengan perbedaan pada baris status, isi, dan panjang. Adalah mungkin untuk membuat sebuah fungsi terpisah yang menerima parameter-parameter ini dan menghasilkan respons yang diformat secara sesuai. Dengan melakukan refaktorisasi seperti ini, kita dapat meningkatkan keterbacaan dan kemudahan pemeliharaan kode. Dengan memisahkan logika pembuatan respons ke dalam fungsi terpisah, kita dapat menghindari duplikasi kode dan membuatnya lebih mudah untuk dikelola di masa mendatang.

![Commit 3 screen capture](/assets/images/commit3.png)

## Commit 4 Reflection

Jika mencoba membuka dua jendela browser dan mengakses 127.0.0.1/sleep di salah satunya dan 127.0.0.1/ di tab lainnya, akan terjadi penundaan dalam pemuatan halaman. Penundaan ini disebabkan oleh server tunggal yang mengelola semua permintaan secara berurutan, bukan secara bersamaan. Ketika server sedang menangani permintaan /sleep dan melakukan penundaan selama lima detik, permintaan lainnya harus menunggu hingga server menyelesaikan penanganan permintaan tersebut.