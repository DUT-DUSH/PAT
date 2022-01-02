# PAT

## 算法初步

> ![image-20220101085841648](https://s2.loli.net/2022/01/01/4zCgaS19hovwVWY.png)


### 散列

* x不是用来计数的i，而是输入的变量   h[x]=true  h[x]是分散的（即"散"列），所以是空间换时间



## 数学问题

### 例_

![image-20211231173534264](https://s2.loli.net/2021/12/31/ejEcDIoN3CqlVys.png)

~~~C++
#include <iostream>
#include <algorithm>
using namespace std;
using namespace std;
int main()
{
	string s;
	cin >> s;
	s.insert(0, 4 - s.length(), '0'); //用来给不足4位的前面补0
	do								  //这里必须要用dowhile，因为即使输入的是6174也要执行一遍
	{
		string a = s, b = s;
		sort(a.begin(), a.end()); //对string要用begin和end
		sort(b.begin(), b.end());
		int result = stoi(a) - stoi(b); //string to int
		s = to_string(result);			//int to string
		s.insert(0, 4 - s.length(), '0');
		cout << a << " - " << b << " = " << s << endl;
	} while (s != "6174" && s != "0000");
	return 0;
}
~~~



>* string转int  以及Int 转string
>
>  ![image-20211231175812653](https://s2.loli.net/2021/12/31/BuySfRJwt4r3LhI.png)
>
>* sstream
>
>  ![ ](https://s2.loli.net/2021/12/31/6m9QxhZ3TednqGO.png)



### 最大公约数和最小公倍数

​	![image-20211231180154142](https://s2.loli.net/2021/12/31/ozyNfmQY4rtOjHx.png)![image-20211231180205859](https://s2.loli.net/2021/12/31/MwINhkoWpyLATY2.png)



### 分数四则运算

#### 1_

![image-20211231183255546](https://s2.loli.net/2021/12/31/wZ25HrGoFu9TMSh.png)



>1. 注意这里面的abs，普通情况化简需要gcd（abs），show对假分数的识别要加abs，以及分子都要加abs
>
>2. up和down什么的一定要看仔细
>
>3. 判断res==1 一定是双等 一定要仔细



~~~C++
#include <iostream>
#include <algorithm>
using namespace std;

typedef long long int ll;

ll gcd(ll a, ll b)
{
    if (b == 0)
        return a;
    else
        return gcd(b, a % b);
}

struct Fraction
{
    ll up, down;
};

Fraction reduction(Fraction result)
{
    if (result.down < 0) //如果分母是负数，让负数在分子上
    {
        result.up = -result.up;
        result.down = -result.down;
    }
    if (result.up == 0) //如果分子等于0，让分母等于1
        result.down = 1;
    else //除此之外正常化简
    {
        int d = gcd(abs(result.up), abs(result.down));
        result.up /= d;
        result.down /= d;
    }
    return result;
}

Fraction add(Fraction f1, Fraction f2)
{
    Fraction result;
    result.up = f1.up * f2.down + f2.up * f1.down;
    result.down = f1.down * f2.down;
    return reduction(result);
}

void show(Fraction r)
{
    r = reduction(r);
    if (r.down == 1) //如果分母等于1，输出分子
        cout << r.up;
    else if (abs(r.up) > r.down) //假分数
        cout << r.up / r.down << " " << abs(r.up % r.down) << "/" << r.down << endl;
    else //真分数
        cout << r.up << "/" << r.down << endl;
}

int main()
{
    int n;
    cin >> n;
    Fraction sum, tmp;
    sum.up = 0;
    sum.down = 1;
    for (int i = 0; i < n; i++)
    {
        scanf("%lld/%lld", &tmp.up, &tmp.down);
        sum = add(sum, tmp);
    }
    show(sum);
}
~~~



#### 2_

![image-20211231183845611](https://s2.loli.net/2021/12/31/OFvfLYktHX4Qh3K.png)



~~~C++
#include <iostream>
using namespace std;

typedef long long ll;

int gcd(ll a, ll b)
{
    if (b == 0)
        return a;
    else
        return gcd(b, a % b);
}

struct Fraction
{
    ll up, down;
};

Fraction reduction(Fraction res)
{
    if (res.down < 0)
    {
        res.up = -res.up;
        res.down = -res.down;
    }
    if (res.up == 0)
        res.down = 1;
    else
    {
        int d = gcd(abs(res.up), abs(res.down));
        res.up /= d;
        res.down /= d;
    }
    return res;
}

void showResult(Fraction res)
{
    res = reduction(res);
    if (res.up < 0)
        cout << "(";
    if (res.down == 1)
        cout << res.up;
    else if (abs(res.up) > abs(res.down))
        cout << res.up / res.down << " " << abs(res.up) % abs(res.down) << "/" << res.down;
    else
        cout << res.up << "/" << res.down;
    if (res.up < 0)
        cout << ")";
}

Fraction add(Fraction f1, Fraction f2)
{
    Fraction res;
    res.up = f1.up * f2.down + f2.up * f1.down;
    res.down = f1.down * f2.down;
    return reduction(res);
}

Fraction minu(Fraction f1, Fraction f2)
{
    Fraction res;
    res.up = f1.up * f2.down - f2.up * f1.down;
    res.down = f1.down * f2.down;
    return reduction(res);
}

Fraction multi(Fraction f1, Fraction f2)
{
    Fraction res;
    res.up = f1.up * f2.up;
    res.down = f1.down * f2.down;
    return reduction(res);
}

Fraction divide(Fraction f1, Fraction f2)
{
    Fraction res;
    res.up = f1.up * f2.down;
    res.down = f1.down * f2.up;
    return reduction(res);
}

int main()
{
    Fraction a, b;
    scanf("%lld/%lld %lld/%lld", &a.up, &a.down, &b.up, &b.down);
    showResult(a);
    cout << " + ";
    showResult(b);
    cout << " = ";
    showResult(add(a, b));
    cout << endl;

    showResult(a);
    cout << " - ";
    showResult(b);
    cout << " = ";
    showResult(minu(a, b));
    cout << endl;

    showResult(a);
    cout << " * ";
    showResult(b);
    cout << " = ";
    showResult(multi(a, b));
    cout << endl;

    showResult(a);
    cout << " / ";
    showResult(b);
    cout << " = ";
    if (b.up == 0)
        cout << "Inf";
    else
        showResult(divide(a, b));
    cout << endl;
}
~~~



### 素数

![image-20211231184203941](https://s2.loli.net/2021/12/31/Qpn3fvFATMXi9dq.png)



> 单纯的求素数 用isPrime判断就行了
>
> 但如果要求素数表，求N-M范围内的素数，
>
> N是指第N个要用findPrime方便



~~~C++
#include <iostream>
using namespace std;

const int maxn = 1000001;

bool p[maxn] = {0};
int prime[maxn];
int cnt = 0;

void findPrime(int n)
{
    for (int i = 2; i < maxn; i++)
    {
        if (p[i] == 0)
        {
            prime[cnt++] = i;
            if (cnt == n)
                break;
            for (int j = i + i; j < maxn; j += i)
                p[j] = 1;
        }
    }
}

int main()
{
    int m, n;
    cin >> m >> n;
    int count = 0;
    findPrime(n);
    for (int i = m; i <= n; i++)
    {
        cout << prime[i - 1];
        count++;
        if (count % 10 != 0&&i!=n)
            cout << " ";
        else
            cout << endl;
    }
}
~~~



### 质因子分解

![image-20211231184600158](https://s2.loli.net/2021/12/31/EFvCbsNQG58KpDB.png)



~~~C++
#include <iostream>
#include <cmath>
using namespace std;

const int maxn = 1000001;
bool p[maxn] = {0};
int prime[maxn];
int pNum = 0;

void findPrime()
{
    for (int i = 2; i < maxn; i++)
    {
        if (p[i] == 0)
        {
            prime[pNum++] = i;
            for (int j = i + i; j < maxn; j += i)
                p[j] = 1;
        }
    }
}

struct factor
{
    int x, cnt;
} fac[10];

int main()
{
    findPrime();
    int n, num;
    cin >> n;

    if (n == 1)
        cout << "1=1";
    else
    {
        cout << n << "=";
        int sqr = (int)sqrt(1.0 * n); //n要*1.0   因为sqrt参数是浮点数  外面要再次转化为int
        for (int i = 0; i < pNum && prime[i] <= sqr; i++)
        {
            if (n % prime[i] == 0) //如果prime[i]是n的因子，数它有几个
            {
                fac[num].x = prime[i];
                fac[num].cnt = 0;
                while (n % prime[i] == 0)
                {
                    fac[num].cnt++;
                    n /= prime[i];
                }
                num++;
                
            }
            if (n == 1) //如果在n的下降当中到达1了，那么就结束了
                break;
        }
        if (n != 1) //如果n!=1,那么n就是最后一个因子
        {
            fac[num].x = n;
            fac[num++].cnt = 1; //这不能两个都num++，这样就加两次了，一个bug点
        }
        for (int i = 0; i < num; i++)
        {
            if (i > 0)
                cout << "*";
            cout << fac[i].x;
            if (fac[i].cnt > 1)
                cout << "^" << fac[i].cnt;
        }
    }
}
~~~

> ![image-20211231184907168](https://s2.loli.net/2021/12/31/kOLngr71pPI3uJm.png)



### 大整数运算

![image-20211231185214054](https://s2.loli.net/2021/12/31/dSOJln7cufaFITG.png)

![image-20211231185235572](https://s2.loli.net/2021/12/31/rxCKc4gAXLtzinu.png)

![image-20211231185249612](https://s2.loli.net/2021/12/31/vPmlTGZHudw6RIo.png)

 

## STL

### vector

> * 记得push_back()和pop_back()，因为开始的时候vector的长度是0，所以不能v[i]来赋值
> * 插入、删除和排序里用的都是vi.begin()，vi.end()
> * 虽然说vector不能处理string->int，但是也不是没办法，只需要写一个转换函数，把string转换成int就行了
> * 这个vector大小也要提前开出来，不过不能开太大，比规定的大一点就行了   
> * 这个最后一组超时是因为你用了string， 数据量大的用char[]好一点



![image-20211205135317226](https://s2.loli.net/2021/12/05/HKA4OhV95xYJW2s.png)



~~~C++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int getId(string s)
{
    int ans = 0;
    for (int i = 0; i < 3; i++)
        ans = ans * 26 + s[i] - 'A'; //应该×26
    ans = ans * 10 + (s[3] - '0');
    return ans;
}

int main()
{
    vector<int> v[26 * 26 * 26 * 10];
    int n, k;
    cin >> n >> k;
    for (int i = 0; i < k; i++)
    {
        int course, name;
        cin >> course >> name;
        for (int j = 0; j < name; j++)
        {
            string s;
            cin >> s;
            v[getId(s)].push_back(course);
        }
    }

    for (int i = 0; i < n; i++)
    {
        string s;
        cin >> s;
        sort(v[getId(s)].begin(), v[getId(s)].end());
        cout << s << " " << v[getId(s)].size();
        for (int j = 0; j < v[getId(s)].size(); j++)
            cout << " " << v[getId(s)][j];
        cout << endl;
    }
}
~~~



### set

> * 这个find很重要，他返回的的是迭代器，于是可以对这个迭代器进行earse的操作



![image-20211205140522988](https://s2.loli.net/2021/12/05/Qvke2YPhioBwZSr.png)



~~~C++
#include <iostream>
#include <set>
using namespace std;
set<int> st[55];

void compare(int x, int y)
{
    int totalNum = st[y].size(), sameNum = 0;
    for (set<int>::iterator it = st[x].begin(); it != st[x].end(); it++) //遍历set的方式
    {
        if (st[y].find(*it) != st[y].end()) //在一个set中找另一个set的元素，不需要for循环
            sameNum++;
        else
            totalNum++;
    }
    printf("%.1f%\n", sameNum / totalNum);
}

int main()
{
    int n, tmp, value;
    cin >> n;
    for (int i = 0; i < n; i++)
    {
        cin >> tmp;
        for (int j = 0; j < tmp; j++)
        {
            cin >> value;
            st[i].insert(value); //set要这么增加值
        }
    }
    int m, k1, k2;
    cin >> m;
    for (int i = 0; i < m; i++)
    {
        cin >> k1 >> k2;
        compare(k1 - 1, k2 - 1);
    }
}
~~~



### string

>* string赋值用**拼接**方式
>
>  ![image-20211205142749978](https://s2.loli.net/2021/12/05/g84wrhexuUCkBE5.png)



![image-20211205143215608](https://s2.loli.net/2021/12/05/KfVh2xgqoA14ydz.png)



~~~C++
#include <iostream>
#include <string>
using namespace std;
//科学计数法 按整数部分是否为0来讨论
//如果是0，本体部分是小数点后前三个非0的数，指数部分是第一个非0数前0个数的相反数
//如果不是0，本体部分是前三个数，指数部分是小数点前位数
//题目有陷阱可能是 00123.23；000.012  要先去除前导0 string erase

int n;

string deal(string s, int &e)
{
    while (s.length() > 0 && s[0] == '0') //去除前导0
        s.erase(s.begin());
    if (s[0] == '.') //如果是小数，去0 求e
    {
        s.erase(s.begin());
        while (s.length() > 0 && s[0] == '0') //一定要有length>0因为有可能小数点后都是0
        {
            s.erase(s.begin());
            e--;
        }
    }
    else //说明小数点前有数字
    {
        int k = 0;
        while (k < s.length() && s[k] != '.') //求 e
        {
            k++;
            e++;
        }
        if (k < s.length()) //说明遇到了小数点，要去掉小数点，方便之后取n位本位
            s.erase(s.begin() + k);
    }

    if (s.length() == 0)
        e = 0; //如果去除0长度为0说明这个数是0

    string res;
    for (int i = 0; i < n; i++)
    {
        if (i < s.length()) //string要这么赋值
            res += s[i];
        else
            res += '0';
    }
    return res;
}

int main()
{
    string s1, s2, s3, s4;
    cin >> n >> s1 >> s2;
    int e1 = 0, e2 = 0;
    s3 = deal(s1, e1);
    s4 = deal(s2, e2);
    if (s3 == s4 && e1 == e2)
        cout << "YES 0." << s3 << "*10^" << e1 << endl;
    else
        cout << "NO 0." << s3 << "*10^" << e1 << " 0." << s4 << "*10^" << e2 << endl;
}
~~~



### map

>* getline前要加getchar()
>
>  ![image-20211205145505191](https://s2.loli.net/2021/12/05/LzP29wO4ZyfDN5v.png)



#### 1_

![image-20211205145148248](https://s2.loli.net/2021/12/05/HPV2zOgwlq9FkyG.png)



~~~C++
#include <iostream>
#include <map>
using namespace std;
//这个是string 和int的对应  所以想到map
string unitDigit[13] = {"tret", "jan", "feb", "mar", "apr", "may", "jun", "jly", "aug", "sep", "oct", "nov", "dec"};
string tenDigit[13] = {"tret", "tam", "hel", "maa", "huh", "tou", "kes", "hei", "elo", "syy", "lok", "mer", "jou"};
string numToStr[170];      //打表  这是数字到火星文
map<string, int> strToNum; //因为这个最高也就170，所以选择打表 火星文到数字

void init()
{
    for (int i = 0; i < 13; i++) //对十位为0和个位为0操作
    {
        numToStr[i] = unitDigit[i];
        strToNum[unitDigit[i]] = i;
        numToStr[i * 13] = tenDigit[i];
        strToNum[tenDigit[i]] = i * 13;
    }
    for (int i = 1; i < 13; i++) //对十位个位都不为0操作，string拼接就行了
    {
        for (int j = 1; j < 13; j++)
        {
            string str = tenDigit[i] + " " + unitDigit[j];
            numToStr[i * 13 + j] = str;
            strToNum[str] = i * 13 + j;
        }
    }
}

int main()
{
    init();
    int n;
    cin >> n;
    getchar();
    while (n--)
    {
        string s;
        //cin >> s;
        getline(cin, s);    //如果是火星文就会有空格，cin不能接受空格，要用getline
        if (s[0] >= '0' && s[0] <= '9')
        {
            int num = 0;
            for (int i = 0; i < s.length(); i++)
                num = num * 10 + (s[i]-'0');  //记住这块的-'0'总是忘，string->int要减0
            cout<<numToStr[num]<<endl;
        }
        else
            cout<<strToNum[s]<<endl;

    }
}
~~~



#### 2_

![image-20211205145554381](https://s2.loli.net/2021/12/05/amF9nMOIZgrUHQ6.png)



~~~C++
#include <iostream>
#include <map>
using namespace std;
//就是求出现次数最多的数字,map是int对应int就是每个都是10的9次方之内,而普通数组开不了这么大


//想法是开个数组，记录value以及对应的cnt再比较就行了，但是这样value太大会超时，所以用map
int main()
{
    int n, m;
    int value;
    cin >> n >> m;
    map<int, int> cnt;
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < m; j++)
        {
            scanf("%d", &value);
            if (cnt.find(value) != cnt.end())   //直接用find()
                cnt[value]++;
            else
                cnt[value] = 1;
        }
    }

    int k=0,max=0;
    for (map<int, int>::iterator it = cnt.begin(); it != cnt.end(); it++)
    {
        if(it->second>max)      //map查看元素的方式
        {
            k=it->first;
            max=it->second;
        }
    }
    cout<<k;
}
~~~



#### 3_

![image-20211205145816288](https://s2.loli.net/2021/12/05/UZYbo91vynED4uR.png)



~~~c++
#include <iostream>
#include <map>
using namespace std;

bool check(char c) //检查c是否有效
{
    if (c >= '0' & c <= '9')
        return true;
    if (c >= 'A' & c <= 'Z')
        return true;
    if (c >= 'a' & c <= 'z')
        return true;
    return false;
}

int main()
{
    map<string, int> cnt;
    string s;
    getline(cin, s);
    for (int i = 0; i < s.length(); i++)    //建立map
    {
        string word;
        while(check(s[i])==true)
        {
            if(s[i]>='A'&&s[i]<='Z')
                s[i]+=32;
            word+=s[i];
            i++;
        }
        if(word!="")    //string的判空
        {
            if(cnt.find(word)!=cnt.end())
                cnt[word]++;
            else
                cnt[word]=1;
        }
    }

    string ans;
    int max=0;
    for(map<string, int>::iterator it=cnt.begin();it!=cnt.end();it++)
    {
        if(it->second>max)
        {
            ans=it->first;
            max=it->second;
        }
    }
    cout<<ans<<" "<<max;
}
~~~



#### 4_

![image-20211205150011844](https://s2.loli.net/2021/12/05/lLk9NbXUAmuYdcJ.png)

![image-20211205150029913](https://s2.loli.net/2021/12/05/tx7oeEgYacyqMzO.png)

![image-20211205150043610](https://s2.loli.net/2021/12/05/5dsUCc2I9OelQkv.png)



~~~c++
#include <iostream>
#include <map>
#include <set>
#include<string>
using namespace std;

map<string, set<int>> mpTitle, mpAuthor, mpKey, mpPub, mpYear;

void query(map<string, set<int>>& mp, string& str)
{
    if(mp.find(str)==mp.end())
        cout<<"Not Found"<<endl;
    else
        for (set<int>::iterator it=mp[str].begin();it!=mp[str].end();it++)
            printf("%07d\n",*it);
}


int main()
{
    int n, m, id, type;
    string title, author, key, pub, year;
    scanf("%d",&n);
    for(int i=0;i<n;i++)
    {
        scanf("%d",&id);
        char c=getchar();
        getline(cin,title);
        mpTitle[title].insert(id);
        getline(cin, author);
        mpAuthor[author].insert(id);
        while(cin>>key) //接受一系列的关键词，到了换行符就退出
        {
            mpKey[key].insert(id);
            c=getchar();
            if(c=='\n')
                break;
        }
    
    getline(cin,pub);
    mpPub[pub].insert(id);
    getline(cin, year);
    mpYear[year].insert(id);
    }

    string temp;
    scanf("%d",&m);
    for(int i=0;i<m;i++)
    {
        scanf("%d: ",&type);  //因为这后面不紧挨着换行符所以不用getchar
        getline(cin, temp);
        cout<<type<<": "<<temp<<endl;
        if(type==1)
            query(mpTitle, temp);
        else if(type==2)
            query(mpAuthor, temp);
        else if (type == 3)
            query(mpKey, temp);
        else if (type == 4)
            query(mpPub, temp);
        else
            query(mpYear, temp);
    }
}
~~~



### 其他常用函数

#### reverse()

> * 记住这是前闭后开的

![image-20211205150508784](https://s2.loli.net/2021/12/05/4YLtKgEfl9uke18.png)

![image-20211205150532270](https://s2.loli.net/2021/12/05/cpOAzGx5l9i84du.png)



#### lower_bound() 和 upper_bound()

![image-20211205150735005](https://s2.loli.net/2021/12/05/l59rTS2G3XPzM7h.png)

![image-20211205150757047](https://s2.loli.net/2021/12/05/aSnMqvNj58l2ArJ.png)

![image-20211205150816965](https://s2.loli.net/2021/12/05/2v65fiGEyN9kHYT.png)

![image-20211205150826985](https://s2.loli.net/2021/12/05/Y2nFQGTJakctu9m.png)







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

### 二叉树的遍历

>* **中序序列可以与先序序列、后序序列、层次序列中的任意一个来构建唯一的二叉树**，而后三者两两搭配或是三个一起上都无法构建唯一的二叉树。原因是先序、后序、层次都是**提供根结点**，作用是相同的，都必须由中序序列来区分出左右子树



 ~~~C++
 //先序遍历
 void preorder(node *root)
 {
     if (root == NULL)
         return;
     cout<<root->data;
     preorder(root->lchild);
     preorder(root->rchild);
 }
 
 //中序遍历
 void inorder(node *root)
 {
     if (root == NULL)
         return;
     preorder(root->lchild);
     cout << root->data;
     preorder(root->rchild);
 }
 
 //后序遍历
 void postorder(node *root)
 {
     if (root == NULL)
         return;
     preorder(root->lchild);
     preorder(root->rchild);
     cout << root->data;
 }
 
 
 //层次遍历
 //即对二叉树从根结点开始的BFS
 
 struct node
 {
     int data;
     int layer; //层次
     node *lchild;
     node *rchild;
 };
 
 void LayerOrder(node *root)
 {
     queue<node *> q; //注意队列里存地址
     root->layer = 1; //根结点的层次为1
     q.push(root);
     while (!q.empty())
     {
         node *top = q.front(); //取出队首元素
         q.pop();
         cout<<top->data;    //访问队首元素
         if(top->lchild!= NULL)  //左子树非空
         {
             top->lchild->layer=top->layer+1;
             q.push(top->lchild);
         }
         if (top->lchild != NULL) //右子树非空
         {
             top->rchild->layer = top->layer + 1;
             q.push(top->rchild);
         }
     }
 }
 ~~~



#### 给定先序和中序，创建二叉树

> * 思路：先序序列pre1,pre2,pre3...，中序序列in1,in2,in3...。
>   1. 先在中序找到某个结点ink，使得ink==pre1，这就在中序中找到了根结点
>   2. 左子树的结点个数 numLeft = k - 1
>   3. 左子树先序区间：[2 , k]       左子树中序区间：[1 , k-1]   
>   4. 右子树先序区间：[k+1 , n]    右子树中序区间：[k+1 , n]
> * 通用：先序序列[preL , preR], 中序序列[inL ,  inR]
>   1. 左子树结点个数:numLeft = k - inL
>   2. 左子树先序区间：[preL + 1 , preL+ numLeft]        左子树中序区间：[inL , k-1]      
>   3.   右子树先序区间：[preL+numLeft+1 , preR]     右子树中序区间：[k+1 , inR]

 ![image-20211127162746070](https://i.loli.net/2021/11/27/cvLPYQySzCHft2X.png)



~~~C++
int pre[100010];
int in[100010];
node *creat(int preL, int preR, int inL, int inR)
{
    if (preL > preR) //先序序列长度小于0，直接返回
        return NULL;
    node *root = new node; //存放二叉树的根结点
    root->data = pre[preL];
    int k;
    for (k = inL; k <= inR; k++) //这里是 <=
    {
        if (in[k] == pre[preL]) //找到in[k]==pre[preL]的结点
            break;
    }

    int numLeft = k - inL;

    //构建左子树
    root->lchild = creat(preL + 1, preL + numLeft, inL, k - 1);
    //注意这里的preL + numLeft， 实际上是 preL+1 + numLeft-1  ,因为是闭区间，加0的长度是1，长度是numLeft应该加numLeft-1

    //构建右子树
    root->rchild = creat(preL + numLeft + 1, preR, k + 1, inR);

    return root;
}
~~~



#### 1_

![image-20211129141147084](https://i.loli.net/2021/11/29/FtDcQbrOzaEinoM.png)



~~~C++
#include <iostream>
#include <queue>
using namespace std;

const int maxn = 31;

struct node
{
    int data;
    node *lchild;
    node *rchild;
};
int post[maxn], in[maxn];
int n, num = 0;

node *creat(int postL, int postR, int inL, int inR) //注意这里的都是下标
{
    if (postL > postR)
        return NULL;
    node *root = new node;
    root->data = post[postR]; //指出根结点
    int k;
    for (k = inL; k <=inR; k++)
        if (in[k] == post[postR])
            break;

    int numLeft = k - inL;

    //注意这里的postL+numLeft-1
    root->lchild = creat(postL, postL + numLeft - 1, inL, k - 1); //指出左结点

    root->rchild = creat(postL + numLeft, postR - 1, k + 1, inR); //指出右结点

    return root;
}

void BFS(node *root)
{
    queue<node *> q;
    q.push(root);
    while (!q.empty())
    {
        node *top = q.front();
        q.pop();
        cout << top->data;
        num++;
        if (num < n)
            cout << " ";
        if (top->lchild!=NULL)
            q.push(top->lchild);
        if (top->rchild!=NULL)
            q.push(top->rchild);
    }
}

int main()
{
    cin >> n;
    for (int i = 0; i < n; i++)
        cin >> post[i];
    for (int i = 0; i < n; i++)
        cin >> in[i];
    node *root = creat(0, n - 1, 0, n - 1);
    BFS(root);
}
~~~



#### 2_

![image-20211129154755397](https://i.loli.net/2021/11/29/pdGWMT3fai2POU9.png)



~~~C++
#include <iostream>
#include <queue>
#include <stack>
using namespace std;

const int maxn = 31;

struct node
{
    int data;
    node *lchild;
    node *rchild;
};
int pre[maxn], in[maxn];
int n, num = 0;

node *creat(int preL, int preR, int inL, int inR)
{
    node *root = new node;
    if (preL > preR)
        return NULL;
    root->data = pre[preL];
    int k;
    for (k = inL; k <= inR; k++)
        if (in[k] == pre[preL])
            break;

    int numLeft = k - inL;

    root->lchild = creat(preL + 1, preL + numLeft, inL, k - 1);
    root->rchild = creat(preL + numLeft + 1, preR, k + 1, inR);
    return root;
}

void postorder(node *root)
{
    if (root == NULL)
        return;
    postorder(root->lchild);
    postorder(root->rchild);
    cout << root->data;
    num++;
    if (num < n)
        cout << " ";
}
int main()
{
    stack<int> sta;
    cin >> n;
    int num1 = 0, num2 = 0;
    for (int i = 0; i < 2 * n; i++)
    {
        string s;
        cin >> s;
        if (s == "Push")
        {
            int data;
            cin >> data;
            pre[num1++] = data;
            sta.push(data);
        }
        else
        {
            in[num2++] = sta.top();
            sta.pop();
        }
    }

    node *root = new node;
    root = creat(0, n - 1, 0, n - 1);
    postorder(root);
}
~~~



#### 3_

![image-20211129202809415](https://i.loli.net/2021/11/29/hBEUGnDATNHweoi.png)





> * 这道题用的是静态二叉树，先通过题中输入构建静态二叉树，通过后序遍历可以反转二叉树
> * 要先找到根，根也就是不是任何结点的子节点，可以通过一个bool数组实现
> * 代码分模块就很清晰

~~~C++
#include <iostream>
#include <queue>
#include <algorithm>
using namespace std;

const int maxn = 110;
int n, num = 0;
bool notRoot[maxn] = {false};
struct node
{
    int lchild, rchild;
} Node[maxn];

void print(int root)
{
    cout << root;
    num++;
    if (num < n)
        cout << " ";
    else
        cout << endl;
}

int strToNum(char c)
{
    if (c == '-')
        return -1;
    else
    {
        notRoot[c - '0'] = true;
        return c - '0';
    }
}

void postorder(int root) //反转二叉树的办法
{
    if (root == -1)
        return;
    postorder(Node[root].lchild);
    postorder(Node[root].rchild);
    swap(Node[root].lchild, Node[root].rchild); //交换左右孩子节点
}

void BFS(int root)
{
    queue<int> q;
    q.push(root);

    while (!q.empty())
    {
        int top = q.front();
        print(top);
        q.pop();
        if (Node[top].lchild != -1)
            q.push(Node[top].lchild);
        if (Node[top].rchild != -1)
            q.push(Node[top].rchild);
    }
}

void inorder(int root)
{
    if (root == -1)
        return;
    inorder(Node[root].lchild);
    print(root);
    inorder(Node[root].rchild);
}

int findRoot()
{
    for (int i = 0; i < n; i++)
    {
        if (notRoot[i] == false)
            return i;
    }
}
int main()
{
    char lchild, rchild;
    cin >> n;
    for (int i = 0; i < n; i++)
    {
        cin >> lchild >> rchild;
        Node[i].lchild = strToNum(lchild);
        Node[i].rchild = strToNum(rchild);
    }

    int root = findRoot();
    postorder(root);
    BFS(root);
    num = 0;
    inorder(root);
}

~~~



### 树的遍历

#### 1_

![image-20211130092024391](https://i.loli.net/2021/11/30/9FY8rzxE2BqvyIC.png)



~~~C++
#include<iostream>
#include<cmath>
#include<vector>
using namespace std;

const int maxn = 100010;
struct node
{
    double data;
    vector<int> child;
}Node[maxn];
int n;
double p, r, ans=0; //ans为结点货物的价格之和

void DFS(int index, int depth)      //树的DFS
{
    if(Node[index].child.size()==0)   //到达叶结点即死胡同
    {
        ans+=Node[index].data*pow(1+r, depth);
        return ;
    }
    for(int i=0; i<Node[index].child.size();i++)
        DFS(Node[index].child[i], depth+1);     //递归访问子结点
}

int main()
{
    int k, child;
    cin>>n>>p>>r;
    r/=100;
    for(int i=0;i<n;i++)
    {
        cin>>k;
        if(k==0)
            cin>>Node[i].data;
        else
            for(int j=0;j<k;j++)
            {
                cin>>child;
                Node[i].child.push_back(child);
            }
    }

    DFS(0,0);
    printf("%.1f",p*ans);
}
~~~



#### 2_

![image-20211130094518275](https://i.loli.net/2021/11/30/PdhSVUBLKEf1b8w.png)



~~~C++
#include <iostream>
#include <cmath>
#include <vector>
using namespace std;

const int maxn = 110;
vector<int> Node[maxn];
int hashTable[maxn] = {0};

void DFS(int index, int level)
{
    hashTable[level]++;
    //这里不需要递归边界，因为通过for循环就可以保存每一层的结点树
    for (int i = 0; i < Node[index].size(); i++) //遍历所有孩子结点
        DFS(Node[index][i], level + 1);
}

int main()
{
    int n, m, parent, k, child;
    cin >> n >> m;
    for (int i = 0; i < m; i++)
    {
        cin >> parent >> k;
        for (int j = 0; j < k; j++)
        {
            cin >> child;
            Node[parent].push_back(child);
        }
    }
    DFS(1, 1); //根结点为1号， 层号为1
    int maxLevel = -1, maxValue = 0;
    for (int i = 1; i < maxn; i++)
    {
        if (hashTable[i] > maxValue)
        {
            maxValue = hashTable[i];
            maxLevel = i;
        }
    }
    cout << maxValue <<" "<< maxLevel;
}
~~~



#### 3_

![image-20211130102136973](https://i.loli.net/2021/11/30/MSnpztdBKYl3Jfq.png)



~~~C++
#include<iostream>
#include<cmath>
#include<vector>
using namespace std;

const int maxn = 110;
vector<int> Node[maxn];
int leaf[maxn] = {0};
int maxd=1;

void DFS(int index, int depth)
{
    maxd=max(maxd,depth);
    if(Node[index].size()==0)
    {
        leaf[depth]++;
        return ;
    }
    for(int i=0;i<Node[index].size();i++)
        DFS(Node[index][i],depth+1);
}

int main()
{
    int n, m;
    cin>>n>>m;
    for(int i=0;i<m;i++)
    {
        int parent, k;
        cin>>parent>>k;
        for(int j=0;j<k;j++)
        {
            int child;
            cin>>child;
            Node[parent].push_back(child);
        }
    }
    DFS(1,1);
    for(int i=1;i<=maxd;i++)
    {
        cout << leaf[i];
        if (i < maxd)
            cout << " ";
    }
}
~~~



#### 4_

![image-20211130163806010](https://i.loli.net/2021/11/30/mzJ27cnwD4FNELd.png)

![image-20211130163923045](https://i.loli.net/2021/11/30/lGwAgP1idkBWRF3.png)



>* 怎么确定递归边界？不能思维僵化，为什么以前都是看是否是叶子结点？因为题目的要求就是你在叶子结点的时候才进行操作，比如算链上的总金额。
>
>  * 这次为什么是sum？因为题目中的要求就是weight的和等于题中所给，递归边界自然就是了
>
>* 参数怎么判断？
>
>  * 递归边界是什么？参数中一定有能够和递归边界比较的参数sum
>
>  * 如何往下递归？参数中一定有能催动往下递归的参数index,
>  * numNode则是要输出path的必要条件，因为长度和内容都是在递归过程中动态改变的

~~~C++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
const int maxn = 110;
struct node
{
    int weight;
    vector<int> child;
} Node[maxn];

int n, m, s;
int path[maxn]; //记录路径

bool cmp(int a, int b)
{
    return Node[a].weight > Node[b].weight;
}

//参数一个是必须要有的索引，一个是当前路径上的结点个数
//没有它就无法输出合适的空格以及路径的赋值，一个是sum，用以递归边界的比较
void DFS(int index, int numNode, int sum)
{
    if (sum > s)
        return;
    if (sum == s)
    {
        if (Node[index].child.size() != 0)
            return;

        for (int i = 0; i < numNode; i++)
        {
            cout << Node[path[i]].weight; //path存的仅仅是结点的索引而已
            if (i < numNode - 1)
                cout << " ";
            else
                cout << endl;
        }
        return;
    }

    for (int i = 0; i < Node[index].child.size(); i++)
    {
        int child = Node[index].child[i];
        path[numNode] = child;
        DFS(child, numNode + 1, sum + Node[child].weight);
    }
}

int main()
{
    cin >> n >> m >> s;
    for (int i = 0; i < n; i++)
        cin >> Node[i].weight;
    for (int i = 0; i < m; i++)
    {
        int parent, k;
        cin >> parent >> k;
        for (int j = 0; j < k; j++)
        {
            int child;
            cin >> child;
            Node[parent].child.push_back(child);
        }
        sort(Node[parent].child.begin(), Node[parent].child.end(), cmp);
    }

    path[0] = 0; //路径中的第一个结点设置为0号结点
    DFS(0, 1, Node[0].weight);
    return 0;
}

~~~



### 二叉查找树

#### BST基本操作

##### 查找

~~~C++
void search(node *root, int x)
{
    if (root == NULL) //空树，查找失败
    {
        cout << "search failed" << endl;
        return;
    }
    if (x == root->data) //查找成功
        cout << root->data << endl;
    else if (x < root->data)
        search(root->lchild, x);
    else
        search(root->rchild, x);
}

~~~



##### 插入操作

~~~C++
//插入和查找一样的，只不过如果找不到就插入，找到了就退出
void insert(node* &root, int x)		//翻前面看看什么时候该引用？
{
    if(root == NULL)    //结点为空，即为插入位置
    {
        root = newnode(x);
        return;
    }
    if (x == root->data) //查找成功,说明已经有了要插入的结点
        return;
    else if (x < root->data)
        insert(root->lchild, x);
    else
        insert(root->rchild, x);
}
~~~



##### BST的建立

~~~C++
node *creat(int data[], int n)
{
    node *root = NULL;
    for (int i = 0; i < n; i++)
        insert(root, data[i]);
    return root;
}
~~~



##### BST的删除

> 删除的结点用前驱结点或者后继节点代替

~~~C++
node *findMax(node *root) //寻找root为根结点树中的最大权值结点
{
    while (root->rchild != NULL)
        root = root->rchild; //不断往右，找到最大结点
    return root;
}
node *findMin(node *root) //寻找root为根结点树中的最小权值结点
{
    while (root->lchild != NULL)
        root = root->lchild; //不断往左，找到最小结点
    return root;
}

//删除以root为根结点的树中权值为x的结点
void deleteNode(node *&root, int x)
{
    if (root == NULL) //不存在权值为x的结点
        return;
    if (root->data == x) //找到欲删除结点
    {
        if (root->lchild == NULL && root->rchild == NULL) //如果是叶子结点就直接删除
            root == NULL;
        else if (root->lchild != NULL) //左子树不为空
        {
            node *pre = findMax(root->lchild);   //找root前驱，即左子树中权值最大结点
            root->data = pre->data;              //用前驱覆盖root
            deleteNode(root->lchild, pre->data); //删除结点pre
        }
        else //右子树不为空
        {
            node *pre = findMin(root->rchild);   //找root后继，即右子树中权值最小结点
            root->data = pre->data;              //用后继覆盖root
            deleteNode(root->rchild, pre->data); //删除结点pre
        }
    }
    else if (x < root->data)
        deleteNode(root->lchild, x);
    else
        deleteNode(root->rchild, x);
}
~~~



#### BST性质

> * 中序遍历的结果是有序的





#### 1_

![image-20211202104620446](https://i.loli.net/2021/12/02/qht5Zy8jC69nexD.png)



> * 创建指针时，new和 =NULL的区别
>
>   * =null只是声明一个对象，并不实际占用空间。
>
>     如：HBPWDto hbpwDto = null; 相当于HBPWDto hbpwDto ;
>
>    * new，把对象实例化了，这个对象会一直占用空间（虽然不一定使用），直到被回收。
>
> * 如果只是做赋值操作，只需要声明就行了 ，在需要的时候再赋予具体的值
>
> * 如果是通过该对象做取值操作，就需要一开始就实例化，否则报nulpointException。
>
> * 这就是insert里面为什么要new，因为要进行root->data，必须要先有一个空间



~~~C++
#include <iostream>
#include <vector>
using namespace std;

struct node
{
    int data;
    node *lchild;
    node *rchild;
};

void insert(node *&root, int x)
{
    if (root == NULL)
    {
        root = new node; //这句不能落，因为此时是NULL，要重新new一个
        root->data = x;
        root->lchild = root->rchild = NULL;
        return;
    }
    if (x < root->data)
        insert(root->lchild, x);
    else
        insert(root->rchild, x);
}

void preorder(node *root, vector<int> &vi) //这里要引用，因为修改了vector的内容，不加只是传了个副本
{
    if (root == NULL)
        return;

    vi.push_back(root->data);
    preorder(root->lchild, vi);
    preorder(root->rchild, vi);
}

//镜像BST的先序只需要颠倒左右子树的访问次序就行了
void preorderMirror(node *root, vector<int> &vi)
{
    if (root == NULL)
        return;

    vi.push_back(root->data);
    preorderMirror(root->rchild, vi);
    preorderMirror(root->lchild, vi);
}

void postorder(node *root, vector<int> &vi)
{
    if (root == NULL)
        return;

    postorder(root->lchild, vi);
    postorder(root->rchild, vi);
    vi.push_back(root->data);
}

void postorderMirror(node *root, vector<int> &vi)
{
    if (root == NULL)
        return;

    postorderMirror(root->rchild, vi);
    postorderMirror(root->lchild, vi);
    vi.push_back(root->data);
}

vector<int> origin, pre, preM, post, postM;

int main()
{
    int n, data;
    node *root = NULL; //这个必须是NULL不能是new，因为insert比较的是root==NULL，如果不是NULL，第一层就循环不下去了
    cin >> n;
    for (int i = 0; i < n; i++) //建立BST
    {
        cin >> data;
        origin.push_back(data);
        insert(root, data);
    }

    preorder(root, pre);
    preorderMirror(root, preM);
    postorder(root, post);
    postorderMirror(root, postM);

    if (origin == pre)
    {
        cout << "YES" << endl;
        for (int i = 0; i < post.size(); i++)
        {
            cout << post[i];
            if (i < post.size() - 1)
                cout << " ";
        }
    }
    else if (origin == preM)
    {
        cout << "YES" << endl;
        for (int i = 0; i < postM.size(); i++)
        {
            cout << postM[i];
            if (i < postM.size() - 1)
                cout << " ";
        }
    }
    else
        cout << "NO" << endl;
}
~~~



#### 2_

![image-20211202205620954](https://i.loli.net/2021/12/02/4ROqN1IzsMY926w.png)



![image-20211202205641358](https://i.loli.net/2021/12/02/XAt5JET73Z89rzw.png)

> * 看到是完全二叉排序树，想到应该用上面的性质做
> * 另一重要性质是中序序列递增

~~~C++
#include <iostream>
#include <algorithm>

using namespace std;
const int maxn = 1010;
//因为BST的中序序列是递增的，所以先递增给出的序列，那么这就是BST中序的序列
//然后对二叉树进行中序遍历的同时，给CBT赋值，那么这就是完全二叉树的层次序列了
//因为中序遍历的情况下，一定是先遍历左子树，由于是复制操作，所以一定会赋值

//n为结点数， number用以存放结点权值，CBT存放完全二叉树
//index从小到大枚举number数组
int n, number[maxn], CBT[maxn], index = 0;

void inorder(int root)
{
    if (root > n) //空结点，直接返回
        return;

    inorder(root * 2);
    CBT[root] = number[index++];
    inorder(root * 2 + 1);
}

int main()
{
    cin >> n;
    for (int i = 0; i < n; i++)
        cin >> number[i];
    sort(number, number + n); //从小到大排序
    inorder(1);               //根结点必须设为1号
    for (int i = 1; i <= n; i++)
    {
        cout << CBT[i];
        if (i < n)
            cout << " ";
    }
}
~~~



#### 3_

![image-20211202210049400](https://i.loli.net/2021/12/02/rFHBwtm6J4p13e7.png)

![image-20211202210105257](https://i.loli.net/2021/12/02/lOXuFAxdtjC1HKh.png)



~~~C++
#include <iostream>
#include <queue>
#include <algorithm>

using namespace std;
const int maxn = 1010;

struct node
{
    int data;
    int lchild, rchild;
} Node[maxn];

int n, in[maxn], num = 0;

//这已经成模板了
//给定序列生成BST就用性质
//中序序列有序，先排序题中序列再匹配就行
void inorder(int root)
{
    if (root == -1)
        return;

    inorder(Node[root].lchild);
    Node[root].data = in[num++];
    inorder(Node[root].rchild);
}

void BFS(int root)
{
    queue<int> q;
    q.push(root);
    num = 0;
    while (!q.empty())
    {
        int top = q.front();
        q.pop();
        cout << Node[top].data;
        num++;
        if (num < n)
            cout << " ";
        if (Node[top].lchild != -1)
            q.push(Node[top].lchild);
        if (Node[top].rchild != -1)
            q.push(Node[top].rchild);
    }
}

int main()
{
    int lchild, rchild;
    cin >> n;
    for (int i = 0; i < n; i++)
    {
        cin >> lchild >> rchild;
        Node[i].lchild = lchild;
        Node[i].rchild = rchild;
    }

    for (int i = 0; i < n; i++)
        cin >> in[i];
    sort(in, in + n);
    inorder(0); //因为这时候不需要用2n和2n+1判断了，所以可以从0开始
    BFS(0);
}
~~~



### 平衡二叉树

#### AVL定义

~~~C++
struct node
{
    int value, height;
    node *lchild, *rchild;
};

//新建结点
node *newNode(int v)
{
    node *Node = new node;
    Node->value = v;
    Node->height = 1;
    Node->lchild = Node->rchild = NULL;
    return Node;
}

//获取高度
int getHeight(node *root)
{
    if (root == NULL)
        return 0;
    return root->height;
}

//计算平衡因子
int getBalanceFactor(node *root)
{
    //左子树高度减右子树高度
    return getHeight(root->lchild) - getHeight(root->rchild);
}

//更新结点root的height
void updateHeight(node *root)
{
    root->height = max(getHeight(root->lchild), getHeight(root->rchild)) + 1;
}
~~~



#### AVL基本操作

##### 查找

~~~C++
//同BST
void search(node *root, int x)
{
    if (root == NULL) //空树，查找失败
    {
        cout << "search failed" << endl;
        return;
    }
    if (x == root->data) //查找成功
        cout << root->data << endl;
    else if (x < root->data)
        search(root->lchild, x);
    else
        search(root->rchild, x);
}
~~~

##### 插入

![image-20211203085012960](https://i.loli.net/2021/12/03/KLe5HwizQy6c1Wf.png)

~~~C++
//左旋
void L(node *&root)
{
    node *temp = root->rchild;
    root->rchild = temp->lchild;
    temp->lchild = root;
    updateHeight(root); //要先更新root，因为此时temp变成根结点，要先知道root的高度
    updateHeight(temp);
    temp = root;
}

//右旋
void R(node *&root)
{
    node *temp = root->lchild;
    root->lchild = temp->rchild;
    temp->rchild = root;
    updateHeight(root); //要先更新root，因为此时temp变成根结点，要先知道root的高度
    updateHeight(temp);
    temp = root;
}

void insert(node *&root, int v)
{
    if (root == NULL)
    {
        root = newNode(v);
        return;
    }
    if (v < root->value) //往左插入，左子树高度增加
    {
        insert(root->lchild, v);
        updateHeight(root);              //更新树高，这也是递归更新
        if (getBalanceFactor(root) == 2) //进行旋转
        {
            if (getBalanceFactor(root->lchild) == 1) //LL型
            {
                R(root); //结点右旋
            }
            else if (getBalanceFactor(root->lchild) == -1) //LR型
            {
                L(root->lchild); //先平衡因子为-1的结点左旋变成LL型
                R(root);
            }
        }
    }
    else //往右插入，右子树高度增加
    {
        insert(root->rchild, v);
        updateHeight(root);
        if (getBalanceFactor(root) == -2)
        {
            if (getBalanceFactor(root->rchild) == -1) //RR型
            {
                L(root);
            }
            else if (getBalanceFactor(root->rchild) == 1) //RL型
            {
                R(root->rchild);
                L(root);
            }
        }
    }
}
~~~

#### AVL建立

~~~C++
node *creat(int data[], int n)
{
    node *root = NULL; //这里必须是NULL，因为insert判断条件是NULL
    for (int i = 0; i < n; i++)
        insert(root, data[i]);
    return root;
}
~~~



#### 1_

![image-20211203100200053](https://i.loli.net/2021/12/03/71LiMjOE6s3WbVP.png)

​	

~~~C++
#include <iostream>
using namespace std;

const int maxn = 21;
int arr[maxn];
int n;

struct node
{
    int value, height;
    node *lchild, *rchild;
};

node *newNode(int v)
{
    node *root = new node;
    root->value = v;
    root->height = 1;
    root->lchild = root->rchild = NULL;
    return root;
}

int getHeight(node *root)
{
    if (root == NULL)
        return 0; //空结点高度为0
    return root->height;
}

int getBalanceFactor(node *root)
{
    return getHeight(root->lchild) - getHeight(root->rchild);
}

void updateHeight(node *root)
{
    root->height = max(getHeight(root->lchild), getHeight(root->rchild)) + 1;
}

void L(node *&root) //改变了AVL，所以要加引用
{
    node *temp = root->rchild;
    root->rchild = temp->lchild;
    temp->lchild = root;
    updateHeight(root);
    updateHeight(temp);
    root = temp; //别忘了这句话，最后根结点要设为temp
}

void R(node *&root)
{
    node *temp = root->lchild;
    root->lchild = temp->rchild;
    temp->rchild = root;
    updateHeight(root);
    updateHeight(temp);
    root = temp;
}

void insert(node *&root, int v)
{
    if (root == NULL)
    {
        root = newNode(v); //指向空的指针指向新结点
        return;
    }

    if (v < root->value)
    {
        insert(root->lchild, v);
        updateHeight(root);
        if (getBalanceFactor(root) == 2)
        {
            if (getBalanceFactor(root->lchild) == 1)
            {
                R(root);
            }
            else if (getBalanceFactor(root->lchild) == -1)
            {
                L(root->lchild);
                R(root);
            }
        }
    }
    else
    {
        insert(root->rchild, v);
        updateHeight(root);
        if (getBalanceFactor(root) == -2)
        {
            if (getBalanceFactor(root->rchild) == -1)
            {
                L(root);
            }
            else if (getBalanceFactor(root->rchild) == 1)
            {
                R(root->rchild);
                L(root);
            }
        }
    }
}

node *creat(int data[], int n)
{
    node *root = NULL;
    for (int i = 0; i < n; i++)
        insert(root, data[i]);
    return root;
}

int main()
{
    cin >> n;
    for (int i = 0; i < n; i++)
        cin >> arr[i];
    node *root = creat(arr, n);
    cout << root->value << endl;
}
~~~



### 并查集

#### 定义

![image-20211206083453881](https://s2.loli.net/2021/12/06/cpvmFZMPD3wOV5a.png)



#### 基本操作

~~~C++
//初始化
for (int i = 1; i <= n; i++)
{
    father[i] = i;
}

//查找：  返回元素x所在集合的根结点
int findFather(int x)
{
    int a = x;
    while (x != father[x]) //如果不是根结点
        x = father[x];     //继续循环

    //此时x存放的就是根结点
    //对其进行路径压缩
    //即把所有结点的父节点都设置成根结点，降低之后查找的复杂度
    while(a != father[a])
    {
        int z = a ;
        a = father[a];    //回溯父亲结点
        father[z] = x;
    }

}

//合并
void union(int a, int b)
{
    int faA = findFather(a);
    int faB = findFather(b);

    if (faA != faB) //如果不属于同一个集合
        father[faA] = faB;
}
~~~



#### 1_

![image-20211206091750792](https://s2.loli.net/2021/12/06/b42QHVRcaPugD6X.png)

![image-20211206091811873](https://s2.loli.net/2021/12/06/jESKq2wN8daYckf.png)



~~~C++
#include <iostream>
using namespace std;

//就是通过判断根结点，一个集合只有1个根结点
//findFather寻找元素的根结点，一个集合中不同元素的根结点相同

const int maxn = 110;
int father[maxn];
bool isRoot[maxn];

void init(int n)
{
    for (int i = 1; i <= n; i++)
    {
        father[i] = i;
        isRoot[i] = false;
    }
}

int findFather(int x)
{
    while (x != father[x])
        x = father[x];
    return x;
}

void Union(int a, int b)
{
    int faA = findFather(a);
    int faB = findFather(b);

    if (faA != faB)
        father[faA] = faB;
}

int main()
{
    int n, m, a, b;
    cin >> n >> m;
    init(n);
    for (int i = 0; i < m; i++)
    {
        cin >> a >> b;
        Union(a, b);
    }
    for(int i=1;i<=x;i++)
        isRoot(findFather[i]) = true;
    int ans = 0;
    for(int i=1;i<=n;i++)
        ans+=isRoot[i];
    cout<<ans;
}
~~~



#### 2_

![image-20211206102300889](https://s2.loli.net/2021/12/06/VtiQGzobqn76CxZ.png)



~~~C++
#include <iostream>
#include <algorithm>
using namespace std;

const int maxn = 1010;
int father[maxn];
int isRoot[maxn] = {0};
int course[maxn] = {0};

bool cmp(int a, int b)
{
    return a > b;
}

int findFather(int x)
{
    while (x != father[x])
        x = father[x];

    return x;
}

void Union(int a, int b)
{
    int faA = findFather(a);
    int faB = findFather(b);

    if (faA != faB)
        father[faA] = faB;
}

void init(int n)
{
    for (int i = 1; i <= n; i++)
    {
        father[i] = i;
        isRoot[i] = 0;
    }
}

int main()
{
    int n;
    cin >> n;
    init(n);
    for (int i = 1; i <= n; i++)
    {
        int k;
        scanf("%d:", &k);
        for (int j = 0; j < k; j++)
        {
            int temp;
            cin >> temp;
            if (course[temp] == 0) //这步很重要，如果是第一次喜欢就course[temp] = i 否则就和上一个联合
                course[temp] = i;
            Union(i, course[temp]);
        }
    }

    for (int i = 1; i <= n; i++)
        isRoot[findFather(i)]++;

    int ans = 0;
    for (int i = 1; i <= n; i++)
        if (isRoot[i] != 0)
            ans++;

    cout << ans << endl;

    sort(isRoot+1, isRoot + n+1, cmp);

    for (int i = 1; i <= ans; i++)
    {
        cout << isRoot[i];
        if (i < ans)
            cout << " ";
    }
}
~~~



### 堆

#### 基本操作

~~~C++
#include <iostream>
using namespace std;

const int maxn = 100;
int heap[maxn], n = 10;

//向下调整
//low为调整结点的数组下标， high为堆最后一个元素的数组下标
void downAdjust(int low, int high)
{
    int i = low, j = i * 2;
    while (j <= high) //存在孩子节点
    {
        //如果有孩子存在，并且值大于左孩子
        if (j + 1 <= high && heap[j + 1] > heap[j])
            j = j + 1;

        //孩子中的最大值大于想调整的结点
        if (heap[j] > heap[i])
        {
            swap(heap[j], heap[i]);
            i=j;
            j=i*2;
        }
        else
            break;
    }
}

//建堆
void creatHeap()
{
    for(int i = n/2;i>=1;i--)
        downAdjust(i,n);
}

//删除堆顶元素
void deleteTop()
{
    heap[1] = heap[n--];    //用最后一个元素覆盖堆顶元素，并且让元素个数-1
    downAdjust(1,n);
}


//添加元素
//low一般为1，high为新添加结点的数组下标
void upAdjust(int low, int high)
{
    int i=high, j=i/2;
    while(j>=low)
    {
        if(heap[j]<heap[i])
        {
            swap(heap[j],heap[i]);
            i=j;
            j=i/2;
        }
        else
            break;
    }
}

void insert(int x)
{
    heap[++n] = x;
    upAdjust(1,n);
}

void heapSort()
{
    creatHeap();
    for(int i=n;i>1;i--)
    {
        swap(heap[i],heap[1]);
        downAdjust(1,i-1);
    }
}
~~~




#### 1_

![image-20211207093050837](https://s2.loli.net/2021/12/07/EFHcD9KqQUvb35M.png)



~~~C++
#include <iostream>
#include <algorithm>
using namespace std;

const int maxn = 110;
int origin[maxn], tempOri[maxn], changed[maxn]; //原始数组，原始数组备份，目标数组
int n;

void showArr(int A[])
{
    for (int i = 1; i <= n; i++)
    {
        cout << A[i];
        if (i < n)
            cout << " ";
    }
}
bool isSame(int A[], int B[])
{
    for (int i = 1; i <= n; i++)
    {
        if (A[i] != B[i])
            return false;
    }
    return true;
}

bool insertionSort()
{
    int flag = 0;
    for (int p = 2; p <= n; p++)
    {
        if (p != 2 && isSame(tempOri, changed))
            flag = 1;
        int tmp = tempOri[p];
        int i = p - 1;
        while (i >= 1 && tempOri[i] > tmp)
        {
            tempOri[i + 1] = tempOri[i];
            i--;
        }
        tempOri[i + 1] = tmp;
        if (flag == 1)
            return 1;
    }
    return 0;
}

void downAdjust(int low, int high)
{
    int i = low, j = i * 2;
    while (j <= high)
    {
        if (j + 1 <= high && tempOri[j + 1] > tempOri[j])
            j = j + 1;
        if (tempOri[j] > tempOri[i])
        {
            swap(tempOri[j], tempOri[i]);
            i = j;
            j = i * 2;
        }
        else
            break;
    }
}

void creat()
{
    for (int i = n / 2; i >= 1; i--)
        downAdjust(i, n);
}
void heapSort(int n)
{
    bool flag = false;
    creat();
    for (int i = n; i > 1; i--)
    {
        if (i != n && isSame(tempOri, changed))
            flag = true;
        swap(tempOri[1], tempOri[i]);
        downAdjust(1, i - 1);   //调整堆顶  这里是i-1，动态变化的
        if (flag == true)
        {
            showArr(tempOri);
            return;
        }
    }
}

int main()
{
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> origin[i];
        tempOri[i] = origin[i];
    }
    for (int i = 1; i <= n; i++)
        cin >> changed[i];

    if (insertionSort())
    {
        cout << "Insertion Sort" << endl;
        showArr(tempOri);
    }
    else
    {
        cout << "Heap Sort" << endl;
        for (int i = 1; i <= n; i++)
            tempOri[i] = origin[i];
        heapSort(n);
    }
}
~~~



## 图算法专题

### 图的DFS遍历

~~~C++
#include <iostream>
#include <algorithm>
using namespace std;

//邻接矩阵
const int MAXV = 1000;		//最大顶点数
const int INF = 1000000000; //INF为一大数

int n, G[MAXV][MAXV];
bool vis[MAXV] = {false}; //如果顶点已被访问，则为true

void DFS(int u, int depth) //当前访问顶点， 深度
{
	vis[u] = true;
	//此处写操作
	//对所有从u出发能到达的分支顶点进行枚举
	for (int v = 0; v < n; v++)	//对每个顶点V
	{
		if(vis[v]==false&&G[u][v]!=INF)
			DFS(v,depth+1);
	}
}

void DFSTrave()	//遍历图G
{
	for(int u=0;u<n;u++)
		if(vis[u] == false)
			DFS(u,1);
}
~~~

~~~C++
#include <iostream>
#include <algorithm>
using namespace std;

//邻接表
const int MAXV = 1000;		//最大顶点数
const int INF = 1000000000; //INF为一大数

int n;
vector<int> Adj[MAXV];	//邻接表
bool vis[MAXV] = {false}; //如果顶点已被访问，则为true

void DFS(int u, int depth)
{
	vis[u] = true;
	for(int i=0;i<Adj[u].size();i++)
	{
		int v = Adj[u][i];
		if(vis[v] == false)
			DFS(v, depth + 1);
	}
}

void DFSTrave()
{
	for(int u=0; u<n;u++)
		if(vis[u] == false)
			DFS(u,1);
}
~~~

#### 1_

![image-20220101193952733](https://s2.loli.net/2022/01/01/ZypciW7YV1q49OM.png)



~~~C++
#include <iostream>
#include <string>
#include <map>

using namespace std;
const int maxn = 2010;

map<int, string> intToString;
map<string, int> stringToInt;
map<string, int> Gang; //head -> 人数

int G[maxn][maxn] = {0}, weight[maxn] = {0};
int n, k, numPerson = 0;  //边数， 阈值， 总人数
bool vis[maxn] = {false}; //标记是否被访问

//访问单个连通块
void DFS(int nowVisit, int &head, int &numMember, int &totalValue)
{
	numMember++;		  //成员人数+1
	vis[nowVisit] = true; //nowvisit已访问
	if (weight[nowVisit] > weight[head])
		head = nowVisit;

	for (int i = 0; i < numPerson; i++) //枚举所有人
	{
		if (G[nowVisit][i] > 0) //如果nowVisit能到达i
		{
			totalValue += G[nowVisit][i];
			G[nowVisit][i] = G[i][nowVisit] = 0; //注意这个 防止回头重新计算，是环路的原因
												 
			if (vis[i] == false)
				DFS(i, head, numMember, totalValue);
		}
	}
}

//遍历每个图
void DFSTrave()
{
	for (int i = 0; i < numPerson; i++)
		if (vis[i] == false)
		{
			int head = i, numMember = 0, totalValue = 0;
			DFS(i, head, numMember, totalValue);
			if (numMember > 2 && totalValue > k)
				Gang[intToString[head]] = numMember;
		}
}

int change(string s) //int string通过map的相互转化
{
	if (stringToInt.find(s) != stringToInt.end())
		return stringToInt[s];
	else
	{
		stringToInt[s] = numPerson;
		intToString[numPerson] = s;
		return numPerson++;
	}
}

int main()
{
	int w;
	string str1, str2;
	cin >> n >> k;
	for (int i = 0; i < n; i++)
	{
		cin >> str1 >> str2 >> w;
		int id1 = change(str1);
		int id2 = change(str2);
		weight[id1] += w;
		weight[id2] += w;
		G[id1][id2] += w;
		G[id2][id1] += w;
	}
	DFSTrave();
	cout << Gang.size() << endl;
	map<string, int>::iterator it;
	for (it = Gang.begin(); it != Gang.end(); it++)
		cout << it->first << " " << it->second << endl;
}
~~~



#### 2_

![image-20220102153443433](https://s2.loli.net/2022/01/02/PU3T9NgzqZrn2mx.png)



~~~C++
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;

const int maxn = 1010;
vector<int> G[maxn];
bool vis[maxn] = {false};

int currentPoint;
void DFS(int v) //这里DFS的作用其实就是标记被访问，然后计算出连通图的个数
{
	if (v == currentPoint)
		return;

	vis[v] = true;
	for (int i = 0; i < G[v].size(); i++)
	{
		if (vis[G[v][i]] == false)
			DFS(G[v][i]);
	}
}

int main()
{
	int n, m, k;
	cin >> n >> m >> k;
	for (int i = 0; i < m; i++)
	{
		int a, b;
		cin >> a >> b;
		G[a].push_back(b);
		G[b].push_back(a);
	}

	for (int i = 0; i < k; i++)
	{
		cin >> currentPoint;
		memset(vis, false, sizeof(vis));
		int block = 0; //连通块的个数
		for (int i = 1; i <= n; i++)
		{
			if (i != currentPoint && vis[i] == false)
			{
				DFS(i);
				block++;
			}
		}
		cout << block - 1 << endl;
	}
}
~~~





### 图的BFS遍历

~~~C++
#include <iostream>
#include <queue>
using namespace std;

const int MAXV = 1000;
//邻接矩阵
int n, G[MAXV][MAXV];
bool inq[MAXV] = {false};

void BFS(int u)
{
	queue<int> q;
	q.push(u);
	inq[u] = true;
	while (!q.empty())
	{
		int u = q.front(); //去除队首元素
		q.pop();		   //队首元素出队
		for (int v = 0; v < n; v++)
		{
			if (inq[v] == false && G[u][v] != INF)
			{
				q.push(v);
				inq[v] = true;
			}
		}
	}
}

void BFSTrave()
{
	for (int u = 0; u < n; u++)
	{
		if (inq[u] != false)
			BFS(u);
	}
}
~~~



~~~C++
#include <iostream>
#include <queue>
using namespace std;

const int MAXV = 1000;
//邻接表
vector<int> Adj[MAXV];
int n;
bool inq[MAXV] = {false};

void BFS(int u)
{
	queue<int> q;
	q.push(u);
	inq[u] = true;
	while(!q.empty())
	{
		int u = q.front();
		q.pop();
		for(int i=0;i<Adj[u].size();i++)
		{	
			int v = Adj[u][i];
			if(inq[v] == false)
			{
				q.push(v);
				inq[v] = true;
			}
		}
	}
}

void BFSTrave()
{
	for (int u = 0; u < n; u++)
	{
		if (inq[u] != false)
			BFS(u);
	}
}
~~~



#### 1_

![image-20220102142627734](https://s2.loli.net/2022/01/02/5HleT9GoaY72Bmz.png)



~~~C++
#include <iostream>
#include <cstring>
#include <vector>
#include <queue>
using namespace std;

const int MAXV = 1010;

struct Node
{
	int id;
	int Layer;
};
vector<Node> Adj[MAXV];
bool inq[MAXV] = {false};

int BFS(int s, int L)
{
	int numForward = 0; //转发数
	queue<Node> q;
	Node start;
	start.id = s;
	start.Layer = 0;
	q.push(start);
	inq[start.id] = true;
	while (!q.empty())
	{
		Node top = q.front();
		int u = top.id;
		q.pop();
		for (int i = 0; i < Adj[u].size(); i++)
		{
			Node next = Adj[u][i];
			next.Layer = top.Layer + 1;
			if (inq[next.id] == false && next.Layer <= L)
			{
				q.push(next);
				inq[next.id] = true;
				numForward++;
			}
		}
	}

	return numForward;
}

int main()
{
	Node user;
	int n, L, numFollow, idFollow;
	cin >> n >> L;
	//建立邻接表
	for (int i = 1; i <= n; i++)
	{
		user.id = i;
		cin >> numFollow;
		for (int j = 0; j < numFollow; j++)
		{
			cin >> idFollow;
			Adj[idFollow].push_back(user);
		}
	}
	int numQuery, s;
	cin >> numQuery;
	for (int i = 0; i < numQuery; i++)
	{
		memset(inq, false, sizeof(inq)); //因为每次循环后queue要初始化的，要不上一次的数据还在
		cin >> s;
		int numForward = BFS(s, L);
		cout << numForward << endl;
	}
}
~~~








