题干如下：




![](https://github.com/lukealpha/phototable_for_luogu_Blog/blob/master/pimg1082_1.png?raw=true)
![](https://github.com/lukealpha/phototable_for_luogu_Blog/blob/master/pimg1082_2.png?raw=true)
#### \#1-朴素BFS
先使用最朴素的BFS来实现，会占用大量内存，同时会超时。

实现方法很简单，将每一个棋盘作为一种状态，进行广度优先搜索。
唯一需要解决的问题就是有关于状态的存储，如何存储呢？

首先使用一个结构体Node来存储。x,y 表示目前空格所在位置，而step表示已经移动过的次数(即状态树中已经走过的步数)
graph\[i]\[j] 表示当前状态棋盘第i行第j列的棋子数字
```cpp
struct Node{
    int x,y;
    int graph[4][4];
    int step;
    
};
```
用一个map来记录当前状态是否已经走过
```cpp
map<string,bool> used;
```
将当前状态转换成字符串形式表示 然后直接访问查重即可
例如：
```cpp
used["124357680"] == true;
```
即表示:
```
1  2  4
3  5  7
6  8  0
```
这种状态已经被访问过。
如何获得这个字符串呢 也很简单实用函数make\_route()即可
```cpp
//make_route()
string make_route(Node x){
    string returnValue;
    for(int i = 1; i <= 3; i ++){
        for (int j = 1; j <= 3; j ++) {
            returnValue += x.graph[i][j] + '0';
        }
    }
    return returnValue;
}
```

在使用的时候直接通过此函数获得查重字符串
```cpp
if(!used[make_route(x)]){
	statement...
}
```
x为Node类型(上文已定义)

Bfs部分就比较简单了，直接抽象成枚举空格四个方向移动，然后将空格分别与目标位置的棋子交换即可
```cpp
struct Node{
    int x,y;
    int graph[4][4];
    int step;
    
};
```

返回最小值

#### \#2-进一步优化
这一步优化没有显著的效率提升，但是为后文的A\*优化做好准备。
使用Dijkstra算法。

Dijkstra算法与BFS算法相似，但是Dijkstra加入了堆优化(使用优先队列)

每次不直接取出队头元素，而是取出step最小的那一个，然后继续用它来更新到其他的点的最短路径的值。这里直接使用Dijkstra没有造成任何优化，但Dijkstra本质上是解决路径权值不同的最短路的问题。

因为如果路径权值不同，BFS就失效了，但是每一次从目前堆中取最小的一个点，这样最先到达这个点的路径依然是最短的。因此Dijkstra也可以用来解决单源最短路问题，第一次到达时进行更新，而后面几次到达这个点时就不再更新，跑完整个图，所有的dis\[x]就被求出来了。

在朴素代码上简单修改 换用优先队列即可。同时为自定义的结构体重载运算符
```cpp
#include <iostream>
#include <algorithm>
#include <stdio.h>
#include <queue>
#include <map>
#include <string.h>
using namespace std;
map<string,int> used;
struct Node{
    int x,y;
    int graph[4][4];
    int step;
    
};
bool operator< (Node a, Node b){
    return a.step > b.step;
}
string make_route(Node x){
    string returnValue;
    for(int i = 1; i <= 3; i ++){
        for (int j = 1; j <= 3; j ++) {
            returnValue += x.graph[i][j] + '0';
        }
    }
    //cout<<returnValue<<endl;;
    return returnValue;
}
Node input;
int togo[4][2] = {{0,1},{0,-1},{-1,0},{1,0}};
priority_queue<Node> statusTree;
int bfs(){
    statusTree.push(input);
    while(!statusTree.empty()){
        Node now = statusTree.top();
        if(make_route(now) == "123456780"){
            return now.step;
        }
        statusTree.pop();
        int nx,ny;
        for(int i = 0; i <= 3; i ++){
            nx = now.x + togo[i][0];
            ny = now.y + togo[i][1];
            if(nx >= 1 && ny >= 1 && nx <= 3 && ny <= 3){
                Node next = now;
                next.x = nx;
                next.y = ny;
                next.step ++;
                //swap(next.graph[now.x][now.y],next.graph[next.x][next.y]);
                next.graph[now.x][now.y] = next.graph[next.x][next.y];
                next.graph[next.x][next.y] = 0;
                if(!used[make_route(next)]){
                    statusTree.push(next);
                    used[make_route(next)] = true;
                }
            }
        }
    }
    return -1;
}
int main(){
    input.step = 0;
    for(int i = 1; i <= 3; i ++){
        for(int j = 1; j <= 3; j ++){
            scanf("%d",&input.graph[i][j]);
            if(!input.graph[i][j]){
                input.x = i;
                input.y = j;
            }
        }
    }
    printf("%d",bfs());
    return 0;
}
```
注意重载运算符不要弄反了。。。
####\#3 - 进一步优化 - 使用A\*估值函数
A\*算法为是启发式函数f(n);
```
f(n) = g(n) + h(n);
```
已经消耗的实际代价g(n), 对从中间节点到目标的预测h(n)

一定要确h(n)小于最终代价，不然选择的最先走到的路径就可能不是最短路了，想一想为什么？

因此在结构体中添加value,表示A\*的计算结果
同时修改重载运算符根据value做小根堆
```cpp
struct Node{
    int x,y,value;
    int graph[4][4];
    int step;
    
};
bool operator< (Node a, Node b){
    return a.value > b.value;
}
```
要注意在任何地方初始化一个新的Node型值时，都要对value进行维护。
简单修改主题程序如下，然后获得100分做法:
```cpp
#include <iostream>
#include <algorithm>
#include <stdio.h>
#include <queue>
#include <map>
#include <math.h>
#include <string.h>
using namespace std;
map<string,int> used;
struct Node{
    int x,y,value;
    int graph[4][4];
    int step;
    
};
bool operator< (Node a, Node b){
    return a.value > b.value;
}
string make_route(Node x){
    string returnValue;
    for(int i = 1; i <= 3; i ++){
        for (int j = 1; j <= 3; j ++) {
            returnValue += x.graph[i][j] + '0';
        }
    }
    //cout<<returnValue<<endl;;
    return returnValue;
}
int H(Node x){
    int ans = 0;
    int tx[10]={3,1,1,1,2,2,2,3,3};
    int ty[10]={3,1,2,3,1,2,3,1,2};
    for(int i = 1; i <= 3; i ++){
        for(int j = 1;j <= 3; j ++){
            ans += abs(i-tx[x.graph[i][j]]);
            ans += abs(j-ty[x.graph[i][j]]);
        }
    }
    return ans;
}
Node input;
int togo[4][2] = {{0,1},{0,-1},{-1,0},{1,0}};
priority_queue<Node> statusTree;
int bfs(){
    statusTree.push(input);
    while(!statusTree.empty()){
        Node now = statusTree.top();
        if(make_route(now) == "123456780"){
            return now.step;
        }
        statusTree.pop();
        int nx,ny;
        for(int i = 0; i <= 3; i ++){
            nx = now.x + togo[i][0];
            ny = now.y + togo[i][1];
            if(nx >= 1 && ny >= 1 && nx <= 3 && ny <= 3){
                Node next = now;
                next.x = nx;
                next.y = ny;
                next.step ++;
                //swap(next.graph[now.x][now.y],next.graph[next.x][next.y]);
                next.graph[now.x][now.y] = next.graph[next.x][next.y];
                next.graph[next.x][next.y] = 0;
                next.value = H(next)+next.step;
                if(!used[make_route(next)]){
                    statusTree.push(next);
                    used[make_route(next)] = true;
                }
            }
        }
    }
    return -1;
}
int main(){
    input.step = 0;
    for(int i = 1; i <= 3; i ++){
        for(int j = 1; j <= 3; j ++){
            scanf("%d",&input.graph[i][j]);
            if(!input.graph[i][j]){
                input.x = i;
                input.y = j;
            }
        }
    }
    input.value = input.step + H(input);
    printf("%d",bfs());
    return 0;
}
```
全文终

备用图:





![](https://github.com/lukealpha/phototable_for_luogu_Blog/blob/master/20190531062206_70414.png?raw=true)
![](https://github.com/lukealpha/phototable_for_luogu_Blog/blob/master/2.png?raw=true)

[Uses Github for Phototables](https://github.com/lukealpha/phototable_for_luogu_Blog)

