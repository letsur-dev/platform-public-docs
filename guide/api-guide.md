# API 사용 가이드

Letsur API는 **하나의 진입점으로 여러 AI 공급사의 모델**을 호출할 수 있는 서비스입니다. OpenAI SDK·Anthropic SDK 등 기존에 쓰시던 클라이언트에서 **`base_url`과 키만 바꾸면** 그대로 동작합니다.

## 시작하기 전에

- **Base URL**: `https://gw.letsur.ai`
- **API 키 발급**: Space → **AI Gateway → 키** 탭에서 발급. 발급 직후 **한 번만 표시**되므로 즉시 안전한 곳에 보관하세요.
- **모델 코드**: **AI Gateway → 카탈로그** 탭에서 사용 가능한 모델과 지원 엔드포인트 확인 (예: `claude-sonnet-4-6`)
- **응답에 원가 예상치 포함**: 모든 호출 응답에 `estimated_cost` 필드가 붙습니다 (USD 기준).

---

## 1. OpenAI Chat Completions 호환

`POST /v1/chat/completions` — 가장 일반적인 사용 방식입니다.

### curl

```bash
export GATEWAY_API_KEY="sk-..."

curl https://gw.letsur.ai/v1/chat/completions \
  -H "Authorization: Bearer $GATEWAY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-6",
    "messages": [
      {"role": "user", "content": "Letsur Gateway에 연결됐는지 확인해줘."}
    ]
  }'
```

### Python (OpenAI SDK)

```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["GATEWAY_API_KEY"],
    base_url="https://gw.letsur.ai/v1",
)

response = client.chat.completions.create(
    model="claude-sonnet-4-6",
    messages=[{"role": "user", "content": "안녕하세요"}],
)

# LLM이 생성한 텍스트
print(response.choices[0].message.content)
```

### 응답 예시

```json
{
  "id": "chatcmpl-...",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "안녕하세요! 무엇을 도와드릴까요?"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 21,
    "completion_tokens": 38,
    "total_tokens": 59
  },
  "estimated_cost": {
    "amount": "0.00120000",
    "currency": "USD",
    "disclaimer": "Estimated based on published pricing. Actual charges may differ."
  }
}
```

LLM이 생성한 텍스트는 **`choices[0].message.content`** 에 있습니다.

### 핵심 포인트

- 공급사 prefix(`openai/`, `anthropic/`) **불필요** — 카탈로그의 모델 코드를 그대로 사용
- 공급사 전환은 `model` 필드 값만 바꾸면 됨
- `stream: true` 스트리밍 지원. `stream_options.include_usage: true` 시 마지막 직전 청크에 `usage`와 `estimated_cost` 포함

---

## 2. Anthropic Messages 네이티브

`POST /v1/messages` — Anthropic SDK를 그대로 붙일 수 있습니다.

### curl

```bash
curl https://gw.letsur.ai/v1/messages \
  -H "x-api-key: $GATEWAY_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-6",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "안녕"}]
  }'
```

### Python (Anthropic SDK)

```python
import os
from anthropic import Anthropic

client = Anthropic(
    api_key=os.environ["GATEWAY_API_KEY"],
    base_url="https://gw.letsur.ai",
)

message = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "안녕하세요"}],
)

# LLM이 생성한 텍스트
print(message.content[0].text)
```

### TypeScript (Anthropic SDK)

```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic({
  apiKey: process.env.GATEWAY_API_KEY,
  baseURL: "https://gw.letsur.ai",
});

const message = await client.messages.create({
  model: "claude-sonnet-4-6",
  max_tokens: 1024,
  messages: [{ role: "user", content: "안녕하세요" }],
});

console.log(message.content[0].text);
```

### 응답 예시

```json
{
  "id": "msg_...",
  "type": "message",
  "role": "assistant",
  "model": "claude-sonnet-4-6",
  "content": [
    {"type": "text", "text": "안녕하세요! 무엇을 도와드릴까요?"}
  ],
  "stop_reason": "end_turn",
  "usage": {
    "input_tokens": 10,
    "output_tokens": 24
  },
  "estimated_cost": {
    "amount": "0.00085000",
    "currency": "USD",
    "disclaimer": "Estimated based on published pricing. Actual charges may differ."
  }
}
```

LLM이 생성한 텍스트는 **`content[0].text`** 에 있습니다.

> Anthropic SDK는 `base_url`에 `/v1`을 포함하지 않습니다. SDK가 내부적으로 `/v1/messages` 경로를 붙입니다. `curl`로 직접 호출할 때는 `/v1/messages`를 명시해야 합니다.

---

## 3. OpenAI Responses API

`POST /v1/responses` — OpenAI의 최신 API. 단일 호출 내 **내장 도구(web search, code interpreter 등)** 사용과 **상태 있는 대화**를 지원합니다. 카탈로그에서 **`mode=responses`** 로 표시된 모델에서 사용 가능합니다.

### Python (OpenAI SDK)

```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["GATEWAY_API_KEY"],
    base_url="https://gw.letsur.ai/v1",
)

response = client.responses.create(
    model="gpt-5",  # responses 모드 지원 모델 코드로 교체
    input="안녕하세요",
)

# SDK가 제공하는 편의 속성
print(response.output_text)
```

### 응답 예시

```json
{
  "id": "resp_...",
  "object": "response",
  "model": "gpt-5",
  "output": [
    {
      "type": "message",
      "role": "assistant",
      "content": [
        {"type": "output_text", "text": "안녕하세요! 무엇을 도와드릴까요?"}
      ]
    }
  ],
  "usage": {
    "input_tokens": 10,
    "output_tokens": 24,
    "total_tokens": 34
  },
  "estimated_cost": {
    "amount": "0.00085000",
    "currency": "USD",
    "disclaimer": "Estimated based on published pricing. Actual charges may differ."
  }
}
```

LLM이 생성한 텍스트는 **`output[0].content[0].text`** 에 있습니다. OpenAI SDK는 편의를 위해 **`response.output_text`** 속성으로 바로 접근할 수 있습니다.

---

## 4. 이미지 생성 (나노바나나)

Google Gemini 이미지 생성 모델(`gemini-2.5-flash-image-preview`, `gemini-3-pro-image-preview`)은 **같은 `/v1/chat/completions` 엔드포인트** 로 호출합니다. 모델 코드만 바꾸면 됩니다.

> `gemini-3-pro-image-preview` 는 공급사(Google) 측 불안정성이 있을 수 있으니 참고하세요.

### 텍스트로 이미지 생성 (Text-to-Image)

```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["GATEWAY_API_KEY"],
    base_url="https://gw.letsur.ai/v1",
)

response = client.chat.completions.create(
    model="gemini-2.5-flash-image-preview",
    messages=[
        {"role": "user", "content": "푸른 하늘 아래 달리는 자동차의 이미지를 만들어줘"}
    ],
)
```

### 이미지 편집 (Image-to-Image)

기존 이미지를 base64 data URL로 넘기면 편집도 가능합니다.

```python
import base64
from pathlib import Path

with open("input.png", "rb") as f:
    b64 = base64.b64encode(f.read()).decode("utf-8")

response = client.chat.completions.create(
    model="gemini-2.5-flash-image-preview",
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "내 자동차가 하늘을 달리도록 편집해줘"},
            {"type": "image_url", "image_url": {"url": f"data:image/png;base64,{b64}"}},
        ],
    }],
)
```

### 응답 예시

표준 Chat Completions 구조에 **`images`** 필드가 추가됩니다 (base64 인코딩된 이미지 배열).

```json
{
  "id": "chatcmpl-...",
  "choices": [
    {
      "message": {
        "role": "assistant",
        "content": "다음은 생성된 이미지입니다."
      }
    }
  ],
  "images": [
    {"image_url": {"url": "data:image/png;base64,iVBORw0KGgo..."}}
  ]
}
```

### 이미지 꺼내기

```python
import base64
from io import BytesIO
from PIL import Image

image_b64 = response.choices[-1].message.images[0]["image_url"]["url"]
_, b64_data = image_b64.split(",", 1)
image = Image.open(BytesIO(base64.b64decode(b64_data)))
image.show()
```

---

## 5. 엔드포인트별 LLM 출력 위치

JSON 구조가 엔드포인트마다 다르므로, **실제 모델이 생성한 텍스트를 꺼내는 위치**만 표로 정리했습니다.

| 엔드포인트 | 스키마 | LLM 텍스트 경로 | SDK 편의 속성 |
|---|---|---|---|
| `/v1/chat/completions` | OpenAI | `choices[0].message.content` | — |
| `/v1/chat/completions` (이미지) | OpenAI + `images` 필드 | `choices[0].message.images[0].image_url.url` (base64) | — |
| `/v1/messages` | Anthropic | `content[0].text` | — |
| `/v1/responses` | OpenAI Responses | `output[0].content[0].text` | `response.output_text` |

---

## 6. 지원 모델

현재 카탈로그에 다음 공급사의 모델이 등록되어 있습니다.

| 공급사 | 대표 모델 예시 |
|---|---|
| Anthropic | Claude 4.x 시리즈 |
| OpenAI | GPT-5.x 시리즈 |
| Google | Gemini 3.x 시리즈 (이미지 생성용 `gemini-2.5-flash-image-preview`·`gemini-3-pro-image-preview` 포함) |
| xAI (Grok) | Grok 4 시리즈 |
| DeepSeek | V3.2, R1 |
| Alibaba (Qwen) | Qwen 3 시리즈 |
| Z.ai (GLM) | GLM 5 시리즈 |
| Minimax | M2 시리즈 |

전체 모델 목록·단가·지원 엔드포인트는 **AI Gateway → 카탈로그** 탭에서 확인하세요.

프로그램적으로는 `GET /v1/models` 로도 조회 가능합니다.

```bash
curl "https://gw.letsur.ai/v1/models?page_size=100" \
  -H "Authorization: Bearer $GATEWAY_API_KEY"
```

응답에 `pagination` 필드(`total`, `page`, `page_size`, `total_pages`)가 포함됩니다. `page_size` 기본 20, 최대 100. 전체 순회는 `?page=N&page_size=100` 으로 페이지를 넘기세요.

---

## 7. 응답 공통 필드

모든 엔드포인트 응답에 포함되는 공통/확장 필드입니다.

- **`usage`** — 요청/응답 토큰 집계. 엔드포인트별 필드명이 약간 다름 (`prompt_tokens`/`completion_tokens` vs `input_tokens`/`output_tokens`).
- **`estimated_cost`** — Letsur 확장 필드. 이번 호출의 원가 USD 예상치.
  - `amount` — 문자열, 8자리 소수
  - `currency` — `USD`
  - `disclaimer` — 실제 청구와 차이 가능 고지

---

## 8. 에러 형식

에러는 **RFC 7807 Problem Details** 포맷으로 반환됩니다.

```json
{
  "type": "api_key_not_found",
  "title": "Api Key Not Found",
  "status": 401,
  "detail": "API key not found or has been revoked",
  "error_ref": "err_a3f2c1d8e5b0"
}
```

### 자주 만나는 상태 코드

| 상태 | `type` | 대응 |
|---|---|---|
| 401 | `api_key_not_found`, `api_key_revoked`, `api_key_expired` | 키 확인 / 재발급 |
| 429 | `usage_limit_exceeded` | 키 월 한도 초과. 다음 월 리셋 대기 또는 한도 상향 |
| 400 | `model_not_allowed`, `invalid_request` | 모델 코드·요청 파라미터 확인 |
| 502 / 504 | `proxy_error`, `upstream_timeout` | 일시 오류. 재시도하거나 `error_ref` 와 함께 문의 |

`error_ref` 는 문의 시 문제 추적에 사용되니 캡처해두세요.

---

## 9. 한도와 청구

- 각 키에는 Admin이 **월 사용 상한**을 설정할 수 있습니다 (키별 안전장치).
- 초과 시 Gateway가 자동으로 요청을 차단합니다 (`429 usage_limit_exceeded`).
- 실제 청구는 Letsur와의 **외부 정산** 기준입니다. `estimated_cost` 는 참고값입니다.

---

## 다음 단계

- **Chat Completions** 엔드포인트 상세 스펙 — Reference 문서 참조
- **Messages (Anthropic 호환)** 엔드포인트 상세 스펙 — Reference 문서 참조
- **Responses API** 상세 스펙 — Reference 문서 참조
- **Claude Code / Codex / Cursor 연동** — [Integrations 가이드](./integrations.md)
- **전체 에러 카탈로그** — 에러 코드 Reference 참조

> 문의: Letsur 운영팀
