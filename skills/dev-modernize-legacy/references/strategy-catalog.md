# Strategy catalog

레거시 코드 점진 현대화를 위한 8개 패턴. 작업 절차 §3에서 사례별로 3~5개를 *큐레이션* 한다. 전부 나열은 큐레이션 안 한 것.

각 패턴마다 *핵심 / 적합 / 부적합 / 출처* 만. 더 자세한 건 원전 참고.

## 1. Strangler Fig (Martin Fowler, 2004)

새 구현을 옛 구현 옆에 두고 트래픽/호출을 점진 이전. 옛것은 마지막에 제거.

- ✅ 외부 인터페이스(API/UI) 는 유지하면서 내부 교체할 때
- ✅ 다운타임 불가능한 시스템
- ✗ 옛-새 코드가 같은 상태를 동시 변경해야 할 때 (동기화 지옥)

원전: https://martinfowler.com/bliki/StranglerFigApplication.html

## 2. Branch by Abstraction

교체 대상을 인터페이스/추상화 뒤로 숨김 → 옛 구현·새 구현 둘 다 같은 인터페이스 만족. feature flag 로 전환.

- ✅ 내부 라이브러리/모듈 교체
- ✅ 옛것 *돌리면서* 새것 개발해야 할 때
- ✗ 외부 노출 API 자체를 바꿔야 할 때 (이 경우 Strangler)

원전: https://martinfowler.com/bliki/BranchByAbstraction.html

## 3. Characterization Tests (Michael Feathers, *Working Effectively with Legacy Code*)

손대기 전에 *현재 동작*을 그대로 캡처하는 테스트부터 작성. "이게 옳은가" 묻지 말고 "지금 뭘 하나"만 기록.

- ✅ **거의 항상 Phase 1**으로 들어감 — 테스트 없는 코드를 손대는 건 자살
- ✅ 의도 파악하기 어려운 비즈니스 로직

원전: Feathers, *Working Effectively with Legacy Code* (2004), Ch. 13.

## 4. Mikado Method

"이걸 바꾸려면 무엇을 먼저?" 를 반복하며 의존성 그래프 역추적. 잎부터 작업.

- ✅ "어디서부터 시작?" 이 막힐 때
- ✅ 변경이 줄줄이 다른 곳을 깨는 영역

원전: Ellnestam & Brolund, *The Mikado Method* (2014).

## 5. Golden Master / Parallel Run

신·구 시스템 동시 가동 → 같은 입력에 다른 출력 나오면 알람. 신뢰가 쌓이면 옛것 제거.

- ✅ 비즈니스 로직 (계산·가격·매칭) 재작성
- ✅ 결정성 있는 입출력
- ✗ 비결정적 동작 (외부 시간/난수/외부 API 결과 의존)

원전: Fowler, https://martinfowler.com/bliki/ParallelChange.html (관련); GitHub 의 Scientist 라이브러리도 같은 발상.

## 6. 부분 재작성 + 단계적 마이그레이션

모듈 단위로 새로 짜고 하나씩 흡수. 큰 재작성을 작은 재작성 N개로 쪼갬.

- ✅ 도메인 경계가 명확할 때 (bounded context)
- ✗ 강결합으로 경계가 흐릴 때 → 먼저 Mikado 로 경계 찾기

## 7. Big Bang Rewrite

처음부터 다시. *거의 항상 비추천*. 가끔 답.

- ✅ 옛 코드 유지 비용 > 새로 짜는 비용 (드뭄, 정량 근거 필수)
- ✅ 옛 스택이 곧 죽음 (언어·HW EOL, 보안 패치 종료)
- ✗ 대부분 경우 — 옛 코드에 박힌 *암묵 지식* 을 잃는 비용을 과소평가

원전: Joel Spolsky, "Things You Should Never Do, Part I" (2000), https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/

## 8. Leave Alone

의외로 정답. 안 바뀌고 잘 돌면 그대로 둬라.

- ✅ 변경 빈도 낮고 incident 없는 영역
- ✅ 다른 우선순위가 더 큰 ROI

명시적으로 *후보로 올린다*. 다른 옵션의 anchor 가 됨.

---

## 큐레이션 가이드

3~5개로 좁힐 때 기준:

- §1 인테이크의 *리스크 허용도* — 낮으면 Strangler/Branch by Abstraction/Golden Master 쪽으로
- 진단의 *결합도* — 높으면 Mikado 가 필수 후보
- 진단의 *테스트 커버리지* — 낮으면 Characterization Tests 가 거의 무조건 후보
- 진단의 *변경 빈도* — 낮은 영역은 Leave Alone 후보
- §1 의 *제약 시간* — 분기+ 가능하면 부분 재작성, 1달 안이면 Branch by Abstraction
