# C 구조체

구조체(Struct)는 서로 관련된 여러 필드를 하나의 사용자 정의 타입으로 묶습니다.  
배열과 함께 사용하면 "학생 목록", "상품 목록" 같은 실전 데이터를 다루기 좋습니다.

## 1) 구조체 선언
```c
typedef struct {
    char name[20];
    int score;
    double height;
} Student;
```

- `typedef`를 사용하면 `struct Student` 대신 `Student`로 바로 사용 가능

## 2) 구조체 변수와 멤버 접근
```c
Student s1;
strcpy(s1.name, "Hong");
s1.score = 95;
s1.height = 175.5;

printf("%s %d %.1f\n", s1.name, s1.score, s1.height);
```

## 3) 구조체 배열
```c
Student list[3] = {
    {"Kim", 80, 170.0},
    {"Lee", 90, 180.2},
    {"Park", 85, 165.4}
};
```

## 4) 구조체와 포인터
포인터로 구조체를 다룰 때는 `->` 연산자를 사용합니다.

```c
Student s = {"Choi", 88, 172.3};
Student* p = &s;
printf("%s %d\n", p->name, p->score);
```

## 5) 함수와 구조체
- 값 전달: 구조체 전체 복사
- 포인터 전달: 원본 수정 가능, 성능 유리

## 6) 연습 문제
1. 학생 구조체 5명을 배열로 만들고 평균 점수 계산
2. 최고 점수 학생 찾기
3. 구조체 포인터를 받아 정보 출력하는 함수 작성

---

[상위 문서로 돌아가기](./README.md)
