# golang_https_demo

Here is a demo for building HTTPS server and client in golang.（基于Go语言的HTTPS服务端和客户端demo）

Reference: https://www.cnblogs.com/pzblog/p/9088286.html



## Run Server （运行服务端）

Only with server certification. （仅服务端有证书）

```shell
go run server.go
```

Both server and client need to load the other one's certification. （服务端和客户端都需要有证书）

```shell
go run server_cla.go
```



## Generate CA certification （生成CA证书）

**1. CA's private key （CA的私钥）**

  ```shell
  openssl genrsa -out ca.key 2048
  ```

  

**2. CA's certification （CA的证书）**

  ```shell
  openssl req -x509 -new -nodes -key ca.key -subj "/CN=tonybai.com" -days 5000 -out ca.crt
  ```

  


## Generate server certification （生成服务端证书）
**1. server's private key （服务端的私钥）**

  ```shell
  openssl genrsa -out server.key 2048
  ```

  

**2. server's request for certification （服务端向CA发出的建立证书的请求）**

   ```shell
   openssl req -new -key server.key -subj "/CN=localhost" -out server.csr
   ```

   

**3. CA generate certification for server （CA生成服务端的证书）**

   ```shell
   openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 5000
   ```

   

## Generate client certification （生成客户端证书）

**1. client's private key （客户端的私钥）**

   ```shell
   openssl genrsa -out client.key 2048
   ```

   

**2. client's request for certification （客户端向CA发出的建立证书的请求）**

   ```sh
   openssl req -new -key client.key -subj "/CN=tonybai_cn" -out client.csr
   ```

   

**3. create .ext file (ExtKeyUsage) on CA side （在CA端创建 .ext 文件）**

TLS in golang need to verify ExtKeyUsage.

   ```shell
   echo "extendedKeyUsage=clientAuth" > client.ext
   ```

   

**4. CA generate certification for client （CA生成客户端的证书）**

   ```shell
   openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -days 5000
   ```
