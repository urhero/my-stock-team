# my-stock-team

한국 주식 리서치 에이전트 팀 — Claude Code 플러그인.

종목명 하나로 **수집(3종 병렬) → 리스크 종합 → 리포트 작성 → 검증 게이트 → PPTX 생성**까지 전체 파이프라인을 실행합니다. 무료 공개 데이터 기반 **학습용** 분석 도구이며 투자 권유가 아닙니다.

## 구성

| 종류 | 이름 | 역할 |
|------|------|------|
| 에이전트 | `fundamental-analyst` | 재무·실적·공시 (DART) — 3개년 요약표 + 코멘트 3줄 |
| 에이전트 | `market-technical-analyst` | 주가·추세·거래 (FinanceDataReader) — 가격 요약표 + 추세 코멘트 |
| 에이전트 | `news-sentiment-analyst` | 뉴스·이슈·심리 (WebSearch) — 핵심 이슈 3~5개 + 심리 한 줄 |
| 에이전트 | `risk-manager` | 3종 결과 종합 → 핵심 리스크 3가지 + 모니터링 (pykrx 유동성 보강) |
| 에이전트 | `verification-analyst` | 완성 리포트 품질 점검(4축) — 문제 표 + 통과/보류 판정 |
| 스킬 | `report-pptx` | `reports/{종목}.md` → 디자인된 PPTX (다크 표지·KB 옐로우·맑은 고딕) |
| 커맨드 | `/stock-report <종목명>` | 위 전체 파이프라인 실행 |

## 설치

```
/plugin marketplace add <이 저장소 경로 또는 URL>
/plugin install my-stock-team@my-stock-team
```

## 사전 준비

- **Python 패키지**: `pip install python-pptx matplotlib finance-datareader pykrx opendartreader`
- **DART API 키** (재무 분석용): [DART OpenAPI](https://opendart.fss.or.kr)에서 무료 발급 후, 작업 디렉터리 `.env`에 `DART_API_KEY=<발급키>` 설정. **키는 절대 커밋하지 마세요.** 키가 없으면 재무 파트는 "확인 불가"로 처리되고 나머지는 동작합니다.
- **폰트**: 맑은 고딕(Windows 기본). 다른 OS는 `skills/report-pptx/build_pptx.py` 상단 `KFONT`/`MPL_FONT`를 설치된 한글 폰트로 변경.

## 사용

```
/stock-report 삼성전자
```

부분 실행도 가능합니다: "삼성전자 재무만", "뉴스 분위기만", "리포트 검증만" 등 — 해당 에이전트로 자동 라우팅됩니다.

## 가드레일 (모든 산출물에 적용)

- 모든 수치에 (출처: 데이터명, 연도/날짜) 표기 — 출처 없는 수치 미게재
- 미확보 데이터는 "확인 불가", 출처 없는 뉴스·루머는 "미확인"
- 매수/매도/보유·목표가·비중 확대/축소 등 투자 행동 단정 금지 — 판단 근거까지만, 최종 판단은 사람
- 리포트 첫머리 "무료 공개 데이터 기반 학습용" 명시, 끝에 출처·기준일 목록 + 면책 문구
