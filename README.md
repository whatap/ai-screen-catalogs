# 화면별 AI 분석 기능 카탈로그

> **목적**: AI Assistant가 각 화면에서 **구체적인 데이터 기반 답변**을 하려면,
> 화면별 도메인 지식과 분석 절차를 알아야 합니다.
>
> 각 제품 담당자는 본인 화면의 `domainKnowledge`와 `analysisGuides`를 추가해주세요.
> 화면 목록과 tagCountCategories는 이미 프론트 소스 코드에서 검증 완료됐습니다.

---

## 현재 상태

| 제품 | 화면 수 | domainKnowledge | analysisGuides | 담당자 |
|------|---------|-----------------|----------------|--------|
| **CPM (K8s)** | 39 | ✅ 19개 | ✅ 8개 | 김재영 (완료) |
| **APM** | 30 | ⚠️ 1개 | ❌ 0개 | **TODO** |
| **Database** | 23 | ❌ 0개 | ❌ 0개 | **TODO** |
| **Browser** | 16 | ❌ 0개 | ❌ 0개 | **TODO** |
| **Server** | 15 | ❌ 0개 | ❌ 0개 | **TODO** |

---

## 담당자가 할 일

### 1단계: 검수 (10분)

`backend/src/screen-catalogs/{product}.json`을 열고:
- 화면 목록이 맞는지 확인 (빠진 화면이 있으면 추가)
- `tagCountCategories`가 맞는지 확인

### 2단계: 주요 화면 3~5개에 domainKnowledge 추가 (30분)

챗봇이 알아야 할 도메인 지식. **이 화면에서 사용자가 자주 묻는 것, 이 데이터를 어떻게 해석하는지**를 적어주세요.

```json
"domainKnowledge": [
  "TPS가 0이면 에이전트 연결을 확인해야 함",
  "APDEX 0.7 이하면 사용자 경험 문제",
  "응답시간이 갑자기 증가하면 SQL 또는 외부 호출 병목 가능성"
]
```

### 3단계: 핵심 화면 1~3개에 analysisGuides 추가 (1시간)

사용자가 특정 질문을 했을 때 **AI가 따라야 할 분석 절차**. 가장 자주 묻는 질문 유형에 대해 작성하세요.

**왜 필요한가?** 분석 가이드가 없으면 AI가 추상적인 일반론만 답변합니다.
가이드가 있으면 실제 데이터를 조회하고, GPU 이름/수치/비용까지 포함한 구체적 답변을 생성합니다.
또한 `name` 필드는 챗봇 UI에서 **사용자에게 버튼으로 표시**됩니다 (예: "📊 현황 요약", "⚠️ 에러 분석").

```json
"analysisGuides": [
  {
    "name": "🐌 느린 트랜잭션 분석",
    "trigger": "느려|지연|응답시간|성능|문제",
    "instruction": "1단계: TPS 추이 조회 → 전체 응답시간 확인\n2단계: 에이전트별 응답시간 비교 → 어떤 인스턴스가 느린지\n3단계: 액티브TX 쌓이는지 확인\n답변: [느린 인스턴스 목록] → [응답시간 수치] → [조치 방안]"
  }
]
```

| 필드 | 필수 | 설명 |
|------|------|------|
| `name` | ✅ | 챗봇 UI에 버튼으로 표시되는 이름 (이모지 포함 권장) |
| `trigger` | ✅ | 이 가이드가 동작할 질문 키워드 (`\|`로 구분) |
| `instruction` | ✅ | AI가 따를 분석 절차 (단계별로 작성) |

### 4단계: Yard API 화면 (선택)

MXQL이 아닌 Yard 전용 API를 사용하는 화면이면 `yardApis`를 추가하세요.

```json
"yardApis": [
  {
    "type": "txmap",
    "path": "/realtime",
    "description": "실시간 트랜잭션 데이터",
    "params": { "index": 0, "loop": 0, "filters": [] },
    "fields": ["txid", "elapsed", "sqlTime", "sqlCount", "cpu", "mem"]
  }
]
```

---

## 제품별 우선순위 화면

### APM — 담당자: TODO

| 우선순위 | 화면 | 이유 |
|---------|------|------|
| 🔴 높음 | `dashboard` | 가장 많이 사용하는 화면. TPS/응답시간/에러 분석 |
| 🔴 높음 | `transaction_map` | 트랜잭션 분석의 핵심. yardApis 추가 필요 |
| 🟡 중간 | `trending` | 성능 추이 분석 |
| 🟡 중간 | `stat_service` | 서비스별 통계 |
| 🟢 낮음 | `daily_hitmap` | 히트맵 분석 |

### Database — 담당자: TODO

| 우선순위 | 화면 | 이유 |
|---------|------|------|
| 🔴 높음 | `instance_monitoring` | DB 실시간 모니터링 |
| 🔴 높음 | `db_lock_tree` | 락 문제 진단 |
| 🟡 중간 | `top_sql` | SQL 성능 분석 |
| 🟡 중간 | `vacuum_analysis` | PostgreSQL vacuum (이미 카테고리 상세) |
| 🟢 낮음 | `session_history` | 세션 분석 |

### Browser — 담당자: TODO

| 우선순위 | 화면 | 이유 |
|---------|------|------|
| 🔴 높음 | `browser_live_stats` | 메인 대시보드 |
| 🟡 중간 | `page_load_statistics` | 페이지 로드 분석 |
| 🟡 중간 | `error_statistics` | 에러 분석 |

### Server — 담당자: TODO

| 우선순위 | 화면 | 이유 |
|---------|------|------|
| 🔴 높음 | `dashboard/resource_board` | 리소스 보드 |
| 🟡 중간 | `server/list` | 서버 목록 |
| 🟡 중간 | `gpu_performance` | GPU 성능 (yardApis 필요) |

---

## 파일 위치

```
backend/src/screen-catalogs/
├── apm.json        ← APM 담당자
├── browser.json    ← Browser 담당자
├── cpm.json        ← ✅ K8s 완료 (김재영)
├── database.json   ← DB 담당자
└── server.json     ← Server 담당자
```

---

## 예시: K8s GPU 트렌드 (완성된 예시 참고)

```json
{
  "gpu/trend": {
    "name": "GPU 트렌드",
    "description": "물리 GPU별 사용률 추이. MIG 개별/Pod 매핑은 볼 수 없음.",
    "tagCountCategories": ["kube_pod"],
    "promqlMetrics": ["DCGM_FI_DEV_WEIGHTED_GPU_UTIL", "DCGM_FI_PROF_GR_ENGINE_ACTIVE"],
    "mxqlQueries": [{
      "description": "GPU 트렌드 히트맵 (kv.load)",
      "template": "kv.load {table: stat_gpu_trend_hitmap-$epoch}..."
    }],
    "domainKnowledge": [
      "물리 GPU별 사용률 추이만 표시",
      "MIG 개별/Pod 매핑은 볼 수 없음 → GR_ENGINE_ACTIVE PromQL로 조회"
    ],
    "analysisGuides": [
      { "trigger": "최적화|비용|절감", "instruction": "1단계: GPU 사용률 조회 → 2단계: 분류 → 3단계: [현황][Idle회수][스팟전환][MIG통합][총절감액]" },
      { "trigger": "상태|현황", "instruction": "GPU 수, Active/Idle, 사용률 목록" },
      { "trigger": "파드|pod|매핑", "instruction": "GR_ENGINE_ACTIVE PromQL → Pod 매핑 표" },
      { "trigger": "MIG|mig", "instruction": "MIG UUID, 사용률, Pod 표" }
    ]
  }
}
```

---

## FAQ

**Q: 어떤 화면부터 해야 하나요?**
A: 🔴 높음 우선순위 화면 1~2개만 먼저 해주세요. 가장 자주 쓰는 화면이 효과가 큽니다.

**Q: domainKnowledge에 뭘 써야 하나요?**
A: "이 화면에서 고객이 자주 묻는 질문"에 대한 답변을 적으면 됩니다.
예: "APDEX가 뭔지", "이 수치가 높으면/낮으면 뭘 의미하는지"

**Q: analysisGuides의 trigger는 어떻게 정하나요?**
A: 사용자가 실제로 쓸 키워드를 `|`로 나열. 예: `느려|지연|성능|응답시간`

**Q: 작성 후 어떻게 제출하나요?**
A: `screen-catalogs/{product}.json`을 직접 수정하여 PR 올려주시거나,
JSON 내용을 Slack `#ai-chatbot` 채널에 올려주세요.
