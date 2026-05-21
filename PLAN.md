# Day 5 강의 (ops-day5) 계획 — 컴팩트 재개용

## 메타
- **강의**: AI 실무 생산성 혁신 Day 5 (마지막 날) · 도시바코리아 + 아모레퍼시픽 개발자 15~20명
- **시간**: 15:00 ~ 18:00 (3시간)
- **repo**: github.com/daniel8824-del/ops-day5 → https://daniel8824-del.github.io/ops-day5/
- **이전 Day**: har-day1 · pos-day2 · rag-day3 · mcp-day4 (4 사이트 완성, cross-link)
- **패턴**: rag-day3 / mcp-day4 CSS 재사용 (Pretendard + JetBrains Mono · white/cyan editorial)

## 현재 상태 (컴팩트 시점)
- `ops-day5/docs/index.html` — head+CSS (mcp-day4 line 1~153) 복사 완료 + Day 5 title/meta 치환. **body 미작성** (다음 작업).
- `ops-day5/docs/downloads/`, `ops-day5/data/` 폴더 생성됨.
- 도시바 자료 미수집, OpenCrab ingest 미실행, 데이터팩 미생성.

## Day 5 사이트 구조 (확정)

```
§ 도입 (강사 구두 · 사이트는 bullet 요약만)
  - 17차시 실전 시스템 개발 사례 (ERP·콘텐츠·모니터링·오케스트레이션)
  - 18차시 개발 중 실패·사고 8선 (환경변수·API key·무단배포·배치중복·
    DB동시접근·과잉구현·비용폭주·외부정책변경) ← 슬라이드 없이 구두

§ 온톨로지 실습 ★ (Day 5 메인 · 시간 대부분)
  ① 강사 시연: 도시바 POS ontology + 그래프 (OpenCrab apps/web · d3)
  ② 학습자: 강사 ZIP 팩 install → ontology_query 실습 → 의견 수렴
  ③ 학습자: 본인 문서 ingest → 본인 ontology → 본인 팩(ZIP) 보관
  ④ 아이디에이션: brainstorming(Day4 설치)으로 발산 → 스킬/MCP tool 설계
     예 "결제실패 → 영향 entity → 티켓" 패턴을 MCP tool로
  ⑤ 그래프화 (선택 심화): apps/web 코드 or MCP 호출법 (여유 학습자만)

§ AI-Ops 전환 로드맵 (마무리 · 방법론 + 본인 로드맵 직접 구성 실습)
  4단계 매핑:
    ① 개인 지시문      = Day 1~2 (CLAUDE.md·스킬)
    ② 팀 표준화        = ontology 데이터팩 (ZIP 팀 공유)
    ③ 파이프라인        = 스킬/MCP tool chain
    ④ 오케스트레이션    = 멀티 MCP + 데이터팩 + 대시보드
  + 1주·1개월·3개월 액션 플랜 (학습자 본인 조직 직접 작성)
  + Day 1~5 총정리 + Q&A

  ★ "왜 온톨로지인가" 정의 (단순 스킬 공유 그 이상):
    스킬 = "어떻게 행동"(절차) / 온톨로지 = "도메인이 무엇·어떻게 연결"(의미)
    스킬만으로 부족: 팀 커지면 각 AI가 같은 도메인 제각각 이해 (파편화 ·
                     "결제"="Payment"="거래" 제각각)
    온톨로지가 채우는 것 (스킬 못 함):
      ① 팀 공통 의미 기반 (모든 스킬·MCP·에이전트가 같은 entity·관계 참조)
      ② 영향 분석 (이거 바꾸면 어디까지? — traversal · 스킬은 절차만)
      ③ 도메인 지식 자산화 (데이터팩 축적·공유·버전관리)
    → 오케스트레이션이 가능하려면 모든 에이전트가 같은 의미 기반을 봐야 함
      = 그게 온톨로지. ②팀 표준화 = 스킬 공유의 다음 차원.
```

## 온톨로지 설명 (crab-guide 반영 · 본질만 · 홍보·제품구조 제외)

```
넣기:
  · ontology = entity + 관계 그래프 (의미 layer)
  · Graph RAG vs 일반 RAG (drug→성분→부작용→졸음 = 관계 traversal)
  · 왜 중요: "데이터 양 X · 관계 이해" (AI 진화 7단계 중 ④ Ontology = 현재
    ③Agent 다음 단계 · AGI 전 병목)
  · 5개념 (ontology · ingest · 임베딩 · RAG 종류 · 그래프)
  · OpenCrab = 이 온톨로지 만드는 실습 도구 (그 이상 설명 X)

빼기 (사용자 명시):
  · 홍보 (FREE/PRO/EXPERT 가격 · marketplace 판매 · "온톨로지 셀럽" ·
    5/31 무료 이벤트 · opencrab.sh 가입 유도)
  · LocalCrab vs OpenCrab 이중구조 · privacy · 클라우드 (제품 구조 설명 불필요)
```

crab-guide 출처: https://crab-guide.pages.dev/ (#home #roadmap #architecture #insights)

## 기술 검증 완료 (실측)

```
데이터팩 = OpenCrab Pack ZIP (JSONL/JSON)
  내부: manifest.json + graph/nodes.jsonl + graph/edges.jsonl +
        neo4j/import.cypher(선택) + sample_queries.json
  기본 저장: SQLite + JSON + Chroma (./opencrab_data)

그래프 시각화 = d3 (apps/web) · Neo4j 불필요
  OpenCrab/apps/web/components/GraphView.tsx (d3 7.9.0)
  fetch(localhost:8000/api/...) → getNodes/getEdges → d3 렌더
  실행: cd OpenCrab/apps/web && npm install && npm run dev → /dashboard

Neo4j = 강사 full pack 검증·export 시만 (옵션) · 학습자 불필요

OpenCrab MCP 30 tools (실측):
  schema_pack: saas(설치됨)/biomedical/legal 3 빌트인
  grammar: 9 spaces (subject·resource·evidence·concept·claim·
           community·outcome·lever·policy) + meta_edges + impact I1~I7
  핵심: ontology_ingest · ontology_add_node/edge · ontology_query/impact ·
        schema_pack_install/list/uninstall
  ※ ZIP export는 MCP 도구 X → dashboard/LocalCrab CLI 담당
```

## 도시바 자료 수집 (2 분야)

```
공개 (tgcs04.toshibacommerce.com · access-control-allow-origin: * · ingest OK):
  - TCx Sky Tech Specs PDF
  - ELERA Self-Service Specs PDF
  - System 7 Tech Specs PDF
  - 웨비나 mp4 (prod.tos2768695.mp4 · HTTP 200 · 2026-05-16)
공개 X (로그인 벽):
  - commerce.toshiba.com/wps/portal 매뉴얼 본문 (Enterprise ID)

분야 1 (POS 제품): TCx Sky · ELERA · TCx Elevate · System 7 · 4690_OS ·
                   Wind_River · TCx Pay · TCx 620~300 · M 시리즈
분야 2 (ITO 운영): Jira · Confluence · POS운영 · Ticket · Incident ·
                   Operator (사내 정보 X → 일반 ITO 패턴 골격)
                   Atlassian: developer.atlassian.com + acli 공개

수집 방식 미정: A. firecrawl 방대 크롤링 / B. PDF URL 직접 다운로드
```

## 역할 분담 (확정)

```
강사:
  1. 도시바 POS 공개 자료 → OpenCrab ontology_ingest → ontology 구축
  2. apps/web 대시보드 시연 (도시바 POS 그래프 · d3)
  3. ZIP 데이터팩 export → ops-day5 다운로드 카드

학습자:
  1. OpenCrab MCP 설치 (기설치 전제 또는 자연어 한 줄)
  2. 강사 팩 install → ontology_query 실습
  3. 본인 문서 ingest → 본인 ontology 팩 보관
  4. brainstorming → 스킬/MCP 아이디에이션
  5. (선택) 그래프화

QMD = Day 3 별개 (문서 검색) · 이번과 무관
yucrates = 강사 커스텀 중 (세계사 학술 CIDOC CRM) · 이번 X
```

## 다음 작업 (컴팩트 후 재개)

```
1. ops-day5/docs/index.html body 작성
   nav(Day1~5) + hero + §도입 bullet + §온톨로지 이해(본질만) +
   §온톨로지 실습(①~⑤) + §AI-Ops 로드맵 + footer
2. 도시바 POS 공개 자료 수집 (firecrawl or PDF) → OpenCrab ingest → 데이터팩 ZIP
3. apps/web 그래프 시연 준비 (강사용)
4. ops-day5 repo 생성 + GitHub Pages + Day1~4 cross-link
```

## 강사 환경 메모
- OpenCrab 원본: /home/daniel/OpenCrab (apps/web · opencrab MCP)
- 강사 커스텀: /home/daniel/opencrab-study (yucrates · 세계사용)
- OpenCrab txt: C:\Users\daniel\Desktop\오픈클랩_260520.txt (베타 카톡 로그 12234줄 · Day 5 도입 인사이트 활용 가능)
