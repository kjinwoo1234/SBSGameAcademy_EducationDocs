# C++와 Blueprint 연동

언리얼은 성능과 구조가 중요한 부분은 C++로, 빠른 반복 조정은 Blueprint로 담당하는 하이브리드 방식이 효율적입니다.

## 사전 준비 체크리스트
- Visual Studio(또는 Rider)와 C++ 개발 도구가 설치되어 있는가?
- Unreal 프로젝트가 C++ 프로젝트로 생성/전환되어 있는가?
- 코드 변경 후 `Compile` 또는 IDE 빌드를 실행할 수 있는가?
- 빌드 오류 발생 시 `.sln` 재생성(Generate Project Files) 절차를 알고 있는가?

## 학습 목표
- C++와 Blueprint의 역할 분담 기준을 설명할 수 있다.
- `UCLASS`, `UPROPERTY`, `UFUNCTION`의 목적을 이해할 수 있다.
- C++ 클래스 기능을 Blueprint에서 호출할 수 있다.

## 핵심
- `UCLASS()`: 클래스를 리플렉션 시스템에 등록
- `UPROPERTY()`: 변수 노출/직렬화/에디터 편집 제어
- `UFUNCTION()`: 함수 노출 및 블루프린트 호출 가능 설정

## 단계별 실습
1. C++ 기반 Actor 클래스를 생성한다.
2. `UPROPERTY(EditAnywhere, BlueprintReadWrite)` 변수 하나를 선언한다.
3. `UFUNCTION(BlueprintCallable)` 함수 하나를 선언/구현한다.
4. 해당 클래스를 상속한 Blueprint를 만들어 변수/함수를 사용한다.

## 실수 포인트
- 매크로 지정자를 누락해 Blueprint에서 변수/함수가 보이지 않는다.
- C++ 변경 후 빌드를 하지 않아 에디터에 반영되지 않는다.

## 이해 점검 체크리스트
- [ ] IDE/툴체인 사전 준비를 완료했는가?
- [ ] C++와 Blueprint 분업 기준을 사례로 설명할 수 있는가?
- [ ] 노출 매크로를 적용한 변수/함수를 작성했는가?
- [ ] Blueprint에서 실제 호출/수정이 가능한지 확인했는가?

## 다음 학습 추천
- [패키징과 배포](./10-packaging-deploy.md)

## 셀프 퀴즈
1. C++ 클래스를 언리얼 리플렉션 시스템에 등록하는 매크로는 무엇인가?
2. Blueprint에서 호출 가능한 C++ 함수를 만들 때 주로 붙이는 매크로 지정자는 무엇인가?

## 정답
1. UCLASS()
2. UFUNCTION(BlueprintCallable)

---

[상위 문서로 돌아가기](./README.md)
