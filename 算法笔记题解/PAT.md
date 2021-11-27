# PAT

## STL

### vector





## 数据结构专题(1)

### 栈应用

![image-20211113125223694](https://i.loli.net/2021/11/13/TPWiBj6Rf3FhHrp.png)

~~~C++
#include <iostream>
#include <stack>
using namespace std;

//判定条件有两个，一是一一比对之后元素都弹出去了，即非空，二是比对的都正确，即flag=true
const int maxn = 1010;
int arr[maxn]; //保存出栈序列
stack<int> st;

int main()
{
    int m, n, k;
    scanf("%d%d%d",&m,&n,&k);
    while (k--) //循环k次，用while代码清晰
    {
        while (!st.empty()) //清空栈，开始要考虑到上一次结束
            st.pop();
        for (int i = 1; i <= n; i++) //保存出栈序列
            scanf("%d", &arr[i]);

        int current = 1;    //初始指向arr的第一个元素
        bool flag = true;

        for (int i = 1; i <=n; i++)
        {
            st.push(i);
            if (st.size() > m) //若容量大于m，则序列不符合
            {
                flag = false;
                break;
            }
            //核心代码：栈顶元素与序列当前元素相同，弹栈
            while (!st.empty()&&st.top()==arr[current])    //这个位置一定记住要判空，这是top使用的时候要先判空，空栈不能用top
            {
                st.pop();   
                current++;  //往下匹配
            }
        }
        if (st.empty() && flag == true)
            printf("YES\n");
        else
            printf("NO\n");
    }
}

~~~



### 队列应用

![image-20211113125045613](https://i.loli.net/2021/11/13/mCco2AD5S6iITpy.png)

~~~C++
#include <iostream>
#include <queue>
using namespace std;
const int maxn = 1010;

//queue仅仅储存老鼠的位置，通过mou[queue]的方式确定老鼠
//每轮都pop出去并且赋值相同的rank=group+1 
//质量最大的push回去，在下一轮中通过push修改rank， 这个很重要
//队列的作用是保存他的序列并且每一次循环通过pop push重置新的group

struct mouse
{
    int weight;
    int r;
} mou[maxn];

int main()
{
    int np, ng, order;
    scanf("%d%d", &np, &ng);
    for (int i = 0; i < np; i++)
        scanf("%d", &mou[i].weight);
    queue<int> q;
    for (int i = 0; i < np; i++)
    {
        scanf("%d", &order);
        q.push(order); //把老鼠的标号入队
    }

    int temp = np, group; //temp为当前轮的比赛总老鼠数，group为组数
    while (q.size() != 1)	//front()和pop()前要判空
    {
        if (temp % ng == 0)
            group = temp / ng;
        else
            group = temp / ng + 1;
        //枚举每一组，选出质量最大的
        for (int i = 0; i < group; i++)
        {
            int k = q.front(); //k存放质量最大的老鼠的编号
            for (int j = 0; j < ng; j++)
            {
                //最后一组老鼠不足ng时，到达最后一个就退出循环
                if (i * ng + j >= temp)
                    break;
                int front = q.front();
                if (mou[front].weight > mou[k].weight)
                    k = front;
                mou[front].r = group + 1;
                q.pop();
            }
            q.push(k);
        }
        temp = group;
    }

    mou[q.front()].r = 1;
    for (int i = 0; i < np; i++)
    {
        printf("%d",mou[i].r);
        if(i<np-1)
            printf(" ");
    }
}

~~~



### 链表应用
#### 1_

![image-20211115102138835](https://i.loli.net/2021/11/15/ZA3Gxu6hLocjDSX.png)



~~~C++
#include <iostream>
#include <algorithm>
using namespace std;

//这是静态链表，因为地址都是int，所以开数组
//静态链表的操作其实就是对数组的操作，只不过数组的下表是离散的
//所以需要一个order以及cmp使之按照链接的方式有序
//这里的next实际上是没有做改变的，因为这是数组，并且只需要输出就行
const int maxn = 100010;
struct Node
{
    int address, data, next;
    int order; //  节点在链表中的序号，为了实现反转
} node[maxn];

bool cmp(Node a, Node b)
{
    return a.order < b.order; //按order从小到大排序
}

int main()
{
    for (int i = 0; i < maxn; i++)
        node[i].order = maxn;
    int begin, n, k, address;
    cin >> begin >> n >> k;
    for (int i = 0; i < n; i++)
    {
        cin >> address >> node[address].data >> node[address].next;
        node[address].address = address;
    }

    int p = begin, cnt = 0;
    while (p != -1) //从首地址开始给node数组赋order
    {
        node[p].order = cnt++;
        p = node[p].next;
    }

    sort(node, node + maxn, cmp); //这个时候看似是链表实则是个数组//排序排的是下标，下标本来表示address，只是为了连成链，之后就没用了，order储存顺序	

    //链表形成之后，对输出的处理
    for (int i = 0; i < cnt / k; i++) //对于每一块输出，一共有cnt/k个完整的块
    {
        for (int j = (i + 1) * k - 1; j > i * k; j--)                                     //这是每一块
            printf("%05d %d %05d\n", node[j].address, node[j].data, node[j - 1].address); //第i块倒序输出

        //下面是对每一块最后一个结点next的处理
        printf("%05d %d ", node[i * k].address, node[i * k].data);
        if (i < cnt / k - 1) //  如果不是最后一块，就指向下一块的最后一个节点
            printf("%05d\n", node[(i + 2) * k - 1].address);
        else //如果是最后一块
        {
            if (cnt % k == 0) //如果后面没有多余不完整的块
                printf("-1\n");
            else //如果还有不完整即不反转的块
            {
                printf("%05d\n", node[(i + 1) * k].address); //指向最后一个不完整块的第一个节点

                for (int j = cnt / k * k; j < cnt; j++) //剩下不完整的块原序输出
                {
                    
                    printf("%05d %d ", node[j].address, node[j].data);
                    if(j<cnt-1)
                        printf("%05d\n",node[j].next);
                    else
                        printf("-1\n");

                }
            }
        }
    }
}
~~~



#### 2_

![image-20211115111912500](https://i.loli.net/2021/11/15/8kntphMqurwBeSa.png)

![image-20211115111935599](https://i.loli.net/2021/11/15/LtmleFdHNCD7prA.png)



~~~C++
#include <iostream>
using namespace std;
const int maxn = 100010;

//实际上就是让你求第一个共用节点
//第一个链表节点flag全设为1
//遍历第二个，如果有flag==1就输出
//只需要一个数组，因为题中给的就是一个数组
//两个链表各自是这个数组中的一部分
//所以对数组的第一部分flag==true，再遍历第二部分就能出结果
struct Node
{
    char data;
    int next;
    bool flag;
} node[maxn];   


int main()
{
    for (int i = 0; i < maxn; i++)
        node[i].flag = false;
    int s1 ,s2 ,n;
    cin>>s1>>s2>>n;
    for(int i=0;i<n;i++)
    {
        int address;
        cin>>address;
        cin>>node[address].data;
        cin>>node[address].next; 
    }
    for(int p=s1;p!=-1;p=node[p].next)  //遍历第一部分并flag==true
        node[p].flag=true;
    for(int p=s2;p!=-1;p=node[p].next)
        if(node[p].flag==true)
        {
            printf("%05d",p);
            return 0;
        }

    cout<<"-1"<<endl;
    return 0;
}
~~~



#### 3_

![image-20211115180632909](https://i.loli.net/2021/11/15/6kDVaX51WqdUuGN.png)



~~~C++
#include <iostream>
#include <algorithm>
using namespace std;

const int maxn=100010;
struct Node
{
    int data, next, address;
    int flag;       //这里既然只是判断节点是否失效，用flag判断就行了，order可以判断有序
} node[maxn];


bool cmp(Node a, Node b)
{
    if (a.flag != b.flag)   //是否有效的判断
        return a.flag > b.flag;
    else    
        return a.data < b.data;
}
int main()
{
    for (int i = 0; i < maxn; ++i)
    {
        node[i].data = maxn;
        node[i].flag = false;
    }
    int n, address, begin;
    cin >> n >> begin;
    for (int i = 0; i < n; ++i) //这里赋值数组下标就是address，单纯的用数组来做会导致无法判断失效节点
    {
        cin >> address; 
        cin >> node[address].data >> node[address].next;
        node[address].address = address;
    }
    int p = begin, Count = 0;
    while (p != -1) //查找有效节点的方式
    {
        Count++;
        node[p].flag = true;
        p = node[p].next;
    }
    sort(node, node + maxn, cmp);
    if (Count == 0)
    {
        cout << "0 -1" << endl;
        return 0;
    }
    printf("%d %05d\n", Count, node[0].address);
    for (int i = 0; i < Count; ++i)
    {
        if (i != Count - 1)
            printf("%05d %d %05d\n", node[i].address, node[i].data, node[i + 1].address);
        else
            printf("%05d %d -1\n", node[i].address, node[i].data);
    }
}
~~~

#### 4_

![image-20211120143218984](https://i.loli.net/2021/11/20/k37CnXmF1Oc4HDV.png)



~~~~C++
#include <iostream>
#include <cmath>
#include <algorithm>
using namespace std;

const int maxn = 100010;
int hashTable[maxn] = {0};

struct Node
{
    int address, data, next;
} node[maxn], dnode[maxn];

int main()
{
    int n, begin;
    cin >> begin >> n;
    for (int i = 0; i < n; i++)
    {
        int address;
        cin >> address;
        cin >> node[address].data >> node[address].next;
        node[address].address = address;
    }

    int p = begin, numd = 0;
    while(p != -1)
    {
        if(hashTable[abs(node[p].data)]==0)     //如果这是正确的节点，那么直接输出就行了
        {
            hashTable[abs(node[p].data)] =1;
            if(p!=begin)
                printf("%05d\n", node[p].address);                 //思考三个变量怎么输出？
            printf("%05d %d ", node[p].address, node[p].data);   //address和data捆绑，就看next怎么输出，此时的address就是上一个的next
        }
        else    //用另外一个数组记录被删除的节点信息
        {
            dnode[numd].address = node[p].address;  
            dnode[numd].data = node[p].data;
            if(numd)
                dnode[numd-1].next=node[p].address;     //最后一个一定是-1，单独标出来就行了
            numd++;
        }
        p=node[p].next;

    }
    cout<<-1<<endl;
    for(int i=0;i<numd;i++)
    {
        printf("%05d %d ", dnode[i].address, dnode[i].data);
        if(i!=numd-1)
            printf("%05d\n",dnode[i].next);
        else
             cout<<"-1"<<endl;  //这里要加else，因为放在外面的话如果无效节点是0也会输出-1
            
     }
}
~~~~





## 搜索专题

### DFS和BFS的区分

>1**.BFS**是用来**搜索最短径路的解**是比较合适的，比如求最少步数的解，最少交换次数的解，因为BFS搜索过程中遇到的解一定是离根最近的，所以遇到一个解，一定就是最优解，此时搜索算法可以终止。这个时候不适宜使用DFS，因为DFS搜索到的解不一定是离根最近的，只有全局搜索完毕，才能从所有解中找出离根的最近的解。（当然这个DFS的不足，可以使用迭代加深搜索ID-DFS去弥补）
>2.**空间优劣**上，**DFS**是有优势的，DFS不需要保存搜索过程中的状态，而BFS在搜索过程中需要保存搜索过的状态，而且一般情况需要一个队列来记录。
>3.**DFS**适合**搜索全部的解**，因为要搜索全部的解，那么BFS搜索过程中，遇到离根最近的解，并没有什么用，也必须遍历完整棵搜索树，DFS搜索也会搜索全部，但是相比DFS不用记录过多信息，所以搜素全部解的问题，DFS显然更加合适。





### DFS

>DFS深搜要点：
>
>1. 使用递归方式求解：先判断当前点是否为终点，非终点时递归调用DFS
>2. 调用DFS前当前点标记为已访问，DFS执行结束后当前点标记为未访问：前者防止递归调用时重复访问当前点走回头路，后者是为探索其他路径时能重复访问该点。
>3. 使用外部变量统计最小步



#### 例_



![image-20211123113017614](https://i.loli.net/2021/11/23/N5f6wVxri4RmyUb.png)



~~~C++
#include <iostream>
#include<algorithm>
using namespace std;

int crr[5];
int a[5][30];
int minTime = 9999999;
int Left , Right ;
void DFS(int x, int y)
{
    
    if (y == crr[x])    //到达终点说明一个方案已经完成
    {

        minTime = min(minTime, max(Left, Right));   
        return;
    }

    //选择左脑处理
    Left+=a[x][y];  //标记
    DFS(x, y+1);
    Left-=a[x][y];  //取消标记

    //选择右脑处理
    Right += a[x][y];
    DFS(x, y+1);
    Right -= a[x][y];
}
int main()
{
    for (int i = 0; i < 4; i++)
        cin >> crr[i];

    for (int i = 0; i < 4; i++)
        for (int j = 0; j < crr[i]; j++)
            cin >> a[i][j];

    int time = 0;
    for (int i = 0; i < 4; i++)
    {
        Left = 0, Right = 0;
        minTime=99999;  //记住for循环中全局变量一定要想是否每一次都需要初始化！
        DFS(i, 0);
        time += minTime;
    }
    cout << time;
}

~~~





#### 1_

![image-20211122151517418](https://i.loli.net/2021/11/22/CXTs7Zn2whayUzM.png)



~~~C++
#include <iostream>
using namespace std;

//这个就可以想象成迷宫问题，死胡同n就是要到达的迷宫终点，求最大的价值也就是求不同的路径

const int maxn = 30;
int n, v, maxValue = 0;
int w[maxn], c[maxn];

//index：处理的物品编号  sumW:当前总重量 sumC：当前总价值
void DFS(int index, int sumW, int sumC)
{
    if (index == n) //已完成n件物品的选择(死胡同)  //每一层的返回条件都是这个，意思就是每到一层就往下直到index==n，然后就返回	
        return;
    DFS(index + 1, sumW, sumC); //不选此index产品	//这块用局部变量保存的sumW要不也要像上面一样两端封住
    //剪枝：只有加入此index件物品之后不超过容量V才能继续
    if (sumW + w[index] <= v)
    {
        if (sumC + c[index] > maxValue)
            maxValue = sumC + c[index];
        DFS(index + 1, sumW + w[index], sumC + c[index]); //选此index件物品，并往下进行	
    }//每一步进行到这完事之后不需要return ，函数结束自然就进入上层函数了！ sumW因为是上层函数的变量所以自然继承！
}

int main()
{
    cin >> n >> v;
    for (int i = 0; i < n; i++)
        cin >> w[i];
    for (int i = 0; i < n; i++)
        cin >> c[i];
    
    DFS(0,0,0); //初始为第0件物品
    cout<<maxValue;
}
~~~



#### 2_

![image-20211122155357004](https://i.loli.net/2021/11/22/3Q5fjVqi6CPAX8n.png)



~~~C++
#include <iostream>
#include<vector>
using namespace std;

const int maxn = 100010;
//序列A中选取k个数
int n, k, x, maxSumSqu=-1, A[maxn];
//temp存放临时方案， ans存放平方和最大的方案
vector<int> temp, ans;
//当前处理index号整数，当前已选整数个数为nowK
//整数和：sum   整数平方和：sumSqu

void DFS(int index, int nowK, int sum, int sumSqu)
{
    if(nowK==k && sum==x)	//如果找到k个数且和为x
    {
        if(sumSqu>maxSumSqu)	//是否更新maxSUmSqu
        {
            maxSumSqu = sumSqu;
            ans = temp;
        }
        return ;	
    }

    //已经处理完n个数，或者超过k个数返回
    if(index == n || nowK>k || sum>x)
        return ;
    
    //选index号数
    temp.push_back(A[index]);
    DFS(index+1,nowK+1,sum+A[index],sumSqu+A[index]*A[index]);

    //不选index号数
    temp.pop_back();
    DFS(index+1, nowK,sum,sumSqu);
}
~~~



#### 3_

![image-20211122165716950](https://i.loli.net/2021/11/22/QTMADfaLzGkErq9.png)

![image-20211122165732708](https://i.loli.net/2021/11/22/tfkIa1ouiwlMdEJ.png)



~~~C++
#include <iostream>
#include <vector>
#include <cmath>
using namespace std;

//这就跟上面一摸一样了

int n, k, p, maxFacSum = -1;
vector<int> fac, ans, temp;

void init() //初始化fac数组
{
    int temp = 0, index = 1;
    while (temp <= n) //当index^p不大于n,不断加入v
    {
        fac.push_back(temp);
        temp = pow(index++, p); //pow：幂次方函数
    }
}

void DFS(int index, int nowK, int sum, int facSum)
{
    if (sum == n && nowK == k) //满足题中条件
    {
        if (facSum > maxFacSum) //若此时更优
        {
            ans = temp;
            maxFacSum = facSum;
        }
        return;
    }
    if (sum > n || nowK > k) //此时到达死胡同
        return;
    if (index - 1 >= 0) //fac[0]不需要选择
    {
        temp.push_back(index);                                  //把底数index加入临时变量temp
        DFS(index, nowK + 1, sum + fac[index], facSum + index); //选的分支
        temp.pop_back();                                 //选的分支结束后把刚加进去的数pop掉，意思是分支回溯回来之后要出栈，即设置为未访问
        DFS(index - 1, nowK, sum, facSum);                      //不选的分支
    }
}

int main()
{
    cin >> n >> k >> p;
    init;
    DFS(fac.size() - 1, 0, 0, 0);
    if (maxFacSum == -1)
        cout << "Impossible" << endl;
    else
    {
        printf("%d = %d^%d", n, ans[0], p);
        for (int i = 1; i < ans.size(); i++)
            printf(" + %d^%d", ans[i], p);
    }
}
~~~





### BFS

![image-20211124212155476](https://i.loli.net/2021/11/24/7uwhMKTH8nBRjpG.png)





>TIPS:
>
>* inq的作用是判断位置是否入队而不是是否访问过，否则就会出现位置在队列里但尚未被访问，但是由于判断的是是否访问过，就会重复进入队列
>* queue在使用的时候是创建了一个副本，所以无论是通过原数组改变队列的顺序内容还是通过队列改变原数组都是不可行的
>* 所以queue存放的元素不要是元素本身，而最好是元素的下标（例如老鼠的排名那道题）







#### 例_

![image-20211125095349230](https://i.loli.net/2021/11/25/1tlI6D3ezbXY9kM.png)



~~~C++
#include <iostream>
#include <queue>

using namespace std;

int T, n;
int sx, sy, ex, ey;
char mp[1010][1010];

int X[4] = {1, -1, 0, 0};
int Y[4] = {0, 0, 1, -1};

struct Node
{
    int x, y;
} node;

void BFS()
{
    queue<Node> q;
    node.x = sx;
    node.y = sy;
    mp[node.x][node.y] = '#'; //走过的标记为#即不能再访问
    q.push(node);
    while (!q.empty())
    {
        Node top = q.front(); //新开一个Node记录开始的节点
        q.pop();
        if (top.x == ex && top.y == ey) //到达终点
        {
            cout << "YES" << endl;
            return ;
        }
        for (int i = 0; i < 4; i++)
        {
            node.x = top.x + X[i];
            node.y = top.y + Y[i];
            if (node.x >= 0 && node.x < n && node.y >= 0 && node.y < n && mp[node.x][node.y] == '.')
            {
                q.push(node);
                mp[node.x][node.y] = '#';
            }
        }
    }
        cout << "NO" << endl;
}
int main()
{
    cin >> T;
    while (T--)
    {
        cin >> n;
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                cin >> mp[i][j];
        cin >> sx >> sy >> ex >> ey;
        if (mp[sx][sy] == '#' || mp[ex][ey] == '#')
            cout << "NO" << endl;
        else 
            BFS();
    }
}
~~~



#### 1_

![image-20211124212234920](https://i.loli.net/2021/11/24/DObQzsLZmcMqSCp.png)



~~~C++
#include <iostream>
#include <queue>
using namespace std;

struct Node
{
    int x, y; //位置

} node;

const int maxn = 100;
int n, m;
int matrix[maxn][maxn];         //01矩阵
bool inq[maxn][maxn] = {false}; //记录位置xy是否入队
int X[4] = {0, 0, 1, -1};       //增量数组
int Y[4] = {1, -1, 0, 0};

bool judge(int x, int y) //判断BFS是否需要访问该位置
{
    if (x >= n || x < 0 || y >= n || y < 0) //越界返回false
        return false;
    if (matrix[x][y] == 0 || inq[x][y] == true) //位置为0或者访问过
        return false;
    return true;
}

//BFS目的是访问xy周围的位置并设为true，表示这些true的都是一块的
void BFS(int x, int y)
{
    queue<Node> q;
    node.x = x;
    node.y = y;
    inq[x][y] = true;
    q.push(node);
    while (!q.empty())
    {
        //因为每次循环中队首元素都不一样，而第一次的是输入的xy
        Node top = q.front(); //队首元素是front()
        q.pop();
        for (int i = 0; i < 4; i++)
        {
            int newX = top.x + X[i];
            int newY = top.y + Y[i];
            if (judge(newX, newY))  //如果需要访问
            {
                node.x = newX;
                node.y = newY;
                q.push(node);           //将节点node加入队列
                inq[newX][newY] = true; //位置设置已入队
            }
        }
    }
}
    int main()
    {
        cin >> n >> m;
        for (int x = 0; x < n; x++)
            for (int y = 0; y < m; y++)
                cin >> matrix[x][y];
        int ans;
        for (int x = 0; x < n; x++)
            for (int y = 0; y < m; y++)
            {
                if (matrix[x][y] = 1 && inq[x][y] == false) //看判断方法，也就是记录第一个遇到的数，如果没被标记过说明没有被BFS查到过也就是不属于这一块
                {
                    ans++;
                    BFS(x, y);  //访问整个块，将所有1标为true
                }
            }

        cout << ans << endl;
    }
~~~



#### 2_

![image-20211126145552806](https://i.loli.net/2021/11/26/AtGYaysmfgcrzWH.png)

~~~C++
#include <iostream>
#include <queue>


//和上一道思路一模一样，只是变成三维的了，用DFS会爆栈
using namespace std;

int T, n, m, slice;
int pixel[1290][130][61];	//虽然只是01但也要设置成int
bool inq[1290][130][61] = {false};

struct Node
{
    int x, y, z;
} node;

int X[6] = {0, 0, 0, 0, 1, -1};
int Y[6] = {0, 0, 1, -1, 0, 0};
int Z[6] = {1, -1, 0, 0, 0, 0};

bool judge(int x, int y, int z)
{
    if (x >= n || x < 0 || y >= m || y < 0 || z >= slice || z < 0) //位置边界判断
        return false;
    if (pixel[x][y][z] == 0 || inq[x][y][z] == true) //位置逻辑判断
        return false;
    return true;
}

int BFS(int x, int y, int z)
{
    int total = 0;
    node.x = x;
    node.y = y;
    node.z = z;
    queue<Node> q;
    q.push(node);
    inq[x][y][z] = true;

    while (!q.empty())
    {
        total++;
        Node top = q.front();
        q.pop();
        for (int i = 0; i < 6; i++)
        {
            int newX = top.x + X[i];
            int newY = top.y + Y[i];
            int newZ = top.z + Z[i];
            if (judge(newX, newY, newZ))
            {
                node.x = newX;
                node.y = newY;
                node.z = newZ;
                q.push(node);
                inq[newX][newY][newZ] = true;
            }
        }
    }
    if (total >= T)
        return total;
    else
        return 0;
}

int main()
{
    cin >> n >> m >> slice >> T;
    for (int z = 0; z < slice; z++)
        for (int x = 0; x < n; x++)
            for (int y = 0; y < m; y++)
                cin >> pixel[x][y][z];

    int ans = 0;
    for (int z = 0; z < slice; z++)
        for (int x = 0; x < n; x++)
            for (int y = 0; y < m; y++)
                if (pixel[x][y][z] == 1 && inq[x][y][z] == false)
                    ans += BFS(x, y, z);
    cout << ans << endl;
}
~~~



## 数据结构专题(2)

### 二叉树基础

#### 二叉树的存储结构

~~~C++
//二叉链表
struct node
{
    int data;
    node *lchild;
    node *rchild;
};

node *root = NULL;

//新建结点
node *newNode(int v)
{
    node *Node = new node;
    Node->data = v;
    Node->lchild = Node->rchild = NULL;
    return Node;
}
~~~



#### 二叉树结点的查找修改

~~~C++
//递归查找x并替换为data
void search(node *root, int x, int newdata)
{
    if (root == NULL)
        return;
    if (root->data == x)
        root->data = newdata;
    search(root->lchild, x, newdata);	//递归遍历查找
    search(root->rchild, x, newdata);
}
~~~



#### 二叉树结点的插入

>* 根指针root要引用，search修改的是指针指向的内容所以不用加&，而insert新建了节点并且把地址赋给root，如果不加引用，对root的修改就无法作用到原变量上
>
>* **判断是否引用**：如果要新建结点，即对二叉树的结构进行修改就需要加引用；如果只是修改当前结点现有的内容，或仅仅是遍历树，就不需要加引用
>  * root == NULL 而不是 *root ==NULL ， *root指的是root这个指针指向地址中的值， 而需要判断的是有无根结点root 而不是根结点root指向的值是否为空

~~~C++
void insert(node *&root, int x)
{
    if (root == NULL)
    {
        root = newNode(x);
        return;
    }
    if (x < root->data) //>...条件根据题意变化，这里是x与根结点比较大小，如果小于就做根结点的左孩子
        insert(root->lchild, x);
    else
        insert(root->rchild, x);
}
~~~



#### 二叉树的创建

~~~C++
//二叉树的创建,数据存储在数组中
node *creat(int data[], int n)
{
    node *root = NULL;
    for (int i = 0; i < n; i++)
        insert(root, data[i]);
    return root;
}
~~~



> * **判断某个结点是否为叶节点**： 该结点左子结点标号root*2 大于总节点个数
> * **判断是否为空结点：**该结点标号大于节点总个数n

### 二叉树

 
