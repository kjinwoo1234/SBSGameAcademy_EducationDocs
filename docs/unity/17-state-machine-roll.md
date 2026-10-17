# Chapter 17 상태 머신과 구르기

## 학습 목표

- 행동이 늘어날수록 `if`문으로 상태를 관리하기 어려워지는 이유를 설명할 수 있다.
- `IState` 인터페이스와 `StateMachine` 클래스로 상태 패턴을 구현할 수 있다.
- 플레이어의 지상, 공중, 구르기 행동을 상태 클래스로 나눌 수 있다.
- 구르기 입력, 지속 시간, 쿨다운, 공중 1회 제한을 만들고 애니메이션에 연결할 수 있다.

## 본문

### 17-1 if문이 엉키는 문제

지금 `PlayerController`는 "바닥이면 이렇게, 공중이면 저렇게"를 `if`로 나눠 처리합니다. 여기에 구르기를 넣으면 "구르는 중이면 점프 안 됨, 방향 전환 안 됨, 중력은 받음"이 끼어듭니다. 이어서 공격, 피격, 사망이 들어오면 모든 함수 첫머리에 "공격 중인가? 맞는 중인가? 죽었나?"를 묻는 `if`가 쌓이고, 하나를 고치면 다른 데가 깨지기 시작합니다.

이 문제를 푸는 흔한 방법이 **상태 패턴**(State Pattern)입니다. 행동을 상태별 클래스로 나누고, **지금은 한 상태만 동작**하게 합니다. 구르기 상태 클래스 안에는 구르기에 필요한 코드만 있고, 다른 상태를 신경 쓰지 않습니다. 이렇게 상태를 바꿔 끼우며 돌리는 장치를 **상태 머신**(State Machine)이라고 합니다. Chapter 16의 Animator Controller도 애니메이션용 상태 머신입니다.

```text
          점프 / 발판에서 떨어짐
 [지상] ─────────────────────────▶ [공중]
   ▲  ◀───────────── 착지 ───────────┘
   │                                  │
   │ 구르기 입력        구르기 입력(공중 1회)
   ▼                                  ▼
 [구르기] ── 0.4초 뒤 바닥이면 지상, 아니면 공중으로
```

### 17-2 상태 인터페이스와 상태 머신

플레이어뿐 아니라 적과 보스도 같은 장치를 쓰도록, 공용 코드는 `Scripts/Core` 폴더에 둡니다.

```csharp
namespace Bastion.Core
{
    public interface IState
    {
        void Enter();
        void Exit();
        void Tick();
        void FixedTick();
    }
}
```

```csharp
using System;

namespace Bastion.Core
{
    /// <summary>
    /// 플레이어, 적, 보스가 공통으로 사용하는 단순 상태 머신.
    /// </summary>
    public sealed class StateMachine
    {
        public IState CurrentState { get; private set; }

        public event Action<IState, IState> StateChanged;

        public void Initialize(IState startState)
        {
            CurrentState = startState ?? throw new ArgumentNullException(nameof(startState));
            CurrentState.Enter();
        }

        public void ChangeState(IState nextState)
        {
            if (nextState == null || nextState == CurrentState)
                return;

            IState previous = CurrentState;
            previous?.Exit();
            CurrentState = nextState;
            CurrentState.Enter();
            StateChanged?.Invoke(previous, CurrentState);
        }

        public void Tick() => CurrentState?.Tick();

        public void FixedTick() => CurrentState?.FixedTick();
    }
}
```

#### 코드 해석

- `IState`: 모든 상태가 갖춰야 할 네 함수입니다. `Enter`는 상태에 들어올 때 한 번, `Exit`는 나갈 때 한 번, `Tick`은 `Update`마다, `FixedTick`은 `FixedUpdate`마다 불립니다.
- `ChangeState`: 이전 상태의 `Exit` → 새 상태의 `Enter` 순서로 부르고, 바뀌었다고 `StateChanged` 이벤트로 알립니다. 애니메이션처럼 상태 변화를 알아야 하는 다른 스크립트가 이 이벤트를 구독합니다.
- `?? throw`: 시작 상태가 `null`이면 바로 오류를 내 실수를 일찍 알립니다.
- 상태 객체는 MonoBehaviour가 아닌 **일반 C# 클래스**입니다. 게임 오브젝트에 붙이지 않고, 컨트롤러가 `new`로 한 번 만들어 계속 재사용합니다.

### 17-3 플레이어 상태의 기반 클래스

플레이어 상태들은 모두 `PlayerController`를 다뤄야 하므로 공통 부모를 둡니다. `Scripts/Player/PlayerState.cs`입니다.

```csharp
using Bastion.Core;

namespace Bastion.Player
{
    public abstract class PlayerState : IState
    {
        protected readonly PlayerController Player;

        protected PlayerState(PlayerController player) => Player = player;

        public virtual void Enter() { }
        public virtual void Exit() { }
        public virtual void Tick() { }
        public virtual void FixedTick() { }

        /// <summary>지상 또는 공중 상태로 복귀한다.</summary>
        protected void ReturnToMovement() =>
            Player.StateMachine.ChangeState(Player.IsGrounded ? Player.GroundedState : Player.AirborneState);
    }
}
```

`virtual`로 빈 함수를 미리 만들어 두어, 자식 상태는 필요한 함수만 `override`합니다.

### 17-4 지상 상태와 공중 상태

Chapter 09~13의 `Update`, `FixedUpdate` 내용을 상태별로 옮깁니다. 파일마다 클래스 하나씩, `PlayerGroundedState.cs`와 `PlayerAirborneState.cs`를 만듭니다.

```csharp
namespace Bastion.Player
{
    public sealed class PlayerGroundedState : PlayerState
    {
        public PlayerGroundedState(PlayerController player) : base(player) { }

        public override void Tick()
        {
            Player.UpdateFacing(Player.Input.MoveX);

            if (Player.TryStartDash())
                return;

            if (Player.HasBufferedJump)
            {
                Player.Jump();
                Player.StateMachine.ChangeState(Player.AirborneState);
            }
        }

        public override void FixedTick()
        {
            if (!Player.IsGrounded)
            {
                Player.StateMachine.ChangeState(Player.AirborneState);
                return;
            }

            Player.ApplyHorizontalMovement(Player.Data.GroundAcceleration, Player.Data.GroundDeceleration);
            Player.StickToGround();
        }
    }
}
```

```csharp
namespace Bastion.Player
{
    public sealed class PlayerAirborneState : PlayerState
    {
        public PlayerAirborneState(PlayerController player) : base(player) { }

        public override void Tick()
        {
            Player.UpdateFacing(Player.Input.MoveX);

            if (Player.TryStartDash())
                return;

            if (!Player.HasBufferedJump)
                return;

            // 발판을 막 벗어났으면 지상 점프, 아니면 남은 공중 점프 사용
            if (Player.IsInCoyoteTime)
                Player.Jump();
            else if (Player.CanAirJump)
                Player.AirJump();
        }

        public override void FixedTick()
        {
            if (Player.IsGrounded)
            {
                Player.StateMachine.ChangeState(Player.GroundedState);
                return;
            }

            Player.ApplyHorizontalMovement(Player.Data.AirAcceleration, Player.Data.AirDeceleration);
            Player.ApplyGravity();
        }
    }
}
```

#### 코드 해석

- 지상 상태는 바닥에서 떨어지면 공중 상태로, 공중 상태는 착지하면 지상 상태로 넘깁니다. "바닥인가"를 묻는 `if`가 각 상태 안에 한 번씩만 있습니다.
- 두 상태 모두 `Tick`의 첫머리에서 구르기를 먼저 확인합니다. 구르기가 시작되면 `return`해 같은 프레임에 점프까지 하지 않게 합니다.

### 17-5 구르기 상태

구르기는 일정 시간 동안 정해진 속도로 미끄러지듯 이동하는 회피 동작입니다. `PlayerDashState.cs`를 만듭니다.

```csharp
using UnityEngine;

namespace Bastion.Player
{
    public sealed class PlayerDashState : PlayerState
    {
        float endTime;

        public PlayerDashState(PlayerController player) : base(player) { }

        public override void Enter()
        {
            Player.Input.ConsumeDash();

            // 방향 입력이 있으면 그쪽으로, 없으면 바라보는 방향으로 구른다.
            Player.UpdateFacing(Player.Input.MoveX);
            if (!Player.IsGrounded)
                Player.CanAirDash = false;

            endTime = Time.time + Player.Data.DashDuration;
            Player.SetVelocity(new Vector2(Player.FacingDirection * Player.Data.DashSpeed, Player.Velocity.y));
        }

        public override void Exit()
        {
            Player.LastDashEndTime = Time.time;
            // 구르기 관성을 최대 달리기 속도로 제한해 미끄러지지 않게 한다.
            float x = Mathf.Clamp(Player.Velocity.x, -Player.Data.MaxRunSpeed, Player.Data.MaxRunSpeed);
            Player.SetVelocity(new Vector2(x, Player.Velocity.y));
        }

        public override void FixedTick()
        {
            // 구르는 동안 수평 속도는 고정하고 중력은 그대로 받는다.
            Player.SetVelocity(new Vector2(Player.FacingDirection * Player.Data.DashSpeed, Player.Velocity.y));
            Player.ApplyGravity();

            if (Time.time >= endTime)
                ReturnToMovement();
        }
    }
}
```

#### 코드 해석

- `Enter`: 입력을 소비하고, 방향을 정하고, 끝날 시각을 기록합니다. 공중에서 구르면 `CanAirDash`를 꺼서 착지 전까지 다시 못 구르게 합니다.
- `FixedTick`: 0.4초 동안 가로 속도를 8.5로 고정합니다. 중력은 그대로 받으므로 공중에서 구르면 조금씩 떨어집니다. 시간이 다 되면 `ReturnToMovement()`로 지상이나 공중 상태로 돌아갑니다.
- `Exit`: 끝난 시각을 기록해 쿨다운(0.3초)을 재고, 남은 속도를 달리기 최고 속도로 줄여 미끄러지지 않게 합니다.
- 구르기 중에는 `UpdateFacing`도 점프 확인도 하지 않습니다. 구르기 상태 클래스에 그런 코드가 없으니 따로 막을 필요가 없습니다. 상태 패턴의 장점이 바로 이것입니다.

### 17-6 PlayerController 정리

컨트롤러는 이제 행동을 결정하지 않고, **물리 상태와 공용 함수를 제공**하는 역할만 맡습니다. 상태들이 쓰도록 함수를 `public`으로 열고, `Update`와 `FixedUpdate`는 상태 머신에 넘깁니다.

```csharp
using System;
using Bastion.Core;
using UnityEngine;

namespace Bastion.Player
{
    /// <summary>
    /// 플레이어의 진입점. 물리 상태(접지, 속도, 방향)와 공용 이동 함수를 제공하고
    /// 실제 행동 결정은 상태(PlayerGroundedState 등)에 맡긴다.
    /// </summary>
    [RequireComponent(typeof(Rigidbody2D), typeof(Collider2D), typeof(PlayerInputReader))]
    public sealed class PlayerController : MonoBehaviour
    {
        [SerializeField] PlayerMovementData data;

        Rigidbody2D body;
        Collider2D bodyCollider;
        readonly RaycastHit2D[] groundHits = new RaycastHit2D[4];

        public PlayerMovementData Data => data;
        public PlayerInputReader Input { get; private set; }
        public StateMachine StateMachine { get; } = new StateMachine();

        public PlayerGroundedState GroundedState { get; private set; }
        public PlayerAirborneState AirborneState { get; private set; }
        public PlayerDashState DashState { get; private set; }

        public bool IsGrounded { get; private set; }
        public float LastGroundedTime { get; private set; } = float.NegativeInfinity;
        public int FacingDirection { get; private set; } = 1;
        public Vector2 Velocity => body.linearVelocity;
        public bool CanAirDash { get; set; } = true;
        public int AirJumpsRemaining { get; private set; }
        public float LastDashEndTime { get; set; } = float.NegativeInfinity;

        /// <summary>지상 점프(코요테 점프 포함)를 했을 때.</summary>
        public event Action Jumped;
        /// <summary>공중 점프(더블점프)를 했을 때.</summary>
        public event Action AirJumped;

        void Awake()
        {
            body = GetComponent<Rigidbody2D>();
            bodyCollider = GetComponent<Collider2D>();
            Input = GetComponent<PlayerInputReader>();

            // 중력은 PlayerMovementData 기준으로 직접 적용한다.
            body.gravityScale = 0f;

            GroundedState = new PlayerGroundedState(this);
            AirborneState = new PlayerAirborneState(this);
            DashState = new PlayerDashState(this);
        }

        void Start() => StateMachine.Initialize(AirborneState);

        void Update() => StateMachine.Tick();

        void FixedUpdate()
        {
            UpdateGrounded();
            StateMachine.FixedTick();
        }

        void UpdateGrounded()
        {
            var filter = new ContactFilter2D { useLayerMask = true, layerMask = data.GroundLayer, useTriggers = false };
            int count = bodyCollider.Cast(Vector2.down, filter, groundHits, data.GroundCheckDistance);

            IsGrounded = false;
            for (int i = 0; i < count; i++)
            {
                // 벽 옆면에 닿은 것은 접지로 보지 않는다.
                if (groundHits[i].normal.y > 0.7f)
                {
                    IsGrounded = true;
                    break;
                }
            }

            // 상승 중에는 발판을 스쳐도 접지로 처리하지 않는다.
            if (IsGrounded && body.linearVelocity.y > 0.01f)
                IsGrounded = false;

            if (IsGrounded)
            {
                LastGroundedTime = Time.time;
                CanAirDash = true;
                AirJumpsRemaining = data.MaxAirJumps;
            }
        }

        public bool HasBufferedJump => Time.time - Input.LastJumpPressedTime <= data.JumpBufferTime;
        public bool HasBufferedDash => Time.time - Input.LastDashPressedTime <= data.JumpBufferTime;
        public bool IsInCoyoteTime => Time.time - LastGroundedTime <= data.CoyoteTime;
        public bool IsDashReady => Time.time - LastDashEndTime >= data.DashCooldown;

        public void ApplyHorizontalMovement(float acceleration, float deceleration, float speedFactor = 1f)
        {
            float target = Input.MoveX * data.MaxRunSpeed * speedFactor;
            float rate = Mathf.Abs(target) > 0.01f ? acceleration : deceleration;
            float x = Mathf.MoveTowards(body.linearVelocity.x, target, rate * Time.fixedDeltaTime);
            body.linearVelocity = new Vector2(x, body.linearVelocity.y);
        }

        public void ApplyGravity()
        {
            float multiplier = 1f;
            if (body.linearVelocity.y < 0f)
                multiplier = data.FallGravityMultiplier;
            else if (body.linearVelocity.y > 0f && !Input.JumpHeld)
                multiplier = data.JumpCutGravityMultiplier;

            float y = body.linearVelocity.y - data.Gravity * multiplier * Time.fixedDeltaTime;
            body.linearVelocity = new Vector2(body.linearVelocity.x, Mathf.Max(y, -data.MaxFallSpeed));
        }

        /// <summary>
        /// 지상에서는 중력을 누적하지 않는다. 가만히 있으면 세로 속도 0 (경사에서 미끄러지지 않음),
        /// 움직이는 중에는 약하게 눌러 내리막에서도 바닥에서 떨어지지 않게 한다.
        /// </summary>
        public void StickToGround()
        {
            float vy = Mathf.Abs(body.linearVelocity.x) > 0.01f ? -data.GroundStickSpeed : 0f;
            body.linearVelocity = new Vector2(body.linearVelocity.x, vy);
        }

        public void Jump()
        {
            Input.ConsumeJump();
            LastGroundedTime = float.NegativeInfinity; // 코요테 점프 중복 방지
            body.linearVelocity = new Vector2(body.linearVelocity.x, data.JumpVelocity);
            Jumped?.Invoke();
        }

        public bool CanAirJump => AirJumpsRemaining > 0;

        public void AirJump()
        {
            Input.ConsumeJump();
            AirJumpsRemaining--;
            body.linearVelocity = new Vector2(body.linearVelocity.x, data.AirJumpVelocity);
            AirJumped?.Invoke();
        }

        public void SetVelocity(Vector2 velocity) => body.linearVelocity = velocity;

        public void UpdateFacing(float moveX)
        {
            if (Mathf.Abs(moveX) < 0.01f)
                return;

            int direction = moveX > 0f ? 1 : -1;
            if (direction == FacingDirection)
                return;

            FacingDirection = direction;
            // 스케일 대신 회전으로 뒤집어 자식 오브젝트까지 함께 반전시킨다.
            transform.rotation = Quaternion.Euler(0f, direction > 0 ? 0f : 180f, 0f);
        }

        /// <summary>구르기 입력이 있고 구를 수 있으면 구르기 상태로 바꾼다.</summary>
        public bool TryStartDash()
        {
            if (HasBufferedDash && IsDashReady && (IsGrounded || CanAirDash))
            {
                StateMachine.ChangeState(DashState);
                return true;
            }
            return false;
        }
    }
}
```

#### 코드 해석

- `StateMachine.Initialize(AirborneState)`: 처음에는 공중 상태로 시작합니다. 바닥 위에서 시작했다면 첫 물리 단계에서 바로 지상 상태로 넘어갑니다.
- `public StateMachine StateMachine { get; } = new StateMachine();`: 상태들과 애니메이션 스크립트가 상태 머신을 읽을 수 있게 열어 둡니다.
- `Jumped`, `AirJumped` 이벤트: 점프했다는 사실을 다른 스크립트(애니메이션, Chapter 32의 소리)에 알립니다. 컨트롤러는 누가 듣는지 모릅니다.
- `HasBufferedDash`: 구르기 입력도 점프처럼 0.12초 보관합니다.

### 17-7 입력, 수치, 애니메이션 추가

**입력.** BastionControls 에셋에 `Dash` 액션(Button)을 만들고 `X [Keyboard]`, `Right Shoulder [Gamepad]`를 연결합니다. `PlayerInputReader`에는 점프와 같은 방식으로 추가합니다.

```csharp
[SerializeField] InputActionReference dashAction;

public float LastDashPressedTime { get; private set; } = NotPressed;

// OnEnable에 추가
dashAction.action.Enable();
dashAction.action.performed += OnDashPerformed;

// OnDisable에 추가
dashAction.action.performed -= OnDashPerformed;

void OnDashPerformed(InputAction.CallbackContext context) => LastDashPressedTime = Time.time;

public void ConsumeDash() => LastDashPressedTime = NotPressed;
```

**수치.** `PlayerMovementData`의 Ground Check 묶음 위에 구르기 묶음을 추가합니다. 무적 시간은 Chapter 19에서 체력을 만들면 씁니다.

```csharp
[Header("Dash (Roll)")]
[Tooltip("구르기 이동 속도")]
[Min(0f)] public float DashSpeed = 8.5f;
[Min(0.01f)] public float DashDuration = 0.4f;
[Min(0f)] public float DashCooldown = 0.3f;
[Tooltip("구르기 중 무적 시간 (시작부터)")]
[Min(0f)] public float DashInvincibleTime = 0.32f;
```

**애니메이션.** Animator에 `IsDashing`(Bool) 파라미터를 만들고, **Any State → Dash**(조건 `IsDashing` = true), **Dash → Idle**(조건 `IsDashing` = false) 전이를 만듭니다. Any State에서 Dash로 가는 전이는 Inspector에서 **Can Transition To Self**를 꺼야 구르는 동안 매 프레임 처음부터 다시 시작하지 않습니다. `PlayerAnimator`를 아래처럼 고칩니다.

```csharp
static readonly int IsDashing = Animator.StringToHash("IsDashing");
static readonly int JumpState = Animator.StringToHash("Jump");

void OnEnable() => player.AirJumped += OnAirJumped;

void OnDisable() => player.AirJumped -= OnAirJumped;

// 공중 점프는 같은 Jump 상태라 전이가 일어나지 않으므로 처음부터 다시 재생한다.
void OnAirJumped() => animator.Play(JumpState, 0, 0f);

void LateUpdate()
{
    var state = player.StateMachine.CurrentState;
    animator.SetFloat(SpeedX, Mathf.Abs(player.Velocity.x));
    animator.SetFloat(VelocityY, player.Velocity.y);
    animator.SetBool(IsGrounded, player.IsGrounded);
    animator.SetBool(IsDashing, state == player.DashState);
}
```

Chapter 16 끝에서 남겨 둔 "공중 점프 때 애니메이션이 처음부터 다시 나오지 않는 문제"를 `AirJumped` 이벤트와 `Animator.Play`로 해결했습니다.

### 17-8 따라하기 — 구르는 기사

1. `Scripts/Core`에 `IState`, `StateMachine`을 만든다.
2. `PlayerState`, `PlayerGroundedState`, `PlayerAirborneState`, `PlayerDashState`를 만든다.
3. `PlayerController`를 17-6처럼 정리하고, 입력·수치·애니메이션을 17-7대로 추가한다.
4. 실행해 X 키로 구르기를 해 본다. 바닥에서는 쿨다운 0.3초마다, 공중에서는 착지 전까지 한 번만 구를 수 있는지 확인한다.
5. 이동, 점프, 더블점프, 코요테 타임이 Chapter 13과 똑같이 동작하는지 확인한다.

5번처럼 겉으로 보이는 동작은 그대로 두고 코드의 구조만 바꾸는 일을 **리팩터링**이라고 합니다. 이제 다음 장에서 공격 상태를 추가할 때 다른 상태는 거의 건드리지 않아도 됩니다.

### 연습문제

1. 문제: `IState`의 네 함수가 각각 언제 불리는지 쓰세요.
2. 문제: 구르기 도중에 점프 키를 눌러도 점프하지 않는 이유를 상태 패턴의 관점에서 쓰세요.
3. 문제: 구르기 거리를 4유닛으로 늘리되 걸리는 시간은 그대로 두려면 어느 수치를 얼마로 바꿔야 하는지 쓰세요.  
   - 입력: DashDuration 0.4초
   - 출력: 바꿀 수치 이름과 값

### 정답 포인트

- Enter는 들어올 때 한 번, Exit는 나갈 때 한 번, Tick은 Update마다, FixedTick은 FixedUpdate마다. 지금 동작하는 것은 구르기 상태 하나뿐이고 그 안에 점프 처리 코드가 없기 때문이다. 4 ÷ 0.4 = 10이므로 DashSpeed를 10으로 바꾼다.

---

[상위 문서로 돌아가기](./README.md)
