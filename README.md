> 전체 실행 소스는 [WhisperFlow-Windows-source.zip](WhisperFlow-Windows-source.zip)에 있습니다. 압축을 풀고 그 안의 실행 안내를 따르세요.
> 코드 변경 전체는 [WINDOWS-PATCH.patch](WINDOWS-PATCH.patch)에서 바로 검토할 수 있습니다. 브라우저로 소스와 패치를 공유하는 저장소입니다.

# Jarvis Shared Voice — Windows 한국어 / English

기존 GPT/Codex와 Claude에서 같은 로컬 음성 도구를 호출하는 Windows 확장판입니다.

- **목소리:** 자비스 / 로봇수사대 케이캅스 데커드
- **선택:** 자비스 한국어 / 자비스 English / 데커드 한국어. 영어 데커드는 제공하지 않습니다.
- **공통 연결:** MCP의 `jarvis_status`, `jarvis_speak`, `jarvis_stop`
- **역할 분리:** 기존 GPT·Claude가 명령 처리와 답변을 담당하고 Qwen은 음성 합성·재생을 담당합니다.
- **확인 상태:** 한국어·영어 음성 샘플 생성과 MCP 프로토콜 연결을 검증했습니다. 데커드는 약한 로봇 효과를 기본으로 사용합니다. 기존 사용자 앱의 연결 설정과 원격 배포는 별도 단계입니다.
- **권장 설치:** `Setup-Native-Qwen.cmd`로 공식 llama.cpp CPU 실행 파일과 Qwen GGUF 모델을 설치합니다. 짧은 시험 문장의 전체 실행은 약 12–15초였습니다. 실시간 음성 대화 수준의 지연을 보장하지 않습니다.

설정 안내: [CONNECTIONS.md](CONNECTIONS.md), Windows 실행 안내: [WINDOWS.md](WINDOWS.md).

웹앱의 원격 커넥터 연결은 로컬 MCP 연결과 다릅니다. GitHub에 코드를 올리는 것만으로 GPT·Claude 웹앱 연결이 완료되지는 않습니다. 원격 HTTPS/OAuth는 아직 배포하지 않았습니다.

참조한 프로젝트:

- [kshicdi/whisperflow](https://github.com/kshicdi/whisperflow): 원본 브라우저 시각화와 음성 서버 분리 구조. 원본 MIT 라이선스를 유지합니다.
- [yogen-ghodke-113/VoiceClaw](https://github.com/yogen-ghodke-113/VoiceClaw): 대화와 실행 역할 분리, 상태 표시 구조 참고. 코드를 복사해 통합하지 않았습니다.

영화·애니메이션 참조 녹음, 대화 기록, 모델, 계정 키는 공개 소스에 포함하지 않습니다. **케이캅스 데커드는 디아블로의 데커드 케인이 아닙니다.**
