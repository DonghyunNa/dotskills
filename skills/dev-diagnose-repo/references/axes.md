# Diagnosis axes — measurement commands

`dev-diagnose-repo` SKILL.md §7축 의 각 축을 *실제로* 측정하는 Bash/Read/Grep 명령. 직접 측정 가능한 축은 이걸 그대로 사용.

## 1. 변경 빈도 — Hotspot

```bash
# 최근 6개월 가장 자주 바뀐 파일 상위 30
git log --since="6 months ago" --pretty=format: --name-only \
  | sort | uniq -c | sort -rn | head -30
```

해석: 상위 5~10개 파일이 *전체 변경의 N%* 를 차지하면 → hotspot. 우선 후보.

## 2. 테스트 커버리지 — 안전망 부재

커버리지 리포트(`coverage.xml`, `lcov.info`) 가 있으면 영역별 % 확인. 없으면 *프록시 측정*:

```bash
# 테스트 파일 / 소스 파일 비율
find . -path ./node_modules -prune -o -name '*.test.*' -print -o -name '*_test.*' -print | wc -l
find . -path ./node_modules -prune -o -name '*.ts' -print -o -name '*.py' -print | wc -l
```

해석: 비율 1:5 이하 → 안전망 빈약. 안전망 구축이 첫 우선순위.

## 3. 결합도 — 변경 충격 반경

```bash
# 순환 의존 — JavaScript/TypeScript
npx madge --circular src/

# Python
pip install pycycle && pycycle --here

# 일반 — 특정 모듈을 import 하는 파일 수
rg -l "from app.legacy_module import" --type py | wc -l
```

해석: fan-in 높은 모듈 = 자르기 어려운 모듈. 추상화 위치 후보.

## 4. 외부 통합 지점

```bash
# DB 호출
rg -l 'SELECT |INSERT |UPDATE |DELETE FROM' --type-add 'code:*.{py,ts,js,go,rb}' --type code

# 외부 API 호출
rg -l 'fetch\(|axios\.|requests\.(get|post)|http\.client' --type code

# 환경변수 (외부 의존 신호)
rg -l 'process\.env\.|os\.environ' --type code
```

해석: 각 통합 지점이 *마이그레이션 경계 후보*. 직접 호출 많으면 추상화 우선 대상.

## 5. 알려진 이슈 영역

코드와 git history 에 박힌 *경고문*:

```bash
# 코드 안 TODO/FIXME/HACK
rg -i 'TODO|FIXME|HACK|XXX' --type code -c | sort -t: -k2 -rn | head -20

# 커밋 메시지에서 incident/hotfix 흔적
git log --since="1 year ago" --grep='hotfix\|incident\|revert\|emergency' --oneline | head -30
```

해석: 같은 파일에 TODO 가 모여있으면 *팀이 알면서도 못 고친 영역*. hotfix 커밋 다발 파일은 fragile.

## 6. 빌드·배포 사이클

- 변경 → prod 까지 시간 (CI/CD 파이프라인 평균)
- 테스트 실행 시간 (1회 풀 테스트 분)
- 마지막 N개 배포의 롤백 비율

직접 측정 어렵고, CI 도구·배포 로그에 의존. **사용자에게 직접 입력 받기** 가 보통 더 빠름.

## 7. 도메인 경계 — bounded context 후보

같이 자주 바뀌는 파일 = 같은 도메인 후보:

```bash
# 같은 커밋에 자주 함께 등장하는 파일 (간이 측정)
git log --since="6 months ago" --name-only --pretty=format:'COMMIT:%H' \
  | awk '/^COMMIT:/{c=$0;next} NF{print c"\t"$0}' \
  | sort | uniq -c | sort -rn | head -50
```

또는 *디렉토리 구조* + *공통 도메인 용어* 로 추정:

```bash
# 디렉토리별 파일 수
find . -type d -not -path '*/node_modules/*' -not -path '*/.git/*' \
  -exec sh -c 'echo "$(ls -1 "$1" | wc -l) $1"' _ {} \; | sort -rn | head -20
```

해석: 응집도 높은 그룹 = 분리 단위 후보. 도메인 용어가 흩어져 있으면 경계가 흐릿함.
