
# 개념

- [[링크드 리스트(Linked List)|링크드 리스트]]의 탐색 기능을 개선한 자료구조
- 헤드에서 테일 방향으로만 탐색할 수 있는 링크드 리스트와 달리, 더블 링크드 리스트는 양방향으로 탐색이 가능
	- 다음 노드를 가리키는 포인터뿐만 아니라, 앞의 노드를 가리키는 포인터도 가지기 때문

![[Pasted image 20251126141241.png]]

```c
typedef int ElementType;

typedef struct tagNode {
    ElementType data;
    struct tagNode* prevNode;
    struct tagNode* nextNode;
} Node;
```

- 이러한 노드를 줄줄이 연결해 더블 링크드 리스트가 된다.

![[Pasted image 20251126141921.png]]

# 주요 연산

## 노드 생성/소멸 연산

-  '이전 노드'를 처리하는 구현이 추가됐을 뿐 기존 링크드 리스트와 크게 다르지 않다.

```c
Node* DLL_CreateNode(ElementType newData) {
    Node* newNode = (Node*)malloc(sizeof(Node));

    newNode->data = newData;
    newNode->prevNode = NULL;
    newNode->nextNode = NULL;

    return newNode;
}
```

```c
void DLL_DestoryNode(Node* node) {
    free(node);
}
```

## 노드 추가 연산

- 새로운 테일의 이전 노드 포인터도 기존 테일의 주소를 가리키는 코드를 추가해야 한다.

![[Pasted image 20251126142540.png]]

```c
void DLL_AppendNode(Node** head, Node* newNode) {
    if ((*head) == NULL) {
        *head = newNode;
    } else {
        Node* tail = (*head);
        while (tail->nextNode != NULL) {
            tail = tail->nextNode;
        }

        tail->nextNode = newNode;
        newNode->prevNode = tail;  // 기존의 테일을 새로운 테일의 prevNode가 가리킴
    }
}
```

## 노드 탐색 연산

- 기존 링크드 리스트의 연산과 다름 없음

```c
Node* DLL_GetNodeAt(Node* head, int location) {
    Node* current = head;

    while (current != NULL && (--location) >= 0) {
        current = current->nextNode;
    }

    return current;
}
```

## 노드 삭제 연산

![[Pasted image 20251126143053.png]]

- 더블 링크드 리스트에서의 노드 삭제 연산에서는 삭제할 노드의 양쪽 포인터 2개, 이전 노드의 다음 노드 포인터, 다음 노드의 이전 노드 포인터, 이렇게 4개의 포인터를 변경해야 한다.
	- 삭제할 노드의 다음 노드는 이전 노드의 다음 노드 포인터가 가리켜야 함
	- 삭제할 노드의 이전 노드는 다음 노드의 이전 노드 포인터가 가리켜야 함
	- 삭제할 노드의 다음 노드 포인터와 이전 노드 포인터는 NULL로 초기화

```c
void DLL_RemoveNode(Node** head, Node* remove) {
    if ((*head) == remove) {
        *head = remove->nextNode;
        if ((*head) != NULL) (*head)->prevNode = NULL;

        remove->prevNode = NULL;
        remove->nextNode = NULL;
    } else {
        Node* temp = remove;

        if (remove->prevNode != NULL)
            remove->prevNode->nextNode = temp->nextNode;

        if (remove->nextNode != NULL)
            remove->nextNode->prevNode = temp->prevNode;

        remove->prevNode = NULL;
        remove->nextNode = NULL;
    }
}
```

## 노드 삽입 연산

![[Pasted image 20251126143813.png]]

- 더블 링크드 리스트에 새로운 노드를 삽입할 때는 이전 노드 포인터에 이전 노드, 다음 노드 포인터에 다음 노드를 가리키게 하고,
- 각각 이전 노드의 다음 노드 포인터와 다음 노드의 이전 노드 포인터는 새로운 노드를 가리키게 한다.

```c
void DLL_InsertAfter(Node* current, Node* newNode) {
    newNode->nextNode = current->nextNode;
    newNode->prevNode = current;

    if (current->nextNode != NULL) {
        current->nextNode->prevNode = newNode;
        current->nextNode = newNode;
    }
}
```

## 노드 개수 세기 연산

- 기존 링크드 리스트의 연산과 다름 없음

```c
int DLL_GetNodeCount(Node* head) {
    unsigned int    count = 0;
    Node*           currnet = head;

    while (currnet != NULL) {
        currnet = currnet->nextNode;
        count++;
    }

    return count;
}
```