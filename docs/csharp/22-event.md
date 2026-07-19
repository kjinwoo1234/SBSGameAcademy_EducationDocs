# Chapter 22 event

## 학습 목표
- `event`와 일반 델리게이트 필드의 외부 사용 차이를 구분한다.
- `+=`/`-=`로 구독·해지하고, 클래스 안에서 이벤트를 발생시킬 수 있다.
- 발행자-구독자 패턴으로 알림을 분리할 수 있다.

## 본문

### 22-1 이벤트 기본

**이벤트**는 델리게이트를 감싸 **알림 전용**으로 쓰는 문법입니다. 밖에서는 **`+=`/`-=`로 구독·해지**만 할 수 있습니다. **발생**은 이벤트를 선언한 클래스 안에서 **`Invoke`**로 합니다. 공개 델리게이트 필드처럼 외부에서 마음대로 호출하는 일을 막습니다.

빈 구독을 미리 두려면 `= delegate { }`처럼 **아무 일도 하지 않는 메서드**를 초깃값으로 둡니다. 구독자가 아직 없어도 `Invoke`를 안전하게 호출하려는 입문 패턴입니다.

| 구분 | 외부에서 | 이럴 때 |
|---|---|---|
| 일반 델리게이트 필드 | 할당·호출 가능 | 내부 전략 교체 |
| `event` | 구독/해지만 | 상태 변화 알림 |

아래를 실행해 `Damage(20)` 후 `HP: 80`이 출력되는지 확인해 보세요.

```csharp
using System;

class Player
{
    public event Action<int> OnHpChanged = delegate { };
    private int hp = 100;

    public void Damage(int d)
    {
        hp -= d;
        OnHpChanged.Invoke(hp);
    }
}

class Program
{
    static void Main()
    {
        Player p = new Player();
        p.OnHpChanged += hp => Console.WriteLine($"HP: {hp}");
        p.Damage(20);
    }
}
```

**예상 출력**

```text
HP: 80
```

**코드 해석**
- `OnHpChanged`는 `event`라서 외부는 `+=`로만 연결합니다.
- `Damage` 안에서만 `Invoke`로 새 체력을 알립니다.
- 구독 람다가 `HP: 80`을 출력합니다.

### 22-2 구독과 해지

구독은 **`+=`**, 해지는 **`-=`**입니다. 같은 메서드를 빼려면 **변수에 담아 둔 핸들러**를 `+=`와 `-=`에 같이 써야 합니다. 람다를 그 자리에서만 쓰면 같은 대상을 `-=`로 지정하기 어렵습니다. 구독을 남기면 객체가 더 오래 남거나 알림이 중복될 수 있으므로, 더 이상 필요 없으면 해지합니다.

아래를 실행해 첫 `Ping`만 로그가 나오고, 해지 후 두 번째 `Ping`에는 로그가 없는지 확인해 보세요.

```csharp
using System;

class Bell
{
    public event Action OnRing = delegate { };

    public void Ring()
    {
        OnRing.Invoke();
    }
}

class Program
{
    static void Log()
    {
        Console.WriteLine("rang");
    }

    static void Main()
    {
        Bell bell = new Bell();
        Action handler = Log;
        bell.OnRing += handler;
        bell.Ring();

        bell.OnRing -= handler;
        bell.Ring();
        Console.WriteLine("done");
    }
}
```

**예상 출력**

```text
rang
done
```

**코드 해석**
- `handler`에 `Log`를 담아 `+=`로 구독합니다. 첫 `Ring`에서 `rang`가 나옵니다.
- `-= handler`로 해지한 뒤 두 번째 `Ring`에서는 로그가 없습니다.
- 마지막 `done`으로 프로그램이 끝까지 갔음을 확인합니다.

### 22-3 발행자-구독자 패턴

**발행자**는 “일이 생겼다”만 알리고, **구독자**는 각자 반응을 구현합니다. 두 쪽을 직접 메서드 이름으로 묶지 않아 **결합이 느슨**합니다. `22-1`의 `Player`가 발행자이고, `Main`의 람다가 구독자입니다.

| 용어 | 의미 |
|---|---|
| 발행자 | 이벤트를 발생시키는 객체 |
| 구독자 | 이벤트를 받아 처리하는 쪽 |
| Invoke | 연결된 구독자를 호출해 알리는 동작 |

이 장에서는 `event` 선언·구독·해지·`Invoke`까지입니다. UI 프레임워크 전용 이벤트 인자는 다루지 않습니다.

### 연습문제

**문제 1**
- 문제: 레벨이 오를 때 이벤트를 발생시키는 클래스를 작성하세요.
- 입력: 없음. `LevelUp()`을 한 번 호출한다고 가정
- 출력:
  ```text
  Level Up!
  ```
- 조건: `event` + 구독자 1개 이상. 발행은 클래스 메서드 안에서 `Invoke`

**문제 2**
- 문제: 구독 후 `-=`로 해지하면 더 이상 호출되지 않는지 확인하세요.
- 입력: 없음. 같은 이벤트 트리거 2회, 중간에 해지
- 출력:
  ```text
  rang
  done
  ```
- 조건: 핸들러를 변수에 담아 `+=`/`-=`. 해지 후 두 번째 트리거에는 `rang` 없음

**문제 3**
- 문제: 외부 코드가 해야 할 일로 맞는 쪽을 고르세요. `event`에 대해 A) `+=`로 구독 B) 밖에서 `Invoke`로 발생
- 입력: 없음
- 출력: `A` 와 “발생은 선언한 클래스 안” 취지의 한 줄
- 조건: 본문 표·`22-1` 기준

### 정답 포인트

- 문제 1: 레벨 메서드 끝에서 `OnLevelUp.Invoke()` 등. 구독에서 `Level Up!` 출력
- 문제 2: `Action handler = Log; +=` 후 `-=` , 두 번째 호출에는 로그 없음
- 문제 3: `A`. 외부는 구독/해지만, `Invoke`는 발행자 클래스 안
- 공통: 이벤트는 알림, 발행과 처리를 분리

---

[상위 문서로 돌아가기](./README.md)
