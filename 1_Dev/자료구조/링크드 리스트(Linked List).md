
# 개념

- 링크드 리스트(Linked List)는 **노드를 연결해서 만든 리스트**라고 해서 붙여진 이름이다.
- 링크드 리스트의 노드는 데이터를 보관하는 필드와 다음 노드와의 연결 고리 역할을 하는 포인터로 이루어진다.

![[Pasted image 20251110153943.png]]

- 이러한 노드들을 엮으면 링크드 리스트가 된다.

![[Pasted image 20251110154015.png]]

- 다른 [[리스트(List) ADT|리스트]]처럼 첫 번째 노드를 헤드, 마지막 노드를 테일이라고 부른다.

![[Pasted image 20251110154114.png]]

# 장점

- 배열과 달리 새로운 노드의 추가, 삽입, 삭제가 쉽고 빠르다.

# 단점

- 다음 노드를 가리키는 포인터가 있기 때문에 각 노드마다 추가적인 메모리 공간이 필요하다.
- 배열과 비교해서 특정 위치에 있는 노드에 접근하기 위한 비용이 크다.

# 활용 방법

- 링크드 리스트는 레코드의 추가, 삽입, 삭제가 잦지만 조회든 드문 곳에서 사용하기 적합하다.
	- 예: 데이터베이스에서 조회해온 레코드를 순차적으로 다루는 등

# 노드 표현

```c
typedef int ElementType;

typedef struct tagNode {
    ElementType Data; // 데이터
    struct Node* NextNode; // 다음 노드
} Node;
```

# 주요 연산

## 노드 생성(CreateNode) / 소멸(DestroyNode)

```c
// SSL은 Singly Linked List라는 의미
Node* SSL_CreateNode(ElementType NewData) {
    Node* NewNode = (Node*)malloc(sizeof(Node));

    NewNode->Data = NewData;    // 데이터 저장
    NewNode->NextNode = NULL;   // 다음 노드에 대한 포인터는 NULL로 초기화

    return NewNode;     // 노드의 주소를 반환
}
```

```c
void SSL_Destory(Node* Node) {
    free(Node);
}
```

## 노드 추가(AppendNode)

![[Pasted image 20251110155645.png]]

```c
void SSL_AppendNode(Node** Head, Node* NewNode) {
    // 헤드가 NULL이면 새로운 노드가 헤드가 된다.
    if ((*Head) == NULL) {
        *Head = NewNode;
    } else {
        // 테일을 찾아 NewNode를 연결한다.
        Node* Tail = (*Head);
        while (Tail->NextNode != NULL) {
            Tail = Tail->NextNode;
        }
        Tail->NextNode = NewNode;
    }
}
```

## 노드 탐색(GetNodeAt)

- 링크드 리스트에서 노드 탐색은 헤드부터 시작해서 차근차근 다음 노드로 거쳐가며 원하는 요소를 찾는 다소 비효율성이 큰 방식으로 탐색이 이루어진다.

```c
Node* SLL_GetNodeAt(Node* Head, int Location) {
    Node* Current = Head;

    while (Current != NULL && (--Location) >= 0) {
        Current = Current->NextNode;
    }

    return Current;
}
```

## 노드 삭제(RemoveNode)

![[Pasted image 20251110172104.png]]

```c
void SSL_RemoveNode(Node** Head, Node* Remove) {
    if ((*Head) == Remove) {
        *Head = Remove->NextNode;
    } else {
        Node* Current = *Head;
        while (Current != NULL && Current->NextNode != Remove) {
            Current = Current->NextNode;
        }

        if (Current != NULL) Current->NextNode = Remove->NextNode;
    }
}
```

## 노드 삽입(InsertAfter, InsertNewHead)

![[Pasted image 20251110172418.png]]

```c
void SSL_InsertAfter(Node* Current, Node* NewNode) {
    NewNode->NextNode = Current->NextNode;
    Current->NextNode = NewNode;
}
```

## 노드 개수 세기 연산

```c
void SSL_GetNodeCount(Node* head) {
    int count = 0;
    Node* current = head;

    while (current != NULL) {
        current = current->NextNode;
        count++;
    }

    return count;
}
```
