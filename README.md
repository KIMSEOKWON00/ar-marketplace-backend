```
   █████╗ ██████╗    ███╗   ███╗ █████╗ ██████╗ ██╗  ██╗███████╗████████╗██████╗ ██╗      █████╗  ██████╗███████╗
  ██╔══██╗██╔══██╗   ████╗ ████║██╔══██╗██╔══██╗██║ ██╔╝██╔════╝╚══██╔══╝██╔══██╗██║     ██╔══██╗██╔════╝██╔════╝
  ███████║██████╔╝   ██╔████╔██║███████║██████╔╝█████╔╝ █████╗     ██║   ██████╔╝██║     ███████║██║     █████╗
  ██╔══██║██╔══██╗   ██║╚██╔╝██║██╔══██║██╔══██╗██╔═██╗ ██╔══╝     ██║   ██╔═══╝ ██║     ██╔══██║██║     ██╔══╝
  ██║  ██║██║  ██║██╗██║ ╚═╝ ██║██║  ██║██║  ██║██║  ██╗███████╗   ██║   ██║     ███████╗██║  ██║╚██████╗███████╗
  ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚═╝     ╚═╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═╝╚══════╝   ╚═╝   ╚═╝     ╚══════╝╚═╝  ╚═╝ ╚═════╝╚══════╝

                                     B A C K E N D   S E R V E R
```

<div align="center">

### AR(증강현실) 중고거래 마켓플레이스 iOS 앱을 위한 Node.js/Express + Socket.IO 백엔드 서버

[![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/Express-4.19.2-000000?style=flat-square&logo=express&logoColor=white)](https://expressjs.com/)
[![Socket.IO](https://img.shields.io/badge/Socket.IO-4.8.0-010101?style=flat-square&logo=socket.io&logoColor=white)](https://socket.io/)
[![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![Knex.js](https://img.shields.io/badge/Knex.js-3.1.0-D26B38?style=flat-square)](https://knexjs.org/)
[![JWT](https://img.shields.io/badge/JWT-jsonwebtoken-000000?style=flat-square&logo=jsonwebtokens&logoColor=white)](https://jwt.io/)
[![AWS EC2](https://img.shields.io/badge/AWS-EC2-FF9900?style=flat-square&logo=amazonec2&logoColor=white)](https://aws.amazon.com/ec2/)
[![AWS RDS](https://img.shields.io/badge/AWS-RDS-527FFF?style=flat-square&logo=amazonrds&logoColor=white)](https://aws.amazon.com/rds/)

</div>

---

## 목차

- [한 줄 소개](#한-줄-소개)
- [주요 기능](#주요-기능)
- [기술 스택](#기술-스택)
- [시스템 아키텍처](#시스템-아키텍처)
- [API 문서](#api-문서)
- [데이터베이스 설계](#데이터베이스-설계)
- [기술적 도전과 해결](#기술적-도전과-해결)
- [알려진 이슈 및 개선 방향](#알려진-이슈-및-개선-방향)
- [Cloud 인프라 구성](#cloud-인프라-구성)
- [환경 설정 및 실행 방법](#환경-설정-및-실행-방법)
- [iOS 앱 레포](#ios-앱-레포)
- [팀원](#팀원)

---

## 한 줄 소개

> 중고 물품 거래 시 **.usdz 3D AR 모델**을 함께 등록하여, iOS의 **AR Quick Look**으로 구매자가 실제 크기의 상품을 미리 확인할 수 있는 중고거래 마켓플레이스의 백엔드 서버입니다.

REST API(Express)와 실시간 채팅(Socket.IO)이 하나의 서버 프로세스에서 동작하며, Knex.js 위에 자체 설계한 경량 쿼리 추상화 계층을 통해 MySQL과 통신합니다.

---

## 주요 기능

| 기능 | 설명 |
|---|---|
| 🔐 회원/인증 | JWT(HS512) 기반 인증, 회원가입 즉시 자동 로그인, 소프트 삭제 방식 탈퇴 |
| 📦 게시물(중고거래) | 등록/수정/검색/상세조회, 거래상태·활성화 상태 관리, 읍면동 기준 지역 필터, 가격/찜수/최신순 정렬 |
| 🧊 AR 모델 첨부 | `.usdz` 파일 업로드 및 `model/vnd.usdz+zip` 헤더로 서빙 → iOS AR Quick Look 연동 |
| 🖼️ 이미지 업로드 | multer 기반 다중 이미지 업로드(최대 10장, 각 10MB 제한) |
| ⭐ 즐겨찾기 | 게시물 찜하기/취소, 목록 조회 시 N+1 쿼리 없이 찜 여부·개수 동시 반환 |
| 📍 관심지역 / 검색기록 | 사용자별 관심 읍면동 등록, 게시물 검색어 자동 기록 |
| 💬 실시간 채팅 | Socket.IO 기반 1:1 채팅(채팅목록/메시지 전송·수신/읽음 처리) |

---

## 기술 스택

> `package.json`의 전체 의존성 중 실제 코드에서 `require()` 사용이 확인된 패키지만 기재했습니다.
> (`mysql`, `express-session`, `express-mysql-session`, `express-winston`, `body-parser`는 선언만 되어 있고 실사용이 확인되지 않아 제외)

**Web / Realtime**

![Express](https://img.shields.io/badge/Express-4.19.2-000000?style=flat-square&logo=express&logoColor=white)
![Socket.IO](https://img.shields.io/badge/Socket.IO-4.8.0-010101?style=flat-square&logo=socket.io&logoColor=white)
![express--async--errors](https://img.shields.io/badge/express--async--errors-3.1.1-000000?style=flat-square)
![CORS](https://img.shields.io/badge/cors-2.8.5-000000?style=flat-square)

**Database**

![Knex.js](https://img.shields.io/badge/Knex.js-3.1.0-D26B38?style=flat-square)
![MySQL2](https://img.shields.io/badge/mysql2-3.9.7-4479A1?style=flat-square&logo=mysql&logoColor=white)

**Auth**

![jsonwebtoken](https://img.shields.io/badge/jsonwebtoken-9.0.2-000000?style=flat-square&logo=jsonwebtokens&logoColor=white)
![bcrypt](https://img.shields.io/badge/bcrypt-5.1.1-338033?style=flat-square)
![uuid](https://img.shields.io/badge/uuid-9.0.1-000000?style=flat-square)

**File Upload / Validation**

![multer](https://img.shields.io/badge/multer-1.4.5--lts.1-000000?style=flat-square)
![zod](https://img.shields.io/badge/zod-3.23.8-3E67B1?style=flat-square)

**etc**

![dotenv](https://img.shields.io/badge/dotenv-16.4.5-ECD53F?style=flat-square)
![chalk](https://img.shields.io/badge/chalk-4.1.2-000000?style=flat-square)

**로깅 참고**: `express-winston`이 `package.json`에 선언되어 있으나 실사용 0건으로 확인되어 위 목록에서 제외했습니다. 실제 요청/응답 로깅은 `chalk` 기반 자체 구현(`utils/log.js` + `middlewares/request-logger.js`/`response-logger.js`)입니다.

---

## 시스템 아키텍처

### 전체 구조

```
                        ┌─────────────────────────────┐
                        │        iOS 클라이언트          │
                        └───────────┬─────────┬───────┘
                                    │         │
                    REST (JSON /   │         │  Socket.IO
                    multipart)     │         │  (실시간 채팅)
                                    ▼         ▼
                        ┌──────────────────────────────┐
                        │   Express + Socket.IO 서버    │
                        │      (app.js, 단일 포트)       │
                        └───────────┬─────────┬────────┘
                                    │         │
                              Knex.js      mysql2/promise
                            (REST 쿼리)     (Socket 전용 풀)
                                    │         │
                                    ▼         ▼
                        ┌─────────────────────────────┐
                        │       MySQL (RDS / Local)   │
                        └─────────────────────────────┘
```

REST API와 Socket.IO는 `http.createServer(app)` 하나를 공유하는 **단일 프로세스 구조**입니다. 별도의 API 게이트웨이나 마이크로서비스 분리는 없습니다.

### 레이어 구조

```
routes/         HTTP 메서드 + 경로 정의, authenticate 미들웨어 부착 여부 결정 (11개 도메인)
                    │
controllers/    요청 파싱(zod 검증) → repository 호출 조합 → 응답 반환
                    │
services/       ⚠ auth 도메인 1개만 존재 (토큰 발급/검증/해싱 캡슐화)
                    │
repositories/   Knex 쿼리 빌더(자체 Model 추상화) 또는 raw SQL로 DB 접근
```

`services/`가 `auth-service.js` 하나뿐이라, auth 도메인만 서비스 계층으로 분리되고 나머지(post/chat/user 등)는 컨트롤러가 서비스 역할까지 겸하는 구조입니다. repository 계층은 두 스타일이 혼재합니다 — 대다수 도메인은 **Knex 쿼리 빌더 + 자체 `Model` 추상화**(zod 스키마 기반 동적 컬럼 getter)를 사용하고, `chat`/`message` 도메인만 **raw SQL**을 직접 작성합니다.

### Socket.IO 연동 구조

- Express 앱과 같은 HTTP 서버·같은 포트를 공유 (`socketIo(server, { allowEIO3: true })`)
- REST(Knex 경유)와 별도로, Socket.IO는 `mysql2/promise`로 독립된 커넥션 풀을 직접 생성해 사용
- 메시지 전송은 프로세스 메모리 내 FIFO 큐로 순차 처리 (Redis 등 외부 큐 미사용)
- ⚠️ Socket.IO 경로에는 JWT 인증이 없으며, 클라이언트가 보낸 `username`을 그대로 신뢰합니다.

### iOS 앱과의 통신 흐름

1. REST: `Authorization: Bearer <JWT>` 헤더로 인증, JSON 또는 `multipart/form-data`로 요청
2. AR 모델: `.usdz` 파일을 `Content-Type: model/vnd.usdz+zip`으로 서빙 → iOS가 AR Quick Look을 자동 실행
3. 실시간 채팅: Socket.IO 클라이언트 SDK로 REST와 별도 채널 연결

---

## API 문서

> `routes/` 폴더 기준 실제 라우트 그룹은 총 **11개**입니다(REST 엔드포인트 **31개** + 커스텀 라우트 1개(`/uploads/model/:filename`) + 정적 파일 서빙 1개, `routes/*.js` 11개 파일 전수 확인 기준).

### 주요 엔드포인트

| 그룹 | 메서드 | 경로 | 설명 | 인증 |
|---|---|---|---|---|
| auth | POST | `/auth/signup` | 회원가입 (가입 즉시 토큰 발급) | ❌ |
| auth | POST | `/auth/login` | 로그인, accessToken 발급 | ❌ |
| auth | POST | `/auth/validate-token` | 토큰 유효성 검사 | ✅ |
| user | GET | `/user/` | 내 정보 조회 | ✅ |
| user | PATCH | `/user/` | 내 정보 수정 (JSON/multipart) | ✅ |
| user | PATCH | `/user/deactivate` | 계정 비활성화(탈퇴) | ✅ |
| post | GET | `/post/search` | 게시물 검색 (지역/정렬/AR 유무 필터) | ✅ |
| post | POST | `/post/` | 게시물 생성 (사진/AR 모델 연결) | ✅ |
| post | PATCH | `/post/change-status` | 거래상태 변경 | ✅ |
| photo | POST | `/photo/` | 이미지 업로드 (최대 10장) | ✅ |
| ar | POST | `/ar/` | AR 모델(.usdz) 업로드 | ✅ |
| ar | GET | `/ar/post_id/:post_id` | AR 모델 메타데이터 조회 | ✅ |
| favorite | POST / DELETE | `/favorite/` | 게시물 즐겨찾기 추가/삭제 | ✅ |
| my-village | GET / POST | `/my-village/` | 관심지역 조회/추가 | ✅ |
| history | GET / DELETE | `/history/` | 검색 기록 조회/삭제 | ✅ |
| address | GET | `/address/search` | 주소 검색 (공개 데이터) | ❌ |
| chat | GET | `/chat/by-username/:username` | 채팅 목록 조회 | ❌ |
| message | GET | `/message/` | 메시지 조회 | ❌ |
| - | GET | `/uploads/model/:filename` | AR 모델 파일 스트리밍 (`model/vnd.usdz+zip`) | ❌ |

전체 라우트 그룹: `auth`(3) · `user`(4) · `post`(8) · `photo`(3) · `ar`(2) · `favorite`(2) · `my-village`(2) · `history`(2) · `address`(3) · `chat`(1) · `message`(1)

> `chat`/`message` 라우트는 `authenticate` 미들웨어가 부착되어 있지 않습니다. `message`의 경우 라우트 파라미터 누락 및 존재하지 않는 컬럼 참조로 실질적으로 동작하지 않는 것으로 확인되었습니다 — 실사용 채팅 기능은 아래 Socket.IO 쪽이 담당합니다.

### JWT 인증 방식

- 발급: `jwt.sign()` — 알고리즘 `HS512`, `issuer: ueong_server`, `audience: ueong_client`, `expiresIn: 15m`, `jwtid`(uuid)
- 검증: `Authorization` 헤더에서 `Bearer <token>` 추출 → `jwt.verify()` → 토큰의 `sub`(username)로 **매 요청마다 DB 재조회**해 `is_active` 확인
- Refresh Token 메커니즘 없음 — 15분 만료 후 재로그인 필요

### AR 모델 파일 서빙 방식

```
POST /ar (multipart, model 필드)
   └─▶ uploads/models/ 디스크 저장 + DB row 생성 (트랜잭션)
POST /post (ar_model_id 포함)
   └─▶ AR 모델을 게시물에 연결
GET /ar/post_id/:post_id
   └─▶ 메타데이터(ar_model_directory) 조회
GET /uploads/model/:filename   ← 단수형 커스텀 라우트
   └─▶ Content-Type: model/vnd.usdz+zip 로 파일 스트리밍
```

### Socket.IO 이벤트 목록

| 이벤트 | 방향 | 용도 |
|---|---|---|
| `registerUser` | C→S | username-socket 매핑 |
| `checkChat` | C→S / S→C(`checkChatResponse`) | 채팅방 존재 여부 확인 |
| `chatList` | C→S / S→C(`chatListResponse`) | 채팅방 목록 조회 |
| `loadExistingMessages` | C→S / S→C(`loadExistingMessagesResponse`) | 기존 메시지 로드 |
| `joinChatRoom` | C→S | 재접속 시 채팅방 일괄 재구독 |
| `sendMessage` | C→S / S→C(`sendMessageResponse`, 방 전체 브로드캐스트) | 메시지 전송 |
| `allReadMessage` | C→S / S→C(`allReadMessageResponse`) | 메시지 읽음 처리 |
| `acknowledge` | C→S / S→C(`response`) | 연결 확인 핑퐁 |
| `disconnect` / `error` | 내부 | 연결 해제 / 에러 로깅 |

---

## 데이터베이스 설계

가장 최신 스키마 덤프 기준 **총 17개 테이블**로 구성되어 있습니다.

**핵심 테이블**: `users`, `post`, `photo`, `ar_model`, `category`, `chats`, `messages`, `favorite`, `my_village`, `post_search_history`, `address_sd`/`address_sgg`/`address_emd`

**주요 관계**
- `post` ← `photo`, `ar_model`, `chats` (1:N)
- `users` ↔ `post` (favorite 다대다), `users` ↔ `address_emd` (my_village 다대다)
- `chats` ↔ `messages` 순환 참조 (`messages.chat_id` → `chats`, `chats.last_message_id` → `messages`)
- `address_sd` → `address_sgg` → `address_emd` (행정구역 3단계 계층)

### 개발/운영 DB 이원화 방식

`.env`에 `_DEV`/`_PROD` 접미사가 붙은 변수 쌍을 두고, `config/index.js`에서 `NODE_ENV` 값에 따라 host/user/password/database 4개 필드만 전환합니다.

```js
host: process.env.NODE_ENV === 'production'
  ? process.env.DB_HOST_PROD
  : process.env.DB_HOST_DEV,
```

운영 DB는 **AWS RDS(MySQL, 서울 리전)**, 개발 DB는 로컬 MySQL을 사용합니다.

### 주요 쿼리 패턴

- 자체 `Model` 추상화 계층 — zod 스키마의 필드를 그대로 재사용해 `Post.post_id` 같은 컬럼 참조 getter를 동적 생성
- 게시물 목록 조회는 `postQuery()` 재사용 베이스 쿼리 + 4종 `leftJoin`(작성자/AR모델/찜여부/찜개수)으로 N+1 쿼리 없이 처리
- 레포지토리 결과를 zod로 재검증하는 `validGet`/`validCreate`/`validUpdate` 패턴으로 "영향받은 행 0건" 같은 상황을 명시적 에러로 승격

---

## 기술적 도전과 해결

### 찜 여부 N+1 문제

- **문제 상황**: 게시물 목록을 조회할 때 각 게시물마다 "현재 로그인한 사용자가 이 게시물을 찜했는지" 여부를 함께 내려줘야 했습니다. 게시물마다 별도 쿼리로 확인하면 목록 길이만큼 쿼리가 늘어나는 N+1 문제가 발생합니다.
- **해결 방식**: `postQuery()`(`post-repository.js`)의 `favorite` 테이블 `leftJoin` 조건에 현재 로그인 사용자의 `user_id`를 반환하는 서브쿼리(`User.user_id(username)`)를 직접 끼워 넣어, 조인 시점에 찜 여부를 함께 계산하도록 구성했습니다.
- **선택 이유**: 애플리케이션 레벨에서 매 row마다 별도 조회를 추가하는 대신 SQL 조인 조건에 상관 서브쿼리를 인라인해, 목록·찜 여부·찜 개수를 단일 쿼리로 한 번에 가져오는 방식을 선택했습니다. 게시물 수가 늘어나도 쿼리 횟수가 고정된다는 장점이 있습니다.

### 다건 insert 시 생성된 각 row의 id 추정

- **문제 상황**: 사진/AR 모델처럼 여러 파일을 한 번에 업로드할 때 insert된 각 row의 id를 응답에 포함해야 했습니다. MySQL은 `INSERT ... RETURNING`을 지원하지 않고, Knex의 MySQL 드라이버도 배치 insert 시 첫 번째 row의 id만 반환합니다.
- **해결 방식**: `LAST_INSERT_ID()`로 얻은 첫 id에 배열 인덱스를 더해(`lastInsertId + i`) 나머지 row들의 id를 추정하고, MySQL의 auto-increment 연속성이 동시 트랜잭션에 의해 깨지지 않도록 `db.transaction`을 `isolationLevel: 'serializable'`로 감쌌습니다(`ar-repository.js`, `photo-repository.js`).
- **선택 이유**: 추가 조회 없이 한 번의 라운드트립으로 여러 row의 id를 확보할 수 있어 효율적이지만, `innodb_autoinc_lock_mode` 설정에 따라 다른 MySQL 환경에서는 이 가정이 깨질 수 있다는 점을 감안해 격리 수준을 높여 안전장치를 둔 선택입니다.

### zod 스키마 재사용 기반 Model 추상화

- **문제 상황**: 입력 검증 스키마(zod)와 쿼리에서 사용하는 테이블/컬럼 참조를 각각 따로 관리하면, 컬럼이 추가·변경될 때마다 두 곳을 동시에 고쳐야 하는 이중 관리 문제가 생깁니다.
- **해결 방식**: `utils/db/model.js`의 `Model` 클래스가 zod 스키마(`schemas.js`)의 필드 목록을 읽어 `Object.defineProperty`로 `Post.post_id`처럼 컬럼을 가리키는 getter를 동적으로 생성합니다. 서브쿼리를 "가상 테이블"처럼 다루는 `FavoriteCount`, 같은 테이블을 다른 역할로 재사용하는 별칭 패턴(`Writer`)도 이 위에 구현되어 있습니다.
- **선택 이유**: 표준 ORM(Sequelize/TypeORM/Prisma) 대신 Knex 위에 자체 경량 추상화를 쌓아, 검증 스키마 하나만 수정하면 쿼리 컬럼 참조도 자동으로 따라가도록 설계했습니다. 소규모 팀에서 별도 ORM 도입 없이 스키마-쿼리 일관성을 확보하기 위한 트레이드오프로 판단됩니다.

---

## 알려진 이슈 및 개선 방향

현재 구조에서 확인된 한계와 향후 보완 방향을 솔직하게 정리합니다.

| 이슈 | 현재 상태 | 개선 방향 |
|---|---|---|
| Socket.IO 인증 미적용 | `registerUser` 이벤트가 클라이언트가 보낸 `username`을 검증 없이 그대로 신뢰함 — REST의 JWT 인증 체계와 분리되어 있음 | 소켓 연결/이벤트 단계에 JWT 검증 미들웨어를 연동해 REST와 동일한 인증 수준을 적용할 예정 |
| JWT `issuer`/`audience` 미검증 | 토큰 발급 시 `issuer`/`audience`를 명시하지만, `jwt.verify()` 호출 시 해당 옵션을 전달하지 않아 서명(`secretKey`)과 만료 시간만 검증됨 | 운영 환경 배포 전 `jwt.verify()`에 `issuer`/`audience`/`algorithms` 옵션을 명시해 발급 의도를 그대로 강제하도록 보완 필요 |
| `chat`/`message` REST 엔드포인트 미사용 | 두 라우트는 인증이 적용되어 있지 않고, `message`는 라우트 파라미터 누락 및 존재하지 않는 컬럼 참조로 정상 동작하지 않음 — 실사용 채팅 기능은 Socket.IO로 대체됨 | 레거시 REST 엔드포인트는 정리하거나, 유지할 경우 Socket.IO와 동일한 인증·검증 체계로 재작성 |
| 트랜잭션 커버리지 부족 | 게시물 생성 시 `post` insert → 사진 연결 → AR 모델 연결이 트랜잭션 없이 순차 쿼리로 실행되어 중간 실패 시 정합성이 깨질 수 있음(현재 다건 insert 2곳만 `db.transaction` 사용) | 여러 테이블에 걸친 논리적 단위 작업까지 Knex 트랜잭션 적용 범위를 확대할 예정 |

> 위 항목들은 2인 팀 규모에서 기능 구현을 우선한 결과로 판단하고 있으며, 서비스 확장 단계에서 우선적으로 보완할 계획입니다.

---

## Cloud 인프라 구성

> 실제 저장소에서 확인된 것만 담백하게 기술합니다.

- **배포**: 단일 AWS EC2 인스턴스에 애플리케이션을 직접 설치·실행. `sync_to_ec2.sh` 스크립트로 `rsync -avz` + SSH(PEM 키 인증) 기반 수동 배포
- **DB**: 운영 환경은 AWS RDS(MySQL, 서울 리전)를 사용해 애플리케이션 서버와 DB를 분리
- **환경 분리**: `.env`의 `NODE_ENV` 값으로 개발/운영 DB 접속 정보를 전환
- **파일 저장**: 업로드된 이미지(`uploads/images/`)와 AR 모델(`uploads/models/`)은 EC2 로컬 디스크에 저장되며, `express.static`으로 정적 서빙

### 향후 개선 방향

프로젝트 규모(2인 개발)에 맞춰 서버 동작에 필요한 최소한의 구성만 되어 있으며, 아래는 서비스 확장 시 우선 고려할 수 있는 개선 방향입니다.

- 🐳 **Docker 컨테이너화** — 로컬/운영 환경 차이를 줄이고 인스턴스 교체·스케일아웃을 용이하게
- ⚙️ **CI/CD 파이프라인 도입**(GitHub Actions 등) — 수동 rsync 배포를 자동화된 파이프라인으로 전환
- ☁️ **파일 스토리지 분리**(S3 등) — 로컬 디스크 의존 구조를 외부 스토리지로 이전해 인스턴스 교체 시 파일 유실 위험 제거
- 🩺 **헬스체크/모니터링 도입** — `/health` 엔드포인트 및 외부 모니터링 연동
- 🔁 **프로세스 매니저 도입**(pm2 등) — 크래시/재부팅 시 자동 재시작 보장
- 🗄️ **Knex 마이그레이션 시스템 도입** — 통짜 스냅샷 방식 대신 변경 이력 추적이 가능한 표준 마이그레이션 체계로 전환

---

## 환경 설정 및 실행 방법

### 요구 사항

- Node.js (버전 고정 파일 없음 — LTS 버전 권장)
- MySQL

### 설치

```bash
git clone https://github.com/KIMSEOKWON00/ar-marketplace-backend.git
cd ar-marketplace-backend
npm install
```

### 환경 변수 설정

프로젝트 루트에 `.env` 파일을 생성하고 아래 변수들을 설정합니다.

```bash
# 실행 환경
NODE_ENV=development
PORT=3000

# 로깅
ENABLE_LOGGING=true
ENABLE_REQUEST_LOGGING=true
ENABLE_REQUEST_BODY_LOGGING=true
ENABLE_RESPONSE_LOGGING=true
ENABLE_RESPONSE_BODY_LOGGING=true

# DB 공통
DB_CLIENT=mysql2
DB_PORT=3306
DB_CONNECTION_LIMIT=10
DB_CONNECT_TIMEOUT=10000
DB_MIGRATIONS_TABLE=knex_migrations
DB_POOL_MIN=2
DB_POOL_MAX=10

# DB 개발 환경
DB_HOST_DEV=localhost
DB_USER_DEV=<your-mysql-user>
DB_PASSWORD_DEV=<your-mysql-password>
DB_NAME_DEV=ueong

# DB 운영 환경
DB_HOST_PROD=<your-rds-endpoint>
DB_USER_PROD=<your-rds-user>
DB_PASSWORD_PROD=<your-rds-password>
DB_NAME_PROD=ueong

# 인증
SECRET_KEY=<jwt-secret-key>
BCRYPT_SALT=10
BCRYPT_SALT_BASE=10
ACCESS_TOKEN_HEADER=authorization
```

> ⚠️ `.env`는 DB 접속정보와 JWT 시크릿 등 민감정보를 담고 있으므로 **버전관리(git)에 포함하지 않는 것을 권장합니다.**

### DB 스키마 준비

정식 마이그레이션 도구는 사용하지 않으며, `docs/ueong_database_backup/`의 스키마 덤프를 직접 임포트하거나 `docs/restore_database.sh`를 사용해 최신 스냅샷을 복원합니다.

### 서버 실행

```bash
node app.js
```

기본적으로 `http://localhost:3000`(또는 `.env`의 `PORT`)에서 REST API와 Socket.IO가 함께 기동됩니다.

### EC2 배포

```bash
./sync_to_ec2.sh
```

로컬 프로젝트 전체를 `rsync`로 EC2 인스턴스에 전송합니다(스크립트 내 경로는 환경에 맞게 직접 수정 필요).

---

## iOS 앱 레포

[ar-marketplace-ios](https://github.com/KIMSEOKWON00/ar-marketplace-ios)

---

## 팀원

| 이름 | GitHub |
|---|---|
| 김석원 | [@KIMSEOKWON00](https://github.com/KIMSEOKWON00) |
| 김동건 | [@dongkeonkim-dev](https://github.com/dongkeonkim-dev) |

> 프로젝트는 2인 풀스택 개발(iOS 앱 + Node.js 백엔드) 체제로 진행되었습니다.
