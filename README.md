# 📝 J2S2 API: Personal Insight & Diary Platform

> **"오늘을 기록하고, 내일을 질문하며, 명언으로 영감을 얻는 공간"**
> J2S2는 사용자의 일상을 기록하는 **일기**, 자아 성찰을 돕는 **질문**, 그리고 외부 실시간 수집 기반의 **명언** 서비스를 제공하는 심리 케어 및 기록 플랫폼입니다.

---

## 🏗 아키텍처 및 기술 스택

본 프로젝트는 유지보수성과 확장성을 위해 **Layered Architecture(계층화 아키텍처)**를 채택하여 비동기로 동작합니다.



- **Framework**: `FastAPI` (v0.100+)
- **Database**: `MySQL 8.0+`
- **ORM**: `Tortoise-ORM` (Async ORM)
- **Authentication**: `JWT (JSON Web Token)` & `OAuth2 Password Flow`
- **Security**: `Passlib (PBKDF2-SHA256)` 암호 해싱
- **Validation**: `Pydantic v2` 기반 데이터 검증
- **Scraping**: `HTTPX` & `BeautifulSoup4` (비동기 크롤링)

---

## 📊 데이터베이스 설계 (ERD)

데이터 간의 관계를 정의하여 효율적인 데이터 관리를 수행합니다.

- **User**: 사용자 인증 및 기본 정보 관리
- **Diary**: 사용자의 개인 기록 (User : Diary = 1 : N)
- **Quote**: 외부 사이트에서 수집된 명언 저장소
- **Question**: 자아 성찰용 질문 데이터셋
- **Bookmark**: 명언 및 일기에 대한 북마크 (토글 방식의 N:1 관계)



---

## ✨ 핵심 기능 상세

### 1. 🔐 보안 및 인증 시스템 (`app/core`)
- **중앙 집중식 설정**: `Pydantic Settings`를 사용하여 환경 변수(`.env`)를 안전하게 관리합니다.
- **JWT 기반 권한 제어**: 모든 주요 API는 `get_current_user` 의존성 주입을 통해 인증된 사용자만 자신의 데이터에 접근하도록 보호됩니다.

### 2. 📖 지능형 기록 및 성찰 (`app/repositories`)
- **검색 및 정렬**: 일기 제목에 대한 부분 일치 검색(`icontains`)과 최신순 정렬 기능을 지원합니다.
- **무작위 질문 추출**: `QuestionRepository`를 통해 DB에 저장된 질문 중 하나를 무작위로 호출하여 사용자에게 사색의 기회를 제공합니다.

### 3. 💡 비동기 명언 스크래핑 (`app/services`)
- **실시간 데이터 수집**: `saramro.com`의 수천 개 페이지 중 하나를 랜덤으로 선택하여 명언을 추출합니다.
- **자동 DB 캐싱**: 스크래핑된 명언은 `get_or_create` 로직을 통해 중복 없이 DB에 저장되어 서비스 속도를 높입니다.

### 4. 🔖 스마트 북마크 시스템
- **통합 토글(Toggle) API**: 추가와 삭제를 하나의 엔드포인트에서 처리하여 클라이언트 로직을 단순화했습니다.
- **조인 최적화**: `prefetch_related`를 사용해 북마크 조회 시 발생하는 N+1 문제를 방지하고 쿼리 성능을 최적화했습니다.

---

## 📂 프로젝트 구조

```text
app/
├── core/         # JWT 설정, 암호화 보안, 환경 변수
├── db/           # DB 연결 설정 및 초기화 (Tortoise-ORM)
├── models/       # DB 테이블 스키마 정의
├── repositories/ # 데이터 접근 계층 (Pure DB CRUD Logic)
├── routers/      # API 엔드포인트 정의 (FastAPI Routers)
├── schemas/      # Pydantic 데이터 모델 (DTO)
├── services/     # 명언 스크래핑 및 외부 연동 비즈니스 레이어
└── templates/    # Jinja2 기반 HTML 템플릿
