# 데이터 처리 위탁 현황

최종 업데이트: 2026년 07월 27일

주식회사 렛서(이하 "회사")는 서비스 제공을 위해 아래 업체에 데이터 처리를 위탁합니다. 본 현황은 [개인정보 처리방침](https://platform.letsur.ai/privacy) 제5조 및 제6조에 연결된 공식 부속 고지입니다. 업체가 추가되거나 변경되면 본 페이지에 지체 없이 반영하고 사용자에게 고지합니다. 개별 계약에서 정한 사전 동의 절차는 별도로 따릅니다.

표의 “이전 항목”은 실제 이용하는 기능과 상품을 제공하기 위해 회사가 해당 업체에 전달하는 자료입니다. “요청 데이터”는 입력 자료와 실행 옵션, “사용량 데이터”는 본문을 제외한 호출 건수와 토큰 수 등 이용량 정보입니다. AI 산출물도 다음 요청의 입력이나 파일 보관 대상이 되면 이전 항목에 포함될 수 있습니다. 자세한 정의는 [개인정보 처리방침 제4조](https://platform.letsur.ai/privacy)를 따르며, 원문 없는 운영 정보도 개인이나 계정과 연결되면 개인정보로 처리합니다.

렛서 자체의 콘텐츠 이용 및 보관 조건은 [이용약관 제13조 및 부속서 1](https://platform.letsur.ai/terms), 개인정보의 보관, 접근 및 삭제는 [개인정보 처리방침](https://platform.letsur.ai/privacy)을 따릅니다. 외부 업체의 보관, 학습, 개선 및 평가 목적의 이용 및 삭제에는 해당 상품의 정책과 계약 조건이 적용됩니다. 렛서의 저장 및 이용 제한 설정이 외부 업체의 처리 조건을 변경하지는 않으며, 회사의 법령 및 계약상 위탁 관리와 개인정보 보호 의무는 유지됩니다.

표의 “위탁 계약 종료 시”는 위탁 관계 종료에 따른 처리 기한이며, 모든 자료를 그때까지 보관한다는 뜻은 아닙니다. 회사는 개인정보 처리방침의 자료별 목적과 보관 기간 및 유효한 삭제 요청에 따라 수탁업체의 처리 범위를 관리합니다.

---

## 인프라 및 운영

| 업체명 | 위탁 업무 | 이전 국가 | 이전 항목 | 보유 기간 |
|---|---|---|---|---|
| Amazon Web Services, Inc. | 클라우드 인프라 운영 (컴퓨팅, 스토리지, DB, 이메일 발송 등) | 대한민국(서울 리전) 기본, 재해복구(DR) 및 가용성 확보 목적으로 일본(도쿄 리전) 등 다른 리전으로 일시 이전될 수 있음 | 계정 정보, 회사가 보관하는 결제수단 정보와 거래 기록, 운영 기록 및 선택한 기능의 사용자 자료와 AI 산출물 | 위탁 계약 종료 시 |
| Vercel, Inc. | 프론트엔드 호스팅 | 미국 | 접속 기록, 기술 정보 | 위탁 계약 종료 시 |
| Datadog | 서비스 운영 모니터링 및 장애 분석 | 미국(US 리전) | 서비스 운영 모니터링 정보. 사용자 프롬프트 및 출력은 전송하지 않음 | 이용 중인 상품의 보관 설정에 따름 |

## 결제

| 업체명 | 위탁 업무 | 이전 국가 | 이전 항목 | 보유 기간 |
|---|---|---|---|---|
| 포트원(주) | 전자결제 대행, 빌링키 발급 | 대한민국 | 카드 정보 및 결제 처리 정보 | 위탁 계약 종료 시 |
| (주)KG이니시스 | 전자결제 처리 | 대한민국 | 결제 처리에 필요한 카드 정보 및 승인 정보 | 위탁 계약 종료 시 |
| (주)NHN KCP | 전자결제 처리 | 대한민국 | 결제 처리에 필요한 카드 정보 및 승인 정보 | 위탁 계약 종료 시 |

## AI 모델 제공업체

직접 AI 처리를 수행하는 업체와 API 중개 업체를 함께 안내합니다.

| 업체명 | 위탁 업무 | 이전 국가 | 이전 항목 | 보유 기간 및 관련 정책 |
|---|---|---|---|---|
| Anthropic PBC | AI 모델 API 처리 (Claude) | 미국 | 요청 데이터, 사용량 데이터 | [데이터 처리 약정](https://www.anthropic.com/legal/data-processing-addendum) / [이용 정책(AUP)](https://www.anthropic.com/legal/aup) |
| OpenAI OpCo, LLC | AI 모델 API 처리 (GPT) | 미국 | 요청 데이터, 사용량 데이터 | [데이터 처리 약정](https://openai.com/policies/data-processing-addendum/) / [이용 정책(AUP)](https://openai.com/policies/usage-policies/) |
| Google LLC | AI 모델 API 및 영상 생성 API 처리 (Gemini API: Gemini, Veo 등) | 미국 | 요청 데이터, 사용량 데이터 | [API 약관의 데이터 처리 조건](https://ai.google.dev/gemini-api/terms) / [이용 정책(AUP)](https://policies.google.com/terms/generative-ai/use-policy) |
| Amazon Web Services, Inc. | AI 모델 API 처리 (Bedrock) | 미국 | 요청 데이터, 사용량 데이터 | [데이터 보호 안내](https://docs.aws.amazon.com/bedrock/latest/userguide/data-protection.html) |
| Microsoft Corporation | AI 모델 API 처리 (Azure OpenAI) | 미국 등 여러 국가 | 요청 데이터, 사용량 데이터 | [데이터 처리 안내](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/data-privacy) |
| OpenRouter, Inc. | AI 모델 API 라우팅 처리 | 미국 | 요청 데이터, 사용량 데이터 | [데이터 수집 안내](https://openrouter.ai/docs/guides/privacy/data-collection) |
| Vercel, Inc. (AI Gateway) | AI 모델 API 라우팅 처리 | 미국 | 요청 데이터, 사용량 데이터 | [AI 상품 약관](https://vercel.com/legal/ai-product-terms) |
| Tencent Cloud TokenHub | AI 모델 API 실행 및 중개 | 싱가포르(이용 리전) | 요청 데이터 | [데이터 처리 정책](https://www.tencentcloud.com/document/product/1300/78953) |
| BytePlus ModelArk | 이미지 및 영상 등 미디어 생성 API 처리 | 말레이시아(직접 연결 [AP 리전](https://docs.byteplus.com/en/docs/modelark/region-availability), ap-southeast-1). 추가 처리 범위는 상품별 정책에 따름 | 요청 데이터 | [데이터 처리 정책](https://docs.byteplus.com/zh-TW/docs/ModelArk/BytePlus_ModelArk_Data_Processing) |
| Z.AI API | AI 모델 API 처리 | 싱가포르 | 요청 데이터 | [API 데이터 처리 약정(페이지 하단)](https://chat.z.ai/legal-agreement/privacy-policy) |
| FriendliAI | AI 모델 추론 API 처리 | 미국 | 요청 데이터 | [데이터 처리 정책](https://friendli.ai/dpa) |

## 기타 기술 제공자

| 업체명 | 위탁 업무 | 이전 국가 | 이전 항목 | 보유 기간 |
|---|---|---|---|---|
| Slack Technologies, LLC | 고객 문의 접수 및 내부 알림 | 미국 | 문의자 정보(이름, 회사명, 기업 유형, 부서, 직함, 이메일, 연락처), 문의 내용, 유입 정보 | 위탁 계약 종료 시 |
