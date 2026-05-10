---
title: "[Data Structure] 이중 연결 리스트(Doubly Linked List)"
date: 2026-05-11 13:15:00 +0900
permalink: /data-structure/doublylinkedlist/
categories: [Data Structure]
tags: [data structure, 자료구조, doubly linked list, 이중연결리스트]
description: "이중 연결 리스트(Doubly Linked List)에 대한 개념을 알아보고 코드로 구현해보자"
image:
  path: /assets/img/posts/2026-04-17-trie/cover.png  # 배경으로 쓸 이미지 경로
math: true # 수학 수식 사용
toc: false # 오른쪽 목차 유무 설정
---



## ◾ 이중 연결 리스트(Doubly Linked List)

**이중 연결 리스트(Doubly Linked List)**는 각 노드가 데이터와 함께 양방향의 연결 정보(이전 노드, 다음 노드)를 모두 가지는 자료구조다.

기본적인 **단일 연결 리스트(Singly Linked List)**는 '다음 노드(Next)'로 갈 수 있는 정보만 가지고 있는 반면, **이중 연결 리스트**는  '이전 노드(Prev)'로 갈 수 있는 정보도 추가로 가지고 있어 앞뒤로 자유롭게 왕복 이동이 가능하다.

## ◾ '이중 연결 리스트'를 사용하는 이유

**단일 연결 리스트**에서 만족하지 않고 굳이 메모리를 더 써가면서까지 이전 노드를 가리키는 포인터(`prev`)를 하나 더 두는 이유가 무엇인지 한 번 알아보자.

배열(Array)의 삽입과 삭제 연산이 가진 비효율성을 극복하기 위해 등장한 **단일 연결 리스트**. 물리적인 데이터의 자리 이동 없이 포인터(방향표)만 쓱 바꿔주면 되므로, 이론상 데이터의 삽입과 삭제가 $O(1)$의 시간 복잡도로 매우 효율적이다.

하지만 단일 연결 리스트를 조금만 더 깊게 파고들면, **숨겨진 단점**을 발견하게 된다.

#### **단일 연결 리스트의 맹점 : "뒤로 갈 수 없다”**

만약 리스트 내에 존재하는 **특정 노드(**`targetNode`) 를 **삭제**하고 싶다고 가정해보자. (해당 노드의 주소는 이미 알고있다고 가정) 노드를 삭제하려면 앞뒤 노드와의 연결고리를 끊어주기 위헤 반드시 **이전 노드와 다음 노드**의 주소를 알아야 한다.

그런데 **단일 연결 리스트**의 노드는 오직 **'다음 노드'의 주소만 가지고 있다.** 즉, 뒤로 가는 법을 모른다. 결국 삭제할 노드의 바로 앞 노드를 찾기 위해 무조건 **리스트의 처음(Head)부터 다시 순차 탐색을 시작해야 한다.**

```java
// targetNode: 삭제해야 할 노드 (주소를 알고 있음)
// head: 연결 리스트의 첫 번째 노드

Node prev = head;

// targetNode의 '이전 노드'를 찾기 위해 처음부터 순회해야 한다.
// prev의 다음 노드가 targetNode 일때까지 처음부터 순회
while (prev.next != targetNode) {
    prev = prev.next; 
}

// 마침내 이전 노드를 찾았으므로, 
// targetNode를 건너뛰고 이전 노드와 다음 노드를 연결한다.
prev.next = targetNode.next;
```

위 코드가 어떻게 동작하는 확인해보자.

[시각화 자료]

포인터 연결 자체는 $O(1)$이지만, 이전 노드를 찾기 위한 탐색 과정 때문에 결과적으로  **$O(N)$의 시간**이 걸려버리는 모순이 발생한다. 물론 삽입도 마찬가지다.

#### 이중 연결 리스트 : 탐색이 필요없는 진짜 O(1)

**이중 연결 리스트는** 각 노드가 이미 자신의 '이전 노드'가 누구인지(`prev`) 기억하고 있기 때문에, 특정 노드를 삭제하거나 그 위치에 새로운 노드를 삽입할 때 처음부터 탐색할 필요가 없다. 즉, 해당 노드의 위치만 알고 있다면 **삽입과 삭제를 $O(1)$의 시간 복잡도로 즉시 해결할 수 있다.**

```java
// targetNode: 삭제해야 할 노드 (주소를 알고 있음)

Node prevNode = targetNode.prev; // 내 앞 노드 주소
Node nextNode = targetNode.next; // 내 뒤 노드 주소

// 처음부터 탐색할 필요 없이, 즉석에서 앞뒤 노드를 서로 연결하면 된다.
prevNode.next = nextNode;
nextNode.prev = prevNode;
```

코드 동작 과정을 확인해보자.

[시각화 자료]

이중연결리스는 탐색에서도 ~~

또한, 탐색을 할 때도 단일 연결 리스트는 무조건 맨 앞(Head)에서 출발해야 하지만, 이중 연결 리스트는 맨 뒤(Tail)에서부터 거꾸로 탐색(`printBackward`)하는 것도 가능하다. 찾고자 하는 데이터가 리스트의 뒷부분에 있다면 Tail에서 출발하여 탐색 시간을 절반으로 뚝 떼어낼 수 있는 것이다.

---

## ◾ 이중 연결 리스트의 구조 및 동작 원리

이중 연결 리스트를 구성하는 핵심 단위인 노드(Node)는 다음과 같이 세 개의 방으로 나뉘어 있다.

1. **prev:** 내 앞에 있는 노드를 가리키는 포인터
2. **data:** 내가 품고 있는 실제 데이터
3. **next:** 내 뒤에 있는 노드를 가리키는 포인터

`[여기에 이중 연결 리스트 노드 구조(prev, data, next가 이어진 형태) 이미지 삽입]`

### 노드의 삽입 (Insert)

새로운 노드를 리스트의 중간에 삽입한다고 상상해 보자. 단일 연결 리스트보다 챙겨야 할 선(포인터)이 두 배로 늘어난다.

1. 새로운 노드의 `next`를 다음 노드와 연결하고, `prev`를 이전 노드와 연결한다.
2. 이전 노드의 `next`가 새로운 노드를 가리키게 바꾼다.
3. 다음 노드의 `prev`가 새로운 노드를 가리키게 바꾼다.

### 노드의 삭제 (Delete)

삭제는 생각보다 간단하다. 삭제할 노드의 양옆에 있는 노드들이 서로 손을 잡게 만들면 된다.

1. 삭제할 노드의 '이전 노드'가 가진 `next` 포인터를, 삭제할 노드의 '다음 노드'로 곧바로 연결한다.
2. 삭제할 노드의 '다음 노드'가 가진 `prev` 포인터를, 삭제할 노드의 '이전 노드'로 연결한다.
3. 양쪽 연결이 모두 끊어진 노드는 메모리에서 자연스럽게 해제된다.

`[여기에 데이터 삽입 및 삭제 시 4개의 포인터(prev, next)가 서로 엇갈리며 변경되는 과정을 단계별로 보여주는 다이어그램 삽입]`

---

## ◾ 자바 코드 구현

이제 이론으로 알아본 이중 연결 리스트를 자바(Java) 코드로 직접 구현해 보자.

### 1. Node 클래스

```java
class Node {
    int data;
    Node prev; // 이전 노드를 가리키는 포인터
    Node next; // 다음 노드를 가리키는 포인터

    public Node(int data) {
        this.data = data;
        this.prev = null;
        this.next = null;
    }
}
```

### 2. DoublyLinkedList 클래스

검색 속도와 삽입의 효율성을 극대화하기 위해 `head`(머리)와 `tail`(꼬리) 포인터를 모두 유지하는 방식으로 구현한다.

```java
public class DoublyLinkedList {
    private Node head;
    private Node tail;

    public DoublyLinkedList() {
        this.head = null;
        this.tail = null;
    }

    // [앞부분에 삽입]
    public void insertAtBeginning(int data) {
        Node newNode = new Node(data);

        // 리스트가 비어있다면 head와 tail 모두 새로운 노드를 가리킴
        if (head == null) {
            head = tail = newNode;
            return;
        }

        // 기존 head의 이전에 새로운 노드 연결
        head.prev = newNode;
        newNode.next = head;
        // 새로운 노드가 이제 새로운 head가 됨
        head = newNode;
    }

    // [끝부분에 삽입] O(1)의 속도로 추가 가능
    public void insertAtEnd(int data) {
        Node newNode = new Node(data);

        if (tail == null) {
            head = tail = newNode;
            return;
        }

        // 기존 tail의 다음에 새로운 노드 연결
        tail.next = newNode;
        newNode.prev = tail;
        // 새로운 노드가 이제 새로운 tail이 됨
        tail = newNode;
    }

    // [특정 노드 삭제] 탐색 없이 노드 정보만으로 삭제 시 O(1)
    public void deleteNode(Node delNode) {
        if (head == null || delNode == null) {
            return;
        }

        // 1. 만약 삭제할 노드가 head라면?
        if (head == delNode) {
            head = delNode.next;
        }

        // 2. 만약 삭제할 노드가 tail이라면?
        if (tail == delNode) {
            tail = delNode.prev;
        }

        // 3. 삭제할 노드의 다음 노드가 존재한다면, 그 노드의 prev를 한 칸 앞으로 당김
        if (delNode.next != null) {
            delNode.next.prev = delNode.prev;
        }

        // 4. 삭제할 노드의 이전 노드가 존재한다면, 그 노드의 next를 한 칸 뒤로 밈
        if (delNode.prev != null) {
            delNode.prev.next = delNode.next;
        }
        
        // 가비지 컬렉터를 돕기 위해 연결 끊기 (선택사항)
        delNode.prev = null;
        delNode.next = null;
    }

    // [정방향 탐색]
    public void printForward() {
        Node current = head;
        System.out.print("정방향: ");
        while (current != null) {
            System.out.print(current.data + " ");
            current = current.next;
        }
        System.out.println();
    }

    // [역방향 탐색]
    public void printBackward() {
        Node current = tail;
        System.out.print("역방향: ");
        while (current != null) {
            System.out.print(current.data + " ");
            current = current.prev;
        }
        System.out.println();
    }
}
```

---

## ◾ 마무리

자료구조의 세계에서 항상 등장하는 진리가 하나 있다. 바로 '공간(메모리)과 시간(속도)은 Trade-off(교환) 관계'라는 것이다.

이중 연결 리스트는 각 노드마다 `prev`라는 포인터를 하나 더 쥐고 있어야 하므로, 단일 연결 리스트에 비해 메모리를 약 $1.5 \sim 2$배 정도 더 소비하게 된다. (공간 복잡도의 증가)

하지만 그 약간의 메모리를 투자한 덕분에, **양방향 탐색**이라는 엄청난 자유를 얻었고, 특정 노드를 알 때의 삽입과 삭제를 $O(1)$이라는 경이로운 속도로 처리할 수 있게 되었다. (시간 복잡도의 이점)

웹 브라우저의 '뒤로 가기 / 앞으로 가기' 기능이나, 가장 최근에 사용한 데이터를 관리하는 LRU(Least Recently Used) 캐시 알고리즘 등이 모두 이 이중 연결 리스트의 뼈대 위에서 돌아가고 있다. 목적과 상황에 맞게 자료구조의 Trade-off를 저울질하며, 가장 효율적인 코드를 작성해 보자!