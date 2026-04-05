# Chapter 23 도전! 프로그래밍 3

## 학습 목표
- 자료구조 구현과 게임 로직 결합을 경험한다.
- 포인터 중심 설계를 실제 프로젝트에 적용한다.

## 세부 주제
### 23-1 LinkedList 구현하고 뱀 게임 만들기
- 노드 연결 구조, 상태 갱신, 충돌 처리

## 실습 체크리스트
- 연결 리스트 기반 몸통 관리를 분리 함수로 설계한다.

## 본문

### 23-1 LinkedList 구현하고 뱀 게임 만들기
- 뱀의 몸통은 "앞/뒤가 계속 변하는 데이터"라 연결 리스트와 잘 맞습니다.
- 핵심은 `노드 추가`, `노드 삭제`, `머리 이동`, `충돌 검사`를 함수로 분리하는 것입니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| LinkedList(연결 리스트) | 노드들이 포인터로 다음 노드를 가리키며 이어진 자료구조 |
| 노드(node) | 데이터와 다음 노드 주소(`next`)를 담는 한 칸 |

왜 LinkedList가 필요한가:
| 상황 | 배열 대비 장점 |
|---|---|
| 머리 쪽에 새 칸 추가 | 기존 원소 전체 이동 없이 포인터 연결만 바꾸면 됨 |
| 꼬리 삭제/이동 | 연결만 끊고 메모리 해제하면 됨 |
| 몸통 길이 변화 | 크기 고정 배열보다 유연하게 확장/축소 가능 |

권장 구현 순서:
1. 노드 구조체 정의 (`x`, `y`, `next`)
2. 머리 삽입/꼬리 삭제 함수 작성
3. 입력 방향 처리
4. 벽/자기 몸 충돌 검사
5. 먹이 생성/점수 시스템 추가

핵심 기능 체크:
| 기능 | 최소 구현 포인트 |
|---|---|
| 노드 추가 | 새 노드 생성 + 연결 |
| 노드 삭제 | 연결 갱신 + 메모리 해제 |
| 충돌 검사 | 벽/몸통 좌표 비교 |
| 종료 처리 | 리스트 전체 `free` |

예시 코드(연결 리스트 기본):
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int x, y;
    struct Node *next;
} Node;

Node* push_front(Node *head, int x, int y) {
    Node *new_node = (Node*)malloc(sizeof(Node));
    if (!new_node) return head;
    new_node->x = x;
    new_node->y = y;
    new_node->next = head;
    return new_node;
}

void free_list(Node *head) {
    while (head != NULL) {
        Node *next = head->next;
        free(head);
        head = next;
    }
}

int main(void) {
    Node *snake = NULL;
    snake = push_front(snake, 5, 5);
    snake = push_front(snake, 6, 5);
    printf("head: (%d, %d)\n", snake->x, snake->y);
    free_list(snake);
    return 0;
}
```
- 실습에서도 프로그램 종료 전에 리스트를 순회하며 `free`로 메모리를 해제하는 습관을 반드시 지키세요.

예상 출력:
```text
head: (6, 5)
```

연습문제:
1. 문제 1 - 기본 연결 리스트 구현
   - 문제: 단일 연결 리스트에 대해 `push_front`, `pop_back`을 구현하세요.
   - 입력: 좌표 노드 여러 개
   - 출력: 삽입/삭제 후 리스트 상태
   - 조건: 삭제 시 메모리 해제 필수
2. 문제 2 - 충돌 검사 함수
   - 문제: 머리 좌표와 몸통 리스트를 비교해 충돌 여부를 반환하는 `is_collision` 함수를 작성하세요.
   - 입력: 머리 좌표, 몸통 리스트
   - 출력: 충돌 시 1, 아니면 0
   - 조건: 리스트 전체 순회

정답 포인트:
- 메모리 할당 후 사용, 사용 후 해제
- 리스트 연결 끊김/유실 없는지 디버깅 출력으로 확인

---

[상위 문서로 돌아가기](./README.md)
