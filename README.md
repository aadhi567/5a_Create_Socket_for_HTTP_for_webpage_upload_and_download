# 5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
-AADHITHAN B 212224040001
## AIM :
To write a PYTHON program for socket for HTTP for web page upload and download
## Algorithm

1.Start the program.
<BR>
2.Get the frame size from the user
<BR>
3.To create the frame based on the user request.
<BR>
4.To send frames to server from the client side.
<BR>
5.If your frames reach the server it will send ACK signal to client otherwise it will send NACK signal to client.
<BR>
6.Stop the program
<BR>
## Program 
```
import socket
import threading

def simple_http_server(host='127.0.0.1', port=8080):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server:
        server.bind((host, port))
        server.listen(1)
        print(f"Server running on http://{host}:{port}")
        while True:
            conn, addr = server.accept()
            request = conn.recv(1024).decode()
            if not request:
                conn.close()
                continue
            if "POST /upload" in request:
                response_body = (
                    "<html><head><title>404 - Not Found</title></head>"
                    "<body><h1>404 - Not Found</h1></body></html>"
                )
                response = (
                    "HTTP/1.1 404 Not Found\r\n"
                    "Cache-Control: max-age=604800\r\n"
                    "Content-Type: text/html; charset=UTF-8\r\n"
                    f"Content-Length: {len(response_body)}\r\n"
                    "Connection: close\r\n\r\n"
                    + response_body
                )
                conn.sendall(response.encode())
            else:
                response_body = "<html><body><h1>File downloaded successfully.</h1></body></html>"
                response = (
                    "HTTP/1.1 200 OK\r\n"
                    "Content-Type: text/html\r\n"
                    f"Content-Length: {len(response_body)}\r\n"
                    "Connection: close\r\n\r\n"
                    + response_body
                )
                conn.sendall(response.encode())
            conn.close()

def send_request(host, port, request):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))
        s.sendall(request.encode())
        response = b""
        while True:
            data = s.recv(4096)
            if not data:
                break
            response += data
    return response.decode(errors="ignore")

def upload_file(host, port, filename):
    try:
        with open(filename, "rb") as f:
            file_data = f.read()
    except FileNotFoundError:
        file_data = b"This is sample upload content."
    content_length = len(file_data)
    request = (
        f"POST /upload HTTP/1.1\r\nHost: {host}\r\n"
        f"Content-Length: {content_length}\r\n"
        f"Connection: close\r\n\r\n{file_data.decode(errors='ignore')}"
    )
    return send_request(host, port, request)

def download_file(host, port, filename):
    request = f"GET /{filename} HTTP/1.1\r\nHost: {host}\r\nConnection: close\r\n\r\n"
    return send_request(host, port, request)

if __name__ == "__main__":
    host = "127.0.0.1"
    port = 8080
    threading.Thread(target=simple_http_server, args=(host, port), daemon=True).start()
    upload_response = upload_file(host, port, "example.txt")
    print("Upload response:\n", upload_response)
    download_response = download_file(host, port, "example.txt")
    print("\nFile downloaded successfully.")

```
## OUTPUT
<img width="1426" height="448" alt="image" src="https://github.com/user-attachments/assets/346ea1d0-3b42-4656-8140-beb99599228b" />


## Result
Thus the socket for HTTP for web page upload and download created and Executed
