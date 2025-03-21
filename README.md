# Advanced Programming Module 06

| NAME               | ID         | ADVPROG CLASS |
| ------------------ | ---------- | ------------- |
| Sultan Ibnu Mansiz | 2306275840 | B             |

## Reflection 1: Single threaded web server
### 1. Setting Up a TCP Server

To enable communication with a browser, the server must listen for incoming connections. We achieve this using `TcpListener`:

```rust
TcpListener::bind("127.0.0.1:7878")
```
This binds the server to `127.0.0.1` on port `7878`.

- `unwrap()` ensures the server exits if an error occurs during startup.
- `for stream in listener.incoming()`
  - Iterates through incoming client connections.
  - Each connection yields a `TcpStream`.
- `let stream = stream.unwrap();`
  - Extracts data from the connection.
  - `unwrap()` ensures a valid connection.
- `println!("Connection established!");`
  - Logs a message when a new connection is received.

### 2. Handling HTTP Requests

The function `handle_connection(stream)` processes client requests. Without this, the server merely acknowledges connections without interpreting HTTP requests.

#### Implementation

```rust
fn handle_connection(mut stream: TcpStream) {
```
Accepts a `TcpStream` representing the connection between the client and server.

```rust
let buf_reader = BufReader::new(&stream);
```
Uses `BufReader` to facilitate reading the request line by line.

```rust
let http_request: Vec<_> = buf_reader
    .lines()
    .map(|result| result.unwrap())
    .take_while(|line| !line.is_empty())
    .collect();
```
- `.lines()`: Produces an iterator over input lines.
- `.map(|result| result.unwrap())`: Extracts the text, ignoring errors.
- `.take_while(|line| !line.is_empty())`: Stops reading at the empty line, marking the end of the request headers.
- `.collect()`: Stores the retrieved lines in a `Vec<String>`.

```rust
println!("Request: {:#?}", http_request);
```
Outputs the request details in a structured format.

### Example Output

```
Request: [
    "GET / HTTP/1.1",
    "Host: 127.0.0.1:7878",
    "Connection: keep-alive",
    "Cache-Control: max-age=0",
    "sec-ch-ua: \"Chromium\";v=\"134\", \"Not:A-Brand\";v=\"24\", \"Microsoft Edge\";v=\"134\"",
    "sec-ch-ua-mobile: ?0",
    "sec-ch-ua-platform: \"Windows\"",
    "Upgrade-Insecure-Requests: 1",
    "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/134.0.0.0 Safari/537.36 Edg/134.0.0.0",
    "Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
    "Sec-Fetch-Site: cross-site",
    "Sec-Fetch-Mode: navigate",
    "Sec-Fetch-User: ?1",
    "Sec-Fetch-Dest: document",
    "Accept-Encoding: gzip, deflate, br, zstd",
    "Accept-Language: en-US,en;q=0.9,id;q=0.8",
    "Cookie: csrftoken=mhBtv3yLYLjOKKlYzY2WnvtiPAKGyc5z",]
```

This output provides details such as the HTTP method (`GET`), requested resource (`/`), headers, and accepted data formats.

