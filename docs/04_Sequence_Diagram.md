# 🧾 04_Sequence_Diagrams


## 💰 포인트 조회 API

![포인트 조회 API](img/sequence_diagram_getpoint.png)

---

## 💰 포인트 충전 API

![포인트 충전 API](img/sequence_diagram_charge.png)

---

## 📦 상품 조회 API

![상품 조회 API](img/sequence_diagram_getproduct.png)

---

## 🔁 주문/결제 API

![주문/결제 API](img/sequence_diagram_order.png)

---

## 🎟️ 선착순 쿠폰 발급 API

- 선착순 쿠폰 발급은 다수의 사용자 동시 요청이 들어올 수 있음 
- 👉 재고 차감 시 트랜잭션 & 락 처리 같은 동시성 제어 중요 !


![선착순 쿠폰 발급 API](img/sequence_diagram_coupon.png)


---

## 📈 인기 상품 조회 API

### ✅ 배치로 사전 집계 → 통계 테이블에 저장 → API는 그 테이블만 조회

1. 🔄 배치 프로세스: 주기적으로 데이터를 조회하여 인기 상품을 집계
2. 📈 통계 테이블: 집계된 인기 상품 정보를 저장
3. ⚡ API 요청: 사용자 요청 시, 이미 계산된 통계 테이블에서 바로 응답

![인기 상품 조회 API](img/sequence_diagram_top_product.png)

<br>

---

### * 다이어그램 도구: [mermaidchart.com](https://www.mermaidchart.com/)

#### 💰 포인트 조회 API

```mermaid
sequenceDiagram
    actor 사용자
    participant 포인트 서비스
    participant 포인트 계좌

    사용자->>포인트 서비스: 보유 포인트 조회 요청

    alt 유효한 사용자일 경우
        포인트 서비스->>포인트 계좌: 사용자 ID로 포인트 조회
        포인트 계좌-->>포인트 서비스: 보유 포인트 반환
        포인트 서비스-->>사용자: 포인트 조회 성공 응답 (잔액 포함)
    else 사용자 정보 없음 or 조회 실패
        포인트 서비스-->>사용자: 포인트 조회 실패 응답
    end
```

#### 💰 포인트 충전 API

```mermaid
sequenceDiagram
    actor 사용자
    participant 포인트 서비스
    participant 포인트 계좌
    participant 포인트 내역

    사용자->>포인트 서비스: 포인트 충전 요청 (금액)

    alt 유효한 요청일 경우
        포인트 서비스->>포인트 계좌: 포인트 증가 요청
        포인트 계좌-->>포인트 서비스: 반영된 잔액 반환

        포인트 서비스->>포인트 내역: 충전 기록 저장
        포인트 내역-->>포인트 서비스: 기록 완료 응답

        포인트 서비스-->>사용자: 충전 성공 응답 (현재 잔액 포함)
    else 충전 금액이 0 이하 또는 사용자 없음
        포인트 서비스-->>사용자: 충전 실패 응답
    end
```

#### 📦 상품 조회 API
```mermaid
sequenceDiagram
    actor 사용자
    participant 상품 서비스
    participant 상품 옵션

    사용자->>상품 서비스: 상품 조회 요청

    alt 조회 성공
        상품 서비스->>상품 옵션: 옵션 및 재고 정보 조회
        상품 옵션-->>상품 서비스: 옵션 및 재고 정보 반환

        상품 서비스-->>사용자: 상품 + 옵션 조회 성공 응답
    else 오류 발생
        상품 서비스-->>사용자: 조회 실패 응답
    end
```

#### 🔁 주문/결제 API
```mermaid
sequenceDiagram
    actor 사용자
    participant 주문 서비스
    participant 포인트 계좌
    participant 쿠폰
    participant 재고 시스템
    participant 주문 기록
    participant 외부 데이터 플랫폼

    사용자->>주문 서비스: 주문 요청 (상품, 수량, 쿠폰 ID)

    alt 유효한 요청일 경우
        주문 서비스->>재고 시스템: 재고 차감 요청
        재고 시스템-->>주문 서비스: 재고 차감 성공

        주문 서비스->>쿠폰: 쿠폰 유효성 확인 및 사용 처리
        쿠폰-->>주문 서비스: 쿠폰 적용 결과 반환

        주문 서비스->>포인트 계좌: 결제 금액만큼 포인트 차감
        포인트 계좌-->>주문 서비스: 차감 결과 반환

        주문 서비스->>주문 기록: 주문 저장
        주문 기록-->>주문 서비스: 주문 ID 반환

        주문 서비스->>외부 데이터 플랫폼: 주문 정보 전송
        외부 데이터 플랫폼-->>주문 서비스: 전송 완료

        주문 서비스-->>사용자: 주문 완료 응답 (주문 ID 포함)
    else 재고 부족 or 포인트 부족 or 쿠폰 오류
        주문 서비스-->>사용자: 주문 실패 응답
    end
```

#### 🎟️ 선착순 쿠폰 발급 API
```mermaid
sequenceDiagram
    actor 사용자
    participant 쿠폰 서비스
    participant 쿠폰 재고
    participant 사용자 쿠폰 목록

    사용자->>쿠폰 서비스: 쿠폰 발급 요청

    alt 잔여 쿠폰 있음
        쿠폰 서비스->>쿠폰 재고: 발급 가능한지 확인
        쿠폰 재고-->>쿠폰 서비스: 발급 가능

        쿠폰 서비스->>쿠폰 재고: 재고 차감
        쿠폰 재고-->>쿠폰 서비스: 차감 완료

        쿠폰 서비스->>사용자 쿠폰 목록: 사용자에게 쿠폰 발급
        사용자 쿠폰 목록-->>쿠폰 서비스: 발급 완료

        쿠폰 서비스-->>사용자: 쿠폰 발급 성공 응답
    else 쿠폰 소진 or 중복 발급 요청
        쿠폰 서비스-->>사용자: 쿠폰 발급 실패 응답
    end
```

#### 📈 인기 상품 조회 API
```mermaid
sequenceDiagram
    participant 통계 배치 프로세스
    participant 주문 기록
    participant 통계 테이블

    participant 사용자
    participant 인기 상품 API

    %% 배치 프로세스 흐름
    loop 1시간
        통계 배치 프로세스->>주문 기록: 최근 3일 주문 내역 조회
        주문 기록-->>통계 배치 프로세스: 주문 데이터 반환
        통계 배치 프로세스->>통계 테이블: 인기 상품 집계 결과 저장
    end

    %% 사용자 요청 흐름
    사용자->>인기 상품 API: 인기 상품 조회 요청
    인기 상품 API->>통계 테이블: 상위 5개 상품 조회
    통계 테이블-->>인기 상품 API: 인기 상품 데이터 반환
    인기 상품 API-->>사용자: 인기 상품 응답
```