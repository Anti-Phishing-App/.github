# Software Design Specification (SDS)
## 피싱 탐지 시스템 (Phishing Detection System)

### Team Members
| Student ID | Name   |
|------------|--------|
| 22012286   | 김민서 |
| 22010958   | 김성현 |
| 22112144   | 류효정 |
| 22012124   | 박윤호 |
| 22212125   | 윤찬익 |
| 22112102   | 최현수 |

## Content
1. [Introduction](#1-introduction)
2. [Use Case Analysis](#2-use-case-analysis)
3. [Class Diagrams](#3-class-diagrams)
4. [Sequence Diagrams](#4-sequence-diagrams)
5. [State Machine Diagrams](#5-state-machine-diagrams)
6. [User Interface Design](#6-user-interface-design)
7. [Implementation Requirements](#7-implementation-requirements)
8. [Glossary](#8-glossary)
9. [References](#9-references)

## Authors for each section
| Section | Author(s) |
|----------|------------|
| Introduction | 김민서 |
| Use Case Analysis | 윤찬익 |
| Class Diagrams | 박윤호, 김민서 |
| Sequence Diagrams | 김성현 |
| State Machine Diagrams | 최현수 |
| User Interface Design | 류효정 |
| Implementation Requirements | 최현수 |
| Glossary | 김성현 |
| References | 김성현 |

## 1. Introduction
해당 문서는 멀티모델 피싱 탐지 애플리케이션(Anti-Phishing APP)의 Software Design Specification(SDS) 문서이다. 스마트폰과 인터넷이 널리 쓰이면서 피싱 수법이 점점 똑똑해지고, 특히 디지털에 익숙하지 않은 사람들이 피해를 보기 쉬운 상황을 배경으로 하고 있다. 개발 시스템은 크게 Android 클라이언트와 AI 분석 서버(FastAPI)로 이루어지며, 사용자가 통화 음성(실시간), 문자(SMS) 내용, 문서·이미지 같은 의심 데이터를 올리면 서버가 바로 분석해 위험도와 그 근거를 보여 준다. 서버는 OCR(Clova로 텍스트 추출), 키워드 점수(금융·수사기관·압박 표현), KoBERT 기반 문맥 분류, 직인 탐지(Stamp), 레이아웃 지표, URL 검사(도메인/단축링크 등) 같은 모듈을 층별로 두고, Risk Aggregator가 각각의 결과를 가중해 최종 점수를 산출한다. 앱은 입력 화면과 서버 통신을 맡고, 알림·진동·하이라이트로 즉각 경고해 피해를 미리 막을 수 있게 돕는다.

## 2. Use Case Analysis
이번 장은 Use case diagram과 Use case description을 제공한다. Diagram에 관한
고려 사항은 다음과 같다.

● Use case diagram은 description에 대한 부가적인 결과물이다. 이 문서를 읽는 독자는 use case diagram보다 use case description에서 충분한 정보를 얻을 수 있으니 꼭 참고하길 바란다.<br>
● 본 프로젝트 대부분의 기능들은 로그인을 하지 않으면 기능을 이용할 수 없다.(회원가입 기능은 제외)<br>
● 본 프로젝트의 Use case는 총 9가지로, 각각의 Use case는 다음과 같은 기능들을 포괄한다.<br>

-회원가입: 사용자가 아이디, 비밀번호 등 기본 정보를 입력하여 새로운 계정을 생성하는 기능.<br>

-소셜 회원가입: 소셜 로그인 시 가입 정보가 없을 경우, 외부 서비스 프로필을 기반으로 간편하게 신규 계정을 생성하는 기능.<br>

-로그인: 가입된 사용자가 아이디와 비밀번호로 시스템에 접근 권한을 인증받는 기능.<br>

-소셜 로그인: 사용자가 외부 인증 서비스 계정을 통해 간편하게 로그인하는 기능.<br>

-내 정보 조회: 로그인된 사용자가 자신의 가입 정보를 확인하고 수정하는 기능.<br>

-통화 녹음본 보이스피싱 탐지: 사용자가 업로드한 녹음 파일을 서버로 보내 텍스트로 변환하고 보이스피싱 위험도를 분석하는 기능.<br>

-실시간 통화 보이스피싱 탐지: 사용자의 마이크 입력을 실시간으로 스트리밍하여 텍스트로 변환하고 보이스피싱 위험도를 즉시 탐지하는 기능.<br>

-피싱 사이트 탐지: 메시지 등에 포함된 URL이 피싱 사이트인지 다층적인 방법(DB,ML, 규칙)으로 분석하는 기능.<br>

-문서 위조 분석: 사용자가 업로드한 문서 이미지의 직인, 레이아웃, 키워드 등을 분석하여 위조 위험도를 측정하는 기능.<br>

### 2.1 Use case Diagram
![Usecase Diagram](image/Usecase_diagram_antiphishing.png)
### 2.2 Use case Description
<h2>Use case #1 : 회원가입 (Sign Up)</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr><td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td></tr>
  <tr><td><strong>Summary</strong></td><td>사용자가 시스템 이용을 위해 개인 정보를 입력하고 계정을 생성하는 기능</td></tr>
  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.10.26</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>
  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      클라이언트와 서버 간 네트워크가 정상이어야 한다.<br>
      서버 프로세스가 구동 중이어야 한다.<br>
      DB 연결이 가능해야 한다.<br>
      사용자는 아직 해당 아이디로 가입되어 있지 않아야 한다.
    </td>
  </tr>
  <tr>
    <td><strong>Trigger</strong></td>
    <td>사용자가 회원가입 페이지에 접근하여 이름, 아이디, 비밀번호 등 필수 정보를 입력하고 ‘회원가입’ 버튼을 클릭할 때</td>
  </tr>
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>DB에 새 사용자 레코드가 생성되고(비밀번호는 해시로 저장), 서버는 201 Created와 함께 성공 메시지를 반환한다.</td>
  </tr>
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      검증 실패, 중복 아이디, DB 오류 등으로 인해 계정이 정상적으로 생성되지 않는다.<br>
      서버는 실패 원인에 따라 적절한 HTTP 상태코드(400, 409, 422, 500, 503 등)와<br>
      구체적인 오류 메시지(JSON)를 반환한다.<br>
      입력값 누락이나 비밀번호 정책 위반 시에는 클라이언트가 화면에서 즉시 오류 메시지를 표시하고, 중복 아이디나 DB 트랜잭션 실패의 경우 서버 로그에 상세 오류 내역(요청 시간, 원인 코드, 사용자 입력값 일부)을 기록한다.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr><td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Action</strong></td></tr>
  <tr><td align="center">S</td><td>사용자가 회원가입 화면을 연다.</td></tr>
  <tr><td align="center">1</td><td>사용자: 아이디/비밀번호/닉네임 등 필수값 입력 후 제출한다.</td></tr>
  <tr><td align="center">2</td><td>서버: DB에서 아이디 중복 여부 조회.</td></tr>
  <tr><td align="center">3</td><td>서버: 중복이 아니면 비밀번호 해시 생성(bcrypt) 및 사용자 레코드 생성 트랜잭션 시작.</td></tr>
  <tr><td align="center">4</td><td>서버: 사용자 정보를 DB에 커밋 후 성공 로그 기록.</td></tr>
  <tr><td align="center">5</td><td>서버→클라이언트: 201 Created + {"message":"registered"} 반환.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr><td colspan="2"><strong>EXTENSION SCENARIOS</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Branching Action / Result (HTTP)</strong></td></tr>
  <tr><td align="center">2</td><td>2a. 사용자가 필수 입력값을 누락하거나 형식이 잘못된 경우, 서버는 422 Unprocessable Entity를 반환</td></tr>
  <tr><td align="center">2</td><td>2b. 비밀번호가 정책(8자 이상, 문자+숫자 조합)을 만족하지 않으면 400 Bad Request를 반환</td></tr>
  <tr><td align="center">3</td><td>3a. 이미 존재하는 아이디로 가입을 시도하면 서버는 409 Conflict를 반환</td></tr>
  <tr><td align="center">4</td><td>4a. 비밀번호 해시 생성 과정에서 오류가 발생하면 서버는 500 Internal Server Error를 반환</td></tr>
  <tr><td align="center">5</td><td>5a. 데이터베이스 연결이 끊기거나 트랜잭션이 실패하면 서버는 500 Internal Server Error 또는 503 Service Unavailable을 반환</td></tr>
  <tr><td align="center">5</td><td>5b. DB 응답이 지연되면 서버는 504 Gateway Timeout을 반환</td></tr>

  <!-- RELATED INFORMATION -->
  <tr><td colspan="2"><strong>RELATED INFORMATION</strong></td></tr>
  <tr><td><strong>Performance</strong></td><td>≤ 2s</td></tr>
  <tr><td><strong>Frequency</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>&lt;Concurrency&gt;</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>Due Date</strong></td><td></td></tr>
</table>



<h2>Use case #2 : 소셜 회원가입 (Sign-Up with OAuth)</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr><td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td></tr>
  <tr>
    <td><strong>Summary</strong></td>
    <td>
      사용자가 Google, Naver, Kakao 등 외부 소셜 계정(OAuth 2.0)을 이용하여 처음으로 시스템에 가입하는 기능이다.<br>
      서버는 Provider로부터 전달받은 Access Token과 사용자 정보를 검증한 뒤, DB에 신규 사용자 정보를 저장하고 JWT를 발급한다.
    </td>
  </tr>
  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.11.05</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>
  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1.클라이언트와 서버 간 네트워크가 정상이어야 한다.<br>
      2.FastAPI 서버 및 DB 연결이 정상 상태여야 한다.<br>
      3.사용자는 Google, Naver, Kakao 등 OAuth Provider 계정을 보유하고 있어야 한다.<br>
      4.해당 소셜 계정은 아직 시스템 DB에 등록되지 않은 상태여야 한다.<br>
      5.OAuth Provider의 인증 서버와 Redirect URI 설정이 완료되어 있어야 한다.
    </td>
  </tr>
  <tr>
    <td><strong>Trigger</strong></td>
    <td>사용자가 로그인 화면에서 “Google로 로그인” 등 소셜 로그인을 시도했으나, 해당 소셜 계정이 시스템 DB에 존재하지 않아 회원가입 절차로 분기되는 경우. 또는 사용자가 직접 “소셜 계정으로 회원가입” 버튼을 눌러 OAuth 인증을 시작하는 경우.</td>
  </tr>
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      1.서버는 Provider로부터 받은 사용자 정보를 바탕으로 신규 사용자 레코드를 생성한다.<br>
      2.회원정보가 DB에 성공적으로 저장되면, JWT Access Token을 발급하고 201 Created와 함께 응답을 반환한다.<br>
      3.가입 직후 사용자는 자동으로 로그인된 상태가 된다.
    </td>
  </tr>
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      Access Token 검증 실패, 사용자 정보 미수신, 중복 가입, DB 오류 등의 이유로 회원 생성이 완료되지 않으면 서버는 사용자 레코드를 저장하지 않고, 상황에 맞는 HTTP 상태코드 및 오류 메시지(JSON)를 반환한다.<br>
      오류 내용은 서버 로그에 기록된다.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr><td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Action</strong></td></tr>
  <tr><td align="center">S</td><td>사용자가 로그인 또는 회원가입 화면에서 “소셜 계정으로 가입” 버튼을 클릭한다.</td></tr>
  <tr><td align="center">1</td><td>클라이언트는 사용자가 선택한 Provider(Google, Naver, Kakao 등)의 인증 페이지로 리디렉션한다.</td></tr>
  <tr><td align="center">2</td><td>사용자가 해당 Provider 로그인 페이지에서 인증을 완료하고, 접근 권한을 허용한다.</td></tr>
  <tr><td align="center">3</td><td>Provider는 인가 코드(Authorization Code) 또는 Access Token을 시스템 서버의 Redirect URI로 전달한다.</td></tr>
  <tr><td align="center">4</td><td>서버는 전달받은 코드로 Provider API를 호출하여 Access Token을 교환하고 사용자 정보를 요청한다. (이메일, 이름, 프로필 이미지 등)</td></tr>
  <tr><td align="center">5</td><td>서버는 Provider가 반환한 사용자 정보의 무결성을 검증하고, DB에서 동일한 이메일 또는 Provider ID가 이미 존재하는지 확인한다.</td></tr>
  <tr><td align="center">6</td><td>DB에 해당 사용자가 존재하지 않으면, 서버는 새 사용자 레코드를 생성한다.<br>비밀번호는 소셜 계정 기반이므로 별도의 입력 없이 Provider ID 또는 고유 키로 대체 저장한다.</td></tr>
  <tr><td align="center">7</td><td>서버는 신규 사용자 정보를 DB에 커밋하고, 성공 로그를 기록한다.</td></tr>
  <tr><td align="center">8</td><td>서버는 신규 가입 사용자에게 JWT Access Token을 발급하여 응답 본문에 포함하고, 201 Created 상태로 반환한다.</td></tr>
  <tr><td align="center">9</td><td>클라이언트는 토큰을 저장하고, 사용자를 자동 로그인 상태로 전환한다.<br>사용자는 가입 직후 보호된 API를 이용할 수 있다.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr><td colspan="2"><strong>EXTENSION SCENARIOS</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Branching Action / Result (HTTP)</strong></td></tr>
  <tr><td align="center">2</td><td>2a.사용자가 Provider 로그인 중 인증을 취소하거나 창을 닫으면,<br>시스템은 회원가입 절차를 중단하고 “소셜 인증이 취소되었습니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">2</td><td>3a.서버가 Redirect URI에서 인가 코드를 받지 못하거나 잘못된 코드가 전달된 경우,<br>400 Bad Request와 “잘못된 인증 요청입니다.” 메시지를 반환한다.</td></tr>
  <tr><td align="center">4</td><td>4a.Provider API 요청 중 네트워크 오류가 발생하거나 응답이 지연될 경우,<br>서버는 504 Gateway Timeout을 반환하고 “외부 인증 서버 응답 지연” 메시지를 표시한다.</td></tr>
  <tr><td align="center">4</td><td>4b.Access Token 요청이 실패하거나 Provider로부터 “invalid_client” 오류가 발생한 경우,<br>서버는 401 Unauthorized를 반환하고 “소셜 인증에 실패했습니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">5</td><td>5a. Provider가 사용자 정보를 반환하지 않거나 필수 항목(이메일 등)이 누락된 경우,<br>서버는 400 Bad Request를 반환하고 “필수 사용자 정보가 없습니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">5</td><td>5b.DB 조회 결과 동일한 이메일 또는 Provider ID가 이미 존재하면,<br>서버는 409 Conflict를 반환하고 “이미 등록된 소셜 계정입니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">6</td><td>6a. DB 트랜잭션 중 오류가 발생하거나 연결이 끊기면,<br>서버는 500 Internal Server Error 또는 503 Service Unavailable을 반환한다.</td></tr>
  <tr><td align="center">7</td><td>7a.서버 로그 기록 또는 커밋 과정에서 예외가 발생하면,<br>회원 정보는 저장되었더라도 200 OK 대신 206 Partial Content로 응답하고 로그 경고를 남긴다.</td></tr>
  <tr><td align="center">8</td><td>8a. JWT 생성 중 내부 오류가 발생하면,<br>서버는 500 Internal Server Error를 반환하고 “토큰 발급 중 오류가 발생했습니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">9</td><td>9a.클라이언트가 JWT를 저장하는 과정에서 오류가 발생하면,<br>앱은 “로그인 상태를 유지할 수 없습니다.” 메시지를 표시하고 재로그인을 요청한다.</td></tr>

  <!-- RELATED INFORMATION -->
  <tr><td colspan="2"><strong>RELATED INFORMATION</strong></td></tr>
  <tr><td><strong>Performance</strong></td><td>≤ 2s</td></tr>
  <tr><td><strong>Frequency</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>&lt;Concurrency&gt;</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>Due Date</strong></td><td></td></tr>
</table>


<h2>Use case #3 : 로그인 (Login)</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr><td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td></tr>
  <tr><td><strong>Summary</strong></td><td>등록된 계정으로 인증하면 서버가 자격 증명 검증 → JWT 발급을 수행하고, 성공 시 보호된 API 접근을 허용한다.</td></tr>
  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.10.26</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>
  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1. 사용자는 회원가입을 완료해야 한다.<br>
      2. 시스템 서버와 데이터베이스가 정상적으로 동작 중이어야 한다.
    </td>
  </tr>
  <tr>
    <td><strong>Trigger</strong></td>
    <td>사용자가 로그인 화면에서 아이디/비밀번호를 입력하고 로그인을 클릭하거나, 클라이언트가 POST /auth/login을 호출</td>
  </tr>
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>서버가 자격 증명을 검증하고 JWT 액세스 토큰을 발급하여 200 OK와 함께 반환</td>
  </tr>
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>인증에 실패하거나 내부 오류가 발생하면 JWT 토큰은 발급되지 않으며 세션/쿠키도 생성되지 않는다. 서버는 실패 유형에 따라 적절한 HTTP 상태코드(400/401/422/429/423/500/503 등)와 구체적 오류 메시지(JSON)를 반환하고, 클라이언트는 오류 원인을 화면에 표시하여 재시도·비밀번호 재설정·추가 인증 유도 등 대응을 안내한다.</td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr><td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Action</strong></td></tr>
  <tr><td align="center">S</td><td>사용자가 로그인 화면을 연다.</td></tr>
  <tr><td align="center">1</td><td>사용자: 아이디/비밀번호 입력 후 제출.</td></tr>
  <tr><td align="center">2</td><td>서버: 입력 검증(필수값/포맷) 수행.</td></tr>
  <tr><td align="center">3</td><td>서버: DB에서 사용자 조회, 존재 여부 확인.</td></tr>
  <tr><td align="center">4</td><td>서버: 저장된 비밀번호 해시 검증(bcrypt) — 일치하지 않으면 실패.</td></tr>
  <tr><td align="center">5</td><td>서버: JWT 생성</td></tr>
  <tr><td align="center">6</td><td>서버→클라이언트: 200 OK + {access_token, token_type} 반환 & 성공 로그.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr><td colspan="2"><strong>EXTENSION SCENARIOS</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Branching Action / Result (HTTP)</strong></td></tr>
  <tr><td align="center">2</td><td>2a. 사용자가 아이디나 비밀번호를 입력하지 않으면 서버는 422 Unprocessable Entity를 반환하고 "아이디와 비밀번호를 입력해주세요." 메시지를 표시</td></tr>
  <tr><td align="center">3</td><td>3a. 입력한 아이디가 존재하지 않으면 서버는 401 Unauthorized를 반환하고 "아이디 또는 비밀번호가 올바르지 않습니다." 메시지를 표시</td></tr>
  <tr><td align="center">4</td><td>4a.비밀번호가 일치하지 않으면 서버는 401 Unauthorized를 반환하고 동일하게 "아이디 또는 비밀번호가 올바르지 않습니다." 메시지를 표시</td></tr>
  <tr><td align="center">5</td><td>5a. JWT 토큰 생성 중 내부 오류나 키 로드 실패가 발생하면 서버는 500 Internal Server Error를 반환하고 "서버 오류로 로그인에 실패했습니다." 메시지를 표시</td></tr>
  <tr><td align="center">5</td><td>5b. 데이터베이스 연결 또는 쿼리 오류가 발생하면 서버는 500 Internal Server Error 또는 503 Service Unavailable을 반환하고 "일시적 서버 오류입니다. 잠시 후 다시 시도해주세요." 메시지를 표시</td></tr>
  <tr><td align="center">6</td><td>6a. 너무 많은 로그인 시도가 감지되면 서버는 429 Too Many Requests를 반환하고 "시도가 많습니다. 잠시 후 다시 시도해주세요." 메시지를 표시</td></tr>
  <tr><td align="center">6</td><td>6b. 연속된 실패로 계정이 잠금 상태이면 서버는 423 Locked를 반환하고 "보안을 위해 계정이 잠겼습니다. 비밀번호를 재설정해주세요." 메시지를 표시</td></tr>

  <!-- RELATED INFORMATION -->
  <tr><td colspan="2"><strong>RELATED INFORMATION</strong></td></tr>
  <tr><td><strong>Performance</strong></td><td>≤ 2s</td></tr>
  <tr><td><strong>Frequency</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>&lt;Concurrency&gt;</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>Due Date</strong></td><td></td></tr>
</table>


<h2>Use case #4 : 소셜 로그인 (Social Login - OAuth 2.0)</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr><td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td></tr>
  <tr>
    <td><strong>Summary</strong></td>
    <td>
      사용자가 Google, Naver, Kakao 등 외부 OAuth 제공자를 통해 로그인 요청을 하면,서버는 Access Token을 검증하여 사용자 정보를 가져오고,등록된 계정일 경우 JWT를 발급해 인증 세션을 생성한다.<br>
      신규 사용자일 경우 “소셜 회원가입” 단계로 분기한다.
    </td>
  </tr>
  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.11.05</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>
  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1.사용자는 이미 해당 소셜 계정(Google, Naver, Kakao 등)을 보유하고 있어야 한다.<br>
      2.소셜 로그인 공급자(OAuth Provider)의 인증 서버가 정상 작동 중이어야 한다.<br>
      3.서버는 OAuth Redirect URI로 등록되어 있어야 한다.<br>
      4.데이터베이스 연결 및 JWT 서명 키가 유효해야 한다.
    </td>
  </tr>
  <tr>
    <td><strong>Trigger</strong></td>
    <td>사용자가 로그인 화면에서 소셜 로그인 버튼을 클릭한다. 또는 클라이언트가 소셜 로그인 엔드포인트를 호출하여 OAuth 인증 절차를 시작한다.</td>
  </tr>
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      서버가 외부 OAuth Provider로부터 전달받은 Access Token을 검증하고,<br>
      해당 계정이 시스템 DB에 존재하면 JWT Access Token을 발급하여 200 OK와 함께 반환한다.<br>
      사용자는 즉시 로그인된 상태로 보호된 API를 사용할 수 있다.
    </td>
  </tr>
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      Access Token 검증 실패, 등록되지 않은 사용자, 네트워크 장애, 또는 Provider 응답 오류가 발생하면<br>
      JWT가 발급되지 않는다.<br>
      서버는 상황에 따라 적절한 HTTP 상태 코드(401, 400, 500 등)와 JSON 형태의 오류 메시지를 반환한다.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr><td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Action</strong></td></tr>
  <tr><td align="center">S</td><td>사용자가 “Google로 로그인” 또는 “Naver로 로그인” 등의 버튼을 클릭한다.</td></tr>
  <tr><td align="center">1</td><td>클라이언트는 선택된 Provider(Google, Naver 등)의 OAuth 인증 URL로 리디렉션한다.</td></tr>
  <tr><td align="center">2</td><td>클라이언트는 선택된 Provider(Google, Naver 등)의 OAuth 인증 URL로 리디렉션한다.</td></tr>
  <tr><td align="center">3</td><td>사용자는 Provider 로그인 페이지에서 계정 정보를 입력하고, 시스템 접근 권한을 승인한다.</td></tr>
  <tr><td align="center">4</td><td>인증이 완료되면 Provider는 인가 코드(Authorization Code) 또는 Access Token을 서버의 Redirect URI로 전달한다.</td></tr>
  <tr><td align="center">5</td><td>서버는 전달받은 코드로 Provider API에 Access Token 요청을 보낸다.</td></tr>
  <tr><td align="center">6</td><td>Provider는 Access Token 및 사용자 프로필 정보를 서버로 반환한다.</td></tr>
  <tr><td align="center">7</td><td>서버는 Access Token의 유효성을 검증하고, Provider가 제공한 이메일 또는 고유 ID로 사용자 DB를 조회한다.</td></tr>
  <tr><td align="center">8</td><td>DB에 해당 사용자가 존재하면 로그인 성공으로 간주하고 JWT Access Token을 생성한다.</td></tr>
  <tr><td align="center">9</td><td>서버는 200 OK 응답과 함께 {access_token, token_type, user_info}를 반환한다.</td></tr>
  <tr><td align="center">10</td><td>클라이언트는 JWT를 저장하고 이후 API 호출 시 Authorization 헤더에 포함시켜 인증된 요청을 보낸다.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr><td colspan="2"><strong>EXTENSION SCENARIOS</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Branching Action / Result (HTTP)</strong></td></tr>
  <tr><td align="center">2</td><td>2a.소셜 로그인 버튼 클릭 후 Provider 인증 페이지로 이동하는 과정에서 네트워크 오류가 발생하면, 앱은 “연결이 원활하지 않습니다. 잠시 후 다시 시도해주세요.”라는 메시지를 표시하고 로그인 요청을 중단한다.</td></tr>
  <tr><td align="center">3</td><td>3a. 사용자가 Provider 로그인 화면에서 인증을 취소하거나 브라우저 창을 닫으면, 시스템은 “로그인이 취소되었습니다.” 메시지를 표시하고 로그인 화면으로 되돌린다.</td></tr>
  <tr><td align="center">4</td><td>4a.Provider 인증이 완료되지 않아 서버가 Redirect URI에서 인가 코드를 받지 못할 경우, 서버는 400 Bad Request를 반환하고 “잘못된 요청입니다.” 메시지를 사용자에게 전달한다.</td></tr>
  <tr><td align="center">5</td><td>5a.서버가 Provider에 Access Token을 요청하는 과정에서 인증 코드가 만료되었거나 client_secret이 올바르지 않은 경우, 서버는 401 Unauthorized를 반환하고 “소셜 인증에 실패했습니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">6</td><td>6a. Provider가 사용자 프로필 정보를 반환하지 않거나, 응답 포맷이 변경되어 데이터를 해석할 수 없는 경우, 서버는 502 Bad Gateway를 반환하고 “소셜 서버 응답 오류가 발생했습니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">7</td><td>7a. 서버가 Provider로부터 전달받은 Access Token을 검증하는 과정에서 위조 또는 만료가 감지되면, 서버는 401 Unauthorized를 반환하고 “유효하지 않은 토큰입니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">7</td><td>7b.Access Token은 유효하지만 해당 소셜 계정이 시스템 DB에 등록되어 있지 않은 경우, 서버는 404 Not Found를 반환하고 “등록되지 않은 사용자입니다.” 메시지를 표시하며, 회원가입 프로세스(소셜 회원가입 Use Case)로 분기한다.</td></tr>
  <tr><td align="center">8</td><td>8a. JWT 토큰 생성 중 내부 오류가 발생하거나 서명 키를 불러올 수 없는 경우, 서버는 500 Internal Server Error를 반환하고 “서버 오류로 로그인에 실패했습니다.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">9</td><td>9a. 데이터베이스 연결 오류 또는 로그인 기록 저장 실패가 발생하면, 서버는 503 Service Unavailable을 반환하고 “일시적인 서버 오류입니다. 잠시 후 다시 시도해주세요.” 메시지를 표시한다.</td></tr>
  <tr><td align="center">10</td><td>10a.클라이언트가 발급된 JWT를 저장하는 과정에서 오류가 발생하거나, 토큰이 손상·만료된 경우, 사용자는 다시 소셜 로그인을 시도하도록 안내받는다.</td></tr>

  <!-- RELATED INFORMATION -->
  <tr><td colspan="2"><strong>RELATED INFORMATION</strong></td></tr>
  <tr><td><strong>Performance</strong></td><td>≤ 2s</td></tr>
  <tr><td><strong>Frequency</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>&lt;Concurrency&gt;</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>Due Date</strong></td><td></td></tr>
</table>


<h2>Use case #5 : 사용자 정보 조회 (GET /user/me)</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr><td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td></tr>
  <tr>
    <td><strong>Summary</strong></td>
    <td>
      사용자가 로그인한 상태에서 서버에 자신의 정보를 요청하면,<br>
      서버는 JWT 토큰을 검증하여 사용자 ID를 추출하고,<br>
      해당 사용자의 프로필 정보를 DB에서 조회하여 JSON 형태로 반환한다.<br>
      이 기능은 인증이 필요한 API로, 로그인된 사용자만 이용할 수 있다.
    </td>
  </tr>
  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.10.26</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>

  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1.사용자는 로그인(JWT 인증) 상태여야 한다.<br>
      2.클라이언트는 요청 헤더에 Authorization: Bearer &lt;access_token&gt; 형식의 JWT 토큰을 포함해야 한다.<br>
      3.서버의 get_current_user 의존성은 토큰을 해독할 수 있어야 하며, 환경 변수가 정상적으로 설정되어 있어야 한다.<br>
      4.DB 연결이 정상적으로 유지되어 있어야 한다.<br>
      5.사용자 계정의 is_active 값이 True이어야 한다 .
    </td>
  </tr>
  <tr>
    <td><strong>Trigger</strong></td>
    <td>
      사용자가 앱 또는 웹의 “내 정보” 페이지에서 [내 정보 보기] 버튼을 클릭하면, 클라이언트는 GET /user/me 엔드포인트로 요청을 전송한다.<br>
      서버는 헤더의 JWT 토큰을 검증하고, 인증된 사용자 정보를 JSON으로 반환한다.
    </td>
  </tr>
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      1.서버는 Authorization 헤더의 JWT 토큰을 해독하여 user_id를 추출한다.<br>
      2.JWT 토큰이 유효하고 사용자가 존재하면,<br>
      DB에서 사용자 객체를 조회하고 직렬화하여 반환한다.<br>
      반환 데이터에는 username, email, full_name, phone, is_active, 3.created_at 등의 필드가 포함된다.<br>
      4. 200 OK 와 함께 JSON 응답이 반환된다.<br>
      5.서버 로그에는 "사용자 정보 조회 성공" 이벤트가 기록된다.
    </td>
  </tr>
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      1.JWT 토큰 누락 또는 만료 시 서버는 401 Unauthorized 와 "Not authenticated" 메시지를 반환한다.<br>
      2.토큰이 위조되었거나 디코딩 실패 시 서버는 401 Unauthorized 와 "Invalid token" 또는 "Invalid credentials" 메시지를 반환한다.<br>
      3.DB 연결 오류 발생 시 서버는 500 Internal Server Error 와 "데이터베이스 연결 오류" 메시지를 반환한다.<br>
      4.비활성화된 계정일 경우 서버는 403 Forbidden 과 "계정이 비활성화되었습니다." 메시지를 반환한다.<br>
      5.환경 변수 누락 시 서버는 500 Internal Server Error 와 "서버 인증 설정 누락" 메시지를 반환한다.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr><td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Action</strong></td></tr>
  <tr><td align="center">S</td><td>사용자가 “내 정보 보기” 페이지를 연다.</td></tr>
  <tr><td align="center">1</td><td>클라이언트가 GET /user/me 엔드포인트로 요청을 전송한다.</td></tr>
  <tr><td align="center">2</td><td>서버는 Authorization 헤더의 JWT 토큰을 추출한다.</td></tr>
  <tr><td align="center">3</td><td>get_current_user 의존성이 토큰을 해독하고 사용자 ID를 식별한다.</td></tr>
  <tr><td align="center">4</td><td>서버는 DB 세션(Session)을 통해 해당 사용자 정보를 조회한다.</td></tr>
  <tr><td align="center">5</td><td>조회된 사용자 객체(User)를 UserResponse 스키마로 직렬화한다.</td></tr>
  <tr><td align="center">6</td><td>서버는 사용자 정보를 JSON 형태로 반환한다.</td></tr>
  <tr><td align="center">7</td><td>클라이언트는 UI에 사용자 정보를 표시한다.</td></tr>
  <tr><td align="center">8</td><td>서버 로그에 “사용자 정보 조회 성공” 이벤트가 기록된다.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr><td colspan="2"><strong>EXTENSION SCENARIOS</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Branching Action / Result (HTTP)</strong></td></tr>
  <tr><td align="center">2</td><td>2a. Authorization 헤더가 누락되면, 서버는 401 Unauthorized와 "Not authenticated" 메시지를 반환한다.</td></tr>
  <tr><td align="center">3</td><td>3a. JWT 토큰이 만료되면, 서버는 401 Unauthorized와 "Token expired" 메시지를 반환한다.</td></tr>
  <tr><td align="center">3</td><td>3b. JWT 토큰이 위조되었거나 클레임 파싱에 실패하면, 서버는 401 Unauthorized와 "Invalid token" 메시지를 반환한다.</td></tr>
  <tr><td align="center">4</td><td>4a. DB 연결이 실패하면, 서버는 500 Internal Server Error와 "데이터베이스 연결 오류" 메시지를 반환한다.</td></tr>
  <tr><td align="center">4</td><td>4b. 조회된 사용자가 비활성화(is_active=False) 상태이면, 서버는 403 Forbidden과 "계정이 비활성화되었습니다." 메시지를 반환한다.</td></tr>
  <tr><td align="center">5</td><td>5a. 사용자 정보 직렬화 중 예외가 발생하면, 서버는 500 Internal Server Error와 "응답 변환 오류" 메시지를 반환한다.</td></tr>

  <!-- RELATED INFORMATION -->
  <tr><td colspan="2"><strong>RELATED INFORMATION</strong></td></tr>
  <tr><td><strong>Performance</strong></td><td>≤ 2s</td></tr>
  <tr><td><strong>Frequency</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>&lt;Concurrency&gt;</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>Due Date</strong></td><td></td></tr>
</table>


<h2>Use case #6 : 회원 정보 수정 (Edit User Information)</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr>
    <td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td>
  </tr>

  <tr>
    <td><strong>Summary</strong></td>
    <td>
      사용자가 로그인한 상태에서 자신의 개인정보를 수정할 수 있는 기능이다.<br>
      서버는 수정 요청 시 JWT 토큰을 이용해 사용자를 인증하고, 
      유효성 검증 및 DB 갱신을 수행한다.<br>
      수정된 정보는 즉시 반영되어 클라이언트에 갱신된 프로필이 반환된다.
    </td>
  </tr>

  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.11.06</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>

  <!-- PRECONDITIONS -->
  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1. 사용자는 로그인 상태이며 유효한 JWT 액세스 토큰을 보유해야 한다.<br>
      2. 서버의 사용자 정보 테이블이 정상적으로 연결되어 있어야 한다.<br>
      3. 변경 요청 필드 값이 유효한 형식이어야 한다.<br>
      4. 중복 이메일, 닉네임 등 고유 제약 조건 위반이 없어야 한다.
    </td>
  </tr>

  <!-- TRIGGER -->
  <tr>
    <td><strong>Trigger</strong></td>
    <td>
      사용자가 “내 정보 수정” 페이지에서 항목을 변경 후 “저장” 버튼을 클릭할 때.<br>
      클라이언트는 <code>PUT /user/me</code> 요청을 전송한다.
    </td>
  </tr>

  <!-- SUCCESS POST CONDITION -->
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      1. 서버가 JWT 인증을 검증하고 요청 사용자를 식별한다.<br>
      2. 요청 필드의 유효성을 검사하고 DB에서 해당 사용자 정보를 갱신한다.<br>
      3. 수정 성공 시 200 OK 와 함께 갱신된 사용자 정보(JSON)를 반환한다.<br>
      4. 클라이언트는 즉시 최신 정보를 UI에 반영한다.
    </td>
  </tr>

  <!-- FAILED POST CONDITION -->
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      1. JWT 토큰이 없거나 만료된 경우 <code>401 Unauthorized</code> 반환.<br>
      2. 잘못된 필드 형식 또는 제약 조건 위반 시 <code>400 Bad Request</code> 반환.<br>
      3. DB 업데이트 중 오류 발생 시 <code>500 Internal Server Error</code> 반환.<br>
      4. 이메일/닉네임 중복일 경우 <code>409 Conflict</code> 반환.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr>
    <td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td>
  </tr>

  <tr>
    <td><strong>Step</strong></td>
    <td><strong>Action</strong></td>
  </tr>

  <tr><td align="center">S</td><td>사용자가 “내 정보 수정” 페이지를 연다.</td></tr>
  <tr><td align="center">1</td><td>사용자가 닉네임, 이메일, 비밀번호 등 수정할 항목을 입력한다.</td></tr>
  <tr><td align="center">2</td><td>클라이언트가 JWT를 포함해 <code>PUT /user/me</code> 요청을 전송한다.</td></tr>
  <tr><td align="center">3</td><td>서버가 JWT를 해석해 사용자 ID를 식별한다.</td></tr>
  <tr><td align="center">4</td><td>서버가 입력값 유효성을 검사하고 중복 여부를 확인한다.</td></tr>
  <tr><td align="center">5</td><td>검증 통과 시 DB의 사용자 레코드를 갱신한다.</td></tr>
  <tr><td align="center">6</td><td>DB 업데이트가 완료되면 200 OK와 함께 수정된 사용자 정보(JSON)를 반환한다.</td></tr>
  <tr><td align="center">7</td><td>클라이언트는 UI에 최신 프로필을 갱신하고 “수정 완료” 알림을 표시한다.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr>
    <td colspan="2"><strong>EXTENSION SCENARIOS</strong></td>
  </tr>

  <tr><td align="center">2a</td><td>입력값 형식이 유효하지 않으면 클라이언트에서 저장 버튼 비활성화 및 경고 표시.</td></tr>
  <tr><td align="center">4a</td><td>이메일 형식 오류 → <code>400 Bad Request</code> (“잘못된 이메일 형식”).</td></tr>
  <tr><td align="center">4b</td><td>중복 이메일/닉네임 존재 시 → <code>409 Conflict</code> (“이미 사용 중인 정보”).</td></tr>
  <tr><td align="center">5a</td><td>DB 업데이트 실패 → <code>500 Internal Server Error</code> (“정보 저장 실패”).</td></tr>
  <tr><td align="center">6a</td><td>토큰 만료 또는 변조 → <code>401 Unauthorized</code> (“인증 실패”).</td></tr>

  <!-- RELATED INFORMATION -->
  <tr>
    <td colspan="2"><strong>RELATED INFORMATION</strong></td>
  </tr>

  <tr>
    <td><strong>Performance</strong></td>
    <td>≤ 1s</td>
  </tr>

  <tr>
    <td><strong>Frequency</strong></td>
    <td>제한 없음</td>
  </tr>

  <tr>
    <td><strong>Concurrency</strong></td>
    <td>제한 없음 </td>
  </tr>
</table>

<h2>Use case #7 : 회원 탈퇴 (Delete My Account)</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr>
    <td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td>
  </tr>

  <tr>
    <td><strong>Summary</strong></td>
    <td>
      사용자가 로그인 상태에서 자신의 계정을 탈퇴(비활성화)할 수 있는 기능이다.<br>
      실제 데이터 삭제 대신 Soft Delete 방식을 사용하며, 
      서버는 해당 사용자의 <code>is_active</code> 필드를 <code>False</code>로 변경한다.<br>
      탈퇴 후 사용자는 더 이상 로그인할 수 없으며, 관련 데이터는 내부적으로 유지된다.
    </td>
  </tr>

  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.11.06</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>

  <!-- PRECONDITIONS -->
  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1. 사용자는 로그인 상태여야 하며 유효한 JWT 토큰을 보유해야 한다.<br>
      2. 사용자 정보가 DB에 존재해야 한다.<br>
      3. DB 연결이 정상적으로 이루어져야 한다.<br>
      4. 탈퇴 시 사용자와 관련된 세션, 토큰, 캐시가 자동으로 만료되거나 삭제되어야 한다.
    </td>
  </tr>

  <!-- TRIGGER -->
  <tr>
    <td><strong>Trigger</strong></td>
    <td>
      사용자가 “회원 탈퇴” 버튼을 클릭하고 탈퇴 확인을 선택할 때.<br>
      클라이언트는 JWT 토큰을 포함하여 <code>DELETE /user/me</code> 요청을 서버로 전송한다.<br>
    </td>
  </tr>

  <!-- SUCCESS POST CONDITION -->
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      1. 서버가 JWT 인증을 검증하고 현재 사용자를 식별한다.<br>
      2. 해당 사용자의 <code>is_active</code> 필드를 <code>False</code>로 변경한다.<br>
      3. DB 트랜잭션이 커밋되고 사용자 상태가 비활성화된다.<br>
      4. 클라이언트는 “계정이 성공적으로 비활성화되었습니다.” 메시지를 수신하고 로그인 화면으로 리다이렉트된다.
    </td>
  </tr>

  <!-- FAILED POST CONDITION -->
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      1. JWT 토큰이 없거나 만료된 경우 <code>401 Unauthorized</code> 반환.<br>
      2. 사용자 정보가 존재하지 않을 경우 <code>404 Not Found</code> 반환.<br>
      3. DB 세션 오류 또는 커밋 실패 시 <code>500 Internal Server Error</code> 반환.<br>
      4. 이미 탈퇴(비활성화)된 계정인 경우 <code>409 Conflict</code> 반환.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr>
    <td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td>
  </tr>

  <tr>
    <td><strong>Step</strong></td>
    <td><strong>Action</strong></td>
  </tr>

  <tr><td align="center">S</td><td>사용자가 “내 정보” 페이지에서 회원 탈퇴 버튼을 클릭한다.</td></tr>
  <tr><td align="center">1</td><td>클라이언트가 탈퇴 의사를 재확인하는 팝업을 표시한다.</td></tr>
  <tr><td align="center">2</td><td>사용자가 확인을 누르면 JWT를 포함해 <code>DELETE /user/me</code> 요청을 전송한다.</td></tr>
  <tr><td align="center">3</td><td>서버가 <code>get_current_user()</code>를 통해 현재 사용자 인증을 수행한다.</td></tr>
  <tr><td align="center">4</td><td>서버가 사용자 객체의 <code>is_active</code> 필드를 <code>False</code>로 변경한다.</td></tr>
  <tr><td align="center">5</td><td>DB 커밋이 완료되면 <code>{"message": "Account successfully deactivated"}</code> 응답을 반환한다.</td></tr>
  <tr><td align="center">6</td><td>클라이언트는 성공 메시지를 표시하고 자동 로그아웃 처리 후 메인 화면으로 이동한다.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr>
    <td colspan="2"><strong>EXTENSION SCENARIOS</strong></td>
  </tr>

  <tr><td align="center">2a</td><td>사용자가 탈퇴 확인을 취소하면 요청이 중단된다.</td></tr>
  <tr><td align="center">3a</td><td>토큰이 만료되면 <code>401 Unauthorized</code> — “로그인 세션이 만료되었습니다.”</td></tr>
  <tr><td align="center">4a</td><td>이미 <code>is_active=False</code> 상태라면 <code>409 Conflict</code> — “이미 탈퇴된 계정입니다.”</td></tr>
  <tr><td align="center">5a</td><td>DB 커밋 중 예외 발생 시 <code>500 Internal Server Error</code> — “서버 오류로 탈퇴 처리 실패.”</td></tr>

  <!-- RELATED INFORMATION -->
  <tr>
    <td colspan="2"><strong>RELATED INFORMATION</strong></td>
  </tr>

  <tr>
    <td><strong>Performance</strong></td>
    <td>≤ 1s </td>
  </tr>

  <tr>
    <td><strong>Frequency</strong></td>
    <td>사용자당 1회 한정</td>
  </tr>

  <tr>
    <td><strong>Concurrency</strong></td>
    <td>제한 없음 </td>
  </tr>
</table>


<h2>Use case #8 : 통화 녹음본 보이스피싱 탐지</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr><td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td></tr>
  <tr><td><strong>Summary</strong></td><td>사용자가 저장된 통화 녹음 파일(MP3, WAV 등)을 업로드하면, 서버가 CLOVA Speech API를 통해 음성을 텍스트로 변환하고, 결과를 JSON으로 반환하는 기능</td></tr>
  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.10.26</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>

  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1. 사용자는 로그인 상태여야 한다.<br>
      2. 서버와 DB, CLOVA 연결이 정상이어야 한다.<br>
      3. 업로드 파일은 허용 형식/크기를 만족해야 한다.<br>
      ● 형식: audio/mpeg (MP3), audio/wav (WAV)<br>
      ● 최대 크기: 25 MB <br>
      ● 최대 길이: 60분 권장(이상 시 처리 지연 가능)
    </td>
  </tr>
  <tr>
    <td><strong>Trigger</strong></td>
    <td>사용자가 앱에서 음성 파일을 선택 후,<br>POST /api/transcribe/upload 엔드포인트로 업로드를 전송</td>
  </tr>
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      1. 사용자가 POST /api/transcribe/upload 요청을 보내면,<br>
      서버는 CLOVA Speech API로 음성 파일을 전송하고 작업 식별용 토큰 이 포함된 JSON 응답을 반환한다.<br>
      2. 이후 사용자가 /api/transcribe/status/{token} 으로 요청하면, CLOVA Speech API가 변환을 완료한 경우 전체 텍스트, 신뢰도, 단어 정렬 정보 등을 포함한 최종 JSON 결과가 반환<br>
      3. 모든 과정이 정상적으로 완료되면 HTTP 상태코드 200 OK가 반환
    </td>
  </tr>
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      1. CLOVA API 오류 발생 시 CLOVA Speech 서버가 4xx 또는 5xx 응답을 반환<br>
      2. 네트워크/요청 오류 발생 시 CLOVA 서버에 연결하지 못했거나 타임아웃이 발생하면 500 Internal Server Error 응답 반환<br>
      3. 환경 변수 누락 시 API 호출 전 설정이 비어 있으면 500 Internal Server Error 응답 반환<br>
      4. CLOVA Speech가 업로드된 미디어를 처리할 수 없는 경우 400 Bad Request 응답 반환
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr><td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Action</strong></td></tr>
  <tr><td align="center">S</td><td>사용자가 앱 화면에서 음성 파일 업로드 페이지를 연다</td></tr>
  <tr><td align="center">1</td><td>사용자가 변환할 음성 파일을 선택하고 반환 요청을 한다.</td></tr>
  <tr><td align="center">2</td><td>서버가 업로드된 파일의 형식 및 크기 제한(≤25MB) 을 검증한다.</td></tr>
  <tr><td align="center">3</td><td>서버가 CLOVA Speech API의 /recognizer/upload 엔드포인트로 파일을 전송한다.</td></tr>
  <tr><td align="center">4</td><td>CLOVA Speech API가 요청을 수락하면 STT 변환 작업용 토큰 을 반환한다.</td></tr>
  <tr><td align="center">5</td><td>서버는 해당 토큰을 JSON 형태로 클라이언트에 반환한다.</td></tr>
  <tr><td align="center">6</td><td>사용자가 받은 token으로 GET /api/transcribe/status/{token} 을 호출한다.</td></tr>
  <tr><td align="center">7</td><td>CLOVA Speech API가 변환을 완료하면, 서버는 변환된 텍스트 와 신뢰도를 포함한 JSON 응답을 반환한다.</td></tr>
  <tr><td align="center">8</td><td>클라이언트는 변환 결과를 UI에 표시하고, “변환 완료” 메시지를 출력한다.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr><td colspan="2"><strong>EXTENSION SCENARIOS</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Branching Action / Result (HTTP)</strong></td></tr>
  <tr><td align="center">1</td><td>1a. 업로드된 파일 형식이 허용되지 않으면 서버는 415 Unsupported Media Type을 반환하고 오류 메시지를 표시</td></tr>
  <tr><td align="center">1</td><td>1b. 파일 크기가 25MB 제한을 초과하면 서버는 413 Payload Too Large를 반환하고 오류 메시지를 표시</td></tr>
  <tr><td align="center">3</td><td>3a. CLOVA Speech API가 4xx/5xx 오류를 반환하면 서버는 해당 상태코드와 함께 “CLOVA API Error: …” 메시지를 반환한다.</td></tr>
  <tr><td align="center">3</td><td>3b. CLOVA API 요청이 타임아웃되거나 연결이 끊기면 서버는 500 Internal Server Error를 반환하고 “CLOVA API 요청 실패” 메시지를 표시한다.</td></tr>
  <tr><td align="center">3</td><td>3c. CLOVA 환경 변수가 설정되지 않은 경우 서버는 500 Internal Server Error를 반환하고 오류 메시지를 표시한다.</td></tr>
  <tr><td align="center">4</td><td>4a. CLOVA 서버가 업로드된 파일을 처리하지 못한 경우 400 Bad Request를 반환하고 오류 메시지를 표시한다.</td></tr>
  <tr><td align="center">5</td><td>5a. 서버 내부 예외가 발생하면 500 Internal Server Error를 반환하고 오류 메시지를 표시한다.</td></tr>
  <tr><td align="center">6</td><td>6a. 사용자가 유효하지 않은 token으로 상태 조회를 시도하면 404 Not Found를 반환하고 유효하지 않은 토큰이라는 오류 메시지를 표시한다.</td></tr>

  <!-- RELATED INFORMATION -->
  <tr><td colspan="2"><strong>RELATED INFORMATION</strong></td></tr>
  <tr><td><strong>Performance</strong></td><td>≤ 2s</td></tr>
  <tr><td><strong>Frequency</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>&lt;Concurrency&gt;</strong></td><td>제한 없음</td></tr>
  <tr><td><strong>Due Date</strong></td><td></td></tr>
</table>


<h2>Use case #9 : 실시간 통화 보이스피싱 탐지 (WebSocket + gRPC)</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr><td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td></tr>
  <tr>
    <td><strong>Summary</strong></td>
    <td>사용자가 통화 중 음성을 실시간으로 서버에 전송하면, 서버는 CLOVA Speech gRPC API를 통해 음성을 실시간 텍스트(STT) 로 변환하고, 동시에 보이스피싱 탐지 세션(단어 기반 즉시 분석 + KoBERT 종합 분석) 을 수행하여 위험도를 실시간으로 판단하고 경고 메시지를 반환한다.</td>
  </tr>
  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.10.26</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>

  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1.사용자는 로그인 상태여야 한다.<br>
      2.서버는 CLOVA_SECRET_KEY 환경 변수가 설정되어 있어야 한다.<br>
      3.gRPC 클라이언트 및 보이스피싱 세션이 정상적으로 초기화되어야 한다.<br>
      4.네트워크가 안정적이어야 하며, 오디오 스트림은 16kHz, mono, PCM 포맷으로 인코딩되어야 한다.<br>
      5.클라이언트가 전송하는 청크 데이터는 0.5~1초 단위의 바이트 스트림이어야 한다.
    </td>
  </tr>
  <tr>
    <td><strong>Trigger</strong></td>
    <td>사용자가 통화를 시작하면 클라이언트는 서버의 /ws/transcribe/stream 엔드포인트에 WebSocket 연결을 생성하고 오디오 데이터를 스트림 형태로 전송한다.<br>서버는 이 스트림을 CLOVA Speech gRPC에 전달해 실시간 전사(STT)를 수행하고, 동시에 텍스트가 누적되면 보이스피싱 탐지를 실행한다.</td>
  </tr>
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      1.서버는 WebSocket을 통해 클라이언트와 연결하고,오디오 스트림을 CLOVA Speech API로 전달해 실시간 텍스트 변환을 수행<br>
      2.CLOVA로부터 수신된 인식 결과를 JSON(type: transcription) 형태로 클라이언트에 실시간 전송<br>
      3.최종 문장(isFinal=True)이 수신될 때마다 해당 문장이 보이스피싱 탐지 세션에 추가(add_sentence)<br>
      4.즉시 분석(immediate): 위험 단어·패턴 탐지를 통해 단어 기반 위험도를 즉시 계산하고, 위험도(Level 1~3)에 따라 즉시 "phishing_alert" 이벤트를 전송<br>
      5.모든 스트림이 정상 종료되면 서버는 gRPC 연결과 WebSocket 세션을 안전하게 종료
    </td>
  </tr>
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      1.CLOVA gRPC 오류 발생 시 서버는 "type": "error" 와 함께 "gRPC Error: ..." 메시지를 WebSocket으로 전송<br>
      2.WebSocketDisconnect 발생 시 클라이언트 연결이 종료되면 "WebSocket disconnected." 로그를 남기고 세션을 정리<br>
      3.Payload Parsing Error 발생 시 CLOVA 응답의 JSON 파싱 실패 시 "Payload parsing error" 로그를 남기고 "type": "debug" 메시지를 반환<br>
      4.보이스피싱 세션 초기화 실패 시 enable_phishing_detection=False 로 전환되고 탐지 기능을 비활성화<br>
      5.탐지 로직 실행 중 예외 발생 시 add_sentence() 호출 중 오류가 발생하면 "보이스피싱 탐지 오류" 로그를 남기고 해당 문장은 분석에서 제외<br>
      6.환경 변수 누락 시 "type": "error" 와 "CLOVA_SECRET_KEY 환경변수가 필요합니다." 메시지를 전송한 뒤 세션 종료.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr><td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Action</strong></td></tr>
  <tr><td align="center">S</td><td>사용자가 통화 중 실시간 STT 기능을 활성화한다.</td></tr>
  <tr><td align="center">1</td><td>클라이언트가 WebSocket /ws/transcribe/stream 연결을 생성한다.</td></tr>
  <tr><td align="center">2</td><td>서버가 연결을 수락하고, ClovaSpeechClient 및 보이스피싱 세션(create_session(window_size=5))을 초기화한다.</td></tr>
  <tr><td align="center">3</td><td>클라이언트가 오디오 청크를 일정 간격으로 전송한다.</td></tr>
  <tr><td align="center">4</td><td>서버가 오디오 청크를 gRPC 스트림으로 CLOVA Speech에 전송한다.</td></tr>
  <tr><td align="center">5</td><td>서버가 오디오 청크를 gRPC 스트림으로 CLOVA Speech에 전달한다.</td></tr>
  <tr><td align="center">6</td><td>CLOVA Speech가 실시간으로 부분/최종 전사 결과를 반환한다.</td></tr>
  <tr><td align="center">7</td><td>서버가 결과를 "type": "transcription" 메시지로 클라이언트에 전송한다.</td></tr>
  <tr><td align="center">8</td><td>결과 중 isFinal=True인 문장이 감지되면 add_sentence로 세션에 추가한다.</td></tr>
  <tr><td align="center">9</td><td>세션은 단어 기반 즉시 분석을 수행하고, 위험 단어 감지 시 "phishing_alert"(alert_type=immediate)을 전송한다.</td></tr>
  <tr><td align="center">10</td><td>누적 문장이 3개 이상이 되면 KoBERT 종합 분석이 자동 실행되고, 보이스피싱으로 판단 시 "alert_type": "comprehensive" 메시지를 전송한다.</td></tr>
  <tr><td align="center">11</td><td>사용자가 통화를 종료하거나 연결을 닫으면 서버는 gRPC 스트림 및 세션을 종료하고 자원을 정리한다.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr><td colspan="2"><strong>XTENSION SCENARIOS</strong></td></tr>
  <tr><td><strong>Step</strong></td><td><strong>Branching Action / Result (HTTP)</strong></td></tr>
  <tr><td align="center">3</td><td>3a. create_session() 실행이 실패하면, 서버는 enable_phishing_detection=False로 전환하여 탐지 기능을 비활성화한다.</td></tr>
  <tr><td align="center">4</td><td>4a. 네트워크가 불안정해 일부 오디오 청크가 유실되면, 서버는 "type": "debug" 로그를 남기고 누락 구간을 건너뛴다.</td></tr>
  <tr><td align="center">5</td><td>5a. CLOVA Speech API에서 gRPC 오류가 발생하면, 서버는 "type": "error" 메시지를 전송하며 "gRPC Error: ..." 내용을 포함한다.</td></tr>
  <tr><td align="center">6</td><td>6a. CLOVA Speech 응답이 비정상(JSON 파싱 오류)일 경우, 서버는 "Payload parsing error" 로그를 남기고 "type": "debug" 메시지를 반환한다.</td></tr>
  <tr><td align="center">8</td><td>8a. add_sentence() 수행 중 예외가 발생하면, 서버는 "보이스피싱 탐지 오류" 로그를 남기고 해당 문장은 건너뛴다.</td></tr>
  <tr><td align="center">9</td><td>9a. 단어 기반 분석 결과의 위험 수준이 0으로 평가되면, 서버는 탐지 경고를 전송하지 않고 세션만 누적 유지한다.</td></tr>
  <tr><td align="center">10</td><td>10a. KoBERT 모델 추론 중 모델 로드에 실패하면, 서버는 "type": "error" 와 "분석 모델 오류 발생" 메시지를 전송한다.</td></tr>
  <tr><td align="center">10</td><td>10b. KoBERT 분석 결과의 confidence 값이 0.8 미만이면, "alert_type": "comprehensive" 메시지를 전송하지 않고 세션을 유지한다.</td></tr>
  <tr><td align="center">11</td><td>11a. WebSocket 연결이 끊기면, 서버는 "WebSocket disconnected." 로그를 남기고 모든 리소스를 안전하게 해제한다.</td></tr>
  <tr><td align="center">11</td><td>11b. gRPC 세션 종료 중 예외가 발생하면, 서버는 "Real-time transcription resources cleaned up." 로그를 출력한다.</td></tr>

  <!-- RELATED INFORMATION -->
  <tr><td colspan="2"><strong>RELATED INFORMATION</strong></td></tr>
  <tr><td><strong>Performance</strong></td><td>실시간(≤ 1초 지연)</td></tr>
  <tr><td><strong>Frequency</strong></td><td>통화 중</td></tr>
  <tr><td><strong>&lt;Concurrency&gt;</strong></td><td>1건</td></tr>
  <tr><td><strong>Due Date</strong></td><td></td></tr>
</table>

<h2>Use case #10 : 문서 위조 탐지</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr>
    <td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td>
  </tr>

  <tr>
    <td><strong>Summary</strong></td>
    <td>
      본 유스케이스는 사용자가 업로드한 문서 이미지를 분석하여 위조·조작 여부를 판단하는 기능이다.<br>
      단일 이미지를 업로드하는 경우 단일 문서 분석이 수행되며, 
      여러 장의 이미지를 업로드하는 경우 복수 문서 분석이 수행된다.<br>
      서버는 OCR, 키워드 탐지, 레이아웃 분석, 직인 탐지 등의 모듈을 통해 위험 요인을 식별하고, 
      단일 또는 복수 이미지의 종합 위험도를 계산하여 JSON 형태로 반환한다.
    </td>
  </tr>

  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.11.06</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자</td></tr>

  <!-- PRECONDITIONS -->
  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1. 사용자는 로그인 상태여야 한다.<br>
      2. 업로드 파일은 이미지 형식 (.jpg, .jpeg, .png) 이어야 한다.<br>
      3. 서버에 파일 저장 권한 및 디스크 여유 공간이 확보되어야 한다.<br>
      4. OCR, 키워드, 레이아웃, 직인 탐지 모듈이 import 및 초기화되어 있어야 한다.<br>
      5. 다중 업로드의 경우, 각 파일 크기는 25MB 이하이며 전체 용량은 100MB를 초과하지 않아야 한다.
    </td>
  </tr>

  <!-- TRIGGER -->
  <tr>
    <td><strong>Trigger</strong></td>
    <td>
      사용자가 “문서 위조 분석” 또는 “다중 문서 분석” 버튼을 클릭하고 
      하나 이상의 문서 이미지를 업로드할 때.<br>
      단일 업로드 시 <code>POST /process-request</code>, 
      복수 업로드 시 <code>multipart/form-data</code> 형식으로 전송된다.
    </td>
  </tr>

  <!-- SUCCESS POST CONDITION -->
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      1. 서버가 모든 이미지를 정상 저장한다.<br>
      2. 각 이미지별로 <code>analyze_document()</code>가 실행되어 OCR, 키워드, 레이아웃, 직인 탐지를 수행한다.<br>
      3. 단일 이미지는 가중치(직인 30%, 키워드 50%, 레이아웃 20%)로 <code>final_risk</code>를 계산한다.<br>
      4. 복수 이미지의 경우 각 이미지의 위험도를 평균 또는 가중평균하여 <code>total_risk</code>를 계산한다.<br>
      5. 결과는 JSON 객체 또는 JSON 배열 형태로 반환된다.
    </td>
  </tr>

  <!-- FAILED POST CONDITION -->
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      1. 비이미지 파일 업로드 시 <code>415 Unsupported Media Type</code> 반환.<br>
      2. OCR 오류 또는 분석 중 예외 발생 시 <code>500 Internal Server Error</code> 반환.<br>
      3. 서버 자원 부족(메모리 초과 등) 시 <code>503 Service Unavailable</code> 반환.<br>
      4. 일부 파일 오류 발생 시 나머지는 정상 처리하며, 실패한 파일은 별도 메시지로 표시.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr>
    <td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td>
  </tr>

  <tr>
    <td><strong>Step</strong></td>
    <td><strong>Action</strong></td>
  </tr>

  <!-- SINGLE DOCUMENT MODE -->
  <tr><td colspan="2" style="background-color:#f9f9f9;"><strong>(1) 단일 문서 이미지 분석</strong></td></tr>

  <tr><td align="center">S1</td><td>사용자가 “문서 위조 분석” 페이지를 연다.</td></tr>
  <tr><td align="center">1</td><td>문서 이미지 1장을 선택해 업로드한다.</td></tr>
  <tr><td align="center">2</td><td>서버가 파일 유효성을 검증한다.</td></tr>
  <tr><td align="center">3</td><td>서버가 파일을 저장하고 <code>analyze_document()</code>를 호출한다.</td></tr>
  <tr><td align="center">4</td><td>직인, OCR, 키워드, 레이아웃 분석이 순차적으로 수행된다.</td></tr>
  <tr><td align="center">5</td><td>결과를 가중합하여 <code>final_risk</code>를 계산한다.</td></tr>
  <tr><td align="center">6</td><td>JSON 결과 (<code>{filename, url, stamp, keyword, layout, final_risk}</code>) 반환.</td></tr>
  <tr><td align="center">7</td><td>클라이언트는 분석 결과를 시각화한다.</td></tr>

  <!-- MULTI DOCUMENT MODE -->
  <tr><td colspan="2" style="background-color:#f9f9f9;"><strong>(2) 복수 문서 이미지 분석</strong></td></tr>

  <tr><td align="center">S2</td><td>사용자가 “다중 문서 분석” 페이지를 연다.</td></tr>
  <tr><td align="center">1</td><td>여러 장의 문서 이미지를 선택하여 업로드한다.</td></tr>
  <tr><td align="center">2</td><td>서버가 각 파일의 형식과 크기를 검증한다.</td></tr>
  <tr><td align="center">3</td><td>각 파일이 서버에 저장된다.</td></tr>
  <tr><td align="center">4</td><td>각 파일별로 <code>analyze_document()</code>가 병렬 또는 비동기적으로 실행된다.</td></tr>
  <tr><td align="center">5</td><td>서버가 이미지별 위험도를 취합한다.</td></tr>
  <tr><td align="center">6</td><td>평균 또는 가중평균으로 <code>total_risk</code>를 계산한다.</td></tr>
  <tr><td align="center">7</td><td>결과 배열 (<code>[{filename, risk_detail, final_risk}, ...]</code>) 형태로 반환.</td></tr>
  <tr><td align="center">8</td><td>클라이언트는 각 페이지별 결과 및 종합 위험 등급을 시각화한다.</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr>
    <td colspan="2"><strong>EXTENSION SCENARIOS</strong></td>
  </tr>

  <tr><td align="center">2a</td><td>비이미지 파일 업로드 시 <code>415 Unsupported Media Type</code> 오류</td></tr>
  <tr><td align="center">4a</td><td>OCR 처리 실패 시 <code>500 Internal Server Error</code> — “OCR 처리 실패”</td></tr>
  <tr><td align="center">5a</td><td>가중치 계산 오류 시 <code>500 Internal Server Error</code> — “위험도 계산 실패”</td></tr>
  <tr><td align="center">6a</td><td>복수 문서 분석 중 일부 파일 오류 발생 시 해당 결과만 제외</td></tr>
  <tr><td align="center">7a</td><td>병렬 처리 중 메모리 초과 시 <code>503 Service Unavailable</code> 반환</td></tr>

  <!-- RELATED INFORMATION -->
  <tr>
    <td colspan="2"><strong>RELATED INFORMATION</strong></td>
  </tr>


  <tr>
    <td><strong>Performance</strong></td>
    <td>단일 분석 ≤ 3s / 복수 분석(5장 기준) ≤ 5s</td>
  </tr>

  <tr>
    <td><strong>Frequency</strong></td>
    <td>제한 없음</td>
  </tr>

  <tr>
    <td><strong>Concurrency</strong></td>
    <td>동시 요청 최대 10회</td>
  </tr>
</table>


<h2>Use case #11 : 스미싱 탐지</h2>

<table border="1" cellpadding="6" cellspacing="0" width="980" align="center">
  <colgroup>
    <col width="25%">
    <col>
  </colgroup>

  <!-- GENERAL CHARACTERISTICS -->
  <tr>
    <td colspan="2"><strong>GENERAL CHARACTERISTICS</strong></td>
  </tr>

  <tr>
    <td><strong>Summary</strong></td>
    <td>
      본 유스케이스는 앱이 수신한 문자 메시지를 기반으로 스미싱 여부를 판별하는 기능이다.<br>
      서버는 텍스트 기반 탐지를 수행하며, 
      메시지 내에 URL이 존재할 경우 추가적으로 URL 기반 탐지를 병행 수행한다.<br>
      두 분석 결과는 각각의 위험 점수와 탐지 요인을 종합하여 
      최종 스미싱 위험도와 판정을 산출하고 사용자에게 주의·경고 알림을 제공한다.
    </td>
  </tr>

  <tr><td><strong>Scope</strong></td><td>Anti-Phishing-App</td></tr>
  <tr><td><strong>Level</strong></td><td>User level</td></tr>
  <tr><td><strong>Author</strong></td><td>Anti-Phishing-App_Team</td></tr>
  <tr><td><strong>Last Update</strong></td><td>2025.11.06</td></tr>
  <tr><td><strong>Status</strong></td><td>Analysis</td></tr>
  <tr><td><strong>Primary Actor</strong></td><td>사용자 / 앱 (자동 감지 모듈)</td></tr>

  <!-- PRECONDITIONS -->
  <tr>
    <td><strong>Preconditions</strong></td>
    <td>
      1. 앱이 메시지 접근 권한 및 분석 권한을 보유해야 함.<br>
      2. 서버는 텍스트 분석 모듈, URL 분석 모듈, ML 모델, 피싱 DB, HTML 크롤러를 초기화한 상태여야 함.<br>
      3. URL 추출 정규식과 메시지 파싱 로직이 준비되어 있어야 함.<br>
      4. 클라이언트는 메시지 수신 이벤트를 자동 감지하고 서버로 전달할 수 있어야 함.
    </td>
  </tr>

  <!-- TRIGGER -->
  <tr>
    <td><strong>Trigger</strong></td>
    <td>
      디바이스가 새 메시지를 수신하고 앱이 본문을 감지할 때.<br>
      ① 모든 메시지에 대해 텍스트 기반 분석이 즉시 실행된다.<br>
      ② 본문에 URL이 포함된 경우 URL 기반 분석이 추가로 병행 실행된다.
    </td>
  </tr>

  <!-- SUCCESS POST CONDITION -->
  <tr>
    <td><strong>Success Post Condition</strong></td>
    <td>
      1. 서버가 텍스트 분석 및 (URL 존재 시) URL 분석 결과를 종합하여 
         최종 위험도(<code>final_risk</code>)를 계산.<br>
      2. 분석 결과에는 위험도, 판정(level), 주요 탐지 요인, 권고가 포함.<br>
      3. 앱은 결과를 시각화하여 사용자에게 주의·경고 알림을 제공.
    </td>
  </tr>

  <!-- FAILED POST CONDITION -->
  <tr>
    <td><strong>Failed Post Condition</strong></td>
    <td>
      1. 입력 포맷 오류 시 400 Bad Request 반환.<br>
      2. 서버 분석 중 예외 발생 시 500 Internal Server Error 반환.<br>
      3. 일부 분석 실패 시 가능한 부분 결과만 부분 반환.<br>
      4. 네트워크 문제 또는 크롤링 실패 시 기본 안전 응답 반환.
    </td>
  </tr>

  <!-- MAIN SUCCESS SCENARIO -->
  <tr>
    <td colspan="2"><strong>MAIN SUCCESS SCENARIO</strong></td>
  </tr>

  <tr>
    <td><strong>Step</strong></td>
    <td><strong>Action</strong></td>
  </tr>

  <!-- TEXT-BASED DETECTION (ALWAYS EXECUTED) -->
  <tr><td colspan="2" style="background-color:#f9f9f9;"><strong>(1) 텍스트 기반 스미싱 탐지 </strong></td></tr>

  <tr><td align="center">S1</td><td>앱이 메시지 수신 이벤트를 감지하고 본문 전체를 확보</td></tr>
  <tr><td align="center">1</td><td>앱이 텍스트 및 발신자 정보를 엔드포인트로 전송</td></tr>
  <tr><td align="center">2</td><td>서버가 긴급성/행동유도 패턴 탐지 → 패턴 점수 산출</td></tr>
  <tr><td align="center">3</td><td>키워드 탐지 및 가중치 계산 (<code>detect_keywords()</code>)</td></tr>
  <tr><td align="center">4</td><td>ML 분류기(KoBERT 등)로 스미싱 확률(<code>raw_prob_text</code>) 예측</td></tr>
  <tr><td align="center">5</td><td>패턴·키워드·ML 점수를 가중합 </td></tr>
  <tr><td align="center">6</td><td>텍스트 기반 위험도및 분석 요약 JSON 생성</td></tr>

  <!-- URL-BASED DETECTION (OPTIONAL) -->
  <tr><td colspan="2" style="background-color:#f9f9f9;"><strong>(2) URL 기반 스미싱 탐지 (URL 존재 시 추가 실행)</strong></td></tr>

  <tr><td align="center">S2</td><td>앱이 메시지 본문에서 URL을 추출 </td></tr>
  <tr><td align="center">1</td><td>URL이 하나 이상 감지되면 각 URL을 <code>POST /api/phishing-site/analyze</code> 로 전송</td></tr>
  <tr><td align="center">2</td><td><code>detect_immediate(url)</code> 실행 — URL 길이, 도메인, 숫자 비율, 위험 키워드 탐지</td></tr>
  <tr><td align="center">3</td><td><code>detect_comprehensive(url)</code> 실행 — DB 조회, HTML 크롤링, ML 입력</td></tr>
  <tr><td align="center">4</td><td>ML 모델(<code>RandomForest</code>)로 <code>predict_proba()</code> 수행 → URL 위험 확률 계산</td></tr>
  <tr><td align="center">5</td><td>결과를 텍스트 분석 결과와 병합하여 종합 위험도(<code>final_risk</code>) 계산</td></tr>
  <tr><td align="center">6</td><td>앱은 통합된 결과를 사용자에게 표시 (팝업/푸시/상세 분석 화면)</td></tr>

  <!-- EXTENSION SCENARIOS -->
  <tr>
    <td colspan="2"><strong>EXTENSION SCENARIOS</strong></td>
  </tr>

  <tr><td align="center">S1a</td><td>텍스트가 짧거나 의미 없는 경우(1~2글자) → ML 생략 후 키워드·패턴만 평가</td></tr>
  <tr><td align="center">S2a</td><td>URL이 없으면 URL 분석 단계를 건너뛰고 텍스트 분석 결과만 사용</td></tr>
  <tr><td align="center">3a</td><td>HTML 크롤링 실패 → HTML 특징 0으로 대체 후 예측 진행</td></tr>
  <tr><td align="center">4a</td><td>ML 예측 실패 시 기본 안전 응답(is_phishing=false, confidence=0.0) 반환</td></tr>
  <tr><td align="center">6a</td><td>결과 일부 누락 시 가능한 데이터만 포함하여 JSON 반환</td></tr>

  <!-- RELATED INFORMATION -->
  <tr>
    <td colspan="2"><strong>RELATED INFORMATION</strong></td>
  </tr>

  <tr>
    <td><strong>Performance</strong></td>
    <td>텍스트 분석 ≤ 1.5s / URL 분석 ≤ 3s (네트워크 및 모델 크기에 따라 변동)</td>
  </tr>

  <tr>
    <td><strong>Frequency</strong></td>
    <td>모든 메시지 수신 시 자동 실행 (rate limit 및 병렬 제어 필요)</td>
  </tr>

  <tr>
    <td><strong>Concurrency</strong></td>
    <td>최대 동시 요청 10개 이하 권장 (크롤링 및 모델 부하 고려)</td>
  </tr>
</table>




## 3. Class Diagrams

이 장은 다양한 관점에서 바라본 Class diagram과 각각에 대한 설명을 기술한다. 본 문서의 Class diagram의 목차는 다음과 같다.

**3.1. 서버 시스템 클래스 다이어그램**
- 서버의 데이터베이스 모델, 비즈니스 로직, 외부 시스템 간의 관계를 나타낸다.

**3.2. 안드로이드 클라이언트 클래스 다이어그램**
- 안드로이드 앱의 주요 기능(문서 위조 탐지, 서버 통신)을 담당하는 클래스 간의 관계를 나타낸다.

**3.3. 주요 디자인 패턴**
- 시스템 전반에서 사용된 디자인 패턴을 설명한다.

전제는 다음과 같다.

1. 한 번 정의된 클래스는 다시 정의하지 않는다.
2. 이미 정의된 클래스 및 연관관계일 시, 시각적 편의성을 위해 클래스 다이어그램 내에서 클래스 이름으로만 표기하는 것을 허용한다.
3. 모든 클래스는 상세 표(Class Description Table)로 Attributes와 Operations를 명시한다.

### 3.1 서버 시스템 클래스 다이어그램

![### 3.1 DB Class Diagram (OAuth 지원 추가)](image/class_diagram_server.png)

#### 설명

본 다이어그램은 피싱 탐지 시스템의 데이터베이스 및 핵심 비즈니스 로직 클래스들을 나타낸다. PDF의 use case에 명시된 OAuth 인증 기능을 지원하기 위해 User 모델에 `oauth_provider`와 `oauth_id` 필드가 추가되었다.

#### FastAPIApp Class

| Class | FastAPIApp |
|-------|------------|
| Description | 애플리케이션 초기화, 미들웨어/예외처리/라우터 등록을 담당 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | app | FastAPI | Public | FastAPI 인스턴스 |
|  | routers | list | Private | 등록할 APIRouter 목록 |
| Operations | include_routers() | None | Public | 모든 라우터(prefix 포함)를 앱에 등록한다 |
|  | add_middlewares() | None | Private | CORS 등 공통 미들웨어 추가 |
|  | create_app() | FastAPI | Public | 구성/라우터/미들웨어를 적용해 앱 반환 |

---

#### Config Class

| Class | Config |
|-------|-------|
| Description | 환경변수 로드(JWT, DB URL, 외부 API 키 등) 및 설정 제공 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | JWT_SECRET | str | Public | 액세스 토큰 서명 키 |
|  | JWT_ALG | str | Public | 예: HS256 |
|  | ACCESS_EXPIRE_MIN | int | Public | 액세스 토큰 만료(분) |
|  | REFRESH_EXPIRE_DAYS | int | Public | 리프레시 토큰 만료(일) |
|  | DATABASE_URL | str | Public | SQLAlchemy 연결 문자열 |
| Operations | from_env() | Config | Public | OS 환경변수에서 설정 생성 |
|  | load_dotenv() | None | Private | .env 로드(있을 때) |

---

#### Database Class

| Class | Database |
|-------|---------|
| Description | SQLAlchemy 엔진/세션 생성과 세션 의존성 제공 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | engine | Engine | Private | SQLAlchemy 엔진 |
|  | SessionLocal | sessionmaker | Private | 요청 단위 세션 팩토리 |
| Operations | get_db() | Generator[Session] | Public | FastAPI 의존성: 요청 단위 세션 제공/종료 |
|  | create_all() | None | Public | 모델 기반 테이블 생성 |

---

#### Security Class

| Class | Security |
|-------|---------|
| Description | 비밀번호 해싱/검증, JWT 발급 및 검증 로직 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | pwd_context | CryptContext | Private | bcrypt 해싱 컨텍스트 |
|  | secret | str | Private | JWT 서명 키 |
|  | algorithm | str | Private | JWT 알고리즘(예: HS256) |
| Operations | hash_password(pw) | str | Public | 비밀번호 해싱 |
|  | verify_password(pw, hashed) | bool | Public | 해시 검증 |
|  | create_access_token(data, exp_min) | str | Public | 액세스 토큰 생성 |
|  | create_refresh_token(sub, exp_days) | str | Public | 리프레시 토큰 생성 |
|  | decode_token(token) | dict | Public | 토큰 디코딩/검증 |

---

#### Dependencies Class

| Class | Dependencies |
|-------|-------------|
| Description | 공통 의존성(인증 사용자 조회/활성 검사 등) 제공 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | get_current_user(db, token) | User | Public | Authorization 헤더의 JWT로 사용자 로드 |
|  | require_active(user) | User | Public | 비활성 사용자 차단 후 통과 사용자 반환 |

---

#### User Class

| Class | User |
|-------|------|
| Description | 사용자 정보를 저장하는 데이터베이스 모델 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | id | int | Public | 사용자 고유 식별자 (Primary Key) |
|  | username | str | Public | 사용자 이름 (Unique) |
|  | email | str | Public | 이메일 주소 (Unique) |
|  | hashed_password | str | Public | 해시된 비밀번호 |
|  | full_name | str | Public | 사용자 전체 이름 |
|  | phone | str | Public | 전화번호 |
|  | is_active | bool | Public | 계정 활성화 상태 |
|  | created_at | datetime | Public | 계정 생성 일시 |
|  | updated_at | datetime | Public | 계정 정보 수정 일시 |

---

#### LoginRequest Class

| Class | LoginRequest |
|-------|--------------|
| Description | 아이디/비밀번호 로그인 요청 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | username | str | Public | 사용자 이름 |
|  | password | str | Public | 비밀번호 |

---

#### SignupRequest Class

| Class | SignupRequest |
|-------|---------------|
| Description | 회원가입 요청 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | username | str | Public | 사용자 이름(Unique) |
|  | email | str | Public | 이메일(Unique) |
|  | password | str | Public | 비밀번호 |
|  | full_name | str | Public | 이름 |
|  | phone | str | Public | 전화번호(선택) |

---

#### TokenResponse Class

| Class | TokenResponse |
|-------|---------------|
| Description | 액세스/리프레시 토큰 응답 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | access_token | str | Public | 액세스 토큰 |
|  | refresh_token | str | Public | 리프레시 토큰 |
|  | token_type | str | Public | 예: "bearer" |

---

#### UserResponse Class

| Class | UserResponse |
|-------|--------------|
| Description | 사용자 프로필 응답 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | id | int | Public | 사용자 PK |
|  | username | str | Public | 사용자명 |
|  | email | str | Public | 이메일 |
|  | full_name | str | Public | 이름 |
|  | phone | str | Public | 전화번호 |
|  | is_active | bool | Public | 활성 여부 |
|  | created_at | datetime | Public | 생성 일시 |

---

#### UserUpdateRequest Class

| Class | UserUpdateRequest |
|-------|-------------------|
| Description | 사용자 정보 수정 요청 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | full_name | Optional[str] | Public | 이름(선택) |
|  | phone | Optional[str] | Public | 전화번호(선택) |

---

#### VoicePhishingRequest Class

| Class | VoicePhishingRequest |
|-------|----------------------|
| Description | 보이스피싱 분석 요청 DTO(텍스트/스크립트) |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | text | Optional[str] | Public | 분석할 텍스트 |
|  | transcript | Optional[str] | Public | 전사/스크립트 텍스트 |

---

#### VoicePhishingResponse Class

| Class | VoicePhishingResponse |
|-------|-----------------------|
| Description | 보이스피싱 분석 결과 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | risk_score | float | Public | 위험도 점수(0~1) |
|  | tags | list[str] | Public | 탐지 태그 |
|  | top_keywords | list[str] | Public | 주요 키워드 |

---

#### AnalysisResponse Class

| Class | AnalysisResponse |
|-------|------------------|
| Description | 문서/보이스 통합 분석 결과 공통 응답 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | risk_score | float | Public | 종합 위험도 |
|  | modules | dict | Public | 서브 모듈별 결과 맵 |

---

#### MessageResponse Class

| Class | MessageResponse |
|-------|-----------------|
| Description | 단순 메시지 응답 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | message | str | Public | 메시지 문자열 |

---

#### UploadResponse Class

| Class | UploadResponse |
|-------|----------------|
| Description | 업로드 처리 결과 응답 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | file_id | str | Public | 파일 식별자 |
|  | url | str | Public | 접근 URL 또는 경로 |

---

#### AuthRouter Class

| Class | AuthRouter |
|-------|-----------|
| Description | 회원가입/로그인/토큰 갱신을 처리하는 인증 라우터 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | prefix | str | Public | "/auth" |
| Operations | signup(payload, db) | UserResponse | Public | 회원가입 |
|  | login(payload, db) | TokenResponse | Public | 로그인/토큰 발급 |
|  | refresh(token) | TokenResponse | Public | 리프레시 토큰으로 재발급 |

---

#### UserRouter Class

| Class | UserRouter |
|-------|-----------|
| Description | 사용자 정보 조회/수정/삭제를 처리하는 API 라우터 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | prefix | str | Public | "/users" 또는 ""(루트) |
| Operations | get_my_info(current_user) | UserResponse | Public | 로그인한 사용자 정보를 반환한다 |
|  | update_my_info(payload, db, current_user) | UserResponse | Public | 사용자 정보를 수정한다 |
|  | delete_my_account(current_user, db) | MessageResponse | Public | 사용자 계정을 삭제한다 |

---

#### TranscribeRouter Class

| Class | TranscribeRouter |
|-------|------------------|
| Description | 음성 → 텍스트 변환(STT) 및 실시간 탐지 API 라우터 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | prefix | str | Public | "/transcribe" |
| Operations | upload_audio(file, lang) | dict | Public | 업로드 파일 STT 처리 |
|  | ws_stream() | WebSocket | Public | 실시간 음성 스트리밍(STT) |

---

#### VoicePhishingRouter Class

| Class | VoicePhishingRouter |
|-------|---------------------|
| Description | 텍스트/자막 기반 보이스피싱 위험도 분석 API 라우터 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | prefix | str | Public | "/voice-phishing" |
| Operations | analyze_text(req) | VoicePhishingResponse | Public | 텍스트 분석 |
|  | analyze_transcript(file) | VoicePhishingResponse | Public | 자막/스크립트 파일 분석 |

---

#### DocumentRouter Class

| Class | DocumentRouter |
|-------|----------------|
| Description | 문서 위조(키워드/레이아웃/OCR/직인) 종합 분석 API 라우터 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | prefix | str | Public | "/documents" |
| Operations | analyze_document(file) | AnalysisResponse | Public | 업로드 문서 종합 분석 |
|  | get_result(task_id) | AnalysisResponse | Public | 비동기 결과 조회(선택) |

---

#### UploadRouter Class

| Class | UploadRouter |
|-------|--------------|
| Description | 공용 파일 업로드 엔드포인트 라우터 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | prefix | str | Public | "/upload" |
| Operations | upload_file(file) | UploadResponse | Public | 파일 저장 후 경로/ID 반환 |

---

#### VoicePhishingService Class

| Class | VoicePhishingService |
|-------|----------------------|
| Description | 보이스피싱 핵심 비즈니스 로직(키워드·모델 결합 평가) |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | keyword_predictor | KeywordPredictor | Private | 키워드 위험도 예측기 |
|  | bert_classifier | BERTClassifier | Private | KoBERT 분류기 |
| Operations | analyze_text(text) | dict | Public | 텍스트 기반 종합 점수/태그 산출 |
|  | analyze_transcript(path) | dict | Public | 파일 로드 후 분석 |

---

#### KeywordPredictor Class

| Class | KeywordPredictor |
|-------|------------------|
| Description | 위험 키워드/가중치 기반 스코어링 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | predict(text) | dict | Public | 상위 위험 키워드/점수 반환 |

---

#### LayoutPredictor Class

| Class | LayoutPredictor |
|-------|-----------------|
| Description | 문서 레이아웃 특징에 따른 위험도 평가 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | predict(image_or_pdf) | dict | Public | 레이아웃 리스크 지표 산출 |

---

#### OCRPredictor Class

| Class | OCRPredictor |
|-------|---------------|
| Description | OCR 결과 품질/패턴 기반 리스크 평가 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | predict(ocr_json) | dict | Public | 텍스트/좌표 기반 리스크 산출 |

---

#### StampPredictor Class

| Class | StampPredictor |
|-------|----------------|
| Description | 직인(도장) 유무/유사도 판별 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | predict(image) | dict | Public | 직인 탐지/유사도 점수 |

---

#### BERTDataset Class

| Class | BERTDataset |
|-------|-------------|
| Description | KoBERT 입력 텐서 구성용 데이터셋 래퍼 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | encode(texts, labels) | dict | Public | ids/attention_mask/token_type_ids 생성 |

---

#### BERTClassifier Class

| Class | BERTClassifier |
|-------|----------------|
| Description | KoBERT 기반 보이스피싱 분류 모델 래퍼 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | predict(texts) | list[float] | Public | 각 문장의 위험 확률 산출 |
|  | load_from(path) | None | Public | 학습 가중치 로드 |

---

#### SmishingRouter Class

| Class | SmishingRouter |
|-------|----------------|
| Description | 문자 메시지(SMS/MMS) 기반 스미싱 탐지 API 라우터 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | prefix | str | Public | "/smishing" |
| Operations | analyze_message(req) | SmishingResponse | Public | 단일 문자 본문·첨부·링크 종합 분석 |
|  | analyze_urls(req) | list[URLAnalysisResponse] | Public | URL 리스트 일괄 분석 |
|  | upload_attachment(file) | UploadResponse | Public | 첨부 파일 업로드 후 스캔 |

---

#### SmishingService Class

| Class | SmishingService |
|-------|-----------------|
| Description | 스미싱 핵심 비즈니스 로직(링크·도메인·단축 URL·첨부·발신자 종합) |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | sms_parser | SMSParser | Private | 본문에서 URL·전화번호·키워드 추출 |
|  | url_extractor | URLExtractor | Private | URL 정규화·중복 제거 |
|  | shortlink_expander | ShortLinkExpander | Private | 단축 URL 해제(리다이렉트 추적) |
|  | url_feature_extractor | URLFeatureExtractor | Private | URL·도메인·WHOIS·SSL 특징 추출 |
|  | domain_reputation | DomainReputation | Private | 블랙리스트·평판 정보 조회 |
|  | attachment_scanner | AttachmentScanner | Private | 첨부 파일 정적 분석·시그니처 탐지 |
|  | sender_reputation | SenderReputation | Private | 발신 번호 패턴·스푸핑 평가 |
|  | url_classifier | URLClassifier | Private | ML 기반 URL 위험 확률 예측 |
|  | rule_detector | RuleBasedSmishingDetector | Private | 규칙·시그니처 기반 탐지 |
| Operations | analyze_message(req) | dict | Public | 본문·URL·첨부·발신자 통합 위험도·태그 산출 |
|  | analyze_urls(urls) | list[dict] | Public | URL 리스트 상세 리포트 생성 |

---

#### SMSParser Class

| Class | SMSParser |
|-------|-----------|
| Description | 문자 본문에서 URL·숫자·명령형 문구·개인정보 요구 패턴 추출 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | parse(text) | dict | Public | urls, phones, imperative_phrases, pii_requests 반환 |

---

#### URLExtractor Class

| Class | URLExtractor |
|-------|--------------|
| Description | 텍스트에서 URL 추출 후 정규화·디코딩·중복 제거 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | extract(text) | list[str] | Public | 정규화된 URL 리스트 반환 |

---

#### ShortLinkExpander Class

| Class | ShortLinkExpander |
|-------|-------------------|
| Description | 단축 URL·리다이렉트 체인 해제 및 최종 도착지 확인 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | expand(url) | dict | Public | {final_url, hops, suspicious_redirect} 반환 |

---

#### URLFeatureExtractor Class

| Class | URLFeatureExtractor |
|-------|---------------------|
| Description | URL·도메인·SSL·WHOIS 기반 특징치 계산 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | extract(url) | dict | Public | 길이, 서브도메인 수, IP 도메인 여부, 특수문자, TLD, SSL, WHOIS age 등 |

---

#### DomainReputation Class

| Class | DomainReputation |
|-------|------------------|
| Description | 블랙리스트·위협 인텔·신고 이력 기반 도메인 평판 점수 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | score(domain) | dict | Public | {reputation, sources, listed} 반환 |

---

#### AttachmentScanner Class

| Class | AttachmentScanner |
|-------|-------------------|
| Description | 첨부 파일(압축·APK·PDF·HTML 등) 정적 분석·시그니처 탐지 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | scan(path_or_bytes) | dict | Public | 파일 타입, 매크로·스크립트·권한, 해시·시그니처 결과 |

---

#### SenderReputation Class

| Class | SenderReputation |
|-------|------------------|
| Description | 발신 번호 형식·스푸핑 패턴·사칭 키워드 평가 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | evaluate(number, text) | dict | Public | {spoofing_pattern, brand_impersonation, score} 반환 |

---

#### URLClassifier Class

| Class | URLClassifier |
|-------|---------------|
| Description | ML 기반 URL 위험 확률 산출(특징 입력 → 확률·라벨) |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | predict(features) | float | Public | 피싱 확률(0~1) |
|  | load_from(path) | None | Public | 학습 가중치 로드 |

---

#### RuleBasedSmishingDetector Class

| Class | RuleBasedSmishingDetector |
|-------|---------------------------|
| Description | 규칙·시그니처 기반 스미싱 탐지(은행·택배·정부 사칭 등) |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | detect(evidence) | dict | Public | 매칭 규칙, 태그, 가중치 반환 |

---

#### SmishingRequest Class

| Class | SmishingRequest |
|-------|-----------------|
| Description | 스미싱 분석 요청 DTO(본문·URL·첨부·발신자) |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | text | str | Public | 문자 본문 |
|  | urls | Optional[list[str]] | Public | 본문 외 별도 전달된 URL 목록(선택) |
|  | sender | Optional[str] | Public | 발신 번호(선택) |
|  | attachment_ids | Optional[list[str]] | Public | 업로드된 첨부 파일 ID 목록(선택) |

---

#### SmishingResponse Class

| Class | SmishingResponse |
|-------|------------------|
| Description | 스미싱 종합 분석 응답 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | risk_score | float | Public | 총합 위험도(0~1) |
|  | tags | list[str] | Public | 탐지 태그(예: "단축 URL", "택배 사칭", "개인정보 요구") |
|  | url_reports | list[URLAnalysisResponse] | Public | URL별 상세 결과 |
|  | attachment_findings | list[dict] | Public | 첨부 스캔 결과 |
|  | sender_report | dict | Public | 발신자 평가 결과 |
|  | evidence | dict | Public | 근거(규칙 매칭, 특징치, 리다이렉트 체인 등) |

---

#### URLAnalysisRequest Class

| Class | URLAnalysisRequest |
|-------|--------------------|
| Description | 단일 URL 분석 요청 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | url | str | Public | 분석 대상 URL |

---

#### URLBatchRequest Class

| Class | URLBatchRequest |
|-------|-----------------|
| Description | 복수 URL 일괄 분석 요청 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | urls | list[str] | Public | 분석 대상 URL 배열 |

---

#### URLAnalysisResponse Class

| Class | URLAnalysisResponse |
|-------|---------------------|
| Description | URL 단위 상세 분석 응답 DTO |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | input_url | str | Public | 입력된 원본 URL |
|  | final_url | str | Public | 리다이렉트 최종 URL |
|  | hops | int | Public | 리다이렉트 단계 수 |
|  | reputation | float | Public | 도메인 평판 점수(0~1, 높을수록 안전) |
|  | phishing_prob | float | Public | ML 예측 확률(0~1, 높을수록 위험) |
|  | features | dict | Public | URL·도메인·WHOIS·SSL 특징치 요약 |
|  | matched_rules | list[str] | Public | 매칭된 규칙 태그 |
|  | verdict | str | Public | "malicious" , "suspicious" , "benign" |

---

**주요 관계:**
- Router들은 해당 Service를 의존성 주입 방식으로 사용한다
- 모든 Router는 User 모델과 연결되어 인증된 사용자의 요청을 처리한다
- History 모델들은 User와 1:N 관계로 각 사용자의 탐지 이력을 저장한다
- External Systems는 <<external system>> 스테레오타입으로 표시되어 외부 API 의존성을 명확히 표현한다

### 3.2 안드로이드 클라이언트 클래스 다이어그램

![### 3.2 Android Client Class Diagram](image/class_diagram_android.png)

#### 설명

본 다이어그램은 안드로이드 클라이언트의 주요 화면, 뷰모델, 리포지토리, 네트워크, 유틸리티, 리시버 클래스를 포함한다.  
앱은 MVVM(Model-View-ViewModel) 아키텍처를 기반으로 구성되며,  
**문서 이미지 위조 탐지**, **보이스피싱 탐지(실시간/녹음)**, **문자 스미싱 탐지** 기능을 수행한다.  
사용자는 로그인 후 각 기능 화면으로 이동해 서버(FastAPI)와 통신하며 탐지 결과를 확인할 수 있다.

---

#### MainActivity Class

| Class | MainActivity |
|-------|--------------|
| Description | 애플리케이션의 진입점으로, 네비게이션과 전역 UI 상태를 관리하는 Activity 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | navController | NavController | Private | Jetpack Compose 네비게이션 컨트롤러 |
|  | apiService | ApiService | Private | 서버 API 호출용 인스턴스 |
|  | isLoggedIn | Boolean | Private | 로그인 상태 여부 |
| Operations | onCreate(savedInstanceState) | void | Public | Activity 초기화 및 네비게이션 구성 |
|  | navigateTo(screen) | void | Public | 선택한 기능 화면으로 이동 |
|  | showToast(message) | void | Public | 간단한 사용자 메시지 표시 |

---

#### LoginScreen Class

| Class | LoginScreen |
|-------|-------------|
| Description | 전화번호 및 비밀번호를 입력받아 로그인 요청을 수행하는 화면 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | viewModel | AuthViewModel | Private | 로그인 로직을 관리하는 뷰모델 |
| Operations | onLoginClick() | void | Public | 로그인 버튼 클릭 시 호출 |
|  | navigateToRegister() | void | Public | 회원가입 화면으로 이동 |

---

#### RegisterScreen Class

| Class | RegisterScreen |
|-------|----------------|
| Description | 회원 정보를 입력받아 서버에 가입 요청을 보내는 화면 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | viewModel | AuthViewModel | Private | 회원가입 로직 담당 뷰모델 |
| Operations | onRegisterClick() | void | Public | 회원가입 버튼 클릭 시 호출 |
|  | validateInput() | Boolean | Private | 입력값 검증 수행 |

---

#### MainScreen Class

| Class | MainScreen |
|-------|-------------|
| Description | 로그인 이후 메인 메뉴 화면으로, 세 가지 기능(문서, 통화, 문자)을 선택할 수 있음 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | navController | NavController | Private | 각 기능 화면으로의 이동 제어 |
| Operations | onDocumentClick() | void | Public | 문서 탐지 화면으로 이동 |
|  | onCallClick() | void | Public | 보이스피싱 탐지 화면으로 이동 |
|  | onSmsClick() | void | Public | 문자 스미싱 탐지 화면으로 이동 |

---

#### AnalysisScreen Class

| Class | AnalysisScreen |
|-------|----------------|
| Description | 문서 이미지 기반 위조 탐지 화면. 사용자가 이미지를 선택하거나 촬영하여 서버로 업로드함 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | viewModel | AnalysisViewModel | Private | 이미지 분석 로직 담당 |
|  | selectedBitmap | Bitmap | Private | 사용자가 선택한 문서 이미지 |
| Operations | onSelectImage() | void | Public | 갤러리/카메라 이미지 선택 |
|  | onAnalyzeClick() | void | Public | 이미지 업로드 및 분석 요청 |
|  | showResultDialog(result) | void | Public | 분석 결과 팝업 표시 |

---

#### RealtimeScreen Class

| Class | RealtimeScreen |
|-------|----------------|
| Description | 실시간 통화 중 보이스피싱 탐지를 수행하는 화면. WebSocket 기반 오디오 스트리밍을 수행함 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | viewModel | RealtimeViewModel | Private | 실시간 탐지 로직 담당 |
|  | notificationHelper | NotificationHelper | Private | 탐지 결과 알림 표시 도우미 |
| Operations | onStartCallDetection() | void | Public | 통화 수신 시 탐지 시작 |
|  | onStopCallDetection() | void | Public | 탐지 종료 |
|  | showPhishingAlert(level) | void | Public | 위험도에 따른 알림 표시 |

---

#### SmsScreen Class

| Class | SmsScreen |
|-------|-----------|
| Description | 수신된 문자 내용을 분석 요청으로 변환하여 스미싱 탐지를 수행하는 화면 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | viewModel | SmsViewModel | Private | 문자 탐지 로직 담당 |
|  | lastMessage | String | Private | 최근 수신 문자 내용 |
| Operations | onSmsAnalyzeClick() | void | Public | 서버에 문자 내용 전송 |
|  | showSmsResult(result) | void | Public | 분석 결과 UI 표시 |

---

#### AuthViewModel Class

| Class | AuthViewModel |
|-------|----------------|
| Description | 로그인/회원가입 로직을 관리하는 ViewModel 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | repository | AuthRepository | Private | 인증 관련 API 호출 객체 |
| Operations | login(phone, password) | LiveData&lt;AuthResponse&gt; | Public | 로그인 요청 및 응답 처리 |
|  | register(userData) | LiveData&lt;AuthResponse&gt; | Public | 회원가입 요청 처리 |

---

#### AnalysisViewModel Class

| Class | AnalysisViewModel |
|-------|-------------------|
| Description | 문서 이미지 업로드 및 위조 탐지 결과를 관리하는 ViewModel 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | repository | AnalysisRepository | Private | 문서 분석용 API 호출 객체 |
|  | result | LiveData&lt;AnalysisResponse&gt; | Public | 분석 결과 LiveData |
| Operations | analyzeImage(bitmap) | void | Public | 이미지 업로드 및 서버 요청 |
|  | postResult(response) | void | Private | 응답 결과 LiveData에 반영 |

---

#### RealtimeViewModel Class

| Class | RealtimeViewModel |
|-------|-------------------|
| Description | 실시간 보이스피싱 탐지 로직을 관리하는 ViewModel 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | repository | RealtimeRepository | Private | 오디오 스트리밍 API 호출 객체 |
|  | alertLevel | LiveData&lt;Int&gt; | Public | 위험도 감지 수준 LiveData |
| Operations | startRealtimeStream() | void | Public | 서버와 WebSocket 연결 시작 |
|  | stopRealtimeStream() | void | Public | 연결 종료 |

---

#### SmsViewModel Class

| Class | SmsViewModel |
|-------|---------------|
| Description | 문자 스미싱 탐지 결과를 관리하는 ViewModel 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | repository | SmsRepository | Private | 문자 분석 API 호출 객체 |
|  | result | LiveData&lt;SmsResponse&gt; | Public | 문자 분석 결과 LiveData |
| Operations | detectSmsContent(text) | void | Public | 텍스트 기반 탐지 요청 |
|  | postResult(response) | void | Private | 탐지 결과 반영 |

---

#### ApiService Class

| Class | ApiService |
|-------|------------|
| Description | 서버와 통신하기 위한 Retrofit 인터페이스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | login(request) | Call&lt;AuthResponse&gt; | Public | 로그인 요청 |
|  | register(request) | Call&lt;AuthResponse&gt; | Public | 회원가입 요청 |
|  | analyzeDocument(file) | Call&lt;AnalysisResponse&gt; | Public | 문서 이미지 분석 요청 |
|  | analyzeVoice(file) | Call&lt;VoiceResponse&gt; | Public | 보이스피싱 분석 요청 |
|  | analyzeSms(text) | Call&lt;SmsResponse&gt; | Public | 문자 분석 요청 |

---

#### RetrofitClient Class

| Class | RetrofitClient |
|-------|----------------|
| Description | Retrofit 인스턴스를 생성하고 ApiService를 제공하는 싱글톤 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | BASE_URL | String | Private | 서버 기본 URL |
|  | retrofit | Retrofit | Private | Retrofit 인스턴스 |
| Operations | getInstance() | ApiService | Public | ApiService 구현체 반환 |

---

#### FileUtils Class

| Class | FileUtils |
|-------|-----------|
| Description | 이미지 및 오디오 파일을 서버 업로드용 Multipart 형태로 변환하는 유틸리티 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | bitmapToMultipart(bitmap) | MultipartBody.Part | Public | Bitmap을 JPEG 포맷으로 변환 |
|  | audioToMultipart(file) | MultipartBody.Part | Public | Audio 파일을 MultipartBody로 변환 |

---

#### NotificationHelper Class

| Class | NotificationHelper |
|-------|--------------------|
| Description | 탐지 결과에 따른 알림(Notification)을 표시하는 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | showNotification(title, message) | void | Public | 알림 생성 및 표시 |
|  | cancelNotification(id) | void | Public | 지정 ID 알림 취소 |

---

#### SmsReceiver Class

| Class | SmsReceiver |
|-------|--------------|
| Description | 문자 수신 이벤트를 감지하고 탐지 요청을 트리거하는 리시버 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | onReceive(context, intent) | void | Public | 문자 수신 브로드캐스트 처리 |
|  | extractSmsContent(intent) | String | Private | 문자 본문 추출 및 전달 |

---

#### CallReceiver Class

| Class | CallReceiver |
|-------|--------------|
| Description | 전화 수신 및 종료 이벤트를 감지하여 보이스피싱 탐지를 트리거하는 리시버 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | onReceive(context, intent) | void | Public | 통화 상태 감지 |
|  | handleIncomingCall(number) | void | Private | 발신자 정보 처리 및 알림 표시 |

---

#### SaltKeeper Class

| Class | SaltKeeper |
|-------|-------------|
| Description | 문자열 암호화 및 솔트 생성 기능을 제공하는 보안 유틸리티 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | generateSalt() | String | Public | 임의의 솔트 문자열 생성 |
|  | encryptText(text) | String | Public | 텍스트 암호화 수행 |

---

#### Sanitizer Class

| Class | Sanitizer |
|-------|------------|
| Description | 입력 텍스트의 전처리 및 보안 정제를 수행하는 유틸리티 클래스 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | cleanText(input) | String | Public | 특수문자/공백 제거 및 정규화 수행 |

---

#### 클래스 간 주요 관계

- `MainActivity`는 `AuthViewModel`, `AnalysisViewModel`, `RealtimeViewModel`, `SmsViewModel`을 사용한다.  
- 각 `ViewModel`은 대응되는 `Repository`와 연결된다.  
- `Repository`는 `ApiService`를 통해 서버와 통신한다.  
- `ApiService` 인스턴스는 `RetrofitClient`에서 생성된다.  
- `SmsReceiver`와 `CallReceiver`는 시스템 이벤트를 감지하여 대응되는 ViewModel을 호출한다.  
- `NotificationHelper`는 탐지 결과를 사용자에게 즉시 알린다.



## 4. Sequence Diagrams

이 장은 Sequence diagram과 그 설명을 제공한다. 고려 사항은 다음과 같다.

1. 모든 Sequence diagram은 특정 Use case와 대응된다.
2. 모든 Sequence diagram은 특정 Use case의 Main / Extension success scenario의 흐름을 따른다.
3. 메시지를 주고받는 클래스들은 모두 class diagram에서 연결 관계가 있다.
4. Sequence diagram의 메시지는 Class diagram의 operation 혹은 attributes이거나 개념적인 과정/결과를 나타낸다.
5. 흐름 묘사는 Sequence diagram을 그대로 글에 옮겨 적은 것으로, 일치성과 모순성을 검사하기 위해 사용됨과 동시에 diagram으로 이해되지 않는 부분을 해소하는데 사용한다.

### 4.1 소셜 회원가입 (Social Sign-Up with OAuth)

![### 4.1 소셜 회원가입 (Social Sign-Up with OAuth)](image/sequence_diagram_social_signup_oauth.png)

**관련 Use Case**: Use Case #2 - 소셜 회원가입 (Sign-Up with OAuth)

**흐름 설명**:
1. **OAuth 인증 시작**: 사용자가 Android Client에서 소셜 회원가입 버튼을 클릭하면 클라이언트는 선택한 OAuth Provider(Google/Naver/Kakao)의 인증 URL을 요청
2. **리다이렉션**: OAuth Provider가 authorization URL을 반환하고, 클라이언트는 웹 브라우저를 통해 해당 URL로 사용자를 리다이렉트
3. **사용자 인증**: 사용자가 OAuth Provider의 로그인 페이지에서 인증 정보를 입력하고 권한 동의
4. **Authorization Code 발급**: 인증 성공 시 OAuth Provider가 authorization code를 발급하여 콜백 URL로 전달
5. **토큰 교환**: Android Client가 authorization code를 FastAPI 서버의 AuthRouter로 전달하고, AuthRouter는 이를 OAuth Provider로 보내 access token 및 refresh token과 교환
6. **사용자 정보 조회**: AuthRouter가 받은 access token으로 OAuth Provider에게 사용자 프로필 정보(이메일, 이름 등)를 요청
7. **계정 확인 및 생성**:
   - AuthRouter는 받은 OAuth 정보(provider, oauth_id)로 데이터베이스에서 기존 계정 조회
   - 계정이 없으면 새로운 User 레코드 생성 (oauth_provider, oauth_id 필드 포함)
   - 계정이 이미 존재하면 에러 반환 (중복 가입 방지)
8. **JWT 토큰 발급**: 새로 생성된 사용자 정보를 기반으로 JWT access token 및 refresh token 생성
9. **응답 반환**: 생성된 JWT 토큰들을 Android Client로 반환하여 회원가입 완료

**성공 조건**:
- OAuth 인증이 성공적으로 완료됨
- 해당 OAuth 계정으로 이미 가입된 사용자가 없음
- 모든 API 호출이 정상적으로 완료됨 (HTTP 200 OK)

### 4.2 소셜 로그인 (Social Login with OAuth)

![### 4.2 소셜 로그인 (Social Login with OAuth)](image/sequence_diagram_social_login_oauth.png)

**관련 Use Case**: Use Case #4 - 소셜 로그인 (Social Login with OAuth 2.0)

**흐름 설명**:
1. **OAuth 인증 시작**: 사용자가 소셜 로그인 버튼 클릭 시 클라이언트가 OAuth Provider에게 인증 URL 요청
2. **사용자 인증 및 권한 동의**: OAuth Provider의 로그인 페이지에서 사용자가 인증 정보 입력 및 앱 권한 동의
3. **Authorization Code 획득**: 인증 성공 시 OAuth Provider가 authorization code를 콜백 URL을 통해 전달
4. **토큰 교환 및 사용자 정보 조회**:
   - Android Client가 authorization code를 FastAPI 서버로 전달
   - AuthRouter가 OAuth Provider와 토큰 교환 수행
   - 받은 access token으로 사용자 프로필 정보 조회
5. **계정 확인 및 인증**:
   - AuthRouter가 OAuth 정보(provider, oauth_id)로 데이터베이스에서 기존 계정 조회
   - 계정이 존재하면 인증 성공, 없으면 에러 반환 (회원가입 필요)
6. **JWT 토큰 발급**: 인증된 사용자 정보로 새로운 JWT access token 및 refresh token 생성
7. **로그인 완료**: JWT 토큰들을 클라이언트로 반환하여 로그인 세션 시작

**4.1과의 차이점**:
- 4.1(회원가입)은 계정이 없을 때 새로 생성, 4.2(로그인)는 기존 계정이 반드시 존재해야 함
- 로그인은 추가 정보 입력 없이 OAuth 인증만으로 완료됨

### 4.3 실시간 통화 보이스피싱 탐지 (WebSocket + gRPC)

![### 4.3 실시간 통화 보이스피싱 탐지 (WebSocket + gRPC)](image/sequence_diagram_realtime_voice_phishing_websocket_grpc.png)

**관련 Use Case**: Use Case #7 - 실시간 통화 보이스피싱 탐지 (WebSocket + gRPC)

**흐름 설명**:
1. **WebSocket 연결 수립**:
   - 사용자가 실시간 통화 모니터링 시작
   - Android Client가 FastAPI 서버의 `/ws/stream` 엔드포인트로 WebSocket 연결 요청
   - TranscribeRouter가 연결을 수락하고 WebSocketManager에 새 세션 등록
   - 연결 성공 메시지를 클라이언트로 전송

2. **음성 스트리밍 시작 (병렬 처리)**:
   - Android Client가 마이크로부터 실시간 오디오 청크를 캡처하여 WebSocket을 통해 지속적으로 전송
   - 서버는 받은 오디오 데이터를 두 가지 경로로 동시 처리:

3. **경로 1: 즉시 분석 (Immediate Analysis)**:
   - TranscribeRouter가 CLOVA Speech STT gRPC 스트림으로 오디오 청크 전송
   - CLOVA STT가 실시간으로 음성을 텍스트로 변환하여 부분 결과(partial result) 반환
   - 변환된 텍스트를 VoicePhishingDetector의 키워드 기반 즉시 탐지 모듈로 전달
   - 위험 키워드 발견 시 즉시 경고 메시지를 WebSocket을 통해 클라이언트로 전송

4. **경로 2: 종합 분석 (Comprehensive Analysis - 백그라운드)**:
   - 동일한 오디오 청크를 버퍼에 누적
   - 일정 주기(예: 5초)마다 누적된 오디오를 CLOVA STT REST API로 전송하여 완전한 텍스트 변환
   - 변환된 전체 텍스트를 KoBERT 딥러닝 모델로 분석하여 보이스피싱 확률 계산
   - 위험도가 임계값을 초과하면 상세한 분석 결과를 WebSocket으로 전송

5. **실시간 피드백**:
   - 클라이언트는 WebSocket을 통해 즉시 분석 결과와 종합 분석 결과를 모두 수신
   - 화면에 실시간으로 경고 표시 및 위험도 시각화

6. **스트리밍 종료**:
   - 사용자가 통화 종료 또는 모니터링 중단
   - 클라이언트가 종료 메시지 전송
   - TranscribeRouter가 gRPC 스트림 종료 및 WebSocket 연결 해제
   - WebSocketManager에서 세션 정리
   - 최종 분석 결과를 VoicePhishingHistory에 저장

**핵심 특징**:
- **하이브리드 분석**: 빠른 키워드 기반 탐지 + 정확한 ML 기반 탐지를 동시에 수행
- **저지연**: WebSocket 양방향 통신으로 실시간 피드백 제공
- **확장성**: gRPC 스트리밍으로 효율적인 대용량 오디오 처리

### 4.4 통화 녹음본 보이스피싱 탐지

![### 4.4 통화 녹음본 보이스피싱 탐지](image/sequence_diagram_voice_recording_analysis.png)

**관련 Use Case**: Use Case #6 - 통화 녹음본 보이스피싱 탐지

**흐름 설명**:
1. **파일 업로드**:
   - 사용자가 Android Client에서 이미 녹음된 통화 파일 선택 (WAV, MP3, M4A 등)
   - 클라이언트가 HTTP POST 요청으로 오디오 파일을 `/voice-phishing/analyze-audio` 엔드포인트로 전송
   - VoicePhishingRouter가 요청 수신 및 파일 유효성 검증 (크기, 형식, 길이 등)

2. **음성-텍스트 변환**:
   - VoicePhishingRouter가 업로드된 오디오 파일을 CLOVA Speech STT REST API로 전송
   - CLOVA STT가 음성을 분석하여 전체 대화 내용을 텍스트로 변환
   - 변환된 텍스트와 타임스탬프 정보를 반환

3. **보이스피싱 탐지 분석**:
   - VoicePhishingRouter가 변환된 텍스트를 VoicePhishingDetector로 전달
   - VoicePhishingDetector가 두 단계 분석 수행:
     - **키워드 분석**: 보이스피싱 관련 위험 키워드 검출 (예: "금융감독원", "계좌이체", "개인정보" 등)
     - **딥러닝 분석**: KoBERT 모델로 텍스트의 문맥과 패턴을 분석하여 보이스피싱 확률 계산 (0.0 ~ 1.0)
   - 분석 결과 생성:
     - `is_phishing`: 보이스피싱 여부 (Boolean)
     - `confidence`: 탐지 신뢰도 (0.0 ~ 1.0)
     - `risk_level`: 위험 수준 (LOW/MEDIUM/HIGH/CRITICAL)
     - `detected_keywords`: 발견된 위험 키워드 리스트
     - `analysis_detail`: 상세 분석 내용

4. **결과 저장 및 반환**:
   - VoicePhishingRouter가 분석 결과를 VoicePhishingHistory 테이블에 저장
   - 히스토리 레코드 생성 (user_id, audio_file_path, transcribed_text, analysis_result, created_at)
   - 분석 결과를 JSON 형식으로 Android Client에 반환

5. **결과 표시**:
   - Android Client가 분석 결과를 화면에 표시
   - 위험도에 따라 색상 코딩된 경고 메시지 및 상세 정보 제공
   - 사용자가 분석 내역을 히스토리에서 다시 확인 가능

**4.3과의 차이점**:
- 4.3(실시간)은 WebSocket + gRPC 스트리밍 방식, 4.4(녹음본)는 REST API 방식
- 4.3은 즉시 분석과 종합 분석이 병렬로 진행, 4.4는 순차적으로 STT → 분석 진행
- 4.3은 통화 중 실시간 경고, 4.4는 통화 후 사후 분석

### 4.5 문서 위조 탐지

![### 4.5 문서 위조 탐지](image/sequence_diagram_document_analyzer.png)

**관련 Use Case:** Use Case #10 - 문서 위조 탐지

**흐름 설명:**

1.  **이미지 업로드:**
    *   사용자가 Android Client를 통해 분석을 원하는 문서 이미지를 한 장 또는 여러 장 선택하여 업로드합니다.
    *   클라이언트는 `multipart/form-data` 형식으로 이미지 파일들을 FastAPI 서버의 `/process-request` 엔드포인트로 전송합니다.

2.  **조건부 시나리오 분기:**
    *   `DocumentRouter`는 업로드된 이미지의 수에 따라 두 가지 시나리오로 분기하여 분석을 처리합니다.

3.  **시나리오 A: 단일 이미지 분석 (`alt: file = 1`)**
    *   `DocumentRouter`는 한 장의 이미지를 저장한 후, `DocumentAnalyzerService`의 `analyze_document` 메소드를 호출합니다.
    *   `DocumentAnalyzerService`는 직인 탐지, OCR, 키워드 분석, 레이아웃 분석을 순차적으로 수행합니다.
    *   각 분석 결과를 가중합하여 최종 위험도(`final_risk`)를 계산하고, 단일 분석 결과를 JSON **객체** 형식으로 라우터에 반환합니다.

4.  **시나리오 B: 복수 이미지 분석 (`else: files > 1`)**
    *   `DocumentRouter`는 업로드된 모든 이미지를 반복문(`loop`)으로 순회합니다.
    *   각 반복마다 이미지를 저장하고, `DocumentAnalyzerService`를 호출하여 개별 문서 분석을 수행합니다.
    *   모든 개별 분석이 완료되면, 라우터는 각 문서의 위험도를 모두 취합하고 평균 등을 계산하여 전체적인 종합 위험도(`total_risk`)를 산출합니다.
    *   최종 결과를 JSON **배열** 형식으로 클라이언트에 반환합니다.

5.  **결과 표시:**
    *   Android Client는 서버로부터 수신한 단일 결과(JSON 객체) 또는 복수 결과(JSON 배열)를 기반으로, 사용자에게 각 문서의 상세 분석 내용과 종합적인 위험 등급을 시각화하여 보여줍니다.

**핵심 특징:**

*   **조건부 분기 및 확장성 (Conditional Branching & Scalability):** `alt` 구문을 사용하여 업로드된 파일의 수에 따라 단일 분석과 복수 분석 로직을 유연하게 분기 처리합니다. 이는 향후 더 복잡한 조건이 추가되더라도 쉽게 확장할 수 있는 구조적 이점을 제공합니다.
*   **일괄 처리 및 위험도 종합 (Batch Processing & Risk Aggregation):** 여러 개의 문서를 한 번의 요청으로 일괄 처리(`loop`)하고, 각 문서의 분석 결과를 종합하여 단일 위험 지표(`total_risk`)로 도출하는 기능을 제공하여 사용자의 편의성을 극대화합니다.
*   **다각적 종합 분석 (Multi-layered Comprehensive Analysis):** 단일, 복수 시나리오 모두에서 텍스트(OCR, 키워드)뿐만 아니라 시각적 요소(직인, 레이아웃)까지 다층적으로 분석하여, 위조·조작된 문서의 탐지 정확도를 높입니다.

### 4.6 스미싱 탐지

![### 4.6 스미싱 탐지](image/sequence_diagram_smishing_analyzer.png)

**관련 Use Case:** Use Case #11 - 스미싱 탐지

**흐름 설명:**

1.  **분석 요청:**
    *   사용자가 Android Client에 의심스러운 전체 문자 메시지를 입력합니다.
    *   클라이언트는 메시지 내용을 JSON 형식(`sms_content`)으로 래핑하여 FastAPI 서버의 `/api/smishing/analyze` 엔드포인트로 `POST` 요청을 전송합니다.

2.  **텍스트 심층 분석 및 URL 추출:**
    *   요청을 수신한 `SmishingRouter`는 분석을 총괄하는 `SmishingDetector`의 `analyze_sms` 메소드를 호출합니다.
    *   `SmishingDetector`는 전달받은 메시지 텍스트 자체에 대한 심층 분석을 수행합니다. 이 과정에는 URL 추출(`extract_urls`), 긴급성 패턴 분석(`analyze_urgency_patterns`), 발신자 번호 변작 확인(`check_sender_spoofing`), 피싱 키워드 탐지(`detect_keywords`), 그리고 KoBERT 모델을 이용한 문맥 기반의 ML 분석(`predict_smishing_by_ml`)이 포함됩니다.

3.  **URL 하이브리드 분석 (병렬 처리):**
    *   텍스트 분석 과정에서 URL이 추출된 경우, `SmishingDetector`는 URL 분석 전문 모듈인 `PhishingSiteDetector`를 호출하여 추가 분석을 요청합니다. 이 분석은 두 가지 방법으로 동시에(병렬적으로) 수행됩니다.
        *   **즉시 분석 (Immediate Analysis):** URL 자체의 패턴, 키워드, 구조 등을 분석하여 신속하게 위험도를 1차 판단합니다 (`detect_immediate`).
        *   **종합 분석 (Comprehensive Analysis):** 웹 페이지의 HTML 콘텐츠를 크롤링하고, PhishTank DB를 조회하며, 사전 학습된 ML 모델을 통해 피싱 여부를 정밀하게 예측합니다 (`detect_comprehensive`).

4.  **결과 종합 및 최종 위험도 산출:**
    *   모든 분석이 완료되면, `SmishingDetector`는 텍스트 심층 분석 결과와 URL 하이브리드 분석 결과를 종합합니다.
    *   이를 통해 복합적인 스미싱 공격에 대한 최종 위험도를 산출하고 종합 리포트를 생성합니다.

5.  **응답 반환 및 결과 표시:**
    *   `SmishingDetector`는 최종 분석 결과를 `SmishingRouter`로 반환하고, 라우터는 이를 `SmishingAnalysisResponse` JSON 객체로 포장하여 Android Client에 응답합니다.
    *   클라이언트는 이 종합적인 결과를 사용자에게 명확한 경고와 함께 시각적으로 제시합니다.

**핵심 특징:**

*   **입체적 하이브리드 분석 (Multi-layered Hybrid Analysis):** 문자 내용의 맥락(긴급성, 문맥)과 URL의 기술적 위험도를 모두 평가하는 입체적인 분석을 수행하여 탐지 정확도를 극대화합니다.
*   **역할 분리를 통한 모듈화 설계 (Modular Design with Separation of Concerns):** `SmishingDetector`는 전체 흐름을 제어하고 텍스트 분석을 담당하며, 복잡한 URL 분석은 `PhishingSiteDetector`에 위임하여 코드의 유지보수성과 확장성을 높입니다.
*   **병렬 처리를 통한 응답 시간 최적화 (Optimized Response Time via Parallel Processing):** 신속한 URL 패턴 분석과 시간이 소요되는 콘텐츠 기반 종합 분석을 병렬로 동시에 처리하여, 정확도를 희생하지 않으면서도 사용자에게 최대한 빠른 분석 결과를 제공합니다.

## 5. State Machine Diagram

이 장은 시스템 전체의 동작을 표현하는 통합 State machine diagram과 그 설명을 제공한다.

본 시스템은 두 가지 주요 프로세스가 통합된 하나의 State machine으로 구성된다.
1. **OAuth 인증 프로세스**: 사용자 인증 및 로그인의 전체 생명주기를 나타낸다
2. **실시간 음성 스트리밍 프로세스**: WebSocket 기반 통화 모니터링의 전체 생명주기를 나타낸다

State machine diagram은 다음과 같은 특징을 가진다.
- 모든 상태는 명확한 진입 조건과 이벤트를 가진다
- 상태 전이는 특정 Use case의 시나리오를 따른다
- 복합 상태와 병렬 상태를 활용하여 복잡한 프로세스를 표현한다
- 오류 처리 및 재시도 메커니즘을 명확히 나타낸다

![State Machine Diagram](image/statemachine_diagram_unified_system.png)

### 상태 전이 설명

본 다이어그램은 보이스피싱 탐지 시스템의 전체 생명주기를 하나의 통합된 State machine으로 표현한다. 시스템은 최상위 유휴 상태에서 시작하여 두 가지 주요 프로세스 중 하나로 전이된다.

#### OAuth 인증 프로세스

**관련 Use Case**: Use Case #2, #4 - 소셜 회원가입 및 소셜 로그인

사용자 인증 및 로그인의 전체 생명주기를 나타낸다. 총 9개의 상태로 구성된다.

**1. OAuthIdle (OAuth 유휴)**
- OAuth 프로세스의 초기 상태로, 사용자가 아직 인증을 시작하지 않은 상태다
- 이벤트: 소셜 로그인 클릭
- 전이: Redirected 상태로 이동한다

**2. Redirected (리다이렉션)**
- 클라이언트가 OAuth Provider의 인증 URL을 받아 사용자를 리다이렉트한 상태다
- 이벤트: 사용자 인증 완료
- 전이: Authorized 상태로 이동한다

**3. Authorized (권한 부여)**
- 사용자가 OAuth Provider에서 성공적으로 인증하고 권한을 동의한 상태다
- 이벤트: Authorization Code 수신
- 전이: TokenExchange 상태로 이동한다

**4. TokenExchange (토큰 교환)**
- 서버가 OAuth Provider와 토큰 교환을 수행하는 상태다
- 이벤트: Access Token 획득
- 전이: ProfileFetch 상태로 이동한다
- 실패 시: OAuthError 상태로 이동한다

**5. ProfileFetch (프로필 조회)**
- 서버가 access token으로 사용자 프로필 정보를 요청하는 상태다
- 이벤트: 프로필 정보 수신
- 전이: AccountLinking 상태로 이동한다
- 실패 시: OAuthError 상태로 이동한다

**6. AccountLinking (계정 연동)**
- 서버가 OAuth 정보로 데이터베이스에서 계정을 조회하거나 생성하는 상태다
- 이벤트: 계정 연동 성공
- 전이: OAuthComplete 상태로 이동한다
- 실패 시: OAuthError 상태로 이동한다

**7. OAuthComplete (OAuth 완료)**
- OAuth 인증이 성공적으로 완료되고 JWT 토큰이 발급된 상태다
- 종료: 인증 프로세스가 성공적으로 종료되고 시스템은 유휴 상태로 돌아간다

**8. OAuthError (OAuth 오류)**
- OAuth 인증 과정에서 오류가 발생한 상태다
- 이벤트: 재시도
- 전이: OAuthIdle로 돌아가 재시도 가능하다
- 종료: 포기 시 프로세스가 실패로 종료되고 시스템은 유휴 상태로 돌아간다

**9. Cancelled (취소)**
- 사용자가 명시적으로 OAuth 인증을 취소한 상태다
- 종료: 프로세스가 취소로 종료되고 시스템은 유휴 상태로 돌아간다

#### 실시간 음성 스트리밍 프로세스

**관련 Use Case**: Use Case #7 - 실시간 통화 보이스피싱 탐지

WebSocket 기반 실시간 통화 모니터링의 전체 생명주기를 나타낸다. 복합 상태와 병렬 상태를 활용한다.

**1. StreamIdle (스트리밍 유휴)**
- 스트리밍 프로세스의 초기 상태로, WebSocket 연결이 수립되지 않은 상태다
- 이벤트: 모니터링 시작
- 전이: Connecting 상태로 이동한다

**2. Connecting (연결 중)**
- Android Client가 WebSocket 연결을 시도하는 상태다
- 이벤트: WebSocket 연결 성공
- 전이: Active 복합 상태로 이동한다
- 실패 시: StreamError 상태로 이동한다

**3. Active (활성 상태 - 복합 상태)**
- WebSocket 연결이 활성화되어 실시간 처리가 진행되는 상태다
- 3개의 병렬 하위 상태로 구성된다: Streaming, Analyzing, ResultSending

**3-1. Streaming (스트리밍)**
- Android Client가 오디오 청크를 WebSocket으로 지속 전송하는 상태다
- 행동: 오디오 청크를 버퍼에 저장하고 STT 처리 큐에 추가한다

**3-2. Analyzing (분석 - 병렬 처리)**
- 받은 오디오를 분석하는 상태로, 2개의 병렬 하위 상태로 구성된다

**3-2-1. ImmediateAnalysis (즉시 분석)**
- CLOVA STT gRPC 스트림으로 실시간 텍스트 변환 및 키워드 기반 탐지를 수행한다
- 처리 시간: 100~300ms

**3-2-2. ComprehensiveAnalysis (종합 분석)**
- 누적 오디오를 CLOVA STT REST API로 변환하고 KoBERT 모델로 정밀 분석한다
- 처리 시간: 2~5초

**3-3. ResultSending (결과 전송)**
- 분석 결과를 WebSocket으로 클라이언트에 실시간 전송하는 상태다
- 즉시 결과와 종합 결과를 모두 전송한다

**Active 종료**:
- 이벤트: 통화 종료
- 전이: Disconnecting 상태로 이동한다
- 오류 발생 시: StreamError 상태로 이동한다

**4. Disconnecting (연결 해제 중)**
- WebSocket 연결을 정리하고 종료하는 상태다
- 행동: gRPC 스트림 종료, 최종 분석, 결과 저장, WebSocket 닫기
- 이벤트: 정리 완료
- 전이: StreamClosed 상태로 이동한다

**5. StreamClosed (스트리밍 종료)**
- WebSocket 연결이 정상 종료되고 결과가 저장된 상태다
- 종료: 스트리밍 세션이 성공적으로 완료되고 시스템은 유휴 상태로 돌아간다

**6. StreamError (스트리밍 오류)**
- 스트리밍 과정에서 오류가 발생한 상태다
- 이벤트: 재연결
- 전이: Connecting으로 돌아가 재연결을 시도할 수 있다
- 종료: 포기 시 프로세스가 실패로 종료되고 시스템은 유휴 상태로 돌아간다

#### 문서 위조 분석 프로세스

**관련 Use Case**: Use Case #8 - 문서 이미지 업로드 및 위조 분석

사용자가 업로드한 문서 이미지를 순차적으로 분석하여 위조 위험도를 판별하는 생명주기를 나타낸다. 총 9개의 상태로 구성된다.

**1. DocIdle (문서 분석 유휴)**
- 문서 분석 프로세스의 초기 상태이다.
- 이벤트: '문서 분석 요청' (사용자가 이미지 업로드)
- 전이: Uploading 상태로 이동한다.

**2. Uploading (업로드 중)**
- 클라이언트로부터 이미지 파일을 수신하고 서버에 저장하는 상태이다.
- 이벤트: 파일 저장 완료
- 전이: AnalyzingStamp 상태로 이동한다.
- 실패 시: DocAnalysisError 상태로 이동한다. (예: 파일 형식 오류, 저장 실패)

**3. AnalyzingStamp (직인 분석 중)**
- run_stamp_detection() 모듈을 실행하여 직인을 탐지하는 상태이다.
- 이벤트: 직인 분석 완료
- 전이: RunningOCR 상태로 이동한다.
- 실패 시: DocAnalysisError 상태로 이동한다.

**4. RunningOCR (OCR 추출 중)**
- run_ocr() 모듈을 실행하여 문서 내 텍스트를 추출하는 상태이다.
- 이벤트: 텍스트 추출 완료
- 전이: AnalyzingKeywords 상태로 이동한다.
- 실패 시: DocAnalysisError 상태로 이동한다.

**5. AnalyzingKeywords (키워드 분석 중)**
- detect_keywords() 모듈을 실행하여 위험 키워드를 탐지하는 상태이다.
- 이벤트: 키워드 분석 완료
- 전이: AnalyzingLayout 상태로 이동한다.
- 실패 시: DocAnalysisError 상태로 이동한다.

**6. AnalyzingLayout (레이아웃 분석 중)**
- analyze_document_font() 모듈을 실행하여 폰트 및 레이아웃을 분석하는 상태이다.
- 이벤트: 레이아웃 분석 완료
- 전이: CalculatingRisk 상태로 이동한다.
- 실패 시: DocAnalysisError 상태로 이동한다.

**7. CalculatingRisk (위험도 계산 중)**
- 모든 분석 결과를 종합하고 가중치를 적용하여 final_risk를 계산하는 상태이다.
- 이벤트: 계산 완료
- 전이: DocAnalysisComplete 상태로 이동한다.
- 실패 시: DocAnalysisError 상태로 이동한다.

**8. DocAnalysisComplete (문서 분석 완료)**
- 모든 분석이 성공적으로 완료되고 최종 결과가 생성된 상태이다.
- 모든 분석이 성공적으로 완료되고 최종 결과가 생성된 상태이다.

**9. DocAnalysisError (문서 분석 오류)**
- 파일 저장, OCR, 또는 개별 분석 모듈(직인, 키워드 등) 실행 중 오류가 발생한 상태이다.
- 종료: 프로세스가 실패로 종료되고 시스템은 SystemIdle 상태로 돌아간다.

#### 피싱 사이트 탐지 프로세스

**관련 Use Case**: Use Case #9 - 피싱 사이트 탐지

사용자가 요청한 URL의 피싱 위험도를 분석하는 생명주기를 나타낸다. 병렬 상태를 활용한다.

**1. URLIdle (URL 분석 유휴)**
- URL 분석 프로세스의 초기 상태이다.
- 이벤트: 'URL 분석 요청'
- 전이: AnalyzingURL 복합 상태로 이동한다.

**2. AnalyzingURL (URL 분석 중 - 복합 상태)**
- 사용자 요청(URL)을 받아 피싱 위험도를 분석하는 상태이다.
- 2개의 병렬 하위 상태로 구성된다: ImmediateAnalysis, ComprehensiveAnalysis

**2-1. ImmediateAnalysis (즉시 분석)**
- detect_immediate()를 실행하여 URL 문자열 자체의 특징(길이, 키워드 등)을 분석한다.

**2-2. ComprehensiveAnalysis (종합 분석)**
- detect_comprehensive()를 실행하여 DB 조회, HTML 크롤링, ML 모델 예측을 수행한다.

**AnalyzingURL 종료**:
- 이벤트: 분석 완료
- 전이: AggregatingResults 상태로 이동한다.
- 오류 발생 시: URLAnalysisError 상태로 이동한다.

**3. AggregatingResults (결과 종합 중)**
- 즉시 분석과 종합 분석의 결과를 취합하여 최종 AnalysisResponse와 warning_message를 생성하는 상태이다.
- 이벤트: 결과 종합 완료
- 전이: URLAnalysisComplete 상태로 이동한다.

**4. URLAnalysisComplete (URL 분석 완료)**
- URL 분석이 성공적으로 완료되고 JSON 응답이 생성된 상태이다.
- 종료: 프로세스가 성공적으로 종료되고 시스템은 SystemIdle 상태로 돌아간다.

**5. URLAnalysisError (URL 분석 오류)**
- 모델 로드 실패, HTML 크롤링 실패, DB 조회 실패 등 분석 과정에서 오류가 발생한 상태이다.
- 종료: 프로세스가 실패로 종료되고 시스템은 SystemIdle 상태로 돌아간다.

### 시스템 전체 흐름

**SystemIdle (시스템 유휴)**
- 최상위 초기 상태로, 사용자가 시스템에 접속하지 않은 상태다
- 이벤트: 사용자 인증 필요 → OAuth 인증 프로세스로 전이
- 이벤트: 실시간 모니터링 시작 → 실시간 음성 스트리밍 프로세스로 전이
- 이벤트: 문서 분석 요청 → 문서 위조 분석 프로세스로 전이
- 이벤트: URL 분석 요청 → 피싱 사이트 탐지 프로세스로 전이

각 프로세스가 완료되거나 실패하면 시스템은 다시 SystemIdle 상태로 돌아간다. 이를 통해 사용자는 언제든지 인증, 스트리밍, 또는 각종 분석 기능을 새로 시작할 수 있다.

## 7. Implementation Requirements

### H/W platform requirements
- CPU: Qualcomm Snapdragon 660+
- RAM: 4GB+
- Storage: 10GB+

### S/W platform requirements
- OS: Android 7.0 (Nougat)+
- Implementation Language: Kotlin, Python
- Android Studio 2021.1.1+
- JDK 11.0+

### Server platform requirements
- OS: Windows 10/11+
- Python 3.9+
- FastAPI 0.100+
- PostgreSQL 14+

## 8. Glossary

| 이름 | 설명 |
|---|---|
| Anti-Phishing APP | 음성(실시간/녹음본), 텍스트(문자), 이미지(문서) 등 다양한 형태(Multi-modal)의 데이터를 입력받아 복합적인 분석을 통해 피싱 의심 여부를 탐지하고 사용자에게 즉각적인 경고를 제공하는 본 프로젝트의 애플리케이션 명칭. |
| 다각적 종합 분석 | 문서의 위조 여부를 판단할 때, 텍스트 내용뿐만 아니라 직인의 유무, 문서의 전체적인 레이아웃, 사용된 폰트 등 시각적이고 구조적인 요소까지 함께 분석하는 본 프로젝트의 문서 분석 방법론. |
| 위험도 종합기 | 문서 분석 과정에서 분석 모듈로부터 산출된 개별 위험도 점수들을 미리 정의된 가중치에 따라 합산하여, 최종적인 단일 위험도 점수(final_risk)를 계산하는 본 프로젝트 내부의 논리적 컴포넌트 |
| CLOVA Speech | Naver Cloud Platform에서 제공하는 인공지능 음성 인식(STT, Speech-to-Text) 서비스. |
| PhishTank | 전 세계 커뮤니티에 의해 검증되고 공유되는 피싱 사이트 URL의 공개 데이터베이스. |
