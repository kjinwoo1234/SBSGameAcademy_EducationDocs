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

뱀의 몸은 앞뒤가 계속 바뀌는 데이터라 **연결 리스트(LinkedList)**와 잘 맞습니다. 구현할 때는 `노드 추가`, `노드 삭제`, `머리 이동`, `충돌 검사`를 각각 함수로 쪼개 두면 디버깅과 확장이 쉬워집니다.

| 용어 | 의미 |
|---|---|
| LinkedList(연결 리스트) | 노드들이 포인터로 다음 노드를 가리키며 이어진 자료구조 |
| 노드(node) | 데이터와 다음 노드 주소(`next`)를 담는 한 칸 |

배열로도 뱀을 표현할 수 있지만, 리스트는 **앞에 노드를 붙이거나 뒤를 뗄 때** 기존 원소를 통째로 옮기지 않고도 연결만 바꿀 수 있는 경우가 많습니다. 몸 길이가 자주 변하는 상황에 유연합니다.

권장 구현 순서는 다음과 같습니다. 먼저 노드 구조체(`x`, `y`, `next` 등)를 정의하고, 머리 삽입·꼬리 삭제 함수를 만든 뒤 입력 방향 처리, 벽·자기 몸 충돌 검사, 먹이·점수를 순서대로 얹으면 됩니다.

| 기능 | 최소 구현 포인트 |
|---|---|
| 노드 추가 | 새 노드 생성 + 연결 |
| 노드 삭제 | 연결 갱신 + 메모리 해제 |
| 충돌 검사 | 벽/몸통 좌표 비교 |
| 종료 처리 | 리스트 전체 `free` |

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

실습에서도 프로그램이 끝나기 전에 리스트를 순회하며 `free`로 메모리를 돌려 주는 습관을 지키는 것이 중요합니다.

**예상 출력**

```text
head: (6, 5)
```

### 연습문제

**문제 1**  
단일 연결 리스트에 대해 `push_front`와 `pop_back`을 구현하세요. 삭제 시에는 반드시 `free`로 메모리를 해제합니다.

**문제 2**  
머리 좌표와 몸통 리스트를 받아 충돌이면 1, 아니면 0을 반환하는 `is_collision` 함수를 작성하세요. 리스트 전체를 순회합니다.

### 정답 포인트

`malloc`으로 만든 노드는 사용이 끝나면 `free`로 짝을 맞추고, 연결이 끊기지 않았는지 출력으로 확인하면 디버깅이 수월합니다.

---

[상위 문서로 돌아가기](./README.md)
