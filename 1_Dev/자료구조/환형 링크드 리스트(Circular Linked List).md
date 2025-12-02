
# 개념

![[Pasted image 20251127154857.png]]

- 환형 링크드 리스트(Circular Linked List)는 머리(헤드)가 꼬리(테일)을 물고 있는 형태의 [[링크드 리스트(Linked List)|링크드 리스트]]이다.
- 예시는 [[더블 링크드 리스트(Double Linked List)|더블 링크드 리스트]]지만 링크드 리스트로도 만들 수 있다.

# 장점

- 구조 상 테일에 접근하는 비용이 거의 없어진다.
	- 노드 추가 등의 성능이 개선된다.
	- 뒤에서부터 시작하는 노드 탐색을 구현할 수 있다.

# 주요 연산

## 노드 추가 연산

- 환형 링크드 리스트에서 노드 추가 연산은 **테일과 헤드 사이에 새 노드를 삽입**되는 연산이다.

```c
void CDLL_AppendNode(Node** head, Node* newNode) {
    // 헤드 노드가 NULL이라면 새로운 노드가 Head가 된다.
    if ((*head) == NULL) {
        *head = newNode;
        (*head)->nextNode = *head;
        (*head)->prevNode = *head;
    } else {
        // 테일과 헤드 사이에 newNode를 삽입한다.
        Node* tail = (*head)->prevNode;

        tail->nextNode->prevNode = newNode;
        tail->nextNode = newNode;

        newNode->nextNode = (*head);
        newNode->prevNode = tail; // 새로운 테일의 prevNode가 기존의 테일을 가리킨다.
    }
}
```

## 노드 삭제 연산

- 테일과 헤드가 연결되어 있다는 점만 주의하면, 더블 링크드 리스트와 크게 다르지 않다.

```c
void CDLL_RemoveNode(Node** head, Node* remove) {
    if ((*head) == remove) {
        (*head)->prevNode->nextNode = remove->nextNode;
        (*head)->nextNode->prevNode = remove->prevNode;

        *head = remove->nextNode;

        remove->prevNode = NULL;
        remove->nextNode = NULL;
    } else {
        remove->prevNode->nextNode = remove->nextNode;
        remove->nextNode->prevNode = remove->prevNode;

        remove->prevNode = NULL;
        remove->nextNode = NULL;
    }
}
```
