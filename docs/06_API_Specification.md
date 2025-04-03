# 🧾 06_API_Specification

## 📦 API 전체 목록 & 엔드포인트 정리

| NO | API       | Method | URL                | Query Param     | Body                        |
|----|-----------|--------|--------------------|-----------------|-----------------------------|
| 1  | 포인트 조회    | `GET`  | `/point`           | ✅ userId        | ❌                           |
| 2  | 포인트 충전    | `POST` | `/point/charge`    | ❌               | ✅ userId, amount            |
| 3  | 상품 목록 조회  | `GET`  | `/product`         | ✅ color, size 등 | ❌                           |
| 4  | 상품 상세 조회  | `GET`  | `/product/detail`  | ✅ productId     | ❌                           |
| 5  | 주문 생성     | `POST` | `/order`           | ❌               | ✅ userId, items[], couponId |
| 6  | 결제 요청     | `POST` | `/payment`         | ❌               | ✅ orderId                   |
| 7  | 선착순 쿠폰 발급 | `POST` | `/coupon/issue`    | ❌               | ✅ userId                    |
| 8  | 인기 상품 조회  | `GET`  | `/product/popular` | ❌               | ❌                           |


## 🔍 1. 포인트 조회 API

- **Method**: `GET`
- **URL**: `/point`
- **Query Parameter**

  | 필드명    | 타입  | 필수 | 설명     |
    |--------|-----|----|--------|
  | userId | int | ✅  | 사용자 ID |

  **예시 요청**
  ```
  GET /point?userId=1
  ```

- **Response**

  #### ✅ 200 OK – 조회 성공
  ```json
  {
    "status": "SUCCESS",
    "message": "포인트 조회 성공",
    "data": {
      "userId": 1,
      "point": 15000 
    }
  }
  ```

  #### ❌ 404 Not Found – 사용자 없음
  ```json
  {
    "status": "FAIL",
    "message": "사용자를 찾을 수 없습니다.",
    "code": "USER_NOT_FOUND",
    "httpStatus": 404
  }
  ```

---

## 💰 2. 포인트 충전 API

- **Method**: `POST`
- **URL**: `/point/charge`
- **Request Body**

  | 필드명    | 타입  | 필수 | 설명             |
    |--------|-----|----|----------------|
  | userId | int | ✅  | 사용자 ID         |
  | amount | int | ✅  | 충전할 금액 (1원 이상) |

  ```json
  {
    "userId": 1,
    "amount": 10000
  }
  ```

- **Response**

  #### ✅ 200 OK – 충전 성공
  ```json
  {
    "status": "SUCCESS",
    "message": "충전 성공",
    "data": {
      "userId": 1,
      "point": 15000
    }
  }
  ```

  #### ❌ 400 Bad Request – 충전금액이 0 이하인 경우
  ```json
  {
    "status": "FAIL",
    "message": "충전 금액은 1원 이상이어야 합니다.",
    "code": "POINT_INVALID_AMOUNT",
    "httpStatus": 400
  }
  ```

---

## 📦 3. 상품 목록 조회 API

- **Method**: `GET`
- **URL**: `/product`
- **Query Parameter**

    | 필드명   | 타입     | 필수 | 설명        |
    |-------|--------|----|-----------|
    | color | string | ❌  | 필터: 색상    |
    | size  | string | ❌  | 필터: 사이즈   |
    | name  | string | ❌  | 필터: 상품 이름 |

    **예시 요청**
    ```
    GET /product?color=red&size=240
    ```

- **Response**

    #### ✅ 200 OK – 조회 성공
    ```json
    {
      "status": "SUCCESS",
      "message": "상품 목록 조회 성공",
      "data": [
        {
          "productId": 1,
          "name": "나이키 에어맥스",
          "price": 129000,
          "options": [
            {
              "optionId": 101,
              "color": "red",
              "size": "240",
              "stock": 3
            },
            {
              "optionId": 102,
              "color": "red",
              "size": "260",
              "stock": 2
            }
          ]
        },
        {
          "productId": 2,
          "name": "아디다스 울트라부스트",
          "price": 139000,
          "options": [
            {
              "optionId": 201,
              "color": "black",
              "size": "245",
              "stock": 5
            }
          ]
        }
      ]
    }
    ```
    #### ❌ 500 Internal Server Error – 시스템 오류 발생
    ```json
    {
      "status": "FAIL",
      "message": "상품 정보를 불러오지 못했습니다.",
      "code": "PRODUCT_FETCH_FAILED",
      "httpStatus": 500
    }
    ```

---

## 🔍 4. 상품 상세 조회 API

- **Method**: `GET`
- **URL**: `/product/detail`
- **Query Parameter**

    | 필드명       | 타입  | 필수 | 설명        |
    |-----------|-----|----|-----------|
    | productId | int | ✅  | 조회할 상품 ID |

    **예시 요청**
    ```
    GET /product/detail?productId=1
    ```

- **Response**

    #### ✅ 200 OK – 조회 성공

    ```json
    {
      "status": "SUCCESS",
      "message": "상품 상세 조회 성공",
      "data": {
        "productId": 1,
        "name": "나이키 에어맥스",
        "price": 129000,
        "options": [
          {
            "optionId": 101,
            "color": "Red",
            "size": "240",
            "stock": 3
          },
          {
            "optionId": 102,
            "color": "Black",
            "size": "250",
            "stock": 1
          }
        ]
      }
    }
    ```
  
- **Response**

    #### ❌ 404 Not Found – 상품 ID에 해당하는 상품 없음

    ```json
    {
      "status": "FAIL",
      "message": "상품을 찾을 수 없습니다.",
      "code": "PRODUCT_NOT_FOUND",
      "httpStatus": 404
    }
    ```
---

## 🛒 5. 주문 API

- **Method**: `POST`
- **URL**: `/order`
- **Request Body**

    | 필드명        | 타입    | 필수 | 설명        |
    |------------|-------|----|-----------|
    | userId     | int   | ✅  | 주문자 ID    |
    | items      | array | ✅  | 주문할 상품 목록 |
    | └ optionId | int   | ✅  | 상품 옵션 ID  |
    | └ quantity | int   | ✅  | 주문 수량     |
    | couponId   | int   | ❌  | 적용할 쿠폰 ID |
    
    **예시 요청**
    ```json
    {
    "userId": 1,
    "items": [
      { "optionId": 101, "quantity": 2 },
      { "optionId": 102, "quantity": 1 }
    ],
    "couponId": 5
    }
    ```
- **Response**

    #### ✅ 200 OK – 주문 성공

    ```json
    {
      "status": "SUCCESS",
      "message": "주문이 완료되었습니다.",
      "data": {
        "orderId": 1001,
        "totalPrice": 258000,
        "discountedPrice": 232200
      }
    }
    ```
- **Response**

    #### ❌ 400 Bad Request – 필수 항목 누락, 잘못된 요청
    
    ```json
    {
      "status": "FAIL",
      "message": "요청 데이터가 유효하지 않습니다.",
      "code": "ORDER_INVALID_REQUEST",
      "httpStatus": 400
    }
    ```
    
    #### ❌ 404 Not Found – 사용자 없음
    
    ```json
    {
      "status": "FAIL",
      "message": "사용자를 찾을 수 없습니다.",
      "code": "USER_NOT_FOUND",
      "httpStatus": 404
    }
    ```
    
    #### ❌ 409 Conflict – 재고 부족
    
    ```json
    {
      "status": "FAIL",
      "message": "재고가 부족한 상품이 있습니다.",
      "code": "OUT_OF_STOCK",
      "httpStatus": 409
    }
    ```

---

## 🛒 6. 결제 API

- **Method**: `POST`
- **URL**: `/payment`
- **Request Body**

    | 필드명       | 타입    | 필수 | 설명        |
    |-----------|-------|----|-----------|
    | orderId   | int   | ✅  | 결제할 주문 ID |

    **예시 요청**
    ```json
    {
     "orderId": 1001
    }
    ```
- **Response**

  #### ✅ 200 OK – 결제 성공
  ```json
  {
      "status": "SUCCESS",
      "message": "결제가 완료되었습니다.",
      "data": {
        "orderId": 1001,
        "paidAmount": 232200,
        "remainingPoint": 6800
      }
  }
  ```

  #### ❌ 409 Conflict – 포인트 부족
    
  ```json
  {
    "status": "FAIL",
    "message": "보유 포인트가 부족합니다.",
    "code": "POINT_INSUFFICIENT",
    "httpStatus": 409
  }
  ```

  #### ❌  404 Not Found – 주문 정보 없음
  ```json
  {
    "status": "FAIL",
    "message": "주문 정보를 찾을 수 없습니다.",
    "code": "ORDER_NOT_FOUND",
    "httpStatus": 404
  }
  ```
  
---

## 🎟️ 7. 선착순 쿠폰 발급 API

- **Method**: `POST`
- **URL**: `/coupon/issue`
- **Request Body**

    | 필드명    | 타입  | 필수 | 설명     |
    |--------|-----|----|--------|
    | userId | int | ✅  | 사용자 ID |
    
    **예시 요청**
    ```json
    {
      "userId": 1
    }
    ```

- **Response**

    #### ✅ 200 OK – 쿠폰 발급 성공

    ```json
    {
      "status": "SUCCESS",
      "message": "쿠폰이 성공적으로 발급되었습니다.",
      "data": {
        "couponId": 5,
        "discountRate": 20,
        "issuedAt": "2025-04-03T14:20:00",
        "expiredAt": "2025-04-10T23:59:59"
      }
    }
    ```

- **Response**

    #### ❌ 400 Bad Request – 사용자 ID 누락
    
    ```json
    {
      "status": "FAIL",
      "message": "사용자 ID는 필수입니다.",
      "code": "USER_ID_REQUIRED",
      "httpStatus": 400
    }
    ```
    
    #### ❌ 404 Not Found – 사용자 없음
    
    ```json
    {
      "status": "FAIL",
      "message": "사용자를 찾을 수 없습니다.",
      "code": "USER_NOT_FOUND",
      "httpStatus": 404
    }
    ```
    
    #### ❌ 409 Conflict – 이미 쿠폰 발급 받음
    
    ```json
    {
      "status": "FAIL",
      "message": "이미 쿠폰을 발급받은 사용자입니다.",
      "code": "COUPON_ALREADY_ISSUED",
      "httpStatus": 409
    }
    ```
    
    #### ❌ 409 Conflict – 잔여 쿠폰 없음
    
    ```json
    {
      "status": "FAIL",
      "message": "쿠폰이 모두 소진되었습니다.",
      "code": "COUPON_OUT_OF_STOCK",
      "httpStatus": 409
    }
    ```
---

## 📈 8. 인기 상품 조회 API

- **Method**: `GET`
- **URL**: `/product/popular`
- **Query Parameter**: 없음

    **예시 요청**
    ```
    GET /product/popular
    ```

- **Response**

  #### ✅ 200 OK – 인기 상품 조회 성공
    
    ```json
    {
      "status": "SUCCESS",
      "message": "인기 상품 조회 성공",
      "data": [
        {
          "rank": 1,
          "productId": 101,
          "name": "나이키 에어맥스",
          "totalSales": 76
        },
        {
          "rank": 2,
          "productId": 102,
          "name": "아디다스 슈퍼스타",
          "totalSales": 68
        },
        // rank 5위까지...
      ]
    }
    ```
    #### ❌ 400 Bad Request – 잘못된 집계 범위

    ```json
    {
      "status": "FAIL",
      "message": "지원하지 않는 집계 범위입니다. (예: 3d, 7d, 24h)",
      "code": "INVALID_AGGREGATION_RANGE",
      "httpStatus": 400
    }
    ```
  
    #### ❌ 500 Internal Server Error – 서버 오류

    ```json
    {
      "status": "FAIL",
      "message": "인기 상품 조회 중 오류가 발생했습니다.",
      "code": "POPULAR_PRODUCT_FETCH_FAILED",
      "httpStatus": 500
    }
    ```
    