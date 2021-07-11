---
title: "Payment with Iamport"
date: 2020-01-13 09:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

## 아임포트 적용기

진행하는 프로젝트에서 결제 부분 개발을 담당하면서 사용한 Iamport 라이브러리 사용기를 간단하게 작성하고자 한다.  
아임포트를 사용중이고, 인증결제로 진행중이다.

## 아임포트 라이브러리 추가하기

인증결제는 `아임포트 라이브러리` cdn 과 `jQuery`가 필수이다.

```html
<!-- jQuery -->
<script type="text/javascript" src="https://code.jquery.com/jquery-1.12.4.min.js" ></script>
<!-- iamport.payment.js -->
<script type="text/javascript" src="https://cdn.iamport.kr/js/iamport.payment-1.1.5.js"></script>
```

## 가맹점 식별코드

어드민에서 확인가능한 가맹점 식별코드를 가지고
root `index.html`에서 `IMP.init(${가맹점 식별코드})` 해준다.  
윈도우함수에 IMP 객체가 들어가고, 전역객체로 프로젝트 내부에서 사용하게 된다.

```js
const IMP = window.IMP; // 생략해도 괜찮습니다.
IMP.init("imp00000000"); // "imp00000000" 대신 발급받은 "가맹점 식별코드"를 사용합니다.
```

## 결제 호출

IMP 전역객체에는 5개의 메소드가 존재하는데.

- init
- agency
- request_pay
- communicate
- certification

이중 request_pay 메소드를 통해 결제창을 호출한다.  
아래에는 기본적으로 많이 들어가는 파라미터들을 넣어놨다.  
자세한 `params` 는 아래 문서를 통해 확인한다.  
[IMP params](https://docs.iamport.kr/tech/imp#param)

> merchant_uid 는 프로젝트 내부 룰에 따라 식별가능한 규칙으로 변경해도 무방하다.

```js
// IMP.request_pay(param, callback) 호출
// m_redirect_url 같은경우는 모바일 결제 완료시 리다이렉트 되는 url을 명시할 수 있다.

IMP.request_pay({ // param
  pg: "inicis",
  pay_method: "card",
  merchant_uid: "ORD20180131-0000011",
  name: "노르웨이 회전 의자",
  amount: 64900,
  buyer_email: "gildong@gmail.com",
  buyer_name: "홍길동",
  buyer_tel: "010-4242-4242",
  buyer_addr: "서울특별시 강남구 신사동",
  buyer_postcode: "01181",
  m_redirect_url: 'www.test.com'
}, rsp => { // callback
  if (rsp.success) {
      ...,
      // 결제 성공 시 로직,
      ...
  } else {
      ...,
      // 결제 실패 시 로직,
      ...
  }
});
```

## 결제 response

결제에 대한 결과는 디테일하게 구분된다.  
취소한 여부도 알수 있으며, 영수증 url도 같이 전달해준다.  
여기서 사용한 카드이름, 금액, 카드앞 4자리등을 결제 내역 페이지를 통해 정보를 제공한다.

```json
{
  "code": 0,
  "message": null,
  "response": {
    "amount": 10500,
    "apply_num": "45918441",
    "bank_code": null,
    "bank_name": null,
    "buyer_addr": null,
    "buyer_email": "test@test.net",
    "buyer_name": null,
    "buyer_postcode": null,
    "buyer_tel": null,
    "cancel_amount": 0,
    "cancel_history": [],
    "cancel_reason": null,
    "cancel_receipt_urls": [],
    "cancelled_at": 0,
    "card_code": "123",
    "card_name": "농협카드",
    "card_number": "1234100000005678",
    "card_quota": 0,
    "card_type": 1,
    "cash_receipt_issued": false,
    "channel": "pc",
    "currency": "KRW",
    "custom_data": null,
    "escrow": false,
    "fail_reason": null,
    "failed_at": 0,
    "imp_uid": "imp_102421081234",
    "merchant_uid": "merchant_1578448421234",
    "name": "테스트 상품",
    "paid_at": 1578448459,
    "pay_method": "card",
    "pg_id": "IPXXX",
    "pg_provider": "kcp",
    "pg_tid": "20638689381545",
    "receipt_url": "https://admin8.kcp.co.kr/assist/bill.BillActionNew.do?cmd=card_bill&tno=20638689381545&order_no=imp_102421081234&trade_mony=10500",
    "started_at": 1578448421,
    "status": "paid",
    "user_agent": "sorry_not_supported_anymore",
    "vbank_code": null,
    "vbank_date": 0,
    "vbank_holder": null,
    "vbank_issued_at": 0,
    "vbank_name": null,
    "vbank_num": null
  }
}
```

## 참조

[iamport docs](https://docs.iamport.kr/implementation/payment)  
[iamport api swagger](https://api.iamport.kr)
