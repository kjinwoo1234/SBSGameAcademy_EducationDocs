# Unreal 챕터 이미지 자리

본문 `![설명](./img/파일명.png)`에 맞춰 캡처를 이 폴더에 넣으면 됩니다. 파일은 아직 없어도 됩니다.
파일명 앞 두 자리 숫자는 그 이미지가 쓰이는 챕터 번호입니다.

| 파일명 | 넣을 화면 |
|---|---|
| `01-blueprint-event-graph-example.png` | 블루프린트 이벤트 그래프: 이벤트 노드와 기능 노드가 선으로 연결된 예시 화면 |
| `01-launcher-library-install.png` | 에픽게임즈 런처의 언리얼 엔진 > 라이브러리 탭에서 엔진 버전을 설치하는 화면 |
| `01-project-browser-first-person.png` | 프로젝트 브라우저에서 게임 > 일인칭 템플릿과 블루프린트를 고르고 저장 위치·이름을 입력한 화면 |
| `01-world-settings-default-pawn.png` | 월드 세팅 패널의 게임 모드 항목에서 디폴트 폰 클래스 드롭다운을 연 화면 |
| `01-level-editor-layout.png` | 레벨 에디터 전체 화면에 메뉴 바·툴바·뷰포트·아웃라이너·디테일 패널·콘텐츠 브라우저 위치를 번호로 표시한 그림 |
| `02-viewport-perspective-menu.png` | 뷰포트 왼쪽 위 원근 버튼을 눌러 상단·정면·좌측 뷰 목록이 펼쳐진 화면 |
| `02-place-actors-panel.png` | 툴바의 추가 버튼을 눌러 액터 배치 패널을 연 화면과 셰이프 분류의 큐브 항목 |
| `02-room-example.png` | 시작용 콘텐츠의 탁자·의자·벽을 배치해 만든 작은 방의 완성 예시 |
| `03-landscape-new-settings.png` | 랜드스케이프 모드의 새 랜드스케이프 설정 패널과 뷰포트에 표시된 초록색 격자 미리보기 |
| `03-brush-options.png` | 스컬프트 탭의 브러시 타입·브러시 감쇠·툴 강도·브러시 크기 옵션과 뷰포트의 이중 원 브러시 표시 |
| `03-landscape-copy-gizmo.png` | 복사 툴에서 기즈모 상자가 선택 영역을 감싼 모습과 툴 옵션의 맞춤·복사 버튼 |
| `03-landscape-spline-mesh.png` | 랜드스케이프 스플라인 세그먼트를 선택하고 디테일 패널의 스플라인 메시 배열에 바닥 메시를 지정한 화면 |
| `04-material-editor-layout.png` | 머티리얼 에디터 화면: 왼쪽 미리보기, 가운데 그래프의 결과 노드, 아래 디테일 패널 |
| `04-landscape-material-layer-group.png` | M_MyLandscape 그래프: TextureCoordinate·Tiling·Multiply가 텍스처 샘플 세 개의 UVs에 연결되고 MakeMaterialAttributes로 모이는 묶음 |
| `04-landscape-paint-layers.png` | 랜드스케이프 페인트 탭의 레이어 목록에서 Grass·Asphalt 레이어를 만들고 지형 일부를 칠한 화면 |
| `05-debug-node-preview.png` | DebugFloat3Values 노드에 노드 프리뷰를 켜서 미리보기 창에 RGB 세 값이 숫자로 표시된 화면 |
| `05-complementary-color-graph.png` | 보색 머티리얼 그래프: RGBToHSV → ComponentMask(R) → Add 0.5 → Frac → Append ← ComponentMask(GB) → HSVToRGB → 최종 색 |
| `06-pingpong-graph.png` | 왕복 흐름 그래프: Time → Frac 과 1-x 를 Min 으로 합치고 Multiply 2 를 거쳐 최종 색에 연결한 노드와 삼각형 모양 그래프 |
| `06-uv-coordinates.png` | UV 좌표계: 왼쪽 위가 (0,0), 오른쪽 위가 (1,0), 왼쪽 아래가 (0,1), 오른쪽 아래가 (1,1)인 사각형 그림 |
| `06-checker-pattern-graph.png` | 체크 패턴 그래프와 5×5 흑백 체크무늬 미리보기 |
| `07-vertical-band-graph.png` | 세로 띠 그래프: 1 - Step(0.3) 과 Step(0.7) 을 Max 로 합친 노드와 가운데 검정 띠 미리보기 |
| `07-named-reroute-comment.png` | 명명된 경유 노드와 코멘트 상자로 세로선·가로선 영역을 정리한 MF_Rectangle 그래프 |
| `07-face-tiling.png` | 원 두 개와 사각형 두 개로 만든 얼굴 마스크가 3×3으로 반복된 미리보기 |
| `08-hsv-rainbow-gradient.png` | 가로 그레이디언트를 H로 사용해 만든 무지개 그레이디언트 그래프와 미리보기 |
| `08-additive-vs-overlay.png` | 원 세 개의 가산 합성 결과와 오버레이 합성 결과를 나란히 비교한 미리보기 |
| `09-blueprint-add-cube-component.png` | 블루프린트 에디터: 왼쪽 컴포넌트 탭에 Cube가 추가되고 가운데 뷰포트에 정육면체가 보이는 화면 |
| `09-tick-delta-rotation.png` | 이벤트 그래프: Event Tick의 Delta Seconds에 90을 곱해 Add Actor World Rotation의 Delta Rotation Z에 연결한 노드 |
| `10-light-types-comparison.png` | 같은 방을 디렉셔널·포인트·스포트·렉트 라이트로 각각 비췄을 때의 빛과 그림자 모양 비교 |
| `10-bp-sun-rotation-graph.png` | BP_Sun 이벤트 그래프: Event Tick의 Delta Seconds × 20을 Add Actor World Rotation의 Delta Rotation Y에 연결한 노드 |
| `10-skylight-on-off.png` | 스카이 애트머스피어만 있을 때와 스카이 라이트를 함께 켰을 때 그늘의 밝기 차이 비교 |
| `11-water-body-lake-placed.png` | 랜드스케이프 위에 호수 워터 바디를 놓아 지형이 자동으로 파이고 물이 채워진 화면 |
| `11-water-body-types.png` | 바다·호수·강·섬 워터 바디가 한 레벨에 배치되어 강이 호수로 이어지는 화면 |
| `12-variable-move-direction.png` | 내 블루프린트 탭에 MoveDirection 변수가 추가되고 디테일 패널에서 벡터 타입과 인스턴스 편집가능이 설정된 화면 |
| `12-overlap-cast-light-on.png` | BP_AutoLight 이벤트 그래프: On Component Begin Overlap → Cast To BP_Patrol → 포인트 라이트의 Set Visibility |
| `13-floating-actor-in-level.png` | 콘텐츠 브라우저의 C++ 클래스 폴더에 FloatingActor가 보이고 레벨에 배치된 큐브가 떠 있는 화면 |
| `13-bp-floating-actor-mesh.png` | BP_FloatingActor 블루프린트 에디터에서 VisualMesh 컴포넌트의 스태틱 메시를 다른 모형으로 바꾼 화면 |
| `15-components-tab-scene-vs-actor.png` | 캐릭터 블루프린트의 컴포넌트 탭: 가로줄 위에 캡슐·화살표·메시·카메라, 아래에 캐릭터 무브먼트가 나뉘어 표시된 화면 |
| `16-gamemode-classes.png` | 월드 세팅의 게임모드 항목: 디폴트 폰 클래스·HUD 클래스·플레이어 컨트롤러 클래스·게임 스테이트 클래스·플레이어 스테이트 클래스·관전자 클래스 목록 |
| `17-character-movement-details.png` | BP_ThirdPersonCharacter에서 캐릭터 무브먼트 컴포넌트를 선택한 디테일 패널의 Max Walk Speed·Jump Z Velocity 항목 |
| `17-mapping-context-wasd.png` | 입력 매핑 컨텍스트 IMC_Default 에디터에서 IA_Move 액션에 W·A·S·D 키가 매핑된 화면 |
| `18-anim-blueprint-editor.png` | 애니메이션 블루프린트 에디터: 왼쪽 프리뷰, 가운데 애님 그래프의 Output Pose 노드, 오른쪽 아래 애셋 브라우저 |
| `18-state-machine-idle-run.png` | 스테이트 머신 그래프: Entry → Idle ⇄ Run 두 스테이트와 양방향 트랜지션 화살표 |
| `18-anim-event-graph-movement.png` | 애니메이션 블루프린트 이벤트 그래프: Event Blueprint Initialize Animation → Get Owning Actor → Cast To Character → Character Movement → Set MovementComponent |
| `18-conduit.png` | Idle → IsMoving 컨듀잇 → Run 으로 연결된 스테이트 머신 그래프 |
| `19-broken-animation-mismatch.png` | 스켈레톤이 맞지 않는 애니메이션을 억지로 재생해 팔다리가 뒤틀린 마네킹 화면 |
| `19-ik-rig-chains.png` | IK_Paladin 에디터: 왼쪽 본 계층 구조와 아래 IK 리타기팅 탭에 Spine·Head·LeftArm·RightArm·LeftLeg·RightLeg 체인이 등록된 화면 |
| `19-ik-retargeter.png` | IK 리타기터 에디터: 팔라딘(소스)과 마네킹(타깃)이 나란히 같은 동작을 하는 미리보기와 체인 매핑 목록 |
| `20-montage-editor.png` | 몽타주 에디터: 위쪽 몽타주 트랙의 섹션 이름, 그 아래 지그재그로 배치된 공격 시퀀스 네 개, 아래쪽 노티파이 트랙 |
| `20-slot-node.png` | 애님 그래프: 스테이트 머신 → Slot 'DefaultSlot' → Output Pose 순서로 연결된 노드 |
| `20-montage-sections-unlinked.png` | 몽타주 섹션 탭: Attack1~Attack4 섹션이 서로 연결되지 않고 따로 나열된 화면 |
| `21-collision-preset-custom.png` | 컴포넌트 디테일 패널의 콜리전 프리셋을 Custom으로 펼쳐 오브젝트 타입과 채널별 무시·오버랩·블록 체크박스가 보이는 화면 |
| `21-project-settings-collision.png` | 프로젝트 세팅 콜리전 창에서 MyCharacter 오브젝트 채널·Attack 트레이스 채널·MyCharacter 프리셋을 추가한 화면 |
| `21-debug-capsule.png` | 공격 시 캐릭터 앞에 그려진 디버그 캡슐: 맞았을 때 초록색, 빗나갔을 때 빨간색 |
| `22-widget-designer-health-text.png` | 위젯 블루프린트 디자이너: 왼쪽 팔레트와 계층구조, 가운데 캔버스에 배치된 HealthText, 오른쪽 디테일 패널의 변수 여부 체크 |
| `23-behavior-tree-layout.png` | 비헤이비어 트리 에디터: Root 아래 셀렉터, 그 아래 추격 시퀀스와 순찰 시퀀스가 배치된 레이아웃 |
| `23-bt-debug-active-node.png` | 플레이 중 비헤이비어 트리 창에서 실행 중인 태스크가 강조되고 블랙보드 키 값이 표시된 화면 |
| `24-navmesh-bounds-visualization.png` | 레벨 뷰포트에 내비 메시 바운드 볼륨과 초록색 내비 메시 영역이 표시된 화면 |
| `24-nav-link-proxy.png` | 층간 낭떠러지에 내비 링크 프록시를 배치해 위·아래 내비 메시를 이은 레벨 화면 |
| `25-widget-editor-layout.png` | 위젯 블루프린트 에디터 디자이너 탭에 번호로 표시한 메뉴 바·툴바·에디터 모드·팔레트·계층구조·비주얼 디자이너·디테일·애니메이션 영역 |
| `25-anchor-presets.png` | 앵커 프리셋 드롭다운과 캔버스 오른쪽 위 구석에 놓인 꽃 모양 앵커 아이콘 |
| `25-progress-bar-style.png` | 배경 이미지와 채우기 이미지를 지정한 프로그레스 바의 퍼센트 0.3·0.7·1.0 상태 비교 |
| `25-progress-bar-percent-bind.png` | 프로그레스 바 디테일 패널에서 퍼센트 옆 바인드 메뉴가 열린 화면 |
| `25-widget-delegate-bind-graph.png` | 위젯 이벤트 그래프: Event Construct → 캐스트 → Bind Event to On Health Changed, 커스텀 이벤트에서 Current ÷ Max 를 Set Percent 에 연결 |
| `26-open-level-blueprint.png` | 툴바의 블루프린트 버튼을 눌러 레벨 블루프린트 열기 메뉴가 보이는 화면 |
| `26-physics-details.png` | 의자를 선택한 디테일 패널의 피직스 필터: 피직스 시뮬레이트·질량·중력 활성화 항목 |
| `26-spawn-and-fire-graph.png` | 레벨 블루프린트: Space Bar → Spawn Actor(BP_Projectile) → Add Impulse, 스폰 위치와 회전에 Get Player Pawn의 위치와 Control Rotation을 연결한 그래프 |
| `27-projectile-components.png` | BP_MyProjectile 블루프린트의 컴포넌트 계층: Collision 아래 Mesh, 그리고 Movement 컴포넌트 |
| `29-muzzle-impact-fx.png` | 발사 순간 총구 이펙트와 명중 지점의 불꽃 이펙트가 보이는 플레이 화면 |
| `30-pie-listen-server-settings.png` | 에디터 플레이 설정 메뉴: 플레이어 수 2, 넷 모드가 리슨 서버로 플레이로 선택된 화면 |
| `30-pie-two-windows.png` | 창 두 개가 떠서 호스트와 클라이언트가 같은 레벨을 플레이하는 화면 |
| `31-lobby-session-ui.png` | 로비 위젯 디자이너 화면: 방 만들기·새로고침·참가 버튼이 세로로 배치된 모습 |
| `33-combat-replication-pie.png` | PIE 두 창에서 같은 발사체 궤적·체력 감소가 보이는 전투 동기화 확인 화면 |
| `36-lobby-play-screen.png` | 로비 접속 UI 플레이 화면: 세션 목록과 참가 버튼이 보이는 예시 |
| `36-hud-scoreboard.png` | 게임 HUD 체력바·점수와 별도 스코어보드 위젯이 함께 보이는 플레이 화면 |
| `37-pie-listen-server-checklist.png` | Chapter 30과 같은 플레이 설정의 플레이어 수·넷 모드와 PIE 창 두 개 배치 |
