# Unreal Engine 자습 자료

언리얼 엔진 수업의 강의 순서를 따라가는 학습 자료입니다.  
블루프린트와 에디터로 레벨을 꾸미는 것에서 시작해, **언리얼 C++**로 전투·AI·UI를 만들고 **멀티플레이**까지 이어갑니다.  
장 번호는 학습 순서입니다. 앞 장에서 만든 프로젝트와 코드를 뒤 장에서 이어서 쓰므로 순서대로 진행합니다.

## 권장 대상

- 언리얼 엔진을 처음 다루는 학습자
- 블루프린트로 시작해 C++ 게임플레이까지 익히고 싶은 학습자

## 선수지식

- Part 01은 프로그래밍 지식 없이 따라갈 수 있습니다.
- Part 02부터는 C 변수·함수·포인터와 C++ 클래스·상속 기초가 필요합니다. [C](../c/README.md)와 [C++](../cpp/README.md) 과정을 먼저 마치기를 권합니다.

## 읽기 전에

- 에디터 메뉴와 패널 이름은 **한글 에디터** 기준으로 적었습니다. 블루프린트 노드와 C++ 이름은 영문 그대로 적었습니다.
- 메뉴 이름과 템플릿 구성은 **엔진 버전**에 따라 다를 수 있습니다. 본문과 다르면 같은 역할을 하는 항목을 찾아 진행하세요.
- 본 과정 문서에는 설치용 공식 주소 외의 외부 사이트 링크를 넣지 않습니다.
- 본문의 `![…](./img/….png)`는 **아직 파일이 없는 자리표시**입니다. 캡처를 `docs/unrealEngine/img/`에 같은 파일명으로 넣으면 됩니다.
- Part 02 이후의 C++ 예제는 프로젝트 이름을 `Practice`로, 템플릿 캐릭터 클래스를 `AMyCharacter`로 적습니다. 자신의 프로젝트 이름과 클래스 이름으로 바꿔 읽으세요.

## 목차

### Part 01. 블루프린트·에디터 입문

- [Chapter 01 언리얼 시작하기](./01-unreal-first-steps.md)
- [Chapter 02 레벨 디자인](./02-level-design.md)
- [Chapter 03 랜드스케이프](./03-landscape.md)
- [Chapter 04 머티리얼 첫걸음과 랜드스케이프 페인트](./04-material-landscape-paint.md)
- [Chapter 05 머티리얼 — 수와 색](./05-material-number-color.md)
- [Chapter 06 머티리얼 — 시간과 그레이디언트](./06-material-time-gradient.md)
- [Chapter 07 머티리얼 — 도형과 합성](./07-material-shapes-composition.md)
- [Chapter 08 머티리얼 — 선형 보간](./08-material-lerp.md)
- [Chapter 09 블루프린트 첫 액터](./09-blueprint-first-actor.md)
- [Chapter 10 라이팅 기초](./10-lighting-basics.md)
- [Chapter 11 워터 시스템](./11-water-system.md)
- [Chapter 12 블루프린트 인터랙션 기초](./12-blueprint-interaction.md)

### Part 02. 언리얼 C++ 게임플레이

- [Chapter 13 언리얼 C++ 첫걸음 — 부유하는 큐브](./13-cpp-floating-actor.md)
- [Chapter 14 리플렉션·이름 규칙·컨테이너·어서트](./14-reflection-containers-assert.md)
- [Chapter 15 액터와 컴포넌트](./15-actor-component.md)
- [Chapter 16 로깅과 게임모드](./16-logging-gamemode.md)
- [Chapter 17 3인칭 템플릿 분석](./17-third-person-template.md)
- [Chapter 18 애니메이션 블루프린트와 스테이트 머신](./18-anim-blueprint-state-machine.md)
- [Chapter 19 Mixamo와 애니메이션 리타기팅](./19-mixamo-retargeting.md)
- [Chapter 20 애니메이션 몽타주·델리게이트·노티파이](./20-anim-montage-notify.md)
- [Chapter 21 충돌 설정과 공격 판정](./21-collision-damage-trace.md)
- [Chapter 22 데미지와 게임 규칙](./22-damage-playerstate-gamemode.md)
- [Chapter 23 비헤이비어 트리](./23-behavior-tree.md)
- [Chapter 24 내비게이션](./24-navigation.md)
- [Chapter 25 UMG로 UI 만들기](./25-umg-widget-ui.md)
- [Chapter 26 실습 — 창고 부수기 게임](./26-practice-warehouse-wrecker.md)

### Part 03. FPS 미니 게임 완성

- [Chapter 27 발사체와 히트스캔](./27-projectile-hitscan.md)
- [Chapter 28 게임 기획과 구현](./28-game-design-impl.md)
- [Chapter 29 FPS 시스템 통합](./29-fps-system-integration.md)

### Part 04. 네트워킹

- [Chapter 30 멀티플레이 기초](./30-multiplayer-basics.md)
- [Chapter 31 온라인 서브시스템과 세션](./31-online-subsystem-session.md)
- [Chapter 32 체력 리플리케이션](./32-health-replication.md)
- [Chapter 33 전투 리플리케이션](./33-combat-replication.md)
- [Chapter 34 네트워크 역할·권한](./34-network-roles.md)
- [Chapter 35 캐릭터·이동 리플리케이션](./35-character-movement-replication.md)
- [Chapter 36 네트워크 UI·점수](./36-network-ui-score.md)
- [Chapter 37 서버 모델·게임 플로우](./37-server-model-gameflow.md)

---

[상위 문서로 돌아가기](../../README.md)
