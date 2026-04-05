# Chapter 05 입력 처리와 이동

## 학습 목표
- 키보드 입력을 읽어 플레이어 이동을 구현한다.
- 프레임 독립 이동의 필요성을 이해한다.

## 세부 주제
### 05-1 입력 읽기
- `Input.GetAxis`, `Input.GetKey`
### 05-2 이동 구현
- `Translate`, `Rigidbody.MovePosition`
### 05-3 이동 품질
- `deltaTime`, 속도 제한

## 실습 체크리스트
- WASD 이동을 구현하고 속도 값을 조절한다.

## 본문
### 05-1 입력 읽기
- 축 입력은 부드러운 이동에, 키 입력은 단일 액션에 적합합니다.
### 05-2 이동 구현
- 물리 기반 오브젝트는 `Rigidbody` 방식이 더 안정적입니다.
### 05-3 이동 품질
- `deltaTime` 보정이 없으면 PC 성능에 따라 속도가 달라집니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 프레임 독립 | FPS가 달라도 같은 시간 기준 속도를 유지하는 처리 |
| 축 입력(axis input) | -1~1 범위로 연속 값을 주는 입력 방식 |

예시 코드:
```csharp
using UnityEngine;

public class PlayerMove : MonoBehaviour
{
    [SerializeField] float speed = 5f;
    void Update()
    {
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");
        Vector3 dir = new Vector3(h, 0, v);
        transform.Translate(dir * speed * Time.deltaTime, Space.World);
    }
}
```

예상 결과:
```text
WASD 입력에 따라 오브젝트 이동
```

코드 해석:
- 수평/수직 축 값을 벡터로 합쳐 방향을 만들고 시간 보정 후 이동합니다.

연습문제:
1. 문제: Shift 키를 누르면 속도가 2배가 되게 구현
   - 입력: WASD + Shift
   - 출력: 가속 이동
   - 조건: 기본 속도 복귀 포함
2. 문제: 이동 범위를 맵 경계 안으로 제한
   - 입력: 위치 값
   - 출력: 경계 밖 이동 차단
   - 힌트: `Mathf.Clamp`

정답 포인트:
- 입력/이동/보정을 분리해서 생각
- 이동 품질은 `deltaTime`이 핵심

---

[상위 문서로 돌아가기](./README.md)
