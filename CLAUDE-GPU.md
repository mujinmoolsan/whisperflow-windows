# 자비스 한국어 음성 GPU 전환 (Claude, 2026-11-01)

GPT의 Voice Studio(2026-11-01) 위에 **한 가지만** 바꿨습니다: 자비스 한국어가 쓰는 네이티브 GGUF 엔진(`whisperflow/native_qwen.py`)을 그래픽카드로 돌리는 것.
문장 분할·다음 문장 미리 생성·캐시·상주 작업자는 GPT 버전에 이미 있으므로 건드리지 않았습니다.

## 바뀐 점

- `Setup-GPU.cmd` (신규): 공식 llama.cpp `b11960` **CUDA 12.4** 빌드와 런타임을 `.native/llama-cuda`에 설치합니다. GitHub 공식 체크섬을 확인합니다.
  `Setup-Native-Qwen.cmd`도 NVIDIA 드라이버가 보이면 같이 설치를 시도합니다(실패해도 CPU 설치는 그대로).
- `native_qwen.py`: `.native/llama-cuda`가 있으면 `-ngl 99`(모델 전체 GPU), 없으면 기존처럼 `-ngl 0`.
  GPU 실행이 한 번 실패하면 그 프로세스 동안은 자동으로 CPU 빌드로 돌아갑니다(`.windows-data/native-error.log`에 `[GPU]` 기록).
  CPU 스레드 4 고정 → 코어 수 기준 자동(`JARVIS_THREADS`), GPU 레이어 수는 `JARVIS_GPU_LAYERS`, 강제 CPU는 `JARVIS_FORCE_CPU=1`.
- `tests/test_native_gpu.py`: GPU 선택, 실패 시 CPU 전환, CPU 전용 설치 유지.

## 적용 (Windows)

1. NVIDIA 드라이버 최신 확인 (이 PC: Studio 617.42 / GTX 1660 SUPER 확인됨).
2. `Setup-GPU.cmd` 실행 → `GPU (CUDA 12.4) voice runtime is ready.`
3. Voice Studio를 다시 시작하고 자비스 한국어로 같은 문장을 말하게 해서 첫 소리까지 시간을 비교합니다.

## 아직 검증 안 됨

- 실제 PC에서의 GPU 속도. 이 저장소의 자동 검사는 모의 실행만 확인합니다.
- `llama-tts`의 Qwen3-TTS가 CUDA에서 CPU와 같은 음질을 내는지 청취 확인 필요.
- GPT 인계 문서의 핵심 병목(최종 답변을 다 받은 뒤에야 합성 시작, SDK가 전체 파형을 한 번에 반환)은 이 변경으로 해결되지 않습니다. 이 변경은 "목소리 만드는 시간" 자체를 줄이는 쪽입니다.
