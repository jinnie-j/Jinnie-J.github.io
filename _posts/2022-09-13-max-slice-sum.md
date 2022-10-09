---
published: true   
title:  "최대 연속 부분배열 알고리즘"
excerpt: "[자료구조] 최대 연속 부분배열 합을 구하는 4가지 알고리즘"

categories:
- Algorithm

tags:
- [Algorithm]

toc: true
toc_sticky: true

date: 2022-09-13
last_modified_at: 2022-09-13
---

### 최대 부분배열의 합 - 4가지 알고리즘
비어 있지 않은 숫자 배열에서 합이 최대가 되는 연속된 부분 배열 구간의 합을 구하는 방법 4가지가 있다.
- 완전탐섹(브루트포스 알고리즘)
- 중복을 제거한 탐색
- 분할정복(Divide and Conquer)
- 동적 계획법(Dynamic Programming)

#### 완전탐색(브루트포스 알고리즘)
- 단순히 모든 부분배열을 비교하여 최대인 배열을 구하는 알고리즘이다.
- MaxSum은 최종 결과합, ThisSum은 각 부분배열의 합을 저장하는 변수이다.
- 삼중반복문을 이용하여 i(0,n)~j(i,n)까지의 부분배열을 구한다.
- 시간복잡도: O(n^3)
    ```java
    int MaxSubarray1(int A[], int N){
        int MaxSum, ThisSum;
        int i,j,k;
        MaxSum = 0;
        for(i=0;i<n;i++){
            for(j=1;j<n;j++){
                ThisSum=0;
                for(k=i;k<=j;k++){
                    ThisSum = ThisSum + A[k];
                }
                MaxSum = max(MaxSum, ThisSum);
            }
        }
        return MaxSum;
    }
    ```


#### 중복을 제거한 탐색
- 완전탐색에서 중복을 제거한 방법이다.
- 시간복잡도: O(n^2) 
    ```java
    int MaxSubarray2(int A[], int N){
        int MaxSum, ThisSum;
        int i,j;
        MaxSum = 0;
        for(i=0;i<n;i++){
            ThisSum = 0;
            for(j=i;j<n;j++){
                ThisSum = ThisSum + A[j];
                MaxSum = max(MaxSum, ThisSum);
            }
        }
        return MaxSum;
    }
    ```   
    
#### 분할정복법
 - 먼저 배열을 오른쪽 Part와 왼쪽 Part로 나눈다. 이를 배열L, 배열R 이라고 하자
 1. 배열 L에서 최대 부분 배열 합 찾기 (MaxLeft)
 2. 배열 R에서 최대 부분 배열 합 찾기 (MaxRight)
 3. 겹치는 부분에서 최대 부분 배열 합 찾기 (MaxCenter)
 - 위 과정을 진행한 뒤, 1,2,3 중 가장 큰 수를 찾으면 된다.
 - 겹치는 부분에서 최대 부분 배열 합을 찾기 위해서는, 오른쪽 최대 부분 배열 합에 왼쪽 최대 부분 배열 합을 합쳐서 겹친 부분 배열 합을 구하면 된다. 오른쪽 배열에서 구한 최대 합(MaxCenterL)과 왼쪽 부분 배열에서 구한 최대 합(MaxCenterR)을 더하면 겹친 부분 배열에서 구한 최대 합(MaxCenter)과 같기 때문이다. 
 - 시간 복잡도: O(nlong)
    ```java
    int MaxSubarray3(int A[], int Left, int Right)
    {
        int MaxSum, MaxLeft, MaxRight, MaxCenter;
        int MaxCenterL, MaxCenterR, ThisSum;
        int Center, i;
        if (Left == Right) {
            if (A[Left] > 0) return A[Left];
            else return 0;
        }
        Center = (Left + Right) / 2;
        MaxLeft = MaxSubarray3(A, Left, Center);
        MaxRight = MaxSubarray3(A, Center + 1, Right);
        MaxCenterL = 0;
        ThisSum = 0;
        for (i = Center; i >= Left; i--) {
            ThisSum = ThisSum + A[i];
            MaxCenterL = max(MaxCenterL, ThisSum);
        }
        MaxCenterR = 0;
        ThisSum = 0;
        for (i = Center + 1; i <= Right; i++) {
            ThisSum = ThisSum + A[i];
            MaxCenterR = max(MaxCenterR, ThisSum);
        }
        MaxCenter = MaxCenterL + MaxCenterR;
        MaxSum = max(max(MaxLeft, MaxRight), MaxCenter);
        return MaxSum;
    }
    ```

#### 동적 계획법
- 부분 배열의 오른쪽 끝이 A[k]인 최대 부분 배열의 원소 합을 S[k]라고 하자. 그러면 최대부분배열 원소합은 S[0],S[1], ..., S[n-1]중 하나일 것이다.
- S[k] = max{S[k-1]+A[k],0}
- 길이가 0 인 경우, S[k]=0 
- 길이가 1인 경우, S[k]에서 원소 A[k](오른쪽 끝에 있는 원소)를 제거하면 오른쪽 끝이 A[k-1]인 최대 부분배열이 된다.
- 시간복잡도: O(n)
    ```java
    int MaxSubarray4(int A[], int N){
        int MaxSum, ThisSum;
        int k;
        MaxSum = 0;
        ThisSum = 0;
        for(k=0;k<n;k++){
            ThisSum = max(ThisSum + A[k], 0);
            MaxSum = max(MaxSum, ThisSum);
        }
        return MaxSum;
    }
    ```