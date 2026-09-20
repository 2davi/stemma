# stemma-api

Stemma 블로그 엔진의 백엔드다. Spring Boot 애플리케이션 하나가 글, 블록, 저장 이력, 미디어, 분류, 인용, 댓글과 반응을 맡는다.

## 이름

| 자리 | 값 |
| --- | --- |
| 기본 패키지 | `dev.the2davi.stemma` |
| 애플리케이션 이름 | `stemma-api` |
| 설정 접두사 | `stemma.*` |
| 환경 변수 접두사 | `STEMMA_` |
| 데이터베이스 | `stemma` |
| 데이터베이스 역할 | `stemma_app`은 데이터만, `stemma_migrator`는 구조 변경만 |

환경 변수는 Spring Boot의 규칙대로 속성 이름의 점을 밑줄로 바꾸고 대시를 지운 뒤 대문자로 올린다. 그래서 `stemma.outbox.relay-interval`은 `STEMMA_OUTBOX_RELAYINTERVAL`이 된다([Spring Boot 외부 설정 문서](https://docs.spring.io/spring-boot/reference/features/external-config.html)).

## 패키지 구성

기능 단위로 나눈다. `post`, `block`, `revision`, `citation`, `media`, `taxonomy`처럼 도메인 모델의 이름을 그대로 쓴다. 이력 쪽 이름은 설계가 고른 Git 어휘(`revision`, `doc_tree`, `post_ref`)를 따르고, 본문 비평의 은유는 코드 어휘로 끌어들이지 않는다.

## 구현 원칙

쓰기는 블록 단위로 받고, 읽기는 발행 스냅샷 한 덩이로 내보낸다. 발행이 두 형태를 맞추는 유일한 지점이다.

JSONB에 담는 블록 본문의 타입 표시는 `paragraph`, `image` 같은 논리 이름으로 적는다. 클래스 전체 이름을 저장하면 패키지 이름이 데이터에 새겨진다.

계정 정보는 회원 데이터베이스가 아니라 이벤트로 채운 `account_ref`에서 읽는다.

스키마 변경 파일은 이 모듈 안에 둔다. 데이터베이스를 가진 서비스가 그 구조도 가진다.
