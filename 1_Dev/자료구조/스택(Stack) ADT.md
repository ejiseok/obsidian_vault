
# 개념

- 스택(Stack)은 **가장 마지막에 들어간 데이터가 제일 먼저 나오고(LIFO, Last In First Out) 가장 먼저 들어간 데이터는 가장 나중에 나오는(FILO, First In Last Out)** [[자료구조(Data Structure)|자료구조]]다.
- 스택은 큐와 더불어 소프트웨어 분야에서 중요한 역할을 맡고 있다.
	- C언어에서 변수를 선언한 후 수명주기가 끝나면 자동으로 제거하는 자동 메모리도 스택으로 구현되어 있다.
		- 때문에 지역 변수는 스택(자동 매모리)에 할당된다고 표현된다.
	- 거의 대부분의 네트워크 프로토콜도 스택을 기반으로 구성되어 있다.
	- 다양한 프로그램들의 되돌리기(Undo)도 스택을 이용하고 있다.

# 핵심 연산

- 스택 ADT의 주요 연산은 **삽입(Push)** 과 **제거(Pop)** 연산 둘 뿐, 나머지는 보조 연산에 지나지 않는다.

## 삽입(Push)

![[Pasted image 20251130231507.png]]

- 스택에 새로운 노드(요소)를 '쌓는' 연산

## 제거(Pop)

![[Pasted image 20251130231527.png]]

- 스택에서 최상위 노드를 '걷어'내는 연산

# 배열 기반 스택 구현

- 배열을 기반으로 구현한 스택은 용량을 변경하는 비용이 크지만, 구현이 간단하다는 장점이 있다.
## 스택 구조체

```c
typedef struct tagArrayStack {
  Node *nodes; // 노드 배열
  int capacity; // 용량
  int top; // 최상위 노드의 위치
} ArrayStack;
```

## 스택 및 노드 생성/소멸 연산

```c
void AS_CreateStack(ArrayStack **stack, int capacity) {
  // 스택을 자유 저장소에 생성
  (*stack) = (ArrayStack *)malloc(sizeof(ArrayStack));

  // 입력된 capacity만큼의 노드를 자유 저장소에 생성
  (*stack)->nodes = (Node *)malloc(sizeof(Node) * capacity);

  // capacity 및 top 초기화
  (*stack)->capacity = capacity;
  (*stack)->top = -1;
}
```

```c
void AS_DestroyStack(ArrayStack *stack) {
  // 노드를 자유 저장소에서 해제
  free(stack->nodes);

  // 스택을 자유 저장소에서 해제
  free(stack);
}
```

## 노드 삽입 연산(Push)

- 최상위 노드의 인덱스(top)에서 1 더한 곳에 새 노드를 입력하는 연산

```c
void AS_Push(ArrayStack *stack, ElementType data) {
  stack->top++;
  stack->nodes[stack->top].data = data;
}
```

## 노드 제거 연산(Pop)

- 최상위 노드의 인덱스(top) 값을 1 낮추고, 원래 최상위 노드에 있던 데이터는 반환하는 연산

```c
ElementType AS_Pop(ArrayStack *stack) {
  int position = stack->top--;
  return stack->nodes[position].data;
}
```

# 링크드 리스트 기반 스택

- [[링크드 리스트(Linked List)|링크드 리스트]]로 스택을 구현하면 배열 기반에 비해 용량에 제한을 두지 않아도 되는 장점이 있다.

## 스택과 노드 표현

```c
typedef struct tagNode {
  char *data;
  struct tagNode *nextNode;
} Node;
```

```c
typedef struct tagLinkedListStack {
  Node *list; // 데이터를 담는 링크드 리스트
  Node *top; // 링크드 리스트의 테일을 가리킴
} LinkedListStack;
```

## 스택 생성/소멸 연산

```c
void LLS_CreateStack(LinkedListStack **stack) {
  // 스택을 자유 저장소에 생성
  (*stack) = (LinkedListStack *)malloc(sizeof(LinkedListStack));
  (*stack)->list = NULL;
  (*stack)->top = NULL;
}
```

```c
void LLS_DestroyStack(LinkedListStack *stack) {
  while (!LLS_IsEmpty(stack)) {
    Node *popped = LLS_Pop(stack);
    LLS_DestroyNode(popped);
  }

  // 스택을 자유 저장소에서 해제
  free(stack);
}
```

## 노드 생성/소멸 연산

```c
Node *LLS_CreateNode(char *newData) {
  Node *newNode = (Node *)malloc(sizeof(Node));
  newNode->data = (char *)malloc(strlen(newData) + 1);

  strcpy(newNode->data, newData); // 데이터를 저장

  newNode->nextNode = NULL; // 다음 노드에 대한 포인터는 NULL로 초기화

  return newNode; // 노드의 주소를 반환
}
```

```c
void LLS_DestroyNode(Node *_node) {
  free(_node->data);
  free(_node);
}
```

## 노드 삽입 연산

- 스택의 최상위 노드 top에 새 노드를 얹는 연산

```c
void LLS_Push(LinkedListStack *stack, Node *newNode) {
  if (stack->list == NULL) {
    stack->list = newNode;
  } else {
    // 스택의 top에 신규 노드를 연결
    stack->top->nextNode = newNode;
  }

  // 스택의 top 필드에 새 노드의 주소를 등록
  stack->top = newNode;
}
```

## 노드 제거 연산

- 제거 연산은 다음 네 단계로 이루어진다.
	1. 현재 최상위 노드(top)의 주소를 다른 포인터에 복사
	2. 새로운 최상위 노드(현재 최상위 노드)의 바로 아래(이전) 노드를 찾는다.
	3. `LinkedListStack` 구조체의 `top` 필드에 새로운 최상위 노드의 주소를 등록한다.
	4. 미리 저장해뒀던 예전 최상위 노드의 주소를 반환한다.

```c
Node *LLS_Pop(LinkedListStack *stack) {
  // LLS_Pop() 함수가 반환할 최상위 노드 저장
  Node *topNode = stack->top;

  if (stack->list == stack->top) {
    stack->list = NULL;
    stack->top = NULL;
  } else {
    // top 아래에 있던 노드를 새로운 currentTop에 저장
    Node *currentTop = stack->list;
    while (currentTop != NULL && currentTop->nextNode != stack->top)
      currentTop = currentTop->nextNode;

    // currentTop에 top을 저장
    stack->top = currentTop;
    stack->top->nextNode = NULL;
  }

  return topNode;
}
```
