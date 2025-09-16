---
title: "Algorithm - Binary Search - 2편"
author: "Sin SeHwan"
#authorAvatarPath: "/avatar.jpeg"
date: "2025-08-03"
summary: "Binary Search (이진 탐색)[2] - Java"
description: "Binary Search (이진 탐색)[2] - Java"
toc: true
readTime: true
autonumber: true
math: true
tags: ["algorithm", "java"]
showTags: true
hideBackToTop: false
fediverse: "@username@instance.url"
---

# Binary Search (이진 탐색) - 2편

> 개념을 충분히 살펴보았으니, 매개 변수 탐색 유형의 문제를 풀어보면서 이해해봅시다!

## 백준 10816 - 숫자 카드 2

매개 변수 탐색 알고리즘을 활용해서 다음 문제를 풀어봅시다!

[백준 10816 - 숫자 카드 2](https://www.acmicpc.net/problem/10816)

### 문제 설명

이 문제는 집합과 해시를 사용해서 개수를 찾는 형식으로 풀 수도 있지만, 매개 변수 탐색 알고리즘을 이해해보기 좋은 예시 문제라서 가져왔습니다.

위에서 살펴본 다음 개념을 활용해서 풀이할 수 있습니다.
```
(상한 경계 다음 인덱스) - (하한 경계) == (조건을 만족하는 값의 개수)
```

### 문제 풀이

```java
import java.io.*;
import java.util.*;

public class Main {
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));

    int n, m;

    int[] card;
    int[] question;

    public static void main(String[] args) throws IOException {
        new Main().solve();

        br.close();
        bw.close();
    }

    void solve() throws IOException {
        getInput();

        for(int q : question) {
            int count = getUpperBound(q) - getLowerBound(q);
            bw.write(count + " ");
        }
    }

    // target이 들어갈 수 있는 최소 인덱스
    // 조건을 만족하는 최소 인덱스.
    int getLowerBound(int target) {
        int st = 0;
        int en = n;

        while(st < en) {
            int mid = st + (en - st) / 2;

            if(card[mid] < target) {
                st = mid + 1;
            }
            else {
                // card[mid] >= target
                // mid를 포함.
                en = mid;
            }
        }

        return st;
    }

    // target보다 큰 최소 인덱스
    // 조건을 만족하는 최대 인덱스 다음 위치
    int getUpperBound(int target) {
        int st = 0;
        int en = n;

        while(st < en) {
            int mid = st + (en - st) / 2;

            if(card[mid] <= target) {
                st = mid + 1;
            }
            else {
                en = mid;
            }
        }

        return st;
    }

    void getInput() throws IOException {
        n = Integer.parseInt(br.readLine());

        card = Arrays.stream(br.readLine().split(" "))
                .mapToInt(Integer::parseInt)
                .toArray();

        Arrays.sort(card);

        m = Integer.parseInt(br.readLine());

        question = Arrays.stream(br.readLine().split(" "))
                .mapToInt(Integer::parseInt)
                .toArray();
    }
}
```

## 백준 13397 - 구간 나누기 2

이번에는 조금 더 어려운 응용 문제를 살펴보겠습니다.

[백준 13397 - 구간 나누기 2](https://www.acmicpc.net/problem/13397)

### 풀이 방법 도출

문제 설명을 읽어보면 상당히 난해합니다. 문제를 다시 해석해보면,

- N개의 수들을 M개 이하의 구간으로 나눈다.
- 이 때 각 구간의 최댓값은 구간에 속한 수의 최댓값과 최솟값의 차이이다.
- 구간의 점수는 각 구간의 최댓값이다.
- 구간의 점수의 최댓값의 최솟값을 구한다.

> 그러니까, 구간을 나눌 것이고 나눈 구간에는 최댓값이 존재합니다. 그게 구간의 점수가 됩니다. 구간이 여러 개이니 구간 점수도 구간 개수만큼 나오겠죠. 이 구간 점수들의 최댓값이 기준이 되는 값입니다. 이 값이 최소가 되도록 구간을 나누는 문제입니다.

문제 설명을 읽어봤을 때 마땅한 풀이법이 쉽게 떠오르지 않습니다. 또한 Brute-Force로 풀이할 경우 시간복잡도가 O(NM)이 돼서 시간초과가 날 것을 예상할 수 있습니다. 시간복잡도를 조금만 개선하면 될 것 같은데, 마땅한 풀이법이 쉽게 떠오르지 않습니다. 어떻게 풀이해야 할까요?

이 문제는 결국 조건을 만족하는 최솟값을 구하는 문제입니다. 이 때문에 매개 변수 탐색 알고리즘을 적용해 볼 수 있습니다.

1. 특정 조건을 만족하는 최솟값을 구하는 문제이므로 매개 변수 탐색을 적용해본다.
2. 구간의 점수의 최댓값의 최솟값을 x라고 할 때 이 x를 이진 탐색 형식으로 구한다.
3. x를 넘어설 경우 집합을 분할하고 count값을 증가시키는 형태로 x가 정해졌을 때 구간이 총 m개가 도출되는지, 또한 그 m개가 도출되게 하는 x값 중 최소를 탐색하도록 getLowerBound함수를 사용한다.

### 문제 풀이

찾아야 하는 최솟값을 이진 탐색 대상으로 두고, 조건에 맞는 최솟값을 구하기 위해 getLowerBound를 사용하는 형식입니다.

```java
import java.io.*;
import java.util.*;

public class Main {
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));

    int n, m;
    int[] arr;

    public static void main(String[] args) throws IOException {
        new Main().solve();

        br.close();
        bw.close();
    }

    void solve() throws IOException {
        getInput();

        bw.write(getLowerBound() + "");
    }

    int getLowerBound() {
        int st = 0;
        int en = 10000;

        while(st < en) {
            int mid = st + (en - st) / 2;

            if(m < getSectionCount(mid)) {
                st = mid + 1;
            }
            else{
                en = mid;
            }
        }

        return st;
    }

    int getSectionCount(int sectionMaxScore) {
        int count = 1;
        int sectionMax = 0;
        int sectionMin = Integer.MAX_VALUE;
        for(int num : arr) {
            if (num > sectionMax) {
                sectionMax = num;
            }
            if (num < sectionMin) {
                sectionMin = num;
            }

            if (sectionMax - sectionMin > sectionMaxScore) {
                count += 1;
                sectionMin = num;
                sectionMax = num;
            }
        }

        return count;
    }

    void getInput() throws IOException {
        StringTokenizer st = new StringTokenizer(br.readLine());
        n = Integer.parseInt(st.nextToken());
        m = Integer.parseInt(st.nextToken());

        arr = Arrays.stream(br.readLine().split(" "))
                .mapToInt(Integer::parseInt)
                .toArray();
    }
}
```

## 백준 17179 - 케이크 자르기

이번에는 getUpperBound를 사용해서 해결할 수 있는 문제입니다. 앞의 문제와 접근 방식은 비슷합니다.

[백준 17179 - 케이크 자르기](https://www.acmicpc.net/problem/17179)

### 풀이 방법 도출

케잌을 최대한 균등하게 잘라서, 가장 작은 조각의 길이가 가능한 한 최대가 되도록 해 보자! 정도로 이해할 수 있습니다.

1. Brute-Force로 접근할 경우 시간복잡도가 O(NML)이 되는데, 입력 값의 범위를 살펴봤을 때 O(NML)으로는 1초 내에 문제를 풀 수 없다.
2. 특정 조건 하에서 최댓값을 구하는 형식의 문제이므로 BinarySearch를 응용해서 매개 변수 탐색으로 접근해본다.
3. 역으로 가장 작은 조각의 길이를 이진 탐색 대상으로 생각한다.
4. x가 가장 작은 조각의 길이일 때, 이 때의 케이크를 잘라야 하는 횟수를 구하고, 이 횟수가 문제에서 주어진 값과 동일하면서 x가 최대가 되는 값을 찾아나간다.

### 문제 풀이

- 먼저, 유의해야 하는 사항은 getUpperBound는 조건을 만족하지 못 하게 되는 상한을 넘어서는 최초 값입니다. 그래서 항상 getUpperBound를 사용할 때는 반환값에 -1을 해야 최댓값을 구할 수 있습니다.

- 또한 getCutCount 함수에서 positions[i] - present >= unit && l - positions[i] >= unit 이 부분이 중요합니다. 
    - 이전에 자른 위치와 현재 자를 수 있는 위치 사이의 거리가 x값 이상이라고 무조건 자르면 틀립니다. 
    - 잘랐을 때 남은 길이(l - positions[i])도 x보다 커야 자를 수 있습니다.
```java
import java.io.*;
import java.util.*;

public class Main {
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));

    int n, m, l;
    int[] positions;
    int[] order;

    public static void main(String[] args) throws IOException {
        new Main().solve();

        br.close();
        bw.close();
    }

    void solve() throws IOException {
        getInput();

        for(int o : order) {
            bw.write(getUpperBound(o) - 1 + "\n");
        }
    }

    int getUpperBound(int target) {
        int st = 1;
        int en = Integer.MAX_VALUE;

        while(st < en) {
            int mid = st + (en - st) / 2;

            if (target <= getCutCount(mid)) {
                st = mid + 1;
            }
            else {
                en = mid;
            }
        }

        return st;
    }

    int getCutCount(int unit) {
        int present = 0;
        int count = 0;

        for(int i = 0; i < m; i++) {
            if(positions[i] - present >= unit && l - positions[i] >= unit) {
                present = positions[i];
                count += 1;
            }
        }

        return count;
    }

    void getInput() throws IOException {
        StringTokenizer st = new StringTokenizer(br.readLine());

        n = Integer.parseInt(st.nextToken());
        m = Integer.parseInt(st.nextToken());
        l = Integer.parseInt(st.nextToken());

        positions = new int[m];
        for(int i = 0; i < m; i++) {
            positions[i] = Integer.parseInt(br.readLine());
        }

        order = new int[n];
        for(int i = 0; i < n; i++) {
            order[i] = Integer.parseInt(br.readLine());
        }
    }
}
```

## 요약

> 이번 시간에는 이진 탐색과 이를 응용한 매개 변수 탐색에 대해 알아봤습니다.

쉽다면 쉬운 알고리즘이지만, 매개 변수 탐색 유형의 문제는 학습이 많이 필요합니다. 많은 경우에 최적화를 위해 도입할 수 있는 알고리즘이기에, getUpperBound, getLowerBound를 활용한 풀이에 익숙해지시면 많은 도움이 될 것이라고 생각합니다.