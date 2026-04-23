# Integrations — 개발 툴 연동

Letsur Gateway는 OpenAI·Anthropic API 프로토콜과 호환되므로, 이들 프로토콜을 지원하는 대부분의 개발 도구와 바로 연결할 수 있습니다. 일반적으로 **Base URL**과 **API 키** 두 가지 환경 변수만 바꾸면 됩니다.

## Claude Code

Anthropic 공식 CLI. Messages API를 사용합니다.

```bash
export ANTHROPIC_BASE_URL="https://gw.letsur.ai"
export ANTHROPIC_API_KEY="<Letsur에서 발급받은 키>"
```

설정 후 평소처럼 `claude` 명령어를 실행합니다. 사용할 모델은 환경 변수로 지정할 수 있습니다.

```bash
export ANTHROPIC_MODEL="claude-sonnet-4-6"
```

> Anthropic SDK 관례에 따라 `ANTHROPIC_BASE_URL` 에 `/v1` 은 포함하지 않습니다.

## Codex (OpenAI CLI)

OpenAI의 CLI 코딩 도구. Chat Completions API 호환입니다.

```bash
export OPENAI_BASE_URL="https://gw.letsur.ai/v1"
export OPENAI_API_KEY="<Letsur에서 발급받은 키>"

codex "이 함수를 리팩토링해줘"
```

모델은 Codex 설정 파일(`~/.codex/config.toml`) 또는 CLI 플래그로 카탈로그의 모델 코드를 지정합니다.

## Cursor

Cursor 설정에서 **Custom OpenAI endpoint** 를 사용합니다.

1. **Settings → Models** 열기
2. **OpenAI API Key** 에 Letsur에서 발급받은 키 입력
3. **Custom OpenAI API Base URL** 을 `https://gw.letsur.ai/v1` 로 설정
4. 사용할 모델 코드(예: `claude-sonnet-4-6`)를 모델 목록에 추가

> Cursor의 일부 내장 기능(자동완성 등)은 Custom endpoint 로 전환되지 않을 수 있습니다. 외부 모델 호출 시에만 Letsur Gateway가 사용됩니다.

## 기타 OpenAI 호환 도구

`OPENAI_BASE_URL` (또는 `OPENAI_API_BASE`) 을 `https://gw.letsur.ai/v1` 로, `OPENAI_API_KEY` 를 Letsur 키로 설정하면 대부분 동작합니다. LangChain, LlamaIndex, LiteLLM client, Continue.dev 등이 이 방식으로 연결 가능합니다.

---

> 문의: Letsur 운영팀
