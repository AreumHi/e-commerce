
# 🧾 03_Flowchart

## 📌 포인트 충전
사용자가 포인트 충전 페이지에서 원하는 금액을 입력하고 충전 버튼을 누르면,
서버에 충전 요청이 전달됩니다.
요청이 성공하면 사용자 보유 포인트가 증가하고,
실패 시 적절한 안내 메시지가 제공됩니다.

<div style="text-align: center;">
    <img src="img/flowchart_charge.png" alt="포인트 충전" width="300"/>
</div>

---

## 🔁 상품조회 -> 주문 -> 결제
사용자가 상품을 선택하고 쿠폰을 적용한 후, 잔액 확인을 거쳐 결제를 완료하는 흐름입니다.

<div style="text-align: center;">
    <img src="img/flowchart_order.png" alt="상품조회 -> 주문 -> 결제" width="300"/>
</div>

---

## 🎟️ 선착순 쿠폰 발급
사용자가 쿠폰이 없는 경우, 선착순 쿠폰 발급 API를 통해 할인 쿠폰을 요청할 수 있습니다. 발급은 수량 제한이 있기 때문에, 성공 여부에 따라 쿠폰을 발급받거나 실패 안내를 받습니다.

<div style="text-align: center;">
    <img src="img/flowchart_coupon.png" alt="선착순 쿠폰 발급" width="300"/>
</div>

---

## 📈 인기 상품 조회
사용자가 인기 상품을 조회하면, 최근 3일간의 판매 데이터를 기반으로
판매량이 높은 상위 5개의 상품을 정렬해 인기 상품 리스트로 표시합니다.

<div style="text-align: center;">
    <img src="img/flowchart_top_product.png" alt="인기 상품 조회" width="300"/>
</div>


---

### * 다이어그램 도구: [mermaidchart.com](https://www.mermaidchart.com/)

#### 📌 포인트 충전
```mermaid
flowchart TD
    A["포인트 충전 페이지 진입"] --> B["충전 금액 입력"]
    B --> C["충전 버튼 클릭"]
    C --> D["충전 요청"]
    D --> E{"충전 성공 여부"}
    E -- 성공 --> F["잔액 증가 → 충전 성공 안내"]
    E -- 실패 --> G["충전 실패 안내"]
    
    style F fill:#DFF0D8,stroke:#3C763D,stroke-width:2px
    style G fill:#F2DEDE,stroke:#A94442,stroke-width:2px
```



#### 🔁 상품조회 -> 주문 -> 결제
```mermaid

flowchart TD
    A["상품 목록 조회"] --> B["상품 선택"]
    B --> C["주문 요청"]
    C --> D["쿠폰 선택 확인"]
    D -- 보유 쿠폰 선택 --> E["결제 요청"]
    D -- 쿠폰 없음 --> E
    E --> F["잔액 확인 → 결제 진행"]
    F --> G{"결제 성공 여부"}
    G -- 성공 --> H["결제 완료 → 결제 성공 안내"]
    G -- 실패 --> I["결제 실패 안내"]
    
    style H fill:#DFF0D8,stroke:#3C763D,stroke-width:2px
    style I fill:#F2DEDE,stroke:#A94442,stroke-width:2px
```

#### 🎟️ 쿠폰 발급
```mermaid
flowchart TD
    A["서비스 진입"] --> B["남은 쿠폰 수량 확인"]
    B --> C["쿠폰 발급 가능 여부 확인"]
    C -- 발급 가능 --> D["쿠폰 발급 버튼 클릭"]
    C -- 발급 불가 --> H["쿠폰 불가 안내"]
    D --> E["쿠폰 발급 요청"]
    E --> F{"발급 성공 여부"}
    F -- 성공 --> G["쿠폰 목록에 추가"]
    F -- 실패 --> H
    
    style H fill:#F2DEDE,stroke:#A94442,stroke-width:2px
    style G fill:#DFF0D8,stroke:#3C763D,stroke-width:2px
```

#### 📈 인기 상품 조회
```mermaid
flowchart TD
    A[메인 페이지 진입 or 인기상품 영역 접근] --> B[인기상품 조회 요청]
    B --> C[최근 3일간 판매량 기준 인기 상품 정렬]
    C --> D[상위 5개 상품 응답 반환]
    D --> E[화면에 인기 상품 리스트 표시]
    
    style E fill:#DFF0D8,stroke:#3C763D,stroke-width:2px
```