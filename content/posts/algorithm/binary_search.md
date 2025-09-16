---
title: "Algorithm - Binary Search - 1편"
author: "Sin SeHwan"
#authorAvatarPath: "/avatar.jpeg"
date: "2025-07-27"
summary: "Binary Search (이진 탐색)[1] - Java"
description: "Binary Search (이진 탐색)[1] - Java"
toc: true
readTime: true
autonumber: true
math: true
tags: ["algorithm", "java"]
showTags: true
hideBackToTop: false
fediverse: "@username@instance.url"
---

# Binary Search (이진 탐색) - 1편

> 이번 시간에는 'Binary Search' 알고리즘을 살펴보겠습니다. 구간을 이분법적으로 쪼개면서 값을 탐색하는 알고리즘입니다. 알고리즘 교과목의 맨 처음 등장하는 주제이지만, 조금의 응용이 들어가면 상당히 어려워지는 주제이기도 합니다. 또한 적용할 수 있는 경우가 상당히 다양하기에, 꼭 알아둬야 하는 알고리즘이기도 합니다. 개념을 훑어보고, 문제를 살펴보면서 이해해봅시다.

## 어떤 문제를 해결할 수 있을까요?

### 사전 찾기

> 종이로 된 사전에서 직접 단어를 찾아보신 경험이 있으신가요? 

그 때 어떻게 찾으셨는지 기억하시나요? 제가 알 수는 없지만 적어도 사전 첫 페이지부터 끝까지 한 장 한 장 넘겨가면서 찾지는 않으셨을 겁니다. 

예를 들어 '루비'라는 단어를 찾는 상황을 가정해봅시다. 아무 페이지를 펼쳤을 때 만약 '인형'이라는 단어가 보인다면, 당연히 뒷 페이지는 볼 필요 없이 더 앞 페이지를 살펴보면 되겠죠? 

그 다음에 열어본 페이지에서 '노루'가 나왔다면 마찬가지로 그 페이지의 뒷 페이지 범위만 살펴보면 됩니다. '노루'와 '인형' 사이에 있는 페이지 범위 내에서만 탐색하면 되는 것이죠.

이러한 과정을 무의식적으로 여러 번 수행하면서 단어를 찾으셨을 겁니다. 상당히 직관적이죠.

이 과정을 요약하면 다음과 같습니다.
```
1. 원하는 값보다 순서가 빠른 요소를 찾으면, 그 뒷 범위를 탐색한다.

2. 원하는 값보다 순서가 나중인 요소를 찾으면, 그 앞 범위를 탐색한다.
```

이 과정을 일반화해서 원하는 요소를 찾기 위한 방법론으로 이름지은 것이 바로 '이진 탐색'입니다.

### 이진 탐색의 시간복잡도

> 이진 탐색으로 해결할 수 있는 문제가 무엇인지 알기 위해서는 이진 탐색 과정의 시간복잡도를 알아야 합니다.

이진 탐색에서는 탐색 범위의 정확히 절반이 되는 지점을 확인하고, 그 지점이 원하는 값보다 작으면 더 큰 범위만, 원하는 값보다 크면 더 작은 범위만 탐색하면 됩니다. 따라서 탐색할때마다 탐색 범위가 절반으로 줄어듭니다.

결국 이진 탐색을 활용하면 *$O(n)$* 을 **$O(\log n)$** 으로 낮출 수 있습니다.

### 증명 과정

데이터의 총개수가 $n$개일 때, $k$번의 탐색 후에 남는 데이터의 수는 $1$개가 된다고 가정하면 다음과 같은 식이 성립합니다.

$$\frac{n}{2^k} = 1$$

이 식을 $k$에 대해 풀면, 이진 탐색에 필요한 탐색 횟수를 알 수 있습니다.

$$n = 2^k$$

양변에 밑이 2인 로그를 취하면 다음과 같습니다.

$$\log_2 n = \log_2 2^k$$

따라서, 필요한 탐색 횟수 $k$는 다음과 같습니다.

$$k = \log_2 n$$

즉, 이진 탐색의 시간 복잡도는 **$O(\log n)$** 입니다.

## 이진 탐색 구현

구현 방식은 크게 재귀호출을 사용하는 형식과 while문을 사용하는 형식이 있습니다. 

```java
int binarySearch(int target) {
    int st = 0;
    int en = n - 1;

    while(st <= en) {
        int mid = st + (en - st) / 2;

        if(arr[mid] < target) {
            st = mid + 1;
        }
        else if(arr[mid] > target) {
            en = mid - 1;
        }
        else{
            return mid;
        }
    }
    return -1;
}
```

재귀 호출 방식으로 구현할 때는 while문 내부 if문의 각 분기에서 재귀 호출을 수행하면 됩니다.

## 백준 1920 - 수 찾기
- Binary Search 대표 문제를 풀어봅시다!

[백준 1920 - 수 찾기](https://www.acmicpc.net/problem/1920)

### 팁 : mid 값 설정 방법
```java
mid = (st + en) / 2;
mid = st + (en - st) / 2;
```
크게 두 가지 방식이 있는데, 두 번째 방식이 overflow에 더 안전합니다. 만약 en을 Integer.MAX_VALUE로 설정해야 하는 상황에 첫 번째 방식을 적용하면 overflow가 발생할 수 있습니다. 

### 풀이

전형적인 이진 탐색 문제입니다. 수가 존재하는지 탐색하고, 존재한다면 1을, 존재하지 않으면 0을 출력하면 됩니다.


```java
import java.io.*;
import java.util.*;

public class Main {
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));

    int n, m;
    int[] arr;
    int[] questions;

    public static void main(String[] args) throws IOException {
        new Main().solve();

        br.close();
        bw.close();
    }

    void solve() throws IOException {
        getInput();

        for(int i = 0; i < m; i++) {
            int result = binarySearch(questions[i]);

            if(result != -1) {
                bw.write("1\n");
            }
            else{
                bw.write("0\n");
            }
        }
    }

    void getInput() throws IOException {
        n = Integer.parseInt(br.readLine());
        arr = Arrays.stream(br.readLine().split(" "))
                .mapToInt(Integer::parseInt)
                .toArray();

        Arrays.sort(arr);

        m = Integer.parseInt(br.readLine());
        questions = Arrays.stream(br.readLine().split(" "))
                .mapToInt(Integer::parseInt)
                .toArray();
    }

    int binarySearch(int target) {
        int st = 0;
        int en = n - 1;

        while(st <= en) {
            int mid = st + (en - st) / 2;

            if(arr[mid] < target) {
                st = mid + 1;
            }
            else if(arr[mid] > target) {
                en = mid - 1;
            }
            else{
                return mid;
            }
        }
        return -1;

    }
}
```

## Parametric Search (매개 변수 탐색)

> Binary Search 문제의 응용 문제입니다.

### 개념 이해

매개 변수 탐색은 이진 탐색(Binary Search) 기법을 최적화 문제에 응용한 알고리즘입니다.

알고리즘 문제를 풀다 보면 이런 애매한 경우가 있습니다. 예를 들면, 아무리 봐도 적절한 알고리즘이 없는 것 같은데, Brute-Force로 구현하면 시간초과가 나오는 경우입니다.

여러 가지 경우가 있겠지만, 문제 내용이 조건을 만족하는 최적의 값(최솟값, 최댓값)찾기이면 이진 탐색을 응용한 매개 변수 탐색을 적용해 볼 수 있습니다.

예를 들어 원래 시간복잡도가 **$O(n^2)$** 일 때 이진 탐색을 적용하면 **$O(n\log n)$** 으로 개선 할 수 있겠죠.

즉 이진 탐색은 시간복잡도를 줄이기 위한 매우 좋은 도구로 볼 수 있습니다.

### 이진 탐색의 문제점

이진 탐색 알고리즘으로 최적화를 수행해 보자는 생각은 좋지만 하나의 문제점이 있습니다. 중복되는 원소가 존재하거나, 조건을 만족하는 값이 여러 개일 때 이진 탐색 알고리즘의 결과 예측이 쉽지 않다는 것입니다.

예를 들어서, 다음과 같은 상황에서 이진 탐색을 수행하면 어떻게 될까요?

```java
int[] arr = {1, 3, 3, 3, 4, 5}

System.out.println(binarySearch(3));
```
arr[1], arr[2], arr[3] 모두 3이기 때문에 이들 중 먼저 탐색되는 대상의 인덱스가 반환될 것입니다. 이진 탐색 특성상 배열의 길이에 따라 원소 탐색 순서가 달라지는데, 결국 결과가 배열의 길이에 따라 달라집니다. 이는 우리가 원하지 않는 의존성이죠.

그리고 이진 탐색 알고리즘을 수정할 때 종료 조건이 어긋나면 무한루프가 발생하기 쉽기 때문에 수정하기도 쉽지 않습니다.

이러한 문제점을 개선하기 위해, 매개 변수 탐색 알고리즘에서는 이진 탐색을 응용한 getLowerBound, getUpperBound를 사용합니다.

### getLowerBound, getUpperBound

> 조건을 만족하는 범위를 계산할 때 사용 가능한 방법입니다.

#### getLowerBound
> getLowerBound는 조건을 만족하는 최소 경계값을 반환하는 함수입니다.

- 조건 : 3보다 크거나 같은 최소 인덱스 찾기

```java
int[] arr = {1, 3, 3, 3, 4, 5}

System.out.println(getLowerBound(3));
// 결과는 1입니다.
```
- 조금 더 직관적으로 이해해보자면, arr의 정렬 순서를 깨뜨리지 않고 새로운 3을 삽입할 수 있는 최소 위치(인덱스)로 생각하면 이해하기 쉽습니다.

> getLowerBound 구현
```java
int getLowerBound(int target) {
    int st = 0;
    int en = n;

    while(st < en) {
        int mid = st + (en - st) / 2;

        if(arr[mid] < target) {
            st = mid + 1;
        }
        else {
            // arr[mid] >= target
            // mid를 포함.
            en = mid;
        }
    }
}
```

위의 binarySearch 코드와 다른 점을 위주로 살펴보면 getUpperBound의 동작을 이해하기 쉽습니다.

먼저 while 조건식을 ```st < en```으로 수정해서 while문 탈출 시 ```st == en```이 되도록 구성합니다.

그 다음으로, 우리가 기존 binarySearch에서 살펴봤던 조건은 크게 3가지입니다.

```java
arr[mid] < target // if
arr[mid] == target // else
arr[mid] > target // else
```
getUpperBound는 이 중 ```arr[mid] == target```, ```arr[mid] > target``` 두 가지를 구분하지 않고 else 분기에 넣은 형태입니다.

즉, ```arr[mid]```가 ```target```보다 작은 경우, mid보다 작거나 같은 범위를 잘라내고, 그렇지 않다면 mid보다 큰 범위를 잘라내는 방식입니다.

이렇게 진행하면 target보다 작은 범위는 모두 배제되고, 크거나 같은 값 중 최소값을 탐색하게 됩니다. 이 값이 조건을 만족하는 최소값이죠.

다음과 같은 상황에서는 어떤 값이 출력될까요?

- 조건 : 100보다 크거나 같은 최소 인덱스 찾기
```java
int[] arr = {1, 3, 3, 3, 4, 5}

System.out.println(getLowerBound(100));
// 결과는 6입니다.
```
arr 배열에 조건을 만족하는 값이 없으므로 배열의 마지막 인덱스의 다음 인덱스인 6을 가리킵니다. 이 경우 조건을 만족하는 값이 없다고 판단할 수 있겠죠.

리턴 값의 경우 while 루프 종료 시 ```st == en```이므로 둘 중 아무 값을 반환하면 됩니다.

#### getUpperBound

> getUpperBound는 조건을 만족하는 최대 경계값 다음 요소를 반환하는 함수입니다.

- 조건 : 3보다 큰 최소 인덱스 찾기
```java
int[] arr = {1, 3, 3, 3, 4, 5}

System.out.println(getUpperBound(3));
// 결과는 4입니다.
```
- arr의 정렬 순서를 깨뜨리지 않고 새로운 3을 삽입할 수 있는 최대 위치(인덱스)로 생각해도 됩니다.

> getUpperBound 구현

```java
int getUpperBound(int target) {
    int st = 0;
    int en = n;

    while(st < en) {
        int mid = st + (en - st) / 2;

        if(arr[mid] <= target) {
            st = mid + 1;
        }
        else {
            en = mid;
        }
    }

    return st;
}
```
놀랍게도 getLowerBound에서 단 한 글자만 추가하면 됩니다. ```arr[mid] <= target``` 이 부분이 다른데, 등호 하나가 추가된 것 말고는 구성이 같습니다.

```java
arr[mid] < target // if
arr[mid] == target // if
arr[mid] > target // else
```
즉 getUpperBound에서는 ```arr[mid] < target```, ```arr[mid] == target``` 조건을 if 분기에 포함한 형태입니다. 

그러니 if에 해당하는 조건을 만족하지 못하는 하한과 조건을 만족하는 경우는 탐색 범위에서 배제되고, 결국 조건을 만족하기 위한 상한 경계값 다음 첫 번째 요소가 결과로 반환되는 것이죠.

- 조건에 만족하는 값이 없는 경우
```java
int[] arr = {1, 3, 3, 3, 4, 5}

System.out.println(getUpperBound(100));
// 결과는 6입니다.
```
getLowerBound와 마찬가지로 배열의 마지막 인덱스의 다음 인덱스를 반환합니다.


#### 중복 요소 개수 구하기

여기까지 보셨다면 아마 의문이 드실 겁니다. 뭐 하한 경계, 상한 경계를 구하는 것 까지는 알겠는데 굳이 이렇게까지 해야 하나? 이게 중복 요소 체크랑 무슨 상관이 있나 싶죠.

그런데 놀랍게도 getUpperBound와 getLowerBound의 결과를 다시 보면 큰 힌트를 하나 얻을 수 있습니다.

```
(상한 경계 다음 인덱스) - (하한 경계) == (조건을 만족하는 값의 개수)
```
```java
int[] arr = {1, 3, 3, 3, 4, 5}

System.out.println(getLowerBound(3));
// 결과는 1입니다.

System.out.println(getUpperBound(3));
// 결과는 4입니다.

System.out.println(getUpperBound(3) - getLowerBound(3))
// 3 -> 유효한 인덱스 개수는 3개 입니다.

System.out.println(getUpperBound(100) - getLowerBound(100))
// 0 -> 조건을 만족하는 값이 없습니다.
```

즉, getLowerBound, getUpperBound를 사용하면 조건을 만족하는 원소 개수, 상한 경계, 하한 경계 모두 구할 수 있습니다.

상당히 유용하게 사용할 수 있겠다는 생각이 들지 않나요? 다만, 내용이 길어져서, 예제 문제 풀이는 다음 페이지에서 진행하겠습니다.