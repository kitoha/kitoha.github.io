---
layout: post
title: "알고스팟 졸업학기"
date: 2019-05-30 18:49
tags: [algorithm, algospot]
---

**문제 :** https://algospot.com/judge/problem/read/GRADUATION

**풀이 :**  

1. 문제의 입력을 받을 때 해당 과목이 요구하는 선행 과목을 비트마스크를 이용하여 정수형으로 저장해둔다.

2. 각 학기마다 개설된 과목의 번호는 중복이 이루어질 수 도 있으므로 중복을 제거해준다.

3. 동적계획법을 통해 [학기수][들은과목]으로 2차원 배열을 할당하고 최소 값을 찾는다.

동적 계획법을 할 때 선수과목을 모두 들었고 전에는 듣지 않은 과목들을 비트마스크 형식의 저장하고 해당 과목들의 부분집합을 구한다.

부분집합을 구하는 방식 역시 비트마스크를 이용하여 아래와 같은 방식으로 구하면된다.

```c++
for(int i=next;i>0;i=((i-1)&next)){
        int bitcnt=bitCount(i);
        if(bitcnt>l) continue;
        ret=min(ret,solve(pos+1,val|i,cnt+bitcnt)+1);
    }
```

해당 방식으로 부분집합을 구할 때 공집합 즉 학기를 휴학 했을 때는 따로 재귀를 호출해주어야 한다. 그리고 과목으 k개 이상 들었을 때 재귀를 종료해주면 된다.



소스코드 :

```c++
#include<iostream>
#include<cstdio>
#include<vector>
#include<cstring>
#include<algorithm>

using namespace std;

typedef pair<int,int> pi;
const int INF=1e7;
const int R=1<<13;
const int M=13;
int n,k,m,l,test;
int num[M],dp[M][R];
vector<int> adj[M];

bool cmp(pi a,pi b){
    return a.second>b.second;
}

int bitCount(int x){
    if(x==0) return 0;
    return x%2+bitCount(x/2);

}

int solve(int pos,int val,int cnt){

    if(pos>=m){
        if(cnt>=k) return 0;
        else return INF;
    }

    int &ret=dp[pos][val];

    if(ret!=-1) return ret;

    ret=INF;
    int idx=0;
    int next=0;

    for(int i=0;i<adj[pos].size();i++){
        int cur=adj[pos][i];
        if(val&(1<<cur)) continue;
        if((num[cur]&val)!=num[cur]) continue;
        next=next|(1<<cur);
    }

    for(int i=next;i>0;i=((i-1)&next)){
        int bitcnt=bitCount(i);
        if(bitcnt>l) continue;
        ret=min(ret,solve(pos+1,val|i,cnt+bitcnt)+1);
    }

    ret=min(ret,solve(pos+1,val,cnt));

    return ret;
}


int main(){

    scanf("%d",&test);

    for(int tc=1;tc<=test;tc++){

        scanf("%d%d%d%d",&n,&k,&m,&l);

        memset(dp,-1,sizeof(dp));
        memset(num,0,sizeof(num));
        memset(pre,0,sizeof(pre));

        for(int i=0;i<n;i++){
            int r;
            int sum=0;
            scanf("%d",&r);

            for(int j=0;j<r;j++){
                int val;
                scanf("%d",&val);

                sum=sum|(1<<val);
            }
            num[i]=sum;
        }

        for(int i=0;i<m;i++){
            int r;

            scanf("%d",&r);

            for(int j=0;j<r;j++){
                int val;
                scanf("%d",&val);
                adj[i].push_back(val);
            }

            sort(adj[i].begin(),adj[i].end());
            adj[i].erase(unique(adj[i].begin(),adj[i].end()),adj[i].end());
        }

        int ans=solve(0,0,0);

        if(ans!=INF) printf("%d\n",ans);
        else printf("IMPOSSIBLE\n");

        for(int i=0;i<m;i++){
            if(!adj[i].empty()){
                adj[i].clear();
            }
        }

    }

    return 0;
}
```
