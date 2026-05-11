# 📝 J2S2 API: Personal Insight & Diary Platform

> **개인 포트폴리오용 정리** > **프로젝트 기간**: 2026.01.20 ~ 2026.01.23 (4일)  
> **주요 역할**: 백엔드 인프라 구축, 인증 시스템 구현, 비동기 데이터 스크래핑 엔진 개발

---

## 🌟 프로젝트 개요
**J2S2**는 사용자의 일상을 기록하는 **일기**, 자아 성찰을 돕는 **질문**, 그리고 외부 실시간 수집 기반의 **명언** 서비스를 제공하는 심리 케어 플랫폼입니다. 짧은 기간 내에 비동기 아키텍처를 활용하여 안정적인 데이터 수집 및 인증 시스템을 구축하는 것을 목표로 했습니다.

---

## 🛠 담당 기술 스택 및 역할

### 1. 인프라 및 데이터베이스 (Infrastructure & DB)
- **AWS 배포**: EC2 인프라를 구축하고 Nginx Reverse Proxy를 설정을 통한 서비스 배포.
- **DB 설계**: Tortoise-ORM을 활용한 비동기 DB 스키마 설계 및 MySQL 연동.
- **Migration**: Aerich를 이용한 데이터베이스 마이그레이션 관리.

### 2. 보안 및 인증 (Security & Auth)
- **JWT 인증**: `python-jose`를 활용한 JSON Web Token 발행 및 유효성 검증 로직 구현.
- **비밀번호 암호화**: `Passlib`의 `PBKDF2-SHA256` 알고리즘을 적용하여 보안성 강화.
- **접근 제어**: `OAuth2PasswordBearer`와 Dependency Injection을 통한 중앙 집중식 유저 권한 관리.

### 3. 데이터 서비스 (Data Service & Scraping)
- **비동기 스크래핑**: `HTTPX`와 `BeautifulSoup4`를 이용해 외부 사이트(`saramro.com`)에서 대량의 명언 데이터를 실시간으로 수집하는 엔진 개발.
- **지능형 저장 로직**: 수집된 데이터를 DB에 `get_or_create` 방식으로 캐싱하여 API 응답 속도 최적화.
- **랜덤 조회 API**: DB의 `offset` 연산을 활용해 효율적인 랜덤 명언/질문 추출 로직 구현.

### 4. 북마크 시스템 (Bookmark System)
- **스마트 토글**: 명언 및 일기에 대해 '등록/해제'를 하나의 엔드포인트에서 처리하는 토글 방식 로직 설계.
- **성능 최적화**: `prefetch_related`를 적용하여 연관 관계 데이터를 단일 쿼리로 조회(N+1 문제 해결).

---

## 🏗 아키텍처 구조

본 프로젝트는 비동기 처리에 최적화된 **Layered Architecture**를 채택했습니다.

```text
app/
├── core/         # JWT 설정, 암호화 보안, 환경 변수 (담당)
├── db/           # DB 연결 및 초기화 (담당)
├── models/       # DB 테이블 스키마 정의 (담당)
├── repositories/ # 데이터 접근 계층 (담당 - 북마크, 랜덤 조회 등)
├── routers/      # API 엔드포인트 정의
├── schemas/      # Pydantic 데이터 모델 (DTO)
├── services/     # 명언 스크래핑 비즈니스 레이어 (담당)
└── templates/    # HTML 템플릿
