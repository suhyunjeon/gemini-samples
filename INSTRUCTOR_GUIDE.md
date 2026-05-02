# 강사용 운영 가이드 — Gemini × ITS 8시간 핸즈온

**대상**: 유티정보(주) 사내 특강 / 한국지능형교통체계협회 관련
**일자**: 2026-05-07
**환경**: Google Colab + Gemini API (참가자 각자 키 발급)

---

## 노트북 5종 개요와 권장 운영 시간

| # | 파일 | 핵심 주제 | 핸즈온 시간 |
|---|---|---|---|
| 0 | `00_setup.ipynb` | 환경 셋업 + 첫 호출 + thinking_level | 15~20분 |
| 1 | `01_model_matrix.ipynb` | 9개 조합 비용·성능 매트릭스 | 60~70분 |
| 2 | `02_cctv_multimodal.ipynb` | CCTV 객체 검지 + 도로 손상 분류 | 80~90분 |
| 3 | `03_long_context_caching.ipynb` | VDS 시계열 + 1M context + caching | 70~80분 |
| 4 | `04_function_calling_agent.ipynb` | ITS 미니 에이전트 | 80~90분 |

**총 핸즈온 분량 ≈ 5~5.5시간**. 8시간 강의 중 나머지 2.5~3시간은 슬라이드·토론·휴식·질의응답으로 배분.

### 추천 시간표 (09:00~18:00, 점심 1시간)

| 시간 | 세션 |
|---|---|
| 09:00–09:30 | 오프닝 + 강의 목표 + Colab/API 키 점검 (`00_setup`) |
| 09:30–10:40 | Session 1: 모델 전략 + `01_model_matrix` 핸즈온 |
| 10:40–10:55 | 휴식 |
| 10:55–12:15 | Session 2: 멀티모달 + `02_cctv_multimodal` 핸즈온 |
| 12:15–13:15 | 점심 |
| 13:15–14:35 | Session 3: Long Context + `03_long_context_caching` 핸즈온 |
| 14:35–14:50 | 휴식 |
| 14:50–16:30 | Session 4: Function Calling + `04_function_calling_agent` 핸즈온 |
| 16:30–16:45 | 휴식 |
| 16:45–17:45 | 운영 적용 워크숍 (각자 자기 업무 시나리오 1개씩 발굴) |
| 17:45–18:00 | 마무리 Q&A |

---

## 사전 준비 (강의 1주~3일 전 공지)

### 참가자 안내 메일 템플릿

```
유티정보(주) 임직원 여러분께,

5월 7일 Gemini × ITS 특강 사전 준비 안내드립니다.

[필수 준비물]
1. 노트북 (인터넷 접속 가능)
2. 개인 Google 계정 (회사 계정도 가능하나, 외부 API 사용 정책 확인 필요)
3. Gemini API 키 (아래 절차로 미리 발급)

[API 키 발급 — 5분 소요]
1. https://aistudio.google.com 접속
2. Google 계정 로그인
3. 좌측 상단 "Get API key" → "Create API key"
4. AIza로 시작하는 키 문자열을 메모장에 저장
5. 신용카드 등록 불필요 (무료 티어로 실습 진행)

[주의사항]
- 발급한 API 키를 GitHub/Slack/메일 등에 절대 공유하지 마세요 (자동 폐기됨)
- 강의 당일 Colab Secret 기능을 사용해 안전하게 등록하는 방법을 안내드립니다

[당일 사용 환경]
- Google Colab (별도 설치 불필요)
- 별도 데이터 다운로드 불필요 (모두 공개 데이터셋 또는 합성 데이터)
- 회사 망분리 환경에서는 외부 인터넷 접속 가능한 공용 PC 또는 개인 노트북 권장
```

### 강사 본인 사전 점검

강의 **1일 전 반드시** 모든 노트북을 본인 계정으로 한 번 처음부터 끝까지 실행해보세요.

- [ ] 5개 노트북 모두 처음부터 끝까지 실행 성공
- [ ] Wikimedia Commons 이미지 URL이 살아있는지 확인 (URL 일부는 변경 가능성 있음)
- [ ] Gemini 3.x 모델명이 모두 유효한지 확인 (preview 모델은 주기적으로 GA 전환됨)
- [ ] Context Caching 셀이 동작하는지 확인 (preview 모델은 캐싱 미지원일 수 있음)
- [ ] 백업용 이미지 파일 미리 다운로드 (네트워크 이슈 시 로컬 업로드용)

---

## 노트북별 운영 노트

### 00_setup — 가장 사고가 많이 나는 구간

**자주 발생하는 문제**:

1. **Colab Secret 토글 OFF** — 가장 빈번
   - 증상: `userdata.SecretNotFoundError`
   - 해결: 좌측 🔑 → 비밀 옆 "노트북 액세스" 토글 ON

2. **Pro 모델이 호출되지 않음**
   - 증상: 401/403 또는 quota error
   - 원인: Gemini 3.1 Pro Preview는 무료 API 티어가 없음
   - 해결: 강사가 미리 안내. 실습은 Flash / Flash-Lite 위주로 진행하고 Pro는 시연용으로만

3. **모델명 오류**
   - 자료에 자주 나오는 잘못된 이름: `gemini-1.5-pro`, `gemini-2.0-flash`, `gemini-pro`
   - 정확한 이름 (2026-05 기준 preview):
     - `gemini-3.1-pro-preview`
     - `gemini-3-flash-preview`
     - `gemini-3.1-flash-lite-preview`

**예상 질문**:
- Q: "회사 보안정책 때문에 외부 API 호출이 막혀있는데요" → 강의는 개인 계정 + 공용 인터넷 권장. 실 적용 시 Vertex AI(asia-northeast3) + VPC Service Controls 사용을 안내.
- Q: "Gemini Advanced(소비자 구독)랑 API는 어떻게 다른가요?" → Advanced는 사람용 챗 인터페이스, API는 시스템 통합용. 가격 모델도 완전 다름.

---

### 01_model_matrix — 강의의 정량적 척추

**운영 팁**:

- 9개 조합을 모두 돌리면 약 **3~5분** 소요됩니다. 이 동안 강사는 thinking_level의 비용 영향을 다시 강조하면서 진행.
- 결과 매트릭스가 나오면 **반드시 산점도까지 띄우세요**. 그래야 청중이 "Pro high가 비싸기만 한 게 아니라 의미 있는 latency 차이도 있다"는 걸 시각적으로 체감합니다.
- 도전 과제(시나리오 A/B/C)는 **조 단위 토론**으로 5~10분 시간을 주는 게 좋습니다. ITS 운영 경험자가 답을 들고 있으면 토론이 풍부해집니다.

**예상 질문**:
- Q: "실제 운영에서 thinking_level을 동적으로 바꿔서 routing해도 되나요?" → ✅ 권장 패턴. 입력 길이/복잡도에 따라 분기하는 라우터를 두면 비용 60~75% 절감 가능.
- Q: "thinking 토큰 내용을 볼 수 있나요?" → 기본은 비공개. `thinking_config.include_thoughts=True` 옵션으로 thought summary를 받을 수 있으나 토큰은 동일하게 청구.

---

### 02_cctv_multimodal — 청중이 가장 좋아하는 세션

**운영 팁**:

- Bounding box 시각화가 처음 뜨는 순간이 강의의 하이라이트입니다. 여기서 청중 반응이 폭발합니다.
- "왜 작은 객체는 못 잡나요?" 질문이 거의 반드시 나옵니다 → `media_resolution_high`로 다시 돌려서 차이를 즉석 시연하세요.
- 도로 파손 분류 시나리오는 **유티정보가 실제 구축한 도로관리 시스템과 직접 연결**되는 부분입니다. 청중에게 "여러분 시스템에 어떤 식으로 통합 가능할지" 물어보세요.

**예상 질문**:
- Q: "번호판 마스킹은 어떻게 하나요?" → Gemini는 검출만 하고, 마스킹은 클라이언트단에서 OpenCV로 처리. 개인정보보호법 24조 때문에 ITS 영역에서 매우 중요한 주제. → 강의 외 별도 토픽으로 안내.
- Q: "차종 세분화(승용/SUV/트럭/버스)가 정확한가요?" → 한국 차종 인식은 미국 차종 대비 정확도가 다소 떨어지는 경향. 운영 적용 시 Korean traffic 데이터로 fine-tuning 또는 domain-specific 모델(YOLO 등) 병행 추천.

**개인정보 주의 멘트** (꼭 짚을 것):
> "이 핸즈온은 공개 라이선스 이미지를 쓰지만, 실제 CCTV 영상에는 차량 번호판과 보행자 얼굴이 식별 가능한 상태로 들어있습니다. Gemini API에 이 데이터를 넘기는 건 개인영상정보 처리 행위이므로, 운영 적용 시 (1) Vertex AI로 데이터 학습 비사용 보장, (2) 사전 마스킹 또는 (3) 망분리 환경에서 외부 API 미사용 중 하나를 선택해야 합니다."

---

### 03_long_context_caching — 데이터 가용성이 관건

**운영 팁**:

- VDS 데이터는 합성 데이터입니다. 청중이 **자기 회사가 다루는 진짜 VDS 데이터를 떠올리며** 따라오게 유도하세요.
- 이상 패턴 검출 결과가 4월 23일 사고와 4월 25일 야간 정체를 모두 잡으면 좋고, 하나만 잡으면 청중과 함께 "왜 못 잡았을까" 분석.
- 캐싱 비용 비교 표가 나오면 멈추고 "여러분 회사에 같은 매뉴얼/규정에 매일 반복 질의하는 워크로드가 있나요?"라고 물어보세요. 거의 모두 손을 듭니다.

**자주 발생하는 문제**:

1. **Caching 미지원 모델** — preview 모델은 가끔 caching 미지원
   - 증상: `cache.create()`에서 INVALID_ARGUMENT 또는 NOT_FOUND
   - 해결: 셀에 try/except 처리되어 있어 스킵하고 진행 가능. Caching 셀 전체를 건너뛰고 비용 비교 이론만 설명해도 OK.

2. **토큰 32K 미만**
   - 증상: 7일치 데이터로는 32K 미만일 수 있음 (예전 14일치 셀로 보완)
   - 해결: 노트북에 14일치로 확장하는 셀 이미 포함

**예상 질문**:
- Q: "RAG 안 쓰고 통째로 던지는 게 정말 나은가요?" → 데이터가 1M 토큰 이내이고, 동일 데이터를 여러 번 질의하면 **Long Context + Caching이 RAG보다 단순하고 정확도 높음**. 그 이상이면 RAG 필요. ITS 영역의 일반적 워크로드(매뉴얼, 1주~1달 시계열)는 대부분 Long Context로 커버됨.

---

### 04_function_calling_agent — 가장 흥미롭지만 디버깅이 어려운 세션

**운영 팁**:

- 이 노트북의 함수들은 **모두 모킹**입니다. 강의 시작할 때 "여러분 회사 시스템 함수로 바꿔치면 그대로 운영에 쓸 수 있다"고 강조하세요.
- 시나리오 2(사고 인지 + 후속 조치)는 강의의 가장 인상적인 데모입니다. 실행 후 함수 호출 순서를 화이트보드에 정리해보세요.
- 시나리오 3(안전성 확인)은 "운영 시스템에서 LLM을 어디까지 자동화할 것인가" 토론을 유도하는 핵심 슬롯입니다. 휴먼 인 더 루프 정책을 함께 논의.

**예상 질문**:
- Q: "이걸 우리 ATMS/FTMS에 어떻게 붙이나요?" → 노트북의 `FUNCTION_MAP`을 실제 시스템 API로 바꾸면 됨. 다만 **운영 함수일수록 권한 분리 + 감사 로그 + idempotency 필수**.
- Q: "모델이 잘못된 함수를 호출하면 어떡해요?" → 그래서 시스템 프롬프트의 가드레일이 중요. 더 강력한 보호는 "위험도 높은 함수는 model이 plan만 제시하고 사람이 승인" 패턴.

---

## 데이터 출처 정리

| 데이터 | 출처 | 라이선스 |
|---|---|---|
| 도로 정체 이미지 | Wikimedia Commons | Public Domain / CC |
| 교차로 이미지 (시부야 횡단보도) | Wikimedia Commons | CC |
| 포트홀 이미지 | Wikimedia Commons | Public Domain |
| VDS 시계열 | 합성 데이터 (notebook 내 생성) | N/A |
| 사고 이력 | 합성 데이터 | N/A |

**참가자가 강의 후 자체 활용 시 추천 데이터**:

- [공공데이터포털 - 도로공사 교통량 OpenAPI](https://www.data.go.kr/data/15003078/openapi.do)
- [서울 열린데이터광장 - 도로별 교통량](https://data.seoul.go.kr/)
- [ITS 국가교통정보센터 OpenAPI](https://its.go.kr/opendata/) — 무료 키 발급
- [AI Hub - 도로 CCTV 영상 데이터셋](https://aihub.or.kr/) — 회원가입 필요
- [AI Hub - 도로파손 이미지 데이터셋](https://aihub.or.kr/) — 회원가입 필요

---

## 백업 시나리오 (네트워크 장애 등)

**최악의 경우 대비**:

1. Wikimedia 이미지 URL 변경 → 미리 다운받아 Colab으로 직접 업로드 (`/content/` 경로)
2. Gemini API 장애 → AI Studio Web UI로 동일 프롬프트 시연 (https://aistudio.google.com)
3. 회사 인터넷 장애 → 강사 노트북에서 화면 공유로 시연 진행
4. Colab 자체 장애 → 사전 빌드된 결과물 PDF 또는 강의자료 슬라이드로 대체

---

## 강의 종료 후 청중에게 안내할 다음 단계

1. **본 노트북을 깃허브에 푸시**해서 청중이 추후 참조 가능하게 (저작권 정책 확인 후)
2. 회사 적용 시 권장 순서:
   - PoC: AI Studio + 무료 API 키
   - 내부 검증: Vertex AI + asia-northeast3
   - 운영 배포: Vertex AI + VPC Service Controls + 감사 로그
3. 추가 학습 자료:
   - Gemini 3 Developer Guide: https://ai.google.dev/gemini-api/docs/gemini-3
   - Vertex AI 한국 리전 가이드: https://cloud.google.com/vertex-ai/docs/general/locations
   - Pricing Calculator: https://ai.google.dev/gemini-api/docs/pricing

---

## 강의 자료 정정 사항 (기존 PDF 슬라이드 → 본 핸즈온과 정합성)

기존 슬라이드 자료의 다음 부분을 정정하세요:

| 슬라이드 위치 | 잘못된 내용 | 수정 |
|---|---|---|
| 4교시 CCTV 핸즈온 | "Gemini 1.5 Pro 또는 Gemini 2.0 Flash 권장" | "Gemini 3 Flash 또는 Gemini 3.1 Flash-Lite 권장" |
| Hallucination 데모 | "서울에서 2025년에 가장 사고가 많은 도로는?" | "서울에서 2030년 가장 사고 많을 것으로 예측되는 도로는?" |
| 0교시 도구 비교 | "ChatGPT = 분석/설계, Gemini = 업무" | 단정적 분류 삭제, 둘 다 모든 영역 가능하나 강점 영역만 언급 |
| Vertex 비교표 | (그대로 OK) | 한국 ITS 청중이라 asia-northeast3 강조 추가 |

---

## 마지막 팁

- **시연이 깨지면 당황하지 말고** 청중과 함께 디버깅하세요. 21년차 개발자 청중이라 디버깅 과정 자체에서 배움이 큽니다.
- **모르는 질문은 솔직히 모른다고 하고** 추후 메일로 답변하겠다고 약속하는 게 신뢰도가 더 높아집니다.
- **유티정보가 실제 구축한 사이트** (인천국제공항고속도로, 인천대교, 동탄u-City)를 강의 중 적절히 언급하면 청중 몰입도가 크게 올라갑니다.

행운을 빕니다 🚦
