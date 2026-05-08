# VNPAY PAY Demo

Demo Spring Boot tich hop VNPAY:
- Thanh toan thuong (Pay + Return + IPN)
- QueryDR (truy van giao dich)
- Refund (hoan tien)

## 1. Tech Stack

- Java 17
- Spring Boot 3.0.6
- Maven Wrapper (`mvnw`, `mvnw.cmd`)
- Thymeleaf (UI demo)
- Spring Data JPA
- H2 Database (mac dinh)
- WebClient (goi API VNPAY Query/Refund)

## 2. Yeu cau moi truong

- JDK 17
- Khuyen nghi dung Maven Wrapper trong project

Kiem tra nhanh:

```bash
java -version
./mvnw -v
```

## 3. Cau hinh

File cau hinh chinh: `src/main/resources/application.properties`

Thong so quan trong:

```properties
server.port=9999
server.address=0.0.0.0

vnpay.tmn-code=...
vnpay.secret-key=...
vnpay.pay-url=https://sandbox.vnpayment.vn/paymentv2/vpcpay.html
vnpay.return-url=http://localhost:9999/vnpay/vnpay-return
vnpay.api-url=https://sandbox.vnpayment.vn/merchant_webapi/api/transaction
```

Luu y:
- Can thay `vnpay.tmn-code` va `vnpay.secret-key` bang thong tin sandbox/production hop le.
- Khong commit secret that vao git.
- H2 console dang bat tai `/h2-console` (chi nen dung trong moi truong dev).

## 4. Chay ung dung

### Cach 1: Maven Wrapper (khuyen nghi)

Windows:
```bash
mvnw.cmd spring-boot:run
```

Mac/Linux:
```bash
./mvnw spring-boot:run
```

Mo trinh duyet:
- Home: `http://localhost:9999`
- H2 Console: `http://localhost:9999/h2-console`

### Cach 2: Build jar va chay

```bash
./mvnw clean package
java -jar target/payment-demo-0.0.1-SNAPSHOT.jar
```

## 5. Cac endpoint demo

Base path: `/vnpay`

### 5.1 Thanh toan

- `GET /vnpay/pay`  
  Hien thi form tao giao dich.

- `POST /vnpay/submitOrder`  
  Tao URL thanh toan va redirect sang VNPAY.

- `GET /vnpay/vnpay-return`  
  Nhan ket qua tra ve tren browser.

- `GET|POST /vnpay/vnpay-ipn`  
  Nhan callback server-to-server de cap nhat trang thai giao dich.

### 5.2 QueryDR

- `GET /vnpay/query`  
  Hien thi form truy van giao dich.

- `POST /vnpay/process-query`  
  Goi API query va hien thi ket qua.

### 5.3 Refund

- `GET /vnpay/refund`  
  Hien thi form hoan tien.

- `POST /vnpay/process-refund`  
  Goi API refund va hien thi ket qua.

### 5.4 Audit

- `GET /vnpay/audit/url-config`  
  Lay thong tin Return URL/IPN URL dang luu.

## 6. Luong xu ly IPN (tham khao)

Thu tu xu ly IPN nen la:
1. Validate input bat buoc.
2. Check checksum (`vnp_SecureHash`) -> sai tra `97`.
3. Tim don theo `vnp_TxnRef` -> khong thay tra `01`.
4. Doi soat `vnp_Amount` -> sai tra `04`.
5. Neu giao dich da xu ly truoc do:
   - Chi tra `02` khi callback lap lai hop le va thong tin khop voi giao dich da chot.
   - Khong dung rieng `vnp_TxnRef` lam dieu kien duy nhat cho `02`.
6. Neu hop le va xu ly thanh cong trong he thong -> tra `00`.

## 7. Test

Chay unit/integration test:

```bash
./mvnw test
```

## 8. Docker

Build image:

```bash
docker build -t payment-demo .
```

Project co `docker-compose.yml` de dung database phu tro (neu can).

## 9. Cau truc thu muc chinh

- `src/main/java/com/vnpay/springboot/payment/controller`  
  Controller cho pay/query/refund/ipn.
- `src/main/java/com/vnpay/springboot/payment/service`  
  Xu ly nghiep vu VNPAY.
- `src/main/java/com/vnpay/springboot/audit`  
  Audit log va cleanup job.
- `src/main/resources/templates`  
  Giao dien Thymeleaf demo.
- `src/main/resources/application.properties`  
  Cau hinh he thong.

## 10. Luu y van hanh

- Khong de lo `tmnCode`/`secretKey`.
- Can cau hinh timeout/retry cho luong goi API ngoai (Query/Refund) truoc khi dua production.
- Gioi han truy cap H2 console va endpoint nhay cam theo moi truong.
