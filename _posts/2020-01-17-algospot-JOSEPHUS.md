---
layout: post
title: "algospot 조세푸스 문제"
date: 2020-01-17 00:00
tags: [algorithm,algospot]
---

**문제** : [JOSEPHUS](https://algospot.com/judge/problem/read/JOSEPHUS)

**풀이** : 원형 연결리스트를 구현하여 없어진 값의 인덱스에서 시계방향으로 k번째 있는 값들을 인원이 2명남을때 까지 지워주면 됩니다.

**소스코드** :
```c++
#include<iostream>

using namespace std;

const int NODESIZE = 1001;

template<class T>
struct Node {
	int prev;
	int next;
	T val;
};

template<class T>
struct List {
private:
	int HEAD = NODESIZE;
	int TAIL = NODESIZE + 1;
	int _size, pos;
	Node<T> node[NODESIZE + 2];

public:
	List() {
		_size = pos = 0;
		node[HEAD].next = TAIL;
		node[HEAD].prev = TAIL;
		node[TAIL].prev = HEAD;
		node[TAIL].next = HEAD;
	}

	void push_back(T val) {
		int prev = node[TAIL].prev;
		int next = node[prev].next;

		node[pos].val = val;
		node[pos].next = next;
		node[next].prev = pos;

		node[pos].prev = prev;
		node[prev].next = pos;

		pos++;
		_size++;
	}

	void push_front(T val) {
		int next = node[HEAD].next;
		int prev = node[next].prev;

		node[pos].val = val;
		node[pos].next = next;
		node[next].prev = pos;

		node[pos].prev = prev;
		node[prev].next = pos;

		pos++;
		_size++;
	}

	int remove(int idx,int p) {
		int target = node[idx].next;

		while (target == HEAD || target == TAIL) {
			target = node[target].next;
		}

		while (p>0) {
			target = node[target].next;
			if (target == HEAD || target == TAIL) continue;
			p--;
		}

		int prev = node[target].prev;
		int next = node[target].next;

		node[prev].next = next;
		node[next].prev = prev;

		_size--;

		return node[target].prev;
	}

	void GetallElement() {
		int target = node[HEAD].next;

		while (target != TAIL) {
			cout << node[target].val << " ";
			target = node[target].next;
		}

		cout << "\n";
	}

	int size() {
		return _size;
	}

	void clear() {
		_size = pos = 0;
		node[HEAD].next = TAIL;
		node[HEAD].prev = TAIL;
		node[TAIL].prev = HEAD;
		node[TAIL].next = HEAD;
	}
};

int test, n, k;
List<int> list;

int main() {

	cin >> test;

	for (int tc = 1; tc <= test; tc++) {
		cin >> n >> k;

		for (int i = 1; i <= n; i++) {
			list.push_back(i);
		}

		int idx = 0;

		idx = list.remove(NODESIZE,0);

		while (list.size() > 2) {
			idx = list.remove(idx, k - 1);
		}

		list.GetallElement();

		list.clear();
	}

	return 0;
}
```
