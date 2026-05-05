# ITDOTCOM

ITDOTCOM은 **노트북 성능 비교 + 커뮤니티(게시글/댓글)**를 결합한 JSP 기반 웹 프로젝트입니다.  
현재 저장소는 학습/프로토타입 성격이 강하며, 정적 HTML/CSS/JS와 일부 JSP-MySQL 연동 코드가 함께 존재합니다.

## 1) 프로젝트 개요

- **핵심 목표**
  - 노트북 스펙 비교 기능 제공
  - 커뮤니티 형태의 게시글/댓글 UI 제공
  - 회원가입/로그인 기반 사용자 진입 흐름 제공
- **기술 스택(현재 코드 기준)**
  - Frontend: HTML, CSS, JavaScript, Tailwind CDN, jQuery
  - Backend(View): JSP
  - DB: MySQL
  - 서버: Apache Tomcat (war 배포 구조)

## 2) 디렉토리 구조

```text
src/main/webapp/
├─ compare.html / compare.js / compare.css   # 노트북 비교 페이지
├─ testhtml.html / test.css / script.js      # 커뮤니티형 UI/댓글 인터랙션
├─ login.html / login.jsp                    # 로그인 화면/처리
├─ join.html / join.jsp / join.js            # 회원가입 화면/처리
├─ comlist.html, write.html, qnaPage.html... # 게시판/작성 관련 페이지들
├─ userdata.sql                              # 사용자 테이블 생성 스크립트
└─ image/                                    # 비교 대상 노트북 이미지
```

## 3) 주요 기능 분석

### 3-1. 노트북 성능 비교

`compare.js`에서 사전 정의된 노트북 DB(`computerDatabase`)를 기반으로 동작합니다.

- 자동완성 추천(`showSuggestions`)
- 2개 기기 선택 후 비교(`compareComputers`)
- 결과를 표 형태로 렌더링(`displayComparisonResult`)
- 해당 기기 이미지 출력(`displayImages`)

> 현재는 **하드코딩된 샘플 데이터** 기반이며, DB/API 연동은 아직 없습니다.

### 3-2. 커뮤니티/댓글 인터랙션

`script.js`에서 댓글/답글/추천/삭제 동작을 프론트에서 처리합니다.

- 댓글 등록 및 유효성 검사
- 답글 폼 토글/등록
- 추천 1회 제한(`data-liked` 기반)
- 댓글/답글 삭제
- 사용자명 클릭 시 팝업 토글

> 댓글 데이터는 브라우저 메모리 상에서 처리되어, 새로고침 시 유지되지 않습니다.

### 3-3. 회원가입/로그인

- `login.html` → `login.jsp`로 전송
- `join.html` → `join.jsp`로 전송
- JSP에서 MySQL DB에 직접 SQL 실행

`userdata.sql` 기준 사용자 테이블은 아래와 같습니다.

- `userid` (PK)
- `userpw`
- `username`
- `usertel`

## 4) 실행 방법 (로컬)

### 4-1. 사전 준비

1. JDK 설치
2. Tomcat 설치
3. MySQL 실행
4. DB/테이블 생성

```sql
CREATE DATABASE userdata;
USE userdata;
-- 이후 src/main/webapp/userdata.sql 실행
```

### 4-2. JDBC 설정 확인

현재 JSP에 DB 접속 정보가 하드코딩되어 있습니다.

- URL: `jdbc:mysql://localhost:3306/userdata`
- USER: `root`
- PASSWORD: `0000`

환경에 맞게 `login.jsp`, `join.jsp` 내 값을 수정하세요.

### 4-3. Tomcat 배포

- 프로젝트를 war 또는 exploded 형태로 Tomcat `webapps`에 배포
- 서버 실행 후 브라우저에서 접근
  - 예: `http://localhost:8080/ITDOTCOM/main.jsp`

## 5) 현재 코드의 한계 및 개선 포인트

### 보안

- **SQL Injection 취약 가능성**: 문자열 결합 방식 SQL 작성
- 비밀번호 평문 저장
- DB 계정/비밀번호 소스코드 하드코딩

### 아키텍처

- JSP에 DB 로직이 직접 포함(관심사 분리 부족)
- 정적 페이지와 동적 페이지가 혼재
- 상태관리(session/localStorage/sessionStorage) 전략 일관성 부족

### 품질

- `join.js`에 오타(`idKey` vs `idkey`) 등 실행 오류 가능 코드 존재
- 일부 페이지/파일 간 네이밍·역할 불명확
- 테스트 코드 및 빌드 자동화 부재

## 6) 권장 리팩토링 순서

1. SQL 문자열 결합 제거 → `PreparedStatement` 파라미터 바인딩 적용
2. 비밀번호 해시(BCrypt 등) 저장
3. DB 접속정보를 환경변수/설정 파일로 분리
4. MVC 패턴(서블릿/서비스/DAO)로 로직 분리
5. 댓글/게시글을 실제 DB 스키마와 API로 이전
6. 입력값 검증 및 예외 처리 체계화

## 7) 참고 파일

- 메인 진입: `src/main/webapp/main.jsp`
- 로그인 처리: `src/main/webapp/login.jsp`
- 회원가입 처리: `src/main/webapp/join.jsp`
- 비교 기능: `src/main/webapp/compare.js`
- 댓글 기능: `src/main/webapp/script.js`
- DB 스키마: `src/main/webapp/userdata.sql`

---

원하시면 다음 단계로, 제가 바로
1) SQL Injection 방어 코드 적용,
2) 로그인/회원가입 로직 분리(DAO),
3) README에 ERD/화면흐름도 추가
까지 이어서 정리해드릴게요.
