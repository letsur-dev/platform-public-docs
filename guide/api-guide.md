# API 사용 가이드

Letsur API는 **하나의 진입점으로 여러 AI 공급사의 모델**을 호출할 수 있는 서비스입니다. OpenAI SDK·Anthropic SDK·Claude Code 등 기존에 쓰시던 클라이언트에서 **`base_url`과 키만 바꾸면** 그대로 동작합니다.

## 시작하기 전에

- **Base URL**: `https://gw.letsur.ai`
- **API 키 발급**: Space → **AI Gateway → 키** 탭에서 발급. 발급 직후 **한 번만 표시**되므로 즉시 안전한 곳에 보관하세요.
- **모델 코드**: **AI Gateway → 카탈로그** 탭에서 사용 가능한 모델과 지원 엔드포인트 확인 (예: `claude-sonnet-4-6`)
- **응답에 원가 예상치 포함**: 모든 호출 응답에 `estimated_cost` 필드가 붙습니다 (USD 기준).

---

## 1. OpenAI 호환으로 호출하기

가장 일반적인 사용 방식입니다. `POST /v1/chat/completions` 로 OpenAI 스펙 그대로 호출합니다.

### curl

```bash
export LETSUR_API_KEY="sk-..."

curl https://gw.letsur.ai/v1/chat/completions \
  -H "Authorization: Bearer $LETSUR_API_KEY" \
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
    api_key=os.environ["LETSUR_API_KEY"],
    base_url="https://gw.letsur.ai/v1",
)

response = client.chat.completions.create(
    model="claude-sonnet-4-6",
    messages=[{"role": "user", "content": "안녕하세요"}],
)

print(response.choices[0].message.content)
print(response.model_dump().get("estimated_cost"))
```

**핵심 포인트**
- 공급사 prefix(`openai/`, `anthropic/`) **불필요** — 카탈로그의 모델 코드를 그대로 사용
- 공급사 전환은 `model` 필드 값만 바꾸면 됨
- `stream: true` 스트리밍 지원. `stream_options.include_usage: true` 시 마지막 직전 청크에 `usage`와 `estimated_cost` 포함

---

## 2. Anthropic 네이티브로 호출하기

Claude Code나 Anthropic SDK를 사용 중이라면 `/v1/messages` 엔드포인트를 그대로 쓸 수 있습니다.

### curl

```bash
curl https://gw.letsur.ai/v1/messages \
  -H "x-api-key: $LETSUR_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-6",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "안녕"}]
  }'
```

### Claude Code

환경 변수만 설정하면 Claude Code가 Letsur Gateway를 통해 동작합니다.

```bash
export ANTHROPIC_BASE_URL="https://gw.letsur.ai"
export ANTHROPIC_API_KEY="$LETSUR_API_KEY"
```

> Anthropic 스펙은 `base_url`에 `/v1` 을 포함하지 않습니다 (Anthropic SDK 관례).

---

## 3. 지원 모델

현재 카탈로그에 다음 공급사의 모델이 등록되어 있습니다.

| 공급사 | 대표 모델 예시 |
|---|---|
| Anthropic | Claude 4.x 시리즈 |
| OpenAI | GPT-5.x 시리즈 |
| Google | Gemini 3.x 시리즈 |
| xAI (Grok) | Grok 4 시리즈 |
| DeepSeek | V3.2, R1 |
| Alibaba (Qwen) | Qwen 3 시리즈 |
| Z.ai (GLM) | GLM 5 시리즈 |
| Minimax | M2 시리즈 |

전체 모델 목록·단가·지원 엔드포인트는 **AI Gateway → 카탈로그** 탭에서 확인하세요.

프로그램적으로는 `GET /v1/models` 로도 조회 가능합니다.

```bash
curl https://gw.letsur.ai/v1/models \
  -H "Authorization: Bearer $LETSUR_API_KEY"
```

---

## 4. 응답에서 확인할 것

```json
{
  "id": "chatcmpl-...",
  "choices": [...],
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

- `usage` — OpenAI 표준 토큰 집계
- `estimated_cost` — Letsur 확장 필드. **이번 호출의 원가 USD 예상치**이며 실제 청구와 차이가 있을 수 있음

---

## 5. 에러 형식

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

## 6. 한도와 청구

- 각 키에는 Admin이 **월 사용 상한**을 설정할 수 있습니다 (키별 안전장치).
- 초과 시 Gateway가 자동으로 요청을 차단합니다 (`429 usage_limit_exceeded`).
- 실제 청구는 Letsur와의 **외부 정산** 기준입니다. `estimated_cost` 는 참고값입니다.

---

## 다음 단계

- **Chat Completions** 엔드포인트 상세 스펙 — Reference 문서 참조
- **Messages (Anthropic 호환)** 엔드포인트 상세 스펙 — Reference 문서 참조
- **Claude Code / Cursor 연동** — Integrations 가이드 참조
- **전체 에러 카탈로그** — 에러 코드 Reference 참조

> 문의: Letsur 운영팀
