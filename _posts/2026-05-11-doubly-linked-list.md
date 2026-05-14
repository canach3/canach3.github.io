---
title: "[Data Structure] 이중 연결 리스트(Doubly Linked List)"
date: 2026-05-11 13:15:00 +0900
permalink: /data-structure/doublylinkedlist/
categories: [Data Structure]
tags: [data structure, 자료구조, doubly linked list, 이중연결리스트]
description: "이중 연결 리스트(Doubly Linked List)에 대한 개념을 알아보고 코드로 구현해보자"
image:
  path: /assets/img/posts/2026-05-11-doubly-linked-list/cover.png  # 배경으로 쓸 이미지 경로
math: true # 수학 수식 사용
toc: false # 오른쪽 목차 유무 설정
---



# Doubly Linked List

날짜: 2026년 5월 14일
다중 선택: Data Structure
상태: 시작 전

## ◾ 이중 연결 리스트(Doubly Linked List)

**이중 연결 리스트(Doubly Linked List)**는 각 노드가 데이터와 함께 양방향의 연결 정보(이전 노드, 다음 노드)를 모두 가지는 자료구조다.

먼저, 기본적인 **단일 연결 리스트(Singly Linked List)와 구조적으로 어떠한 차이가 있는 지 알아보자.**

#### 단일 연결 리스트 구조

<img src="/assets/img/posts/2026-05-11-doubly-linked-list/image1.png" alt="data structure 단일 연결 리스트" width="100">

**단일 연결 리스트**를 이루고 있는 각 노드는 다음 정보를 가지고 있다.

1. **data :** 해당 노드가 가지고 있는 실제 데이터
2. **next :** 자신의 다음 노드를 가리키는 포인터

#### 이중 연결 리스트 구조

<img src="/assets/img/posts/2026-05-11-doubly-linked-list/image2.png" alt="data structure 이중 연결 리스트" width="100">

**이중 연결 리스트**를 이루고 있는 각 노드는 다음 정보를 가지고 있다.

1. **prev:** 내 앞에 있는 노드를 가리키는 포인터
2. **data:** 내가 품고 있는 실제 데이터
3. **next:** 내 뒤에 있는 노드를 가리키는 포인터

**단일 연결 리스트**는 '다음 노드(Next)'로 갈 수 있는 정보만 가지고 있어 앞으로만 이동이 가능한 반면, **이중 연결 리스트**는  '이전 노드(Prev)'로 갈 수 있는 정보도 추가로 가지고 있어 앞뒤로 자유롭게 왕복 이동이 가능하다.

## ◾ '이중 연결 리스트'를 사용하는 이유

**단일 연결 리스트**에서 만족하지 않고 굳이 메모리를 더 써가면서까지 이전 노드를 가리키는 포인터(`prev`)를 하나 더 두는 이유가 무엇인지 한 번 알아보자.

#### **“단일 연결 리스트는 뒤로 갈 수 없다”**

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

포인터 연결 자체는 $O(1)$이지만, 이전 노드를 찾기 위한 탐색 과정 때문에 결과적으로  **$O(N)$의 시간**이 걸려버리는 모순이 발생한다. 

#### 이중 연결 리스트 : 탐색이 필요없는 진짜 O(1)

**이중 연결 리스트는** 각 노드가 이미 자신의 '이전 노드'가 누구인지(`prev`) 기억하고 있기 때문에, 특정 노드를 삭제하거나 그 위치에 새로운 노드를 삽입할 때 처음부터 탐색할 필요가 없다. 즉, 해당 노드의 정보만 알고 있다면 **삽입과 삭제를 $O(1)$의 시간 복잡도로 즉시 해결할 수 있다.**

1. 이전 노드의 `next`가 다음 노드를 가리키게 바꾼다. (`prevNode.next = nextNode;`)
2. 다음 노드의 `prev`가 이전 노드를 가리키게 바꾼다. (`nextNode.prev = prevNode;`)

동작 과정의 이해를 돕기 위한 코드로, 검증로직은 제외했다 (실전 코드는 마지막에 있음)

```java
// 처음부터 탐색할 필요 없이, 즉석에서 앞뒤 노드를 서로 연결하면 된다.
prevNode.next = nextNode;
nextNode.prev = prevNode;
```

위 코드가 어떻게 동작하는지 확인해보자.

[시각화 자료]

#### 이중 연결 리스트의 또다른 장점

이전 노드 정보를 가지고 있는 것에 대한 장점은 탐색에서도 발휘된다.

단일 연결 리스트는 무조건 맨 앞(Head)에서 출발해야 하지만, **이중 연결 리스트는 맨 뒤(Tail)에서부터 거꾸로 탐색하는 것도 가능하다**. 찾고자 하는 데이터가 리스트의 뒷부분에 있다면 Tail에서 출발하여 **탐색 시간을 절반으로 뚝 떼어낼 수 있는 것이다.**

## ◾ 이중 연결 리스트의 삽입

이중 연결 리스트의 삽입 과정이다. 삭제와 마찬가지로 대상 노드의 양옆의 포인터(`prev`, `next`)만 재설정해주면 된다.

1. 이전 노드의 `next`가 새로운 노드를 가리키게 바꾼다. (`prevNode.next = newNode;`)
2. 새로운 노드의 `prev`가 이전 노드를 가리키게 바꾼다. (`newNode.prev = prevNode;`)
3. 새로운 노드의 `next`가 다음 노드를 가리키게 바꾼다. (`newNode.next = nextNode;`)
4. 다음 노드의 `prev`가 새로운 노드를 가리키게 바꾼다. (`nextNode.prev = newNode;`)

```java
// 이전 노드와 새로운 노드 연결
prevNode.next = newNode;
newNode.prev = prevNode;

// 다음 노드와 새로운 노드 연결
newNode.next = nextNode;
nextNode.prev = newNode;
```

`[여기에 데이터 삽입 시 4개의 포인터(prev, next)가 서로 엇갈리며 변경되는 과정을 단계별로 보여주는 다이어그램 삽입]`

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

아래의 코드는 이해를 돕기 위해 다양한 기능을 모두 구현했지만, 실제 알고리즘 테스트에서는 문제에서 요구하는 필수적인  메서드만 선택적으로 구현하면 된다.

```java
public class DoublyLinkedList {
    private Node head;
    private Node tail;
    private int size; // 리스트 사이즈

    public DoublyLinkedList() {
        this.head = null;
        this.tail = null;
        this.size = 0;
    }

    // [특정 인덱스 노드 구하기]
    public Node get(int idx) {
        if (idx < 0 || idx >= size) {
            return null;
        }

        Node current;
        
        // 인덱스가 절반 앞이면 head부터 정방향 탐색
        if (idx < size / 2) {
            current = head;
            for (int i = 0; i < idx; i++) {
                current = current.next;
            }
        } 
        
        // 인덱스가 절반 뒤면 tail부터 역방향 탐색
        else {
            current = tail;
            for (int i = size - 1; i > idx; i--) {
                current = current.prev;
            }
        }
        return current;
    }

    // [앞부분에 삽입]
    public void addFirst(int data) {
        Node newNode = new Node(data);

        // 리스트가 비어있는 경우 head와 tail 모두 새로운 노드를 가리킴
        if (head == null) {
            head = tail = newNode;
        } else {
            // 기존 head의 이전에 새로운 노드 연결
            head.prev = newNode;
            newNode.next = head;
            
            // 새로운 노드가 이제 새로운 head가 됨
            head = newNode;
        }
        size++;
    }

    // [끝부분에 삽입]
    public void addLast(int data) {
        if (size == 0) {
            addFirst(data);
            return;
        }

        Node newNode = new Node(data);

        // 기존 tail의 다음에 새로운 노드 연결
        tail.next = newNode;
        newNode.prev = tail;
        
        // 새로운 노드가 이제 새로운 tail이 됨
        tail = newNode;
        size++;
    }

    // [특정 인덱스에 삽입]
    public void add(int idx, int data) {
        if (idx < 0 || idx > size) return;
        if (idx == 0) {
            addFirst(data);
            return;
        }
        if (idx == size) {
            addLast(data);
            return;
        }

        Node prevNode = get(idx - 1);
        Node nextNode = prevNode.next;
        Node newNode = new Node(data);

        // 이전 노드와 새로운 노드 연결
        prevNode.next = newNode;
        newNode.prev = prevNode;

        // 다음 노드와 새로운 노드 연결
        newNode.next = nextNode;
        nextNode.prev = newNode;

        size++;
    }

    // [특정 노드 뒤에 삽입]
    public void addAfter(Node prevNode, int data) {
        if (prevNode == null) return;

        Node newNode = new Node(data);
        Node nextNode = prevNode.next;

        // 새로운 노드의 앞뒤 포인터 연결
        newNode.prev = prevNode;
        newNode.next = nextNode;

        // 기존 이전 노드의 다음을 새로운 노드로 연결
        prevNode.next = newNode;

        // 기존 다음 노드가 존재한다면, 그 노드의 이전을 새로운 노드로 연결
        if (nextNode != null) {
            nextNode.prev = newNode;
        } else {
            // 추가된 노드가 마지막 노드라면 tail 갱신
            tail = newNode;
        }
        size++;
    }

    // [특정 인덱스 노드 삭제]
    public void remove(int idx) {
        if (idx < 0 || idx >= size) return;
        
        Node nodeToRemove = get(idx);
        remove(nodeToRemove); // 아래의 삭제 메서드 사용
    }

    // [특정 노드 삭제]
    public void remove(Node node) {
        if (head == null || node == null) {
            return;
        }

        // 삭제할 노드가 head인 경우
        if (head == node) {
            head = node.next;
        }

        // 삭제할 노드가 tail인 경우
        if (tail == node) {
            tail = node.prev;
        }

        // 삭제할 노드의 다음 노드가 존재한다면, 그 노드의 prev를 한 칸 앞으로 당김
        if (node.next != null) {
            node.next.prev = node.prev;
        }

        // 삭제할 노드의 이전 노드가 존재한다면, 그 노드의 next를 한 칸 뒤로 밈
        if (node.prev != null) {
            node.prev.next = node.next;
        }

        // 가비지 컬렉터를 돕기 위해 연결 끊기 (선택)
        node.prev = null;
        node.next = null;
        
        size--;
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

## ◾ 마무리

이중 연결 리스트는 메모리를 더 사용하고 포인터 관리가 까다롭지만, 양방향 탐색과 빠른 O(1) 삭제라는 강력한 이점을 제공한다. 덕분에 웹 브라우저의 '앞/뒤로 가기' 기능이나 백엔드 성능 최적화에 필수적인 LRU 캐시 알고리즘처럼 이전 데이터로의 즉각적인 접근이 필요한 곳에 널리 쓰인다. 

데이터의 앞뒤 탐색이 빈번하거나 빠른 노드 삭제가 필수적인 상황이라면, 이중 연결 리스트 도입을 적극 고려해 볼 만하다.

#### 이중 연결 리스트를 연습할 수 있는 문제

[코딩테스트 연습 - 표 편집](https://school.programmers.co.kr/learn/courses/30/lessons/81303)

[코딩테스트 연습 - [1차] 캐시](https://school.programmers.co.kr/learn/courses/30/lessons/17680)