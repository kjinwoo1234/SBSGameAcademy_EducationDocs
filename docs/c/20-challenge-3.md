# Chapter 20 도전! 프로그래밍 3

## 학습 목표
- 자료구조 구현과 게임 로직 결합을 경험한다.
- 포인터 중심 설계를 실제 프로젝트에 적용한다.

## 세부 주제
### 20-1 LinkedList 구현하고 뱀 게임 만들기
- 노드 연결 구조, 상태 갱신, 충돌 처리

## 실습 체크리스트
- 연결 리스트 기반 몸통 관리를 분리 함수로 설계한다.

## 본문

### 20-1 LinkedList 구현하고 뱀 게임 만들기
- 뱀의 몸통은 "앞/뒤가 계속 변하는 데이터"라 연결 리스트와 잘 맞습니다.
- 핵심은 `노드 추가`, `노드 삭제`, `머리 이동`, `충돌 검사`를 함수로 분리하는 것입니다.

권장 구현 순서:
1. 노드 구조체 정의 (`x`, `y`, `next`)
2. 머리 삽입/꼬리 삭제 함수 작성
3. 입력 방향 처리
4. 벽/자기 몸 충돌 검사
5. 먹이 생성/점수 시스템 추가

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

int main(void) {
    Node *snake = NULL;
    snake = push_front(snake, 5, 5);
    snake = push_front(snake, 6, 5);
    printf("head: (%d, %d)\n", snake->x, snake->y);
    return 0;
}
```

연습문제:
1. 단일 연결 리스트의 `push_front`, `pop_back` 구현
2. 좌표 중복 검사 함수(`is_collision`) 구현

정답 포인트:
- 메모리 할당 후 사용, 사용 후 해제
- 리스트 연결 끊김/유실 없는지 디버깅 출력으로 확인

---

[상위 문서로 돌아가기](./README.md)
