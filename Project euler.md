# Project euler - Writeup for some challenge

## Challenge 1: [Counting Digit](https://euler.stephan-brumme.com/156/)
### Lời giải
- Ta chứng minh các nghiệm luôn < 10^10
- Chia số thành các phần theo lũy thừa của 10.  
- Đếm số lần chữ số xuất hiện trong từng phần.  
- Cộng dồn kết quả (đệ quy).  
- Dùng **binary search** để tìm `n` sao cho `f(n, d) = n`.

### Code 
```c++
#include <bits/stdc++.h>  

using namespace std;
#define int long long

int f[20];
int length(int n)
{
    return to_string(n).size();
}

int count(int n, int d)
{
    int l = to_string(n).size();
    int g = pow(10, l - 1);
    int first_digit = to_string(n)[0] - '0';
    int remain = n % g;

    if (l == 1) return first_digit >= d;

    int bonus = 0;
    if (first_digit < d) bonus = 0;
    if (first_digit == d) bonus = remain + 1;
    if (first_digit > d) bonus = g; 

    return first_digit * f[l - 1] + bonus + count(remain, d);
}

int find(int l, int r, int d)
{
    if (l == r) return count(l, d) == l ? l : 0LL;
    int mid = (l + r) >> 1;
    int cl = count(l, d), cmid1 = count(mid, d), cmid2 = count(mid + 1, d), cr = count(r, d), res = 0;
    if (cmid1 >= l && cl <= mid) res += find(l, mid, d);
    if (cmid2 <= r && cr >= mid + 1) res += find(mid + 1, r, d);
    return res;
}

signed main()
{
    int p = 1, ans = 0;
    for (int i = 1; i <= 11; i++) f[i] = i * p, p *= 10;
    for (int digit = 1; digit <= 9; digit++)
        ans += find(0, 1000000000000, digit);

    cout << ans;
}
```
### Kết quả
1231960200
---
## Challenge 2: [Sums of Powers of Two](https://projecteuler.net/problem=169)
### Lời giải    
-Ta có vector `pos` là các vị trí của bit 1 trong dãy nhị phân sau khi được đảo ngược lại
-Định nghĩa: +dp[i][0]: là số lượng dãy tồn tại đến bit 1 thứ i và bit thứ i được giữ nguyên
             +dp[i][1]: ____________________________________________________ được chuyển thành tổng các bit khác
-Công thức:
        dp[i][0] = dp[i - 1][0] + dp[i - 1][1]
        dp[i][1] = dp[i - 1][0] * (d - 1) + dp[i - 1][1] * d
    với d là khoảng cách giữa bit hiện tại và bit trước nó

### Code 
```c++
#include <bits/stdc++.h>  

using namespace std;
#define int long long

int dp[30][2];
signed main()
{
    //10^25 -> binary
    string s = "100001000101100101010001011000010100000000010100100001001010000000000000000000000000#";
    reverse(s.begin(), s.end()); 
    vector <int> vec; vec.push_back(0);
    for (int i = 0; i < s.size(); i++)
        if (s[i] == '1') vec.push_back(i);

    dp[0][0] = 1; 
    for (int i = 1; i < vec.size(); i++)
    {
        int d = vec[i] - vec[i - 1];
        dp[i][0] = dp[i - 1][0] + dp[i - 1][1];
        dp[i][1] = dp[i - 1][0] * (d - 1) + dp[i - 1][1] * d;
    }

    cout << dp[vec.size() - 1][0] + dp[vec.size() - 1][1];
}
```

### Kết quả
178653872807
---
## Challenge 3: [RSA Encryption](https://projecteuler.net/problem=182)
### Lời giải

Ta có p = 1009 và q = 3643 là 2 số nguyên tố cùng nhau, sử dụng  CRT :
m^e ≡ m (mod p) (1)
m^e ≡ m (mod q)
Lại có:
-trường hợp (mod p) :
	+khi m = 0 thì 0^e ≡ 0 (mod p) (1 nghiệm)
	+khi m != 0 thì: 
         (1) ⟺ elog(m) ≡ log(m) (mod p - 1) 
             ⟺(e - 1)log(m) ≡ 0(mod p - 1)
Số nghiệm của m thõa là gcd(e - 1, p - 1)
Tương tự với trường hợp (mod q)
Vậy nên số nghiệm thõa đối với e là (1 + gcd(e - 1, p - 1))(1 + gcd(e - 1, q - 1)).

### Code 
```c++
#include <bits/stdc++.h>  

using namespace std;
signed main()
{
    int p = 1009, q = 3643, phi = (p - 1)  * (q - 1), sum = 0, m = 1e18;
    for (int e = 0; e < phi; e++)
        if (__gcd(e, phi) == 1)
        { 
            int solu = (1LL + __gcd(e - 1, p - 1)) * (1LL + __gcd(e - 1, q - 1));
            cout << sum << '\n';
            if (solu < m) m = solu, sum = solu;
            else if (solu == m) sum += solu;
        }

    cout << sum << '\n';
}
```
### Kết quả
399788195976
---
## Challenge 4: [Number Mind](https://projecteuler.net/problem=185)
### Lời Giải
### Code
### Đáp án 
---
## Challenge 5: [Prime Factorisation of Binomial Coefficients](https://projecteuler.net/problem=231)
### Lời giải
Ta có: C(n, k) = n! / (k! * (n - k)!)
Vì thế nên kq của chúng ta là F(C(n, k)) = F(n!) - F(k!) - F((n - k)!)
chúng ta có thể dễ dàng tính được F(x!) bằng cách sàng

### Code 
```c++
#include <bits/stdc++.h>  

using namespace std;
#define int long long
#define N 30000000

bitset <N> check;
void sieve()
{
    for (int i = 2; i < N; i++)
        if (!check[i])  for (int j = i * i; j < N; j += i) check[j] = true;
}

int F(int x)
{
    int sum = 0;
    for (int i = 2; i <= x; i++)
        if (!check[i])
            for (int j = i; j <= x; j *= i) sum += x / j * i;

    return sum;
}

signed main()
{
    int n = 20000000, k = 15000000;
    sieve();
    cout << F(n) - F(k) - F(n - k);
}
```
### Kết quả 
7526965179680
---
## Challenge 6: [Tours on a 4 x N Playing Board](https://projecteuler.net/problem=237)
### Lời Giải
### Code
### Đáp án 
---
## Challenge 7: [Squares Under a Hyperbola](https://projecteuler.net/problem=247)
### Lời Giải
### Code
### Đáp án 

