# Integrations — 개발 툴 연동

Letsur Gateway는 OpenAI·Anthropic API 프로토콜과 호환되므로, 이들 프로토콜을 지원하는 대부분의 개발 도구와 바로 연결할 수 있습니다. 일반적으로 **Base URL**과 **API 키** 두 가지만 지정하면 됩니다.

## Claude Code

Anthropic 공식 CLI. Messages API를 사용합니다. Anthropic 공식 가이드에 따라 **환경 변수** 또는 **settings.json** 방식으로 Letsur Gateway를 지정할 수 있습니다.

### 방식 1 — 환경 변수 (가장 간단)

```bash
export ANTHROPIC_BASE_URL="https://gw.letsur.ai"
export ANTHROPIC_API_KEY="<Letsur에서 발급받은 키>"
```

이후 평소처럼 `claude` 명령어를 실행합니다.

> `ANTHROPIC_API_KEY` 대신 `ANTHROPIC_AUTH_TOKEN` 을 사용할 수 있습니다 (둘 중 하나만 설정).
> Anthropic SDK 관례에 따라 `ANTHROPIC_BASE_URL` 에 `/v1` 은 포함하지 않습니다.

### 방식 2 — settings.json

쉘 환경에 의존하지 않는 방식입니다. `~/.claude/settings.json` 의 `env` 블록에 직접 기록할 수 있습니다.

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://gw.letsur.ai",
    "ANTHROPIC_API_KEY": "<Letsur에서 발급받은 키>"
  }
}
```

프로젝트 단위로 설정을 분리하려면 프로젝트 루트에 `.claude/settings.json` 을 두면 됩니다.

### 모델 지정

우선순위: **CLI 플래그 > 환경 변수 > settings.json**.

```bash
# CLI 플래그
claude --model claude-sonnet-4-6

# 환경 변수
export ANTHROPIC_MODEL="claude-sonnet-4-6"
```

```json
// settings.json
{ "model": "claude-sonnet-4-6" }
```

### 참고 — Anthropic 공식 가이드

- [Claude Code — LLM Gateway 통합](https://code.claude.com/docs/en/llm-gateway.md)
- [Claude Code — 환경 변수](https://code.claude.com/docs/en/env-vars.md)

## Codex (OpenAI CLI)

Codex는 환경 변수만으로는 외부 게이트웨이에 연결되지 않습니다. `~/.codex/config.toml` 에 Letsur Gateway를 `model_providers` 로 등록한 뒤 profile 로 선택합니다.

### 1. config.toml 설정

`~/.codex/config.toml` 을 열거나 생성해서 다음을 추가합니다.

```toml
# 기본 프로필을 Letsur Gateway 로 설정
model = "gpt-5"                         # 카탈로그의 responses 모드 지원 모델 코드
model_provider = "letsur"

[model_providers.letsur]
name = "Letsur Gateway"
base_url = "https://gw.letsur.ai/v1"    # 공식 Gateway Base URL
env_key = "GATEWAY_API_KEY"             # 아래 2단계에서 이 이름의 환경변수에 키 저장
```

### 2. API 키 환경 변수 설정

```bash
export GATEWAY_API_KEY="<Letsur에서 발급받은 키>"
```

### 3. 실행

```bash
codex "이 함수를 리팩토링해줘"
```

다른 모델을 쓰고 싶다면 `model` 필드를 카탈로그 코드로 교체하거나, 여러 조합을 `[profiles.xxx]` 로 등록해서 `codex --profile xxx` 로 선택합니다.

> `env_key` 에는 **환경변수 이름** 을 적습니다(키 값 자체가 아닙니다). 실제 API 키는 2단계처럼 환경변수로 export 하세요.

## Cursor

Cursor 설정에서 **Custom OpenAI endpoint** 를 사용합니다.

1. **Settings → Models** 열기
2. **OpenAI API Key** 에 Letsur에서 발급받은 키 입력
3. **Custom OpenAI API Base URL** 을 `https://gw.letsur.ai/v1` 로 설정
4. 사용할 모델 코드(예: `claude-sonnet-4-6`)를 모델 목록에 추가

> Cursor의 일부 내장 기능(자동완성 등)은 Custom endpoint 로 전환되지 않을 수 있습니다. 외부 모델 호출 시에만 Letsur Gateway가 사용됩니다.

## 기타 OpenAI 호환 도구

`OPENAI_BASE_URL` (또는 `OPENAI_API_BASE`) 을 `https://gw.letsur.ai/v1` 로, `OPENAI_API_KEY` 를 Letsur 키로 설정하면 대부분 동작합니다. LangChain, LlamaIndex 등이 이 방식으로 연결 가능합니다.

---

> 문의: Letsur 운영팀
