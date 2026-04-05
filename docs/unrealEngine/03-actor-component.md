# Chapter 03 Actor와 Component

## 학습 목표
- Actor와 Component의 관계를 이해한다.
- 컴포넌트 조합으로 기능을 구성할 수 있다.

## 세부 주제
### 03-1 Actor 기본
- 월드 배치 단위
### 03-2 Component 구조
- 기능 모듈화
### 03-3 Transform 관리
- 위치/회전/크기

## 실습 체크리스트
- 액터에 메쉬/콜리전 컴포넌트를 추가해 동작을 확인한다.

## 본문
### 03-1 Actor 기본
- Actor는 월드에 존재하는 개체의 기본 클래스입니다.
### 03-2 Component 구조
- Scene/StaticMesh/Collision 컴포넌트를 조합해 역할을 구성합니다.
### 03-3 Transform 관리
- 모든 배치 객체는 Transform으로 공간 정보를 가집니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| Actor | 월드에 배치되는 객체 단위 |
| Component | 액터 기능을 분리해 붙이는 모듈 |

예시 코드(컴포넌트 생성):
```cpp
AMyActor::AMyActor()
{
    RootComponent = CreateDefaultSubobject<USceneComponent>(TEXT("Root"));
    Mesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"));
    Mesh->SetupAttachment(RootComponent);
}
```

예상 결과:
```text
컴포넌트 추가 전/후 액터 동작 차이 확인
```

코드 해석:
- 루트 컴포넌트 기준으로 자식 컴포넌트를 계층 연결합니다.
- 컴포넌트 조합으로 액터의 시각/충돌 기능을 확장할 수 있습니다.

연습문제:
1. 문제: 충돌 컴포넌트 추가
   - 입력: 액터 1개
   - 출력: 충돌 영역 활성화
   - 조건: 충돌 프로필 설정
2. 문제: 자식 컴포넌트 계층 구성
   - 입력: 루트 + 자식 2개
   - 출력: 계층 구조
   - 힌트: 부모-자식 Transform 확인

정답 포인트:
- 기능을 컴포넌트로 분리하면 재사용성이 올라감
- 루트 컴포넌트 설정이 이동/회전 기준점

---

[상위 문서로 돌아가기](./README.md)
