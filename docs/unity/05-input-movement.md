# 입력 처리와 캐릭터 이동

키보드/마우스 입력을 받아 플레이어를 이동시키는 파트입니다.

## 기본 입력
- `Input.GetKey(KeyCode.W)`
- `Input.GetAxis("Horizontal")`, `Input.GetAxis("Vertical")`

## 이동 예제
```csharp
float h = Input.GetAxis("Horizontal");
float v = Input.GetAxis("Vertical");
Vector3 dir = new Vector3(h, 0, v);
transform.Translate(dir * speed * Time.deltaTime);
```

## 실수 포인트
- `Time.deltaTime`를 곱하지 않으면 프레임에 따라 속도가 달라짐

---

[상위 문서로 돌아가기](./README.md)
