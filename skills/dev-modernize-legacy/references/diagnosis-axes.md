# Diagnosis axes

작업 절차 §2에서 대상 코드를 *7개 축으로 동시* 스캔. 각 축마다 무엇을 보고, 어떻게 측정하는지.

가능하면 한 번에 하나씩 직렬로 가지 말고 *병렬* 로. Claude 가 직접 Bash/Read/Grep 으로 측정하거나, 사용자에게 데이터를 요청.

## 1. 변경 빈도 — Hotspot 식별

가장 자주 바뀌는 파일 = 가장 자주 깨지는 파일 = 가장 자주 손대야 하는 파일.

```bash
# 최근 6개월 가장 자주 바뀐 파일 상위 30
git log --since="6 months ago" --pretty=format: --name-only \
  | sort | uniq -c | sort -rn | head -30
```

해석: 상위 5~10개 파일이 *전체 변경의 N%* 를 차지하면 → 핫스팟. 우선 후보.

## 2. 테스트 커버리지 — 안전망 부재 영역

- 커버리지 리포트가 있으면 (예: `coverage.xml`, `lcov.info`) 영역별 % 확인
- 없으면 *프록시 측정*:

```bash
# 테스트 파일 / 소스 파일 비율
find . -path ./node_modules -prune -o -name '*.test.*' -print -o -name '*_test.*' -print | wc -l
find . -path ./node_modules -prune -o -name '*.ts' -print -o -name '*.py' -print | wc -l
```

해석: 비율이 1:5 이하 → 안전망 빈약. Phase 1 에 characterization tests 필수.

## 3. 결합도 — 변경 충격 반경

- import 그래프: 파일 A 가 깨졌을 때 같이 안 도는 파일이 몇 개인가
- 순환 의존: A → B → A 가 있으면 분리 어려움

```bash
# 순환 의존 — JavaScript/TypeScript
npx madge --circular src/

# Python
pip install pycycle && pycycle --here

# 일반 — 특정 모듈을 import 하는 파일 수
rg -l "from app.legacy_module import" --type py | wc -l
```

해석: fan-in 높은 모듈 = 자르기 어려운 모듈. Branch by Abstraction 의 추상화 위치 후보.

## 4. 외부 통합 지점 — 깨지면 큰 곳

DB / 외부 API / 메시지큐 / 파일시스템 / 캐시 호출 지점.

```bash
# DB 호출
rg -l 'SELECT |INSERT |UPDATE |DELETE FROM' --type-add 'code:*.{py,ts,js,go,rb}' --type code

# 외부 API 호출
rg -l 'fetch\(|axios\.|requests\.(get|post)|http\.client' --type code

# 환경변수 (외부 의존 신호)
rg -l 'process\.env\.|os\.environ' --type code
```

해석: 각 통합 지점은 *마이그레이션 경계 후보*. 직접 호출이 많으면 Branch by Abstraction 의 추상화 우선 대상.

## 5. 알려진 이슈 영역 — 도메인 지식 발굴

코드 자체에 박힌 *경고문*:

```bash
# 코드 안 TODO/FIXME/HACK
rg -i 'TODO|FIXME|HACK|XXX' --type code -c | sort -t: -k2 -rn | head -20

# 커밋 메시지에서 incident/hotfix 흔적
git log --since="1 year ago" --grep='hotfix\|incident\|revert\|emergency' --oneline | head -30
```

해석: 같은 파일에 TODO 가 모여있으면 *팀이 알면서도 못 고친 영역* — 우선 후보. hotfix 커밋이 자주 들어간 파일은 fragile.

## 6. 빌드·배포 사이클 — 피드백 루프 길이

- 변경 → prod 까지 시간 (CI/CD 파이프라인 평균)
- 테스트 실행 시간 (1회 풀 테스트 분)
- 마지막 N개 배포의 롤백 비율

이 값이 클수록 *작은 변경의 비용* 이 커서 큰 변경으로 묶는 유혹이 생김. 줄이는 게 §5 Phase 1 후보가 되기도 함.

데이터가 없으면 사용자에게 직접 물어 입력 받기.

## 7. 도메인 경계 — bounded context 후보

응집도 높은 그룹은? 같이 자주 바뀌는 파일들은 같은 도메인.

```bash
# 같은 커밋에 자주 함께 등장하는 파일 페어 (협의 분석)
# (정확한 측정은 도구 필요 — 간이 측정만)
git log --since="6 months ago" --name-only --pretty=format:'COMMIT:%H' \
  | awk '/^COMMIT:/{c=$0;next} NF{print c"\t"$0}' \
  | sort | uniq -c | sort -rn | head -50
```

또는 *디렉토리 구조* + *공통 도메인 용어* 로 추정:

```bash
# 디렉토리별 파일 수
find . -type d -not -path '*/node_modules/*' -not -path '*/.git/*' -exec sh -c 'echo "$(ls -1 "$1" | wc -l) $1"' _ {} \; | sort -rn | head -20
```

해석: 응집도 높은 그룹 = 부분 재작성 단위 후보. 도메인 용어가 흩어져 있으면 bounded context 가 흐릿함 → Mikado 로 먼저 정리.

---

## 출력 정리

7개 축 결과를 *한 페이지 진단 리포트* 로:

```markdown
## 진단

- **대상 범위**: <인테이크에서 합의된 모듈/서비스>
- **변경 hotspot**: <상위 5~10 파일과 변경 횟수>
- **테스트 커버리지**: <%, 또는 영역별 부재>
- **결합도**: <순환 의존 / fan-in 높은 모듈>
- **외부 통합**: <DB·API·큐 위치 요약>
- **알려진 이슈 영역**: <TODO 집중 영역, hotfix 다발 파일>
- **빌드·배포 사이클**: <변경→prod 시간, 풀 테스트 시간>
- **도메인 경계 후보**: <응집도 높은 그룹 2~5개>
```

데이터 없이 *추측으로 채우지 말 것*. 못 측정한 축은 "측정 안 됨 — 입력 필요" 로 명시.
