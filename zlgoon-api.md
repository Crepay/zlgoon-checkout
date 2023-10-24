## 공통
* 크레페이 서버
  * 테스트 환경: https://api.staging.crepay.io
  * 프로덕션 환경: 테스트 완료 후 별도 전달
* 프로토콜: https

## API 인증
* HTTP basic authorization 사용
* Authorization header에 ID 및 패스워드(별도 전달)로 생성한 credential을 전달
  * Credential: `base64({ID} + ":" + {PASSWORD})`
  * 헤더: `Authorization: Basic {CREDENTIAL}`

## 결제 가능 금액 조회

* HTTP method: `GET`
* Endpoint: `/v1/pay/code/zlgoon/balance`
* URL parameters: 없음
* Request - `application/json`
    * Body

      |      파라미터       |      타입      |   필수   | 설명                                   |
      |:---------------:|:------------:|:------:|--------------------------------------|
      | `transactionId` |   `String`   |  true  | 트랜잭션 ID                              |
      |   `timestamp`   | `Number(정수)` |  true  | 거래 시간 (UTC timestamp in milliseconds) |
      |    `barcode`    |   `String`   |  true  | 바코드(쿠폰) 번호                           |
      |   `payAmount`   |   `Number(정수)`   |  true  | 승인 요청 금액                             |
      |   `itemName`    |   `String`   |  true  | 상품명                                  |
      |    `brandId`    |   `String`   |  true  | 브랜드 ID                               |
      |   `storeCode`   |   `String`   |  true  | 승인 지점 코드                             |
      |   `storeName`   |   `String`   |  true  | 승인 지점 이름                             |
      |  `posDeviceId`  |   `String`   |  true  | POS 단말 ID                            |
        * Sample
             ```json
             {
               "transactionId": "SAMPLE_TRANSACTION_ID",
               "timestamp": 1697963791000,
               "barcode": "98765432123456789",
               "payAmount": 23500,
               "itemName": "샘플 상품명",
               "brandId": "SAMPLE_BRAND_ID",
               "storeCode": "SAMPLE_STORE_CODE",
               "storeName": "샘플 승인 지점 이름",
               "posDeviceId": "SMPLE_POS_DEVICE_ID"
             }
             ```

* Response - `application/json`
    * 성공
        * HTTP status: `200`
        * Body

          |      파라미터       |      타입      |   필수   | 설명         |
          |:---------------:|:------------:|:------:|------------|
          | `transactionId` |   `String`   |  true  | 트랜잭션 ID    |
          |    `barcode`    |   `String`   |  true  | 바코드(쿠폰) 번호 |
          |    `balance`    | `Number(정수)` |  true  | 결제 가능 금액   |
            * Sample

              ```json
              {
                "transactionId": "SAMPLE_TRANSACTION_ID",
                "barcode": "98765432123456789",
                "balance": 56700
              }
              ```

    * 실패
        * HTTP status: `200`이 아닌 값
        * Body

          |    파라미터     |     타입      |   필수   | 설명               |
          |:-----------:|:-----------:|:------:|------------------|
          | `errorCode` |  `String`   |  true  | 에러코드 (에러코드 표 참고) |
            * Sample

              ```json
              {
                "errorCode": "ZLGN001"
              }
              ```

## 결제(승인) 요청

* HTTP method: `POST`
* Endpoint: `/v1/pay/code/zlgoon/checkout`
* URL parameters: 없음
* Request - `application/json`
    * Body

      |      파라미터       |      타입      |   필수   | 설명                                   |
      |:---------------:|:------------:|:------:|--------------------------------------|
      | `transactionId` |   `String`   |  true  | 트랜잭션 ID                              |
      |   `timestamp`   | `Number(정수)` |  true  | 거래 시간 (UTC timestamp in milliseconds) |
      |    `barcode`    |   `String`   |  true  | 바코드(쿠폰) 번호                           |
      |  `checkoutId`   |   `String`   |  true  | 승인 번호                                |
      |   `payAmount`   | `Number(정수)` |  true  | 결제(승인) 요청 금액                         |
      |   `itemName`    |   `String`   |  true  | 상품명                                  |
      |    `brandId`    |   `String`   |  true  | 브랜드 ID                               |
      |   `storeCode`   |   `String`   |  true  | 승인 지점 코드                             |
      |   `storeName`   |   `String`   |  true  | 승인 지점 이름                             |
      |  `posDeviceId`  |   `String`   |  true  | POS 단말 ID                            |
        * Sample
             ```json
             {
               "transactionId": "SAMPLE_TRANSACTION_ID",
               "timestamp": 1697963791000,
               "barcode": "98765432123456789",
               "checkoutId": "SAMPLE_CHECKOUT_ID",
               "payAmount": 23500,
               "itemName": "샘플 상품명",
               "brandId": "SAMPLE_BRAND_ID",
               "storeCode": "SAMPLE_STORE_CODE",
               "storeName": "샘플 승인 지점 이름",
               "posDeviceId": "SMPLE_POS_DEVICE_ID"
             }
             ```

* Response - `application/json`
    * 성공
        * HTTP status: `200`
        * Body

          |      파라미터       |      타입      |   필수   | 설명            |
          |:---------------:|:------------:|:------:|---------------|
          | `transactionId` |   `String`   |  true  | 트랜잭션 ID       |
          |    `barcode`    |   `String`   |  true  | 바코드(쿠폰) 번호    |
          |    `checkedOutAmount`    | `Number(정수)` |  true  | 결제(승인) 완료된 금액 |
            * Sample

              ```json
              {
                "transactionId": "SAMPLE_TRANSACTION_ID",
                "barcode": "98765432123456789",
                "checkedOutAmount": 23500
              }
              ```

    * 실패
        * HTTP status: `200`이 아닌 값
        * Body

          |    파라미터     |     타입      |   필수   | 설명               |
          |:-----------:|:-----------:|:------:|------------------|
          | `errorCode` |  `String`   |  true  | 에러코드 (에러코드 표 참고) |
            * Sample

              ```json
              {
                "errorCode": "ZLGN011"
              }
              ```

## 결제(승인) 취소

* HTTP method: `PATCH`
* Endpoint: `/v1/pay/code/zlgoon/cancel`
* URL parameters: 없음
* Request - `application/json`
    * Body

      |          파라미터          |      타입      |   필수   | 설명                                   |
      |:----------------------:|:------------:|:------:|--------------------------------------|
      |    `transactionId`     |   `String`   |  true  | 트랜잭션 ID                              |
      |      `timestamp`       | `Number(정수)` |  true  | 거래 시간 (UTC timestamp in milliseconds) |
      |       `barcode`        |   `String`   |  true  | 바코드(쿠폰) 번호                           |
      | `cancelTtransactionId` |   `String`   |  true  | 승인 번호                                |
      |     `cancelAmount`     | `Number(정수)` |  true  | 결제(승인) 취소 요청 금액                      |
      |       `itemName`       |   `String`   |  true  | 상품명                                  |
      |       `brandId`        |   `String`   |  true  | 브랜드 ID                               |
      |      `storeCode`       |   `String`   |  true  | 승인 지점 코드                             |
      |      `storeName`       |   `String`   |  true  | 승인 지점 이름                             |
      |     `posDeviceId`      |   `String`   |  true  | POS 단말 ID                            |
        * Sample
             ```json
             {
               "transactionId": "SAMPLE_TRANSACTION_ID",
               "timestamp": 1697963791000,
               "barcode": "98765432123456789",
               "cancelTtransactionId": "SAMPLE_TRANSACTION_ID_TO_CANCEL",
               "cancelAmount": 23500,
               "itemName": "샘플 상품명",
               "brandId": "SAMPLE_BRAND_ID",
               "storeCode": "SAMPLE_STORE_CODE",
               "storeName": "샘플 승인 지점 이름",
               "posDeviceId": "SMPLE_POS_DEVICE_ID"
             }
             ```

* Response - `application/json`
    * 성공
        * HTTP status: `200`
        * Body

          |          파라미터           |      타입      |  필수  | 설명            |
          |:-----------------------:|:------------:|:----:|---------------|
          |     `transactionId`     |   `String`   | true | 트랜잭션 ID       |
          |        `barcode`        |   `String`   | true | 바코드(쿠폰) 번호    |
          | `canceledTransactionId` |   `String`   | true | 취소된 트랜잭션 ID   |
          |    `canceledAmount`     | `Number(정수)` | true | 결제(승인) 취소된 금액 |
            * Sample

              ```json
              {
                "transactionId": "SAMPLE_TRANSACTION_ID",
                "barcode": "98765432123456789",
                "canceledTransactionId": "SAMPLE_TRANSACTION_ID_TO_CANCEL",
                "canceledAmount": 23500
              }
              ```

    * 실패
        * HTTP status: `200`이 아닌 값
        * Body

          |    파라미터     |     타입      |   필수   | 설명               |
          |:-----------:|:-----------:|:------:|------------------|
          | `errorCode` |  `String`   |  true  | 에러코드 (에러코드 표 참고) |
            * Sample

              ```json
              {
                "errorCode": "ZLGN021"
              }
              ```

## 에러 코드

|      에러코드       | 설명           |
|:---------------:|--------------|
 |`ZLGN001`|존재하지 않는 바코드입니다.|
 |`ZLGN002`|이미 존재하는 transaction ID 입니다.|
 |`ZLGN003`|Timestamp 값이 현재 시간과 차이가 너무 큽니다.|
 |`ZLGN011`|잔액이 부족합니다. (승인 요청시 발생)|
 |`ZLGN012`|계좌 출금에 실패했습니다. (승인 요청시 발생)|
 |`ZLGN021`|취소할 거래번호가 존재하지 않습니다. (승인 취소시 발생)|
