# 충돌과 물리

언리얼 충돌은 `Collision Preset`과 채널 응답 설정이 핵심이며, 양쪽 객체 설정을 함께 확인해야 문제를 빨리 찾을 수 있습니다.

## 학습 목표
- Hit과 Overlap 이벤트의 차이를 설명할 수 있다.
- 충돌 채널 응답을 의도에 맞게 설정할 수 있다.
- 이벤트 미호출 문제를 점검 절차로 해결할 수 있다.

## 핵심
- `Hit`: 물리 충돌 시 호출되는 이벤트
- `Overlap`: 트리거 영역 진입/이탈 감지 이벤트
- 확인 항목: `Collision Enabled`, `Object Type`, `Response`

## 단계별 실습
1. 플레이어에 Capsule Collision, 아이템에 Sphere Collision을 설정한다.
2. 아이템에서 `OnComponentBeginOverlap` 이벤트를 연결한다.
3. 플레이어가 접근하면 메시지를 출력하도록 구성한다.
4. 물리 반응이 필요한 오브젝트에는 `Simulate Physics`를 켜고 Hit 이벤트를 확인한다.

## 실수 포인트
- 한쪽만 Overlap 허용으로 설정해 이벤트가 발생하지 않는다.
- 콜리전 채널이 Block/Ignore로 잘못 설정되어 의도와 다른 반응이 나온다.

## 이해 점검 체크리스트
- [ ] Hit과 Overlap의 쓰임새를 구분할 수 있는가?
- [ ] 충돌 관련 3가지 설정 항목을 직접 점검했는가?
- [ ] 테스트 상황에서 이벤트가 정상 호출되는가?

## 다음 학습 추천
- [UMG UI 기초](./06-umg-ui.md)

## 셀프 퀴즈
1. 트리거 영역 진입 감지에 주로 사용하는 이벤트는 무엇인가?
2. 이벤트가 호출되지 않을 때 반드시 함께 확인해야 하는 것은 무엇인가?

## 정답
1. Overlap 이벤트 (`OnComponentBeginOverlap`)
2. 양쪽 오브젝트의 Collision 설정

---

[상위 문서로 돌아가기](./README.md)
