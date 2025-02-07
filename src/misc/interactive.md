# Introduction to Interactive Problems

## What are Interactive Problems?

以下我們稱選手上傳的程式為 Participant、Judge 端為 Jury。

我們平常寫的題目稱為 Batch 類型，也就是 Jury 給 Input，Participant 計算 Output，Jury 再檢視 Output 的正確性。

而對於 Interactive 類型的題目，Jury 會和 Participant 溝通、互動，Jury 給的 Input 很大一部份是由 Participant 的 Output 決定的。

常見的互動形式(如 Codeforces 所使用的互動形式)有透過 standard IO 來傳遞訊息，在此情況下每次輸出後需要記得 flush 緩衝區，這樣 Jury 才能夠正確讀取 Participant 的輸出。

還有多種互動方式如使用標頭檔呼叫函式等等，端看使用的解題平台(如 OJ.uz)。

這類題目通常會限制 Participant 跟 Jury 溝通的次數，要求你在有限的次數內猜到 Jury 藏著的數字、或引導 Jury 到某個狀態之類的。

比起這樣抽象的理解，不如我們直接來看幾個範例吧。

### Example: Binary Search

> Jury 藏著一個數字 \\(x\\)，Participant 可以問 Jury 一個數字 \\(y\\)，Jury 會回答以下三種情況是否滿足：
>
> - \\(x < y\\)
> - \\(x > y\\)
> - \\(x = y\\)
>
> 其中 \\(1 \le x \le 1000\\)，Participant 想在 \\(10\\) 次詢問以內猜到 \\(x\\) 的值。

<details><summary>Solution</summary>

同一般二分搜的分析，我們希望每次詢問將 \\(x\\) 的範圍縮的愈小愈好。

假設當前 \\(x\\) 可能在區間 \\([l, r]\\)，若我們詢問到的 \\(y \neq x\\)，那 \\(x\\) 可能在的區間會是 \\([l, y-1]\\) 或 \\( [y+1, r] \\)。

若我們考慮最差的情況，也就是 \\(x\\) 永遠落在有最多可能的區間，那不難知道 \\(y = \lfloor \dfrac{l+r}{2} \rfloor\\) 會是最佳的選擇，而我們每次詢問都能把 \\(x\\) 可能在的區間長度減少一半，於是我們最多會問 \\(\lceil lg1000\rceil = 10\\) 次。
</details>

### Example: Chasing

> Jury 在一棵樹 \\(T\\) 上的某個節點，一開始，Jury 會告訴 Participant 它所在的節點 \\(r\\)，跟樹 \\(T\\) 的所有細節。
>
> Jury 跟 Participant 會輪流行動，每回合，Participant 可以把一個 Jury 不在的節點塗黑。而 Jury 會往它所在節點的其中一個非黑色的鄰居移動，並將原先在的節點塗黑，並告訴 Participant 它所在的節點。
>
> Participant 的目標是：在最少的回合數內，將 Jury 困住，即 Jury 無法再往其他節點移動。

<details><summary>Solution</summary>

我們將 \\(T\\) 考慮為一棵以 \\(r\\) 為根的樹，則我們可以用 \\(f(i)\\) 代表 Jury 在 \\(i\\) 這個節點且 \\(i\\) 的父節點已經被塗黑時，Jury 最多能走幾步。

不難發現，\\(f(i)\\) 會是 \\(\{f(c) | c \in child_i\}\\) 這個集合中次大的 -- 令其為 \\(f(u)\\) -- 再加上 \\(1\\)。

因假設 \\(f(v)\\) 是該集合中最大的，對於 Participant 來說，若它塗黑的節點不在 \\(v\\) 的子樹中，那 Jury 自然會往接下來能有有最大步數的 \\(v\\) 走。而若塗黑的節點在 \\(v\\) 的子樹中卻不是 \\(v\\)，那 Jury 最差也能往 \\(u\\) 走。

所以 Participant 最佳的策略一定是將 \\(v\\) 塗黑，而我們能夠用計算出的 \\(f(i)\\) 與 Jury 互動，決定每回合該塗黑哪個節點。
</details>

### Summary

上面介紹了兩個不同類型的 Interactive 題目，Binary Search 是典型的猜數字題目，即 Jury 有一些隱藏的資訊，Participant 需要透過一些詢問猜出 Jury 隱藏的資訊。

後者則是與 Jury 互動，要迫使 Jury 達到某個狀態（不能再走）的下棋類題目。

以下我們將介紹遇到 Interactive 題目時的一些思路。

## Solving Techniques

儘管 Interactive 只是不同類型的輸入輸出模式，理論上來說解 Batch 的技巧就會是解 Interactive 的技巧。但我們還是能夠常常觀察到 Interactive 題目間的相似之處，而不只是毫無章法地把所有在 Batch 的考點都出成 Interactive。

以下，我們將介紹一些關於 Interactive 的基礎實作技巧和常見的思考方向，希望能給讀者一些啟發。

### Implementation

以下兩個小技巧我認為能夠大部份時候幫助 Interactive Problem 的實作。

#### 把問過的詢問存起來

這樣的好處是，在寫 code 的時候不用考慮問問題的次數，只要證明問過的組合不會超過某個上界就好了。

#### 把 query 包成一個 function

其實大部份時候會問的 query 都會符合某些 pattern，把它包成 function 可以有效降低 coding 的複雜度。

就算 query 沒有什麼規律可言，包成 function 也可以減少在主要的 procedure 中處理互動，會讓 code 好看一點。

### Binary Search

> [Codeforces 809B - Glad to see you!](https://codeforces.com/problemset/problem/809/B)
>
> Jury 有一組大小為 \\(k\\) 的集合，集合內的元素為 \\(1 \sim n\\) 且不重複的數字。> Jury 會將 \\(k\\) 跟 \\(n\\) 告訴 Participant。> Participant 可以問 Jury 的問題是：給兩個數字 \\(x, y\\)，Jury 將會在它的集合中找到跟 \\(x\\) 最接近的數字 \\(a\\)、跟 \\(y\\) 最接近的數字 \\(b\\)（\\(a\\) 可能等於 \\(b\\)），並回答是否滿足 \\(|x-a| \le |y-b|\\)。> Participant 需要在 \\(60\\) 個詢問內找到該集合中的任意兩個數字，其中 \\(2 \le k \le n \le 10^5\\)。

<details><summary>Solution</summary>

先考慮如何找到一個在集合中的元素，已知在區間 \\([l, r]\\) 中有至少一個元素，則我們可以詢問 \\(x = mid, y = mid+1\\)，其中 \\(mid = \lfloor \dfrac{l+r+1}{2} \rfloor \\)。如此一來，我們能知道 \\([l, mid]\\) 或 \\([mid+1, r]\\) 含有至少一個元素，如此一來遞迴下去，我們就能知道至少一個元素，令此元素為 \\(h\\)。

\\(h\\) 將區間 \\([1, n]\\) 分隔成了區間 \\([1, h-1]\\) 跟 \\([h+1, n]\\)，我們可以用一樣的方式遞迴找出各區間中的元素，但有可能此區間不包含任何元素，所以我們還需要用額外的一個詢問來確認找到的元素是否真的存在。

```cpp=
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;

const ll N = 2e5 + 5;
const ll MOD = 1e9 + 7;
const ld pi = acos(-1);
const ll INF = (1LL<<60);

ll test(ll pos, ll flag = 0) {
    string ret;
    if (flag == 0) 
        cout << "1 " << pos-1 << " " << pos << "\n";
    else
        cout << "1 " << pos << " " << pos-1 << "\n";
    cin >> ret;
    return (flag ^ (ret == "NIE") ? 1 : 0);
}

ll isValid(ll pos, ll c) {
    string ret;
    cout << "1 " << pos << " " << c << "\n";
    cin >> ret;
    return (ret == "TAK");
}

ll get(ll l, ll r, ll flag = 0) { // [l, r)
    while (l < r-1) {
        ll mid = (l + r) >> 1;
        //cout << "Getting " << l << " " << r << " " << mid << "\n";
        if (!test(mid, flag))
            r = mid;
        else 
            l = mid;
    }
    return l;
}

void solve() {
    ll n, k;
    cin >> n >> k;
    ll a = get(1, n+1);
    ll b = get(1, a, 0), c = get(a+1, n+1, 1);
    if (b != a && isValid(b, a)) 
        cout << "2 " << a << " " << b << "\n";
    else
        cout << "2 " << a << " " << c << "\n";
}

int main () {
    solve();
    return 0;
}
```

以上的 code 中，唯一跟互動有關聯的 function 只有 test 跟 isValid 而已。

我令 test 回傳距離 pos 最近的數字是在左邊還是右邊，並用 flag 來控制如果左右兩邊一樣近的話要選誰。

而 isValid 是用在已經找到某個數字的情況下，確認任意數字是否存在集合內的 function。

get 這個函式則負責尋找在區間 \\([l, r)\\) 中任意一個存在集合中的數字。

</details>

### Random

> [Codeforces 843B - Interactive LowerBound](https://codeforces.com/problemset/problem/843/B)
>
> Jury 有一個 Link-List \\(L\\)，\\(L\\) 的每個節點都有其編號。> 每個節點 \\(i\\) 上存有這個節點指向的下一個節點編號 \\(nxt_i\\)，跟它所紀錄的值 \\(val_i\\)。> 已知，此 Link-List 紀錄的值是遞增的，Participant 每次詢問能夠詢問任意編號 \\(i\\) 的 \\(nxt_i\\) 跟 \\(val_i\\)。> 節點編號範圍是 \\(1 \sim n\\)，其中 \\(1 \le n \le 50000\\)，Participant 需在 \\(2000\\) 次詢問內找到某一給定 \\(x\\) 的 Lower bound。> 一開始，Jury 會告訴 Participant 這個 Link-List 起點的編號跟 \\(n\\)。

<details><summary>Solution</summary>

我們可以先詢問開始節點和其他 999 個隨機節點，在其中選擇最大的小於或等於 \\(x\\) 的 \\(val_i\\)，並以此節點 \\(i\\) 作為起點順序遍歷 Link-List，直到第一個 \\(val\\) 大於或等於 \\(x\\) 的。那麼該節點就會是答案。

此方法找不到正確的 Lower bound 的機率可以估計為：在正確答案之前的 \\(1000\\) 個節點，沒有任何一個被選中為隨機選擇的 \\(999\\)個節點，約為 \\((1-\dfrac{999}{n})^{1000} \approx 1.7 \times 10^{-9} \\)。

```cpp=
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<ll, ll> pll;
#define pb push_back 
#define mp make_pair
#define fi first
#define se second
#define SZ(_a) (ll)(_a).size()

const ll N = 5e4 + 5;
const ll MOD = 1e9 + 7;
const ll K = 1500;

ll n, start, x, ans = -1;
vector<ll> lis;
ll val[N], nxt[N];

void solve() {
    srand(clock());
    cin >> n >> start >> x;
    for (ll i = 1;i <= n; ++i) {
        if (i == start) continue;
        lis.pb(i);
    }
    random_shuffle(all(lis));
    lis.pb(start);
    reverse(all(lis));
    memset(val, -1, sizeof(val));
    for (ll i = 0;i < min(SZ(lis), K); ++i) {
        ll y = lis[i];
        cout << "? " << lis[i] << "\n";
        cin >> val[y] >> nxt[y];
        if (val[y] <= x && (ans == -1 || x-val[ans] >= x-val[y])) {
            ans = y;
        }
    }
    if (val[start] >= x) {
        cout << "! " << val[start] << "\n";
        return ;
    }
    while (ans != -1 && val[ans] < x) {
        if (nxt[ans] == -1) {
            ans = -1;
            break;
        }
        ans = nxt[ans];
        if (val[ans] == -1) {
            cout << "? " << ans << "\n";
            cin >> val[ans] >> nxt[ans];
        }
    }
    if (ans != -1) ans = val[ans];
    cout << "! " << ans << "\n";
}

int main () {
    solve();
    return 0;
}
```

這題由於詢問很單純，於是我就不把詢問包成 function 了。

code 中的 K 代表我們隨機遍歷的節點數。

</details>

### Parity

> [Codeforces 835E - The penguin's game](https://codeforces.com/problemset/problem/835/E)
>
> Jury 有一集合 \\(S\\)，其中恰兩個元素是 \\(y\\)，其他則是 \\(x\\)，Jury 會告訴 Participant \\(x, y\\) 分別是多少。> Participant 每次詢問能問 Jury，\\(S\\) 的一個 subset 的所有元素的 xor 值。> 請 Participant 在 \\(19\\) 次詢問內找到兩個 \\(y\\) 所在的位置，其中 \\(2 \le |S| \le 1000\\)。

<details><summary>Solution</summary>

- \\(x, y\\) 分別是多少其實不重要，我們令 \\(x = 0, y = 1\\)。

考慮我們隨便問一個 subset，能從他的答案裡知道什麼：

- \\(0\\)：要麼兩個 \\(y\\) 都在此 subset，要麼兩個 \\(y\\) 都在此 subset 的 complement。
- \\(1\\)：恰一個 \\(y\\) 在此 subset，恰一個 \\(y\\) 在此 subset 的 complement。

已知若某個 subset 裡只有一個 \\(y\\)，則我們可以用二分搜的方式知道此 \\(y\\) 的位置。

所以我們好像該試著用詢問把兩個 \\(y\\) 先分開，也就是嘗試得到一個 \\(1\\) 的答覆。

又已知兩個 \\(y\\) 的位置至少有一個 bit 不同，則我們可以枚舉每個 bit，詢問此 bit 是 0 的 subset。

則我們能夠在 \\(10\\) 次內將此兩個 \\(y\\) 分開來，接著能再用 \\(2 \times 9\\) 的詢問次數在此兩個 subset 內二分搜出 \\(y\\) 的位置。

但這樣太多詢問了，回想我們枚舉 bit 時，回覆是 \\(0\\) 的詢問其實告訴了我們這兩個 \\(y\\) 的某個 bit 是相同的，所以我們知道這兩個 \\(y\\) 的位置 xor 的結果。我們就只要搜一個 subset，再 xor 得到另一個 \\(y\\) 的位置就好了。

```cpp=
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<ll, ll> pll;
typedef long double ld;
#define pb push_back 
#define mp make_pair
#define fi first
#define se second
#define lwb lower_bound
#define SZ(_a) (ll)(_a).size()
#define SQ(_a) ((_a)*(_a))
#define all(_a) (_a).begin(), (_a).end()

const ll N = 2e5 + 5;
const ll MOD = 1e9 + 7;
const ll INF = (1LL<<60);

ll n, x, y;

ll ask(vector<ll>& rset) {
    if (SZ(rset) == 0) return 0;
    cout << "? " << SZ(rset) << " ";
    for (ll i = 0;i < SZ(rset); ++i) cout << rset[i]+1 << " \n"[i==SZ(rset)-1];
    ll ret = 0;
    cin >> ret;
    return (ret == (x^y) || ret == y);
}

void solve() {
    ll m = 0, l = 0, ans = 0, diff = 0;
    cin >> n >> x >> y;
    for (ll level = 0; (1<<level) < n; ++level) {
        vector<ll> rset;
        for (ll i = 0;i < n; ++i) if ((i>>level)&1) rset.pb(i);
        diff |= (ask(rset) << level);
        if ((diff>>level)&1) l = level;
        m = level;
    }
    for (ll level = m; level >= 0; --level) {
        vector<ll> rset;
        if (level > l) {
            for (ll i = 0;i < n; ++i) if (((i>>level)&1) && ((i>>l)&1)) rset.pb(i);
            if (ask(rset)) {
                ans |= (1<<level);
            }
        } else if (level < l) {
            for (ll i = 0;i < n; ++i) if ((i>>(level+1)) == (ans>>(level+1)) && ((i>>level)&1)) rset.pb(i); 
            if (ask(rset)) {
                ans |= (1<<level);
            }
        } else {
            ans |= (1<<level);
        }
    }
    ll a = ans+1, b = (ans^diff)+1;
    if (a > b) swap(a, b);
    cout << "! " << a << " " << b << "\n";
}

int main () {
    solve();
    return 0;
}
```

ask 這個函式會吃一個 vector 並詢問 Jury 這個 vector 中所有 element 的 xor 值。

</details>

### Constructive

> [Codeforces 727C - Guess the Array](https://codeforces.com/problemset/problem/727/C)
>
> Jury 有一組大小為 \\(N\\) 的陣列 \\(A\\)，Participant 每次能詢問此陣列中任兩個元素的和。> Participant 需要在 \\(N\\) 次詢問內知道這個陣列的所有元素是多少。

<details><summary>Solution</summary>

不難觀察到，把詢問想像成在一張 \\(N\\) 個點的圖上加入一條邊 \\(i, j\\)，則對於連通的節點而言，只要知道其中一個節點的值，我們就能推出另一個節點的值。

而要知道任一個節點的值，我們也可以構造出一個奇環，並透過解方程式的方式得知此奇環上的所有值。

於是我們的構造方式如下，將所有點 \\(2 \sim N\\) 都跟 \\(1\\) 建一條邊，並且建一條邊 \\(2, 3\\)，就能推出整個陣列。

```cpp=
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<ll, ll> pll;
#define pb push_back 
#define SZ(_a) (ll)(_a).size()

const ll N = 2e5 + 5;
const ll MOD = 1e9 + 7;
const ll INF = (1LL<<60);

ll get(ll a, ll b) { // a, b are 0-indexed
    cout << "? " << a+1 << " " << b+1 << "\n";
    ll ret;
    cin >> ret;
    return ret;
}

void solve() {
    ll n;
    cin >> n;
    vector<ll> ans(n), u(n);
    if ((n&1)) {
        ll sum = 0, os = 0;
        for (ll i = 0;i < n; ++i) {
            u[i] = get(i, (i+1)%n);
            sum += u[i];
            if (i&1) os += u[i];
        }
        sum >>= 1;
        ans[0] = sum - os;
        for (ll i = 0;i < n-1; ++i) ans[i+1] = u[i]-ans[i];
    } else {
        ll sum = 0, os = 0;
        for (ll i = 0;i < n; i += 2) {
            u[i] = get(i, (i+1)%n);
            sum += u[i];
        }
        for (ll i = 1;i < n-1; i += 2) {
            u[i] = get(i, (i+1)%n);
            os += u[i];
        }
        u[n-1] = sum - os;
        ans[0] = (get(0, n-2)-u[n-2]+u[n-1])/2;
        for (ll i = 0;i < n-1; ++i) ans[i+1] = u[i]-ans[i];
    }
    cout << "! " << ans << "\n";
}

int main () {
    solve();
    return 0;
}
```

</details>

## Exercises

> [Codeforces 750F - New Year and Finding Roots](https://codeforces.com/problemset/problem/750/F)
>
> Jury 有一棵高度是 \\(h\\) 的 perfect binary tree，Participant 能夠問 Jury 一個節點，Jury 會告訴 Participant 該節點的鄰居集合。> Participant 需在 \\(16\\) 次詢問內得知此 perfect binary tree 的根節點編號，其中 \\(2 \le h \le 7\\)。

<details><summary>Solution</summary>

隨便從一個點出發，我們知道沿著此點的各個鄰居往外走的最長路徑中，必定是往此點的父親走的路徑會長過兩個往此點子節點走的路徑。

所以我們可以隨便挑此點的兩個鄰居，看往外走的最長路徑長度，就能知道此點的哪個鄰居是父親。

這樣我們用了 \\(2h\\) 次詢問，知道了此點的父親，和此點的高度。

接著，對於已經知道其中一個子節點的點 \\(x\\)，我們也能用類似的方法在 \\(h\\) 此詢問內知道此點的父親是誰。

於是我們使用這樣的方式從開始節點一路往上走，直到我們知道高度已經超過 \\(5\\)，因為此時高度太高，用原本的方法會超過詢問限制，就改用 BFS 找到根。

</details>

> [Codeforces 1033E - Hidden Bipartite Graph](https://codeforces.com/problemset/problem/1033/E)
>
> Jury 有一張圖 \\(G\\)，其中有 \\(n\\) 個點，\\(1 \le n \le 600\\)。> Participant 每次能詢問 Jury 一個點集的 subset，Jury 會回答此 subset induce 出的邊的數量。> Participant 需在 \\(20000\\) 次詢問內判斷此圖是否為二分圖，如是，請輸出一種塗色方式；如否，請輸出一個奇環。

<details><summary>Solution</summary>

令 \\(Q(S)\\) 為 Jury 對 \\(S\\) 這個點集的回覆。

則任兩個 disjoint 的點集 \\(A\\),\\(B\\)，其中一端點在 \\(A\\)，另一端點在 \\(B\\) 的邊數為：\\(F(A, B) = Q(A \cup B) - Q(A) - Q(B)\\)。

若我們想找出任意，一端點在 \\(A\\)，另一端點在 \\(B\\) 的邊，我們可以用 \\(F\\) 這個 function，不斷將 \\(B\\) 跟 \\(A\\) 切半直到兩個集合的大小變為 \\(1\\)，就能找出此任意邊。

有了 \\(F\\) 這個 function，我們能夠每次詢問尚未與當前連通塊連通的點與當前連通塊的任意邊，蓋出 \\(G\\) 的 spanning forest，如此一來，就能點著色，再用一次詢問判斷是否為二分圖。找奇環也不難辦到。

</details>
