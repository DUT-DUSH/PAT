# PAT

## 数据结构专题

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
//质量最大的push回去，在下一轮中通过push修改rank
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
                //最后一组老鼠不足ng时退出循环
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

