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

Introduction

Use Case Analysis

Class Diagrams

Sequence Diagrams

State Machine Diagrams

User Interface Design

Implementation Requirements

Glossary

References

## 1. Introduction

## 2. Use Case Analysis


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

![### 3.1 DB Class Diagram (OAuth 지원 추가)](image/class_diagram_database.png)

#### 설명

본 다이어그램은 피싱 탐지 시스템의 데이터베이스 및 핵심 비즈니스 로직 클래스들을 나타낸다. PDF의 use case에 명시된 OAuth 인증 기능을 지원하기 위해 User 모델에 `oauth_provider`와 `oauth_id` 필드가 추가되었다.

#### User Class

| Class | User |
|-------|------|
| Description | 사용자 정보를 저장하는 데이터베이스 모델 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | id | int | Public | 사용자 고유 식별자 (Primary Key) |
| | username | str | Public | 사용자 이름 (Unique) |
| | email | str | Public | 이메일 주소 (Unique) |
| | hashed_password | str | Public | 해시된 비밀번호 |
| | full_name | str | Public | 사용자 전체 이름 |
| | phone | str | Public | 전화번호 |
| | oauth_provider | str | Public | OAuth 제공자 (google, naver, kakao) |
| | oauth_id | str | Public | OAuth 제공자의 사용자 고유 ID |
| | is_active | bool | Public | 계정 활성화 상태 |
| | created_at | datetime | Public | 계정 생성 일시 |
| | updated_at | datetime | Public | 계정 정보 수정 일시 |

#### VoicePhishingDetector Class

| Class | VoicePhishingDetector |
|-------|------------------------|
| Description | KoBERT 기반으로 보이스피싱을 탐지하는 서비스 클래스다 (싱글톤 패턴) |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | model | BERTClassifier | Private | KoBERT 딥러닝 모델 인스턴스 |
| | tokenizer | BertTokenizer | Private | 텍스트를 토큰으로 변환하는 토크나이저 |
| | device | torch.device | Private | 모델 실행 장치 (CPU/GPU) |
| | keywords | List[str] | Private | 보이스피싱 위험 키워드 리스트 |
| 구분 | Name | Type | Visibility | Description |
| Operations | analyze_text(text) | dict | Public | 텍스트를 분석하여 보이스피싱 여부를 판단하는 함수 |
| | keyword_analysis(text) | dict | Public | 키워드 기반 즉시 분석을 수행하는 함수 |
| | ml_analysis(text) | float | Public | KoBERT 모델로 보이스피싱 확률을 계산하는 함수 |
| | preprocess(text) | List[int] | Private | 텍스트를 모델 입력 형식으로 전처리하는 함수 |

#### AuthRouter Class

| Class | AuthRouter |
|-------|------------|
| Description | 회원가입, 로그인, OAuth 인증을 처리하는 API 라우터 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | prefix | str | Public | 라우터 경로 접두사 ("/auth") |
| 구분 | Name | Type | Visibility | Description |
| Operations | signup(request, db) | UserResponse | Public | 일반 회원가입을 처리하는 엔드포인트 함수 |
| | login(request, db) | TokenResponse | Public | 일반 로그인을 처리하고 JWT 토큰을 발급하는 함수 |
| | social_login(provider, code, db) | TokenResponse | Public | OAuth 소셜 로그인을 처리하는 함수 |
| | refresh_token(request, db) | TokenResponse | Public | 리프레시 토큰으로 새 액세스 토큰을 발급하는 함수 |
| | logout(current_user) | MessageResponse | Public | 로그아웃을 처리하는 함수 |
| | get_me(current_user) | UserResponse | Public | 현재 로그인한 사용자 정보를 반환하는 함수 |

#### OAuthProvider Class

| Class | OAuthProvider |
|-------|----------------|
| Description | Google, Naver, Kakao OAuth 2.0 인증을 처리하는 외부 시스템 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | client_id | str | Public | OAuth 앱 클라이언트 ID |
| | client_secret | str | Private | OAuth 앱 클라이언트 시크릿 |
| | redirect_uri | str | Public | OAuth 콜백 URL |
| | authorization_url | str | Public | 사용자 인증 URL |
| | token_url | str | Public | 토큰 교환 URL |
| | userinfo_url | str | Public | 사용자 정보 조회 URL |
| 구분 | Name | Type | Visibility | Description |
| Operations | get_authorization_url() | str | Public | OAuth 인증 페이지 URL을 생성하여 반환하는 함수 |
| | exchange_code(code) | dict | Public | Authorization code를 액세스 토큰으로 교환하는 함수 |
| | get_user_info(access_token) | dict | Public | 액세스 토큰으로 사용자 프로필 정보를 조회하는 함수 |
| | validate_token(access_token) | bool | Public | 액세스 토큰의 유효성을 검증하는 함수 |

#### WebSocketManager Class

| Class | WebSocketManager |
|-------|-------------------|
| Description | 실시간 음성 스트리밍 세션을 관리하는 매니저 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | active_connections | Dict[str, WebSocket] | Private | 활성 WebSocket 연결을 저장하는 딕셔너리 |
| | sessions | Dict[str, dict] | Private | 세션 정보를 저장하는 딕셔너리 |
| | buffers | Dict[str, bytes] | Private | 오디오 데이터 버퍼 |
| 구분 | Name | Type | Visibility | Description |
| Operations | connect(websocket, session_id) | void | Public | 새 WebSocket 연결을 등록하는 함수 |
| | disconnect(session_id) | void | Public | WebSocket 연결을 해제하고 세션을 정리하는 함수 |
| | send_message(session_id, message) | void | Public | 특정 세션에 메시지를 전송하는 함수 |
| | broadcast(message) | void | Public | 모든 활성 연결에 메시지를 브로드캐스트하는 함수 |
| | add_audio_chunk(session_id, chunk) | void | Public | 오디오 청크를 버퍼에 추가하는 함수 |
| | get_buffer(session_id) | bytes | Public | 세션의 오디오 버퍼를 반환하는 함수 |

**주요 관계:**
- Router들은 해당 Service를 의존성 주입 방식으로 사용한다
- 모든 Router는 User 모델과 연결되어 인증된 사용자의 요청을 처리한다
- History 모델들은 User와 1:N 관계로 각 사용자의 탐지 이력을 저장한다
- External Systems는 <<external system>> 스테레오타입으로 표시되어 외부 API 의존성을 명확히 표현한다

### 3.2 안드로이드 클라이언트 클래스 다이어그램

안드로이드 어플리케이션의 기능별 Class diagram을 작성했다. 각 클래스는 관련된 use case와 연결된다.

![### 3.2 기능별 Class Diagram](image/class_diagram_all_routers.png)

아래 [그림 3-2]는 안드로이드 클라이언트에서 서버와 통신하고 문서 위조를 탐지하기 위한 시스템과 관련된 클래스들을 표현한 기능별 Class diagram이다.

#### MainActivity Class

| Class | MainActivity |
|-------|--------------|
| Description | 안드로이드 앱의 메인 화면을 관리하는 Activity 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | apiService | ApiService | Private | 서버 통신을 위한 API 서비스 인스턴스 |
| | currentBitmap | Bitmap | Private | 사용자가 선택한 현재 이미지를 저장하는 변수 (lateinit) |
| 구분 | Name | Type | Visibility | Description |
| Operations | onCreate(savedInstanceState) | void | Public | Activity 생성 시 초기화를 수행하는 함수 |
| | onImageSelected(uri) | void | Public | 사용자가 갤러리에서 이미지를 선택했을 때 호출되는 함수 |
| | onAnalyzeClicked() | void | Public | 분석 버튼을 클릭했을 때 호출되는 함수 |
| | showResults(result) | void | Public | 분석 결과를 화면에 표시하는 함수 |

#### StampDetector Class

| Class | StampDetector |
|-------|---------------|
| Description | OpenCV를 사용하여 문서 이미지에서 직인을 탐지하는 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | findStampRoi(inputBitmap, context) | DetectionResult | Public | 입력 이미지에서 직인 영역을 찾아 반환하는 함수 |
| | detectRedRegions(mat) | Mat | Private | HSV 색상 공간에서 빨간색 영역을 탐지하는 함수 |
| | findContours(mask) | List&lt;MatOfPoint&gt; | Private | 마스크에서 컨투어를 찾는 함수 |
| | filterContours(contours) | List&lt;Rect&gt; | Private | 컨투어를 필터링하여 직인 후보를 추출하는 함수 |

#### ApiService Class

| Class | ApiService |
|-------|------------|
| Description | Retrofit 기반 네트워크 API 인터페이스를 정의하는 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | analyzeStamp(file) | Call&lt;DetectionResponse&gt; | Public | 직인 분석 요청을 서버로 보내는 함수 |
| | uploadImage(file) | Call&lt;UploadResponse&gt; | Public | 이미지 업로드 요청을 서버로 보내는 함수 |

#### RetrofitClient Class

| Class | RetrofitClient |
|-------|----------------|
| Description | Retrofit 인스턴스를 생성하고 관리하는 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Attribute | BASE_URL | String | Private | 서버의 기본 URL을 저장하는 상수 |
| 구분 | Name | Type | Visibility | Description |
| Operations | getInstance() | ApiService | Public | Retrofit을 설정하고 ApiService 구현체를 반환하는 싱글톤 함수 |

#### FileUtils Class

| Class | FileUtils |
|-------|-----------|
| Description | 파일 및 이미지 처리를 위한 유틸리티 클래스다 |

| 구분 | Name | Type | Visibility | Description |
|------|------|------|------------|-------------|
| Operations | getFileFromUri(context, uri) | File | Public | Android Uri를 File 객체로 변환하는 함수 |
| | createMultipartBody(file) | MultipartBody.Part | Public | 파일을 Retrofit의 멀티파트 요청 형식으로 변환하는 함수 |
| | saveToInternalStorage(bitmap, filename) | String | Public | Bitmap을 앱의 내부 저장소에 저장하고 경로를 반환하는 함수 |

**클래스 간 주요 관계:**
- MainActivity는 ApiService, StampDetector, OcrService, FileUtils를 사용한다
- RetrofitClient는 ApiService 인터페이스를 생성한다
- ApiService는 DetectionResponse와 UploadResponse를 반환한다
- StampDetector는 DetectionResult를 생성한다
- DetectionResult는 BoxDto를 사용하여 서버 응답을 클라이언트 화면 좌표로 변환한다

### 3.3 Key Design Patterns

본 시스템에서 사용된 주요 디자인 패턴:

1. Singleton Pattern
   - `VoicePhishingDetector`, `PhishingSiteDetector`: 무거운 ML 모델 로딩을 한 번만 수행하기 위해 싱글톤 패턴 사용

2. Dependency Injection
   - FastAPI의 `Depends`를 통한 의존성 주입 (데이터베이스 세션, 사용자 인증)

3. Repository Pattern
   - SQLAlchemy ORM을 통한 데이터 접근 계층 추상화

4. Strategy Pattern
   - 분석 방법 선택 (immediate, comprehensive, hybrid)을 위한 전략 패턴

5. Facade Pattern
   - `DocumentService`: 복잡한 문서 분석 과정(OCR, Stamp, Keyword, Layout)을 단일 인터페이스로 제공

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

### 시스템 전체 흐름

**SystemIdle (시스템 유휴)**
- 최상위 초기 상태로, 사용자가 시스템에 접속하지 않은 상태다
- 이벤트: 사용자 인증 필요 → OAuth 인증 프로세스로 전이
- 이벤트: 실시간 모니터링 시작 → 실시간 음성 스트리밍 프로세스로 전이

각 프로세스가 완료되거나 실패하면 시스템은 다시 SystemIdle 상태로 돌아간다. 이를 통해 사용자는 언제든지 인증 또는 스트리밍을 새로 시작할 수 있다

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
