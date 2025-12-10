## 프로젝트 개요

이 프로젝트는 [SRS 문서](./srs.md)를 기반으로 설계되었습니다. 프로젝트의 요구사항, 기능, 상세 설명은 SRS 문서에서 확인하실 수 있습니다.

---

## 문서

- [SRS (Software Requirement Specification)](./srs.md) - 프로젝트 요구사항 문서

### OneUI 기반 안드로이드 14의 음성 권한

| API / 클래스      | 동작 및 제한 사항 |
|-------------------|-------------------|
| **AudioRecord**   | `VOICE_COMMUNICATION`, `VOICE_RECOGNITION`과 함께 사용 시 차단 |
| **VOICE_CALL**    | `start()` 자체가 실패하거나, 음성이 0으로 처리되어 권한이 완전히 차단됨 |
| **MediaRecorder** | 통화 연결 시 자동으로 음성이 0으로 처리되며, 녹음 파일은 생성되지만 빈 파일로 생성됨 |

> 안드로이드 12 이상부터 통화 녹음이 사실상 완전히 차단되며,  
> OneUI 환경에서는 일반 녹음까지 추가로 제한됨.

이로 인해, 앱 내 **실시간 통화 캡처 방식** 대신  
현재는 **통화 종료 후 음성 파일 업로드 방식**으로 기능을 대체하였음.

#### 테스트 환경 (전화 / 문자 공통)

- **발신자 단말**
  - Wi-Fi / 모바일 데이터 OFF  
  - 메세지 플러스, 데이터 기반 통화(VoLTE 등)를 방지하기 위함
  - 위 기능들을 사용하며 실시간 통화 녹음 및 스미싱 분석은 통신사 및 휴대폰 제조사 권한이 필요

- **수신자 단말**
  - 모바일 데이터 ON  
  - 서버와의 WebSocket / HTTP 통신 및 알림 수신용

#### 실시간 스트리밍 테스트 로그

앱 측 로그:

![앱 실시간 스트리밍 로그](./image/app_test_log_streaming.png)

서버 측 로그:

![서버 실시간 스트리밍 로그](./image/servertest_log_streaming.png)

위 로그를 통해 다음을 확인할 수 있음.

- WebSocket 엔드포인트 `/api/transcribe/ws?sr=16000&lang=ko-KR` 로의 연결은 정상적으로 수립되고  
- 서버에서 gRPC 기반 CLOVA STT 세션이 정상적으로 시작되며  
- `recv bytes len=2048` 로그가 주기적으로 출력되는 것으로 보아 **PCM 스트림 수신 자체는 정상 동작**함  

다만, **Android 14 / OneUI의 통화 녹음 차단 정책으로 인해**  
실제 통화 음성이 아닌, **볼륨이 0으로 처리된 오디오 데이터만 전달**되고 있음.

따라서,

> 기능(연결, 스트리밍, 서버 파이프라인)은 정상적으로 동작하지만,  
> **권한 문제로 인해 서버에 수신되는 `volume` 값이 모두 `0.00`이며  
> 실질적인 통화 음성 신호는 전달되지 않는 상태**임을 확인할 수 있음.


<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->
