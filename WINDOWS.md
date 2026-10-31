# WhisperFlow — Windows 한국어 / English

현재 권장 설치는 **`Setup-Native-Qwen.cmd`**입니다. Qwen GGUF와 공식 llama.cpp CPU 실행 파일을 사용하며, 참조 음성과 모델은 이 PC에만 저장합니다. 기존 무거운 PyTorch 설치는 비교/분석용 선택 사항입니다. 실제 앱 연결은 [CONNECTIONS.md](CONNECTIONS.md)를 따르세요.

최종 목소리는 **자비스 한국어 / 자비스 영어 / 데커드 한국어(약한 로봇 효과)**입니다. 영어 데커드는 제공하지 않습니다.

원본: https://github.com/kshicdi/whisperflow (MIT). 이 폴더에는 원본과 별도의 Windows 실행 경로를 추가했습니다. 기존 macOS 실행 코드와 영어 효과음 파일은 변경하지 않았습니다.

## 실행

1. `Start-Windows.cmd`를 실행하세요. Python 3.11 이상이 필요하며, Codex의 Python이 있으면 자동으로 사용합니다. 일반 PC에서는 Python Launcher(`py`)가 필요합니다.
2. 첫 실행에서 작은 서버 라이브러리를 이 폴더의 `.windows-env`에 설치합니다. 인터넷 연결이 필요합니다.
3. 브라우저에 `http://127.0.0.1:8767`이 열립니다. 열린 실행 창을 닫으면 서버가 종료됩니다.
4. 오른쪽 위 **음성 설정 · Voice**에서 **한국어 / English**를 선택하세요.

언어별 목소리·속도·음성 엔진과 마지막 언어 선택은 브라우저에 각각 저장됩니다. 언어 선택은 마이크 인식 언어, AI 답변 언어, 읽어주는 음성을 함께 전환합니다. 다른 브라우저에서는 설정을 다시 선택해야 합니다.

**미리듣기 / Preview**는 대화 엔진 없이도 사용할 수 있습니다. **시스템 기본 / System**은 PC에 설치된 음성을 사용합니다. 한국어 남성 음성이 설치돼 있지 않으면 기본 음성이 여성일 수 있으며, 이를 맞춤 남성 음성이라고 표시하지 않습니다.

## 글자 대화

**주된 사용 방식은 기존 GPT·Claude에서 공통 음성 도구를 호출하는 것입니다.** 먼저 [CONNECTIONS.md](CONNECTIONS.md)를 읽으세요. 아래 독립 화면의 대화 엔진 선택은 선택 사항입니다.

독립 화면에서 GPT를 선택하면 OpenAI Responses API를 사용합니다. `OPENAI_API_KEY`는 서버 환경 변수에만 설정하며 브라우저나 GitHub에 넣지 않습니다. 모델은 `JARVIS_GPT_MODEL` 또는 화면에서 지정합니다. API 계정 접근과 사용료를 확인해야 하며, 실제 인증된 API 호출은 아직 검증하지 않았습니다. 공식 안내: https://developers.openai.com/api/docs/quickstart

원본처럼 Claude Code를 응답 생성에 사용합니다. 별도의 설치와 본인 계정 로그인이 필요합니다. 이 프로그램은 로그인이나 구독을 대신하지 않습니다.

- 공식 설치 안내: https://code.claude.com/docs/en/setup
- `claude`를 찾지 못하면 `windows-config.example.json`을 `windows-config.json`으로 복사하고 `JARVIS_CLAUDE_PATH`에 설치된 실행 파일 경로를 지정하세요.
- Windows 네이티브 `claude.exe`를 권장합니다. npm의 `.cmd` 설치는 같은 폴더의 Node 진입 파일이 있을 때 처리합니다.
- 이 Windows 경로는 음성 대화용으로 Claude의 도구와 MCP를 비활성화합니다. 원본의 파일 조작 기능은 포함하지 않습니다.
- 대화 기록은 이 폴더의 `.windows-data`에 저장합니다. Claude에 질문과 최근 대화가 전달됩니다.

## 원하는 한국 남성 음색 만들기: 선택 사항

음성 엔진은 두 가지입니다.

- **System:** 설치된 시스템 음성. 별도의 모델 설치가 필요 없습니다.
- **Qwen:** 로컬 Qwen3-TTS 서버. 음색 설명으로 새 음성을 생성합니다. 모델과 추가 라이브러리가 필요합니다.

현재 확인한 GTX 1660 SUPER는 VRAM 6GB이며 드라이버는 457.51입니다. 최신 PyTorch CUDA 실행이 검증되지 않아 기본 설치 도우미는 **CPU용**으로 제공합니다. 드라이버를 자동 변경하지 않습니다. CPU 생성은 특히 첫 요청에서 오래 걸릴 수 있습니다.

1. Python **3.12**로 `Setup-Qwen-CPU.cmd`를 실행하세요. 라이브러리와 모델에 수 GB의 다운로드·디스크 공간이 필요합니다.
2. 설치 후 `Start-Qwen-CPU.cmd`를 실행하세요. 서버를 켜둡니다.
3. 자비스 화면에서 음성을 **Qwen 맞춤 음성 / Custom**으로 바꾸세요.
4. 한국어는 음성 ID를 **`design:ko`**, 영어는 **`design:en`**으로 설정하고 미리듣기를 누르세요.
5. 첫 생성 때 Hugging Face에서 `Qwen/Qwen3-TTS-12Hz-1.7B-VoiceDesign`을 `.models`에 내려받습니다. 인터넷과 충분한 시스템 RAM이 필요합니다. 모델 다운로드나 생성 실패는 화면에 표시합니다.

맞춤 한국 남성 음색 설명은 `whisperflow/windows.py`의 `PROFILES['ko']`에서 조정할 수 있습니다. Qwen의 기본 지원 기능을 사용하며 녹음에서 별도 학습한 모델은 아닙니다. 동일한 설정과 시드를 사용해도 생성 문장 간 음색의 일관성은 직접 확인해야 합니다.

Qwen 서버가 연결되지 않거나 생성에 실패하면 경고를 표시하고 시스템 음성으로 전환합니다. 이때 들리는 소리는 맞춤 음성이 아닙니다.

## 기존 영어 목소리 보존

원본 영어 효과음과 음성 ID `clone:jarvis`, 기본 속도 `1.4`를 유지했습니다. **원본 작성자의 실제 복제 음성 모델·참조 녹음·TTS 서버는 GitHub에 포함되지 않았습니다.** 따라서 이 코드만으로 영화와 동일한 음성이 바로 나오지는 않습니다.

- 기존 호환 TTS 서버가 있다면 `windows-config.json`의 `QWEN_TTS_URL`을 지정하고 English에서 기존 음성 ID를 사용하세요.
- 새 로컬 VoiceDesign 서버를 사용한다면 영어 음성 ID를 `design:en`으로 바꾸세요. 이 음성은 새로 설계한 영어 음성이며 원본 배우 음성 복제가 아닙니다.

## 포함 범위와 확인 상태

- 브라우저 화면, 한국어·영어 선택, 언어별 설정 저장, 대화 연결, 시스템 음성 미리듣기, Qwen 연결 규격, 말하기 정지.
- Windows에서 실행하는 서버 연결 검사와 언어별 메시지·음성 라우팅 등 자동 검사를 통과했습니다.
- 실제 Claude 로그인 후 대화, 인증된 GPT API 호출, 마이크 녹음, Qwen 모델 전체 설치와 실제 맞춤 음성 품질은 별도 확인이 필요합니다. MCP 및 캐릭터 선택 관련 검증은 HANDOFF.md도 확인하세요.
- 마이크 인식은 브라우저의 Web Speech API입니다. 브라우저/서비스에 따라 외부 서버를 사용하므로 오프라인 Whisper라고 안내하지 않습니다.
- 전역 단축키, 작업 표시줄 메뉴, 카메라·조명 제어, 자동 시작 등록은 추가하지 않았습니다. 원본의 macOS 시스템 설정을 그대로 실행하지 않습니다.
- 서버는 기본적으로 이 PC에서만 접속 가능한 loopback 주소에 열립니다.

## 개발 검증

```powershell
python -m pip install -r requirements-windows.txt
python -m unittest discover -s tests -v
python -m whisperflow.windows
```

공식 Qwen 문서: https://github.com/QwenLM/Qwen3-TTS
