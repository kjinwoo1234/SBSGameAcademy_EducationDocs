# 부록 A New Scene 창과 씬 템플릿

[Chapter 02](./02-scene-gameobject.md)에서 **File > New Scene**을 누르면 템플릿 여러 개가 있는 창이 열립니다. 본문에서는 **Lit 2D (URP)**를 고르라고만 했습니다. 이 부록에서는 나머지 템플릿이 무엇이고, 이름에 붙은 `Lit`, `URP`, `Built-in`, `Deprecated`가 무슨 뜻인지, 잘못 골랐을 때 어떻게 되는지를 설명합니다.

본문을 따라가는 데 이 부록이 꼭 필요하지는 않습니다. "왜 Lit 2D여야 하지?", "다른 걸 고르면 안 되나?"가 궁금할 때 읽으세요.

## 학습 목표

- 씬 템플릿이 무엇이고 왜 여러 개가 있는지 설명할 수 있다.
- 템플릿 이름의 `Lit`, `2D`, `URP`, `Built-in`, `Deprecated`가 각각 무엇을 뜻하는지 말할 수 있다.
- New Scene 창의 템플릿 7개가 각각 무엇을 미리 넣어 주는지 구분할 수 있다.
- 이 과정(2D 게임, Universal 2D 프로젝트)에서 Lit 2D (URP)를 고르는 이유를 설명할 수 있다.
- 다른 템플릿을 잘못 골랐을 때 알아차리고 바로잡을 수 있다.

## 본문

### A-1 씬 템플릿이란

**씬 템플릿**(Scene Template)은 새 씬을 만들 때 쓰는 "밑그림"입니다. 빈 씬에서 시작하면 카메라와 조명부터 직접 만들어야 합니다. 템플릿을 고르면 그 용도에 맞는 오브젝트와 설정이 미리 들어 있는 씬으로 시작합니다.

워드 프로그램의 "새 문서" 창을 떠올리면 쉽습니다. 빈 문서, 이력서 양식, 보고서 양식이 있고, 무엇을 고르든 결국 같은 종류의 문서 파일이 만들어집니다. 씬 템플릿도 마찬가지입니다. **어떤 템플릿으로 만들든 결과는 똑같은 씬 파일(`.unity`)** 이고, 차이는 처음에 무엇이 들어 있느냐뿐입니다. 그래서 잘못 골라도 나중에 오브젝트를 넣고 빼서 얼마든지 고칠 수 있습니다.

그렇다면 2D 프로젝트인데 왜 3D용으로 보이는 템플릿까지 나올까요? 템플릿은 프로젝트에 설치된 **패키지**가 함께 가져옵니다. Chapter 01에서 고른 Universal 2D 프로젝트에는 URP(아래 A-2) 패키지가 들어 있고, 이 패키지는 2D와 3D 템플릿을 모두 들고 옵니다. 여기에 Unity가 기본으로 가진 템플릿까지 합쳐져서 목록이 길어진 것입니다. 목록에 있다고 해서 모두 이 프로젝트에 맞는 것은 아닙니다.

### A-2 이름을 읽는 법

템플릿 이름은 `이름 (괄호)` 모양입니다. 괄호 앞은 **무엇이 들어 있는지**, 괄호 안은 **어떤 렌더 파이프라인용인지**를 뜻합니다.

#### 렌더 파이프라인: URP와 Built-in

**렌더 파이프라인**(Render Pipeline)은 씬에 있는 오브젝트를 화면의 픽셀로 그려 내는 방식 전체를 말합니다. 빛을 어떻게 계산할지, 그림자를 어떻게 만들지, 어떤 순서로 그릴지가 모두 여기서 정해집니다. Unity에는 이 방식이 여러 개 있고, 프로젝트마다 하나를 골라 씁니다.

| 이름 | 뜻 | 이 과정과의 관계 |
|---|---|---|
| **URP** (Universal Render Pipeline) | PC, 모바일, 콘솔 어디서나 가볍게 돌도록 만든 현재의 기본 파이프라인. 2D 전용 기능(2D 조명 등)이 있다. | **이 프로젝트가 쓰는 것.** Chapter 01에서 Universal 2D 템플릿으로 만들었기 때문 |
| **Built-in** (Built-in Render Pipeline) | URP가 나오기 전부터 있던 옛 기본 파이프라인 | 쓰지 않음 |
| HDRP (High Definition Render Pipeline) | 고사양 PC·콘솔용 사실적인 3D 그래픽 파이프라인 | 쓰지 않음. 이 프로젝트에는 설치되어 있지 않아 목록에도 없다 |

중요한 점은 **씬 템플릿의 파이프라인과 프로젝트의 파이프라인이 같아야 제대로 보인다**는 것입니다. 이 프로젝트는 URP이므로 이름에 `(URP)`가 붙은 템플릿이 맞는 짝입니다.

#### Deprecated

**Deprecated**는 "더 이상 권장하지 않으며 앞으로 없어질 예정"이라는 뜻입니다. 지금은 동작하지만 새 기능이 붙지 않고, 언젠가 목록에서 사라집니다. 이름에 `Built-in / Deprecated`가 붙은 템플릿은 "옛 Built-in 파이프라인용이고, 이제 쓰지 말라"는 표시입니다. 새로 시작하는 작업에서는 고르지 않습니다.

#### Lit과 Unlit

**Lit**은 "조명을 받는다"는 뜻입니다. Lit으로 그려지는 물체는 씬에 있는 조명의 밝기와 색에 따라 밝아지거나 어두워집니다. 반대말은 **Unlit**으로, 조명과 관계없이 원래 색 그대로 그려집니다.

`Lit 2D`는 "2D 조명을 받는 2D 씬"이라는 뜻입니다. 그래서 이 템플릿에는 씬 전체를 비추는 조명이 처음부터 들어 있습니다. 조명이 있으니 나중에 횃불 주변만 밝히거나, 동굴을 어둡게 하는 연출도 할 수 있습니다.

#### 2D와 3D: 카메라의 Projection

2D 템플릿과 3D 템플릿의 가장 큰 차이는 카메라가 세상을 보는 방식, 곧 **Projection**(투영)입니다.

| Projection | 보이는 모습 | 쓰는 곳 |
|---|---|---|
| **Orthographic**(직교) | 멀리 있어도 크기가 줄지 않는다. 원근감이 없는 평평한 화면 | 2D 게임 |
| **Perspective**(원근) | 멀리 있을수록 작게 보인다. 사람 눈과 같은 화면 | 3D 게임 |

2D 템플릿은 Orthographic 카메라를, 3D 템플릿은 Perspective 카메라를 넣어 줍니다. 횡스크롤 게임에서 Perspective 카메라를 쓰면 캐릭터가 화면 가장자리로 갈수록 미묘하게 비스듬히 보이고, 바닥 타일의 크기가 깊이에 따라 달라 보입니다.

### A-3 New Scene 창 둘러보기

![New Scene 창: 1 Lit 2D (URP) 템플릿, 2 고른 템플릿의 설명(카메라와 Global Light 2D), 3 Create 버튼](./img/02-new-scene-dialog.png)

창은 왼쪽 목록과 오른쪽 상세 정보로 나뉩니다.

- **Scene Templates in Project**: 이 프로젝트에서 쓸 수 있는 템플릿 목록입니다. 오른쪽 위 검색 칸에 이름을 쳐서 찾을 수 있고, 오른쪽 아래 슬라이더로 썸네일 크기를 바꿉니다.
- **핀 아이콘**: Lit 2D (URP) 썸네일 오른쪽 아래의 압정 모양은 "고정(Pin)"입니다. 고정한 템플릿은 목록 맨 앞에 오고, 창을 열면 처음부터 선택되어 있습니다. 템플릿에 마우스를 올리면 나오는 압정을 눌러 켜고 끌 수 있습니다.
- **오른쪽 상세 정보**: 고른 템플릿의 이름, 템플릿 파일 위치, 설명(Description)이 나옵니다. 템플릿을 고를 때는 썸네일보다 이 **Description**을 읽는 것이 정확합니다.
- **Locate / Edit**: 템플릿이 프로젝트 안의 파일일 때만 나옵니다. Locate는 Project 창에서 그 파일을 찾아 보여 주고, Edit는 템플릿 설정을 고치는 화면을 엽니다. Lit 2D (URP)는 Chapter 01에서 프로젝트를 만들 때 `Assets/Settings` 폴더에 함께 생긴 파일이라 이 두 버튼이 보입니다. 지금은 누를 일이 없습니다.
- **Load additively**: 켜면 지금 열린 씬을 닫지 않고, 새 씬을 그 옆에 **함께** 엽니다. 씬 여러 개를 동시에 열어 두는 고급 작업 방식입니다. 이 과정에서는 끈 채로 둡니다. 실수로 켰다면 Hierarchy에 씬 이름이 두 개 보이므로 알아챌 수 있습니다.
- **Create / Cancel**: Create를 누르면 고른 템플릿으로 새 씬이 열립니다. 이때 씬은 아직 저장되지 않은 `Untitled` 상태이므로, 본문처럼 `Ctrl + S`로 저장해야 파일이 생깁니다.

### A-4 템플릿 하나씩 보기

아래 설명의 "들어 있는 것"은 Create를 누른 직후 Hierarchy에 보이는 오브젝트입니다. Unity 버전에 따라 조금 다를 수 있으니, 궁금하면 직접 만들어 Hierarchy를 확인해 보세요. 확인용으로 만든 씬은 저장하지 않고 닫으면 됩니다.

#### Lit 2D (URP) — 이 과정에서 쓰는 것

- **들어 있는 것**: `Main Camera`(Orthographic), `Global Light 2D`
- **설명 원문의 뜻**: "직교 카메라와 Global Light 2D가 들어 있다. Universal RP의 2D Renderer와 함께 동작한다."
- **언제 쓰나**: URP로 만드는 2D 게임의 거의 모든 씬. 이 과정의 Sandbox, 스테이지, 타이틀 씬 모두 이 템플릿으로 만듭니다.

`Global Light 2D`는 씬 전체를 고르게 비추는 2D 조명입니다. 해가 화면 전체를 비추는 것과 비슷합니다. 이 조명의 밝기(Intensity)를 낮추면 씬 전체가 어두워지고, 색을 바꾸면 노을이나 밤 분위기가 납니다.

**2D Renderer**는 URP 안에서 2D 그리기를 맡는 설정입니다. URP에는 3D용 Universal Renderer와 2D용 2D Renderer가 있고, Universal 2D 프로젝트는 처음부터 2D Renderer를 쓰도록 맞춰져 있습니다. Lit 2D 템플릿은 이 2D Renderer를 전제로 만들어진 짝입니다.

#### Basic (URP)

- **들어 있는 것**: `Main Camera`(Perspective), 3D 조명인 `Directional Light`, 화면 효과를 담는 `Global Volume`
- **언제 쓰나**: URP로 만드는 3D 게임을 시작할 때의 기본 씬
- **썸네일**: 하늘과 지평선이 보이는 3D 공간

`Directional Light`는 태양처럼 한 방향에서 씬 전체를 비추는 3D 조명입니다. `Global Volume`은 화면 전체에 색 보정, 번짐(Bloom) 같은 후처리 효과를 거는 오브젝트입니다.

이 프로젝트는 2D Renderer를 쓰므로 **3D 조명이 스프라이트를 비추지 않습니다.** 2D Renderer에서 스프라이트를 비추는 것은 `Light 2D` 계열 조명뿐입니다. 그래서 이 템플릿으로 씬을 만들면 이름은 "Basic"이지만 2D 게임에는 맞지 않는 씬이 됩니다.

#### Indoors (URP)

- **들어 있는 것**: Basic (URP)의 구성에 더해 실내 장면을 꾸민 예시 오브젝트와 여러 조명
- **언제 쓰나**: 3D 실내 공간(방, 복도)의 조명을 어떻게 잡는지 보여 주는 **샘플**. 실제 게임 씬의 출발점보다는 "이렇게 설정하면 이렇게 보인다"를 구경하는 용도에 가깝습니다.
- **썸네일**: 어두운 공간에 스포트라이트를 받은 물체와 조명 스탠드

#### Outdoors (URP)

- **들어 있는 것**: Basic (URP)의 구성에 더해 야외 장면을 꾸민 예시 오브젝트와 하늘·햇빛 설정
- **언제 쓰나**: 3D 야외 공간의 햇빛과 하늘 표현을 보여 주는 샘플. Indoors와 짝을 이룹니다.
- **썸네일**: 밝은 하늘 아래 놓인 물체

Indoors와 Outdoors는 둘 다 3D용이고, 예시 오브젝트가 들어 있어 씬이 처음부터 무겁습니다. 이 과정에서는 고르지 않습니다.

#### Empty

- **들어 있는 것**: 아무것도 없음. Hierarchy가 비어 있습니다.
- **언제 쓰나**: 카메라나 조명이 필요 없는 씬. 예를 들어 다른 씬 위에 "덧붙여" 여는 데이터 전용 씬, 또는 모든 것을 직접 구성하고 싶을 때.

카메라가 없으므로 Game 탭에는 아무것도 그려지지 않고 `No cameras rendering`(그리는 카메라 없음)이라는 안내가 나옵니다. 처음 배울 때 이 템플릿을 고르면 "왜 화면이 안 나오지?"에서 막히기 쉽습니다.

#### Basic 2D (Built-in / Deprecated)

- **들어 있는 것**: Orthographic `Main Camera`
- **언제 쓰나**: 옛 Built-in 파이프라인으로 만든 2D 프로젝트용. 이제는 쓰지 않습니다.

이름에 2D가 붙어 있어 가장 헷갈리는 템플릿입니다. 카메라는 2D용이 맞지만, **2D 조명이 없고** URP가 아닌 Built-in용입니다. 이 프로젝트에서 고르면 씬은 만들어지지만, 나중에 2D 조명을 쓰는 장에서 다른 씬과 보이는 모습이 달라집니다. Lit 2D (URP)가 이 템플릿의 "현재 버전"이라고 생각하면 됩니다.

#### Basic 3D (Built-in / Deprecated)

- **들어 있는 것**: Perspective `Main Camera`, `Directional Light`
- **언제 쓰나**: 옛 Built-in 파이프라인으로 만든 3D 프로젝트용. 이제는 쓰지 않습니다. Basic (URP)가 이 템플릿의 현재 버전입니다.

### A-5 한눈에 비교

| 템플릿 | 2D/3D | 파이프라인 | 카메라 | 조명 | 이 과정에서 |
|---|---|---|---|---|---|
| **Lit 2D (URP)** | 2D | URP | Orthographic | Global Light 2D | **사용** |
| Basic (URP) | 3D | URP | Perspective | Directional Light | 사용 안 함 |
| Indoors (URP) | 3D | URP | Perspective | 실내 조명 여러 개 | 사용 안 함 |
| Outdoors (URP) | 3D | URP | Perspective | 햇빛과 하늘 | 사용 안 함 |
| Empty | — | — | 없음 | 없음 | 사용 안 함 |
| Basic 2D (Built-in / Deprecated) | 2D | Built-in | Orthographic | 없음 | 사용 안 함 |
| Basic 3D (Built-in / Deprecated) | 3D | Built-in | Perspective | Directional Light | 사용 안 함 |

고르는 기준을 질문 두 개로 정리하면 다음과 같습니다.

1. 내 프로젝트의 파이프라인과 괄호 안 이름이 같은가? → 이 과정은 `(URP)`
2. 2D 게임인가, 3D 게임인가? → 이 과정은 2D

두 질문에 모두 맞는 것은 **Lit 2D (URP)** 하나뿐입니다.

### A-6 잘못 골랐을 때

템플릿을 잘못 골랐는지는 Hierarchy를 보면 바로 알 수 있습니다. Lit 2D (URP)로 만든 씬이라면 `Main Camera`와 `Global Light 2D` 두 개가 있어야 합니다.

| 증상 | 원인이 된 템플릿 |
|---|---|
| Hierarchy가 비어 있고 Game 탭에 `No cameras rendering` | Empty |
| `Directional Light`가 있고 Scene 뷰에 하늘과 지평선이 보인다 | Basic (URP), Basic 3D |
| 처음 보는 물체와 조명이 여러 개 있다 | Indoors, Outdoors |
| `Main Camera`만 있고 `Global Light 2D`가 없다 | Basic 2D (Built-in) |

**가장 쉬운 해결법은 다시 만드는 것입니다.** 아직 아무것도 올리지 않았다면 저장하지 않고 **File > New Scene**으로 Lit 2D (URP)를 다시 고르세요. 이미 같은 이름으로 저장했다면, 새로 만든 씬을 같은 위치에 같은 이름으로 저장해 덮어씁니다.

이미 오브젝트를 많이 올려서 다시 만들기 아깝다면 직접 고칠 수도 있습니다.

1. `Directional Light`, `Global Volume`처럼 3D용 오브젝트가 있다면 Hierarchy에서 골라 `Delete`로 지운다.
2. `Main Camera`를 고르고 Inspector의 Camera 컴포넌트에서 **Projection**을 **Orthographic**으로 바꾼다.
3. Hierarchy 빈 곳을 우클릭하고 **Light > Global Light 2D**를 눌러 2D 조명을 추가한다.
4. 카메라가 없다면(Empty) Hierarchy 우클릭 **Camera**로 먼저 카메라를 만든 뒤 2, 3을 한다.

### A-7 더 알아보기: 내 템플릿 만들기

자주 쓰는 구성이 생기면 템플릿으로 저장해 둘 수 있습니다. 예를 들어 이 과정 후반에는 스테이지 씬마다 카메라 설정, 배경, UI를 똑같이 넣게 됩니다. 이런 씬을 한 번 만든 뒤 **File > Save As Scene Template**을 누르면 그 씬이 New Scene 창에 새 템플릿으로 나타납니다. 지금 할 필요는 없고, "템플릿은 직접 만들 수도 있다"는 것만 알아 두면 됩니다.

### 연습문제

1. 문제: 템플릿 이름 `Basic 2D (Built-in / Deprecated)`를 세 부분으로 나누어 각각의 뜻을 쓰세요.
2. 문제: Orthographic과 Perspective 카메라의 차이를 "멀리 있는 물체"를 넣어 한 줄로 설명하세요.
3. 문제: 친구가 이 과정의 프로젝트에서 Basic (URP)로 씬을 만들었습니다. Hierarchy에서 무엇을 보고 알 수 있는지, 어떻게 고치면 되는지 쓰세요.
4. 문제: Empty 템플릿으로 만든 씬에서 Game 탭에 아무것도 보이지 않는 이유를 쓰세요.

### 정답 포인트

- `Basic 2D`는 2D 카메라만 든 기본 구성, `Built-in`은 옛 렌더 파이프라인용, `Deprecated`는 더 이상 권장하지 않고 없어질 예정이라는 뜻.
- Orthographic은 멀리 있어도 크기가 같고, Perspective는 멀리 있을수록 작게 보인다.
- `Directional Light`와 `Global Volume`이 있고 `Global Light 2D`가 없다. Lit 2D (URP)로 다시 만들거나, 3D 오브젝트를 지우고 카메라를 Orthographic으로 바꾼 뒤 Global Light 2D를 추가한다.
- Empty에는 카메라가 없고, 화면은 카메라가 찍은 것만 그려지기 때문이다.

---

[Chapter 02로 돌아가기](./02-scene-gameobject.md)

[상위 문서로 돌아가기](./README.md)
