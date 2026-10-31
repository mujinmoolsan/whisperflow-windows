# GPT·Claude에서 공통 자비스 호출

## 동작 방식

GPT나 Claude가 답변/명령 처리를 담당하고, 자비스는 말할 문장을 받아 PC에서 음성을 생성·재생합니다. 별도의 GPT 답변 모델을 새로 학습할 필요가 없습니다. 기존 앱에서 아래 MCP 음성 도구를 호출할 때는 별도 OpenAI API 키도 필요하지 않습니다.

- `jarvis_status`: 음성 서버와 참조 파일 설치 상태 확인
- `jarvis_speak`: 문장, 캐릭터, 언어, 속도를 전달하여 말하기. 자비스는 `ko`/`en`, 데커드는 `ko`만 지원합니다.
- `jarvis_stop`: 재생 중지

두 앱은 같은 Qwen 서버를 사용합니다. 참조 녹음은 `.references`에 보관하며 공개 저장소에 포함하지 않습니다.

## 로컬 MCP를 지원하는 앱

1. `Setup-Native-Qwen.cmd`로 가벼운 CPU 런타임과 Qwen GGUF 모델을 설치하고 `Start-Qwen-CPU.cmd`로 음성 서버를 실행합니다. 기존 `Setup-Qwen-CPU.cmd`는 선택 사항인 무거운 PyTorch 비교 환경입니다.
2. 앱의 MCP 서버 설정에 실행 명령을 등록합니다. 설치에 사용한 Python을 명령으로, 프로젝트의 `run_jarvis_mcp.py` 절대 경로를 인수로 사용합니다. `make_connection_configs.py`로 이 PC용 설정 예시를 만들 수 있으며, 앱의 실제 설정은 자동 변경하지 않습니다.
3. 앱을 다시 열고 `jarvis_status`가 표시되는지 확인합니다. 연결 설정은 준비한 것과 앱에 실제 등록된 것을 구별해야 합니다.

Codex 설정 예시(실제 경로로 교체):

```toml
[mcp_servers.jarvis_voice]
command = 'C:\path\to\project\.qwen-env\Scripts\python.exe'
args = ['-m', 'whisperflow.jarvis_mcp']
cwd = 'C:\path\to\project'
```

Claude Desktop/Claude Code 등 stdio MCP 호스트의 JSON 예시:

```json
{
  "mcpServers": {
    "jarvis_voice": {
      "command": "C:/path/to/project/.qwen-env/Scripts/python.exe",
      "args": ["C:/path/to/project/run_jarvis_mcp.py"]
    }
  }
}
```

## Claude 웹앱 및 ChatGPT 웹앱

웹앱의 원격 커넥터는 제공사 클라우드에서 서버에 연결하므로 이 PC의 `localhost` 주소로는 연결되지 않습니다. HTTPS로 접근할 수 있는 원격 MCP 서버와 인증 연결이 추가로 필요합니다. 코드를 GitHub에 올리는 것만으로 서버가 실행되거나 연결되는 것은 아닙니다.

이 버전은 PC를 인터넷에 공개하지 않습니다. 로컬 HTTP MCP 시험은 `python -m whisperflow.jarvis_mcp --http`로 제공하며 기본 주소는 `http://127.0.0.1:8768/mcp`입니다. 인증 없이 이 포트를 인터넷에 공개하지 마세요. 웹앱용 OAuth/원격 배포는 아직 구성하지 않았습니다.

공식 안내:

- https://learn.chatgpt.com/docs/extend/mcp
- https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp

## 목소리

- 자비스: 사용자 소유 PC의 참조 녹음으로 영어/한국어 생성. 한국어는 교차 언어 합성 결과를 직접 확인하며 조정해야 합니다.
- 로봇수사대 케이캅스 데커드: 검증한 참조 구간을 사용하고 기본 `robot_effect=soft`를 적용합니다. `none`/`strong`으로 변경할 수 있습니다. 사용자 확인 없는 회차 전체를 학습 자료로 취급하지 않습니다.
- 설명으로 새 음색을 만드는 `design:ko`/`design:en`도 지원하지만 더 큰 모델과 여유 메모리가 필요합니다.

참조 녹음 준비 예시:

```powershell
.qwen-env/Scripts/python.exe prepare_reference.py "reference.m4a" --character jarvis --start 3 --duration 12
```

`--character deckard`로 데커드 참조 파일을 준비할 수 있습니다. 배경음악이나 다른 인물의 대사가 없는 구간을 고르고 직접 확인하세요. 파일을 준비한 것과 합성 품질이 검증된 것은 다릅니다.

추가 회차에서 후보를 찾는 선택 사항 도구는 `find_voice_candidates.py`입니다. 이미 다운로드한 Python Qwen 체크포인트의 화자 인코더만 사용하며, 유사도는 신원 확인이나 화자 확정이 아닙니다. 후보를 듣고 확인하기 전에는 승인된 참조 음성을 자동 교체하지 않습니다. 가중치 미세조정을 실행한 것으로 표현하지 않습니다.
