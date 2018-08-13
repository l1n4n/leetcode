# Leetcode Ideas and Notes
## 188 Best Time to Buy and Sell Stock IV (DP)
Say you have an array for which the  _i_th  element is the price of a given stock on day  _i_.

Design an algorithm to find the maximum profit. You may complete at most  **k**  transactions.

**Note:**  
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

**Example 1:**

**Input:** [2,4,1], k = 2
**Output:** 2
**Explanation:** Buy on day 1 (price = 2) and sell on day 2 (price = 4), profit = 4-2 = 2.

**Example 2:**

**Input:** [3,2,6,5,0,3], k = 2
**Output:** 7
**Explanation:** Buy on day 2 (price = 2) and sell on day 3 (price = 6), profit = 6-2 = 4.
             Then buy on day 5 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3. 

- [hold vs unhold](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/discuss/54114/Easy-understanding-and-can-be-easily-modified-to-different-situations-Java-Solution)
The basic idea is to create two tables. hold and unhold.

hold[i][j] means the maximum profit with at most j transaction for 0 to i-th day. hold means you have a stock in your hand.

unhold[i][j] means the maximum profit with at most j transaction for 0 to i-th day. unhold means you don't have a stock in your hand.

The equation is

> **hold[i][j] = Math.max(unhold[i-1][j]-prices[i],hold[i-1][j]);**
> 
> **unhold[i][j] = Math.max(hold[i-1][j-1]+prices[i],unhold[i-1][j]);**

- hold: 1. buy at day i given unhold at day i-1; 2. do nothing at day i given hold at day i-1
- unhold: 1.sell at day i given hold at day i-1; 2. do nothing given unhold at day i-1
- when you sell your stock this is a transaction but when you buy a stock, it is not considered as a full transaction. so this is why the two equation look a little different.

And we have to initiate hold table when  **k = 0**.

initialization (k = 2, rows for transaction 0, 1, 2)

|[3,|2,|6,|5,|0,|3]|
|--|--|--|-|-|-|
|-3|-2|-2|-2|0|0|
|-3||||||
|-3||||||

|||||||
|--|--|--|-|-|-|
|0|0|0|0|0|0|
|0|0|0|0|0|0|
|0|0|0|0|0|0|

day 2

|[3,|2,|6,|5,|0,|3]|
|--|--|--|-|-|-|
|-3|-2|-2|-2|0|0|
|-3|-2|||||
|-3|-2|||||

|||||||
|--|--|--|-|-|-|
|0|0|0|0|0|0|
|0|0|||||
|0|0|||||

day 3

|[3,|2,|6,|5,|0,|3]|
|--|--|--|-|-|-|
|-3|-2|-2|-2|0|0|
|-3|-2|-2||||
|-3|-2|-2||||

|||||||
|--|--|--|-|-|-|
|0|0|0|0|0|0|
|0|0|4||||
|0|0|4||||

day 4

|[3,|2,|6,|5,|0,|3]|
|--|--|--|-|-|-|
|-3|-2|-2|-2|0|0|
|-3|-2|-2|-1|||
|-3|-2|-2|-1|||

|||||||
|--|--|--|-|-|-|
|0|0|0|0|0|0|
|0|0|4|4|||
|0|0|4|4|||

day 5

|[3,|2,|6,|5,|0,|3]|
|--|--|--|-|-|-|
|-3|-2|-2|-2|0|0|
|-3|-2|-2|-1|4||
|-3|-2|-2|-1|4||

|||||||
|--|--|--|-|-|-|
|0|0|0|0|0|0|
|0|0|4|4|4||
|0|0|4|4|4||

day 6

|[3,|2,|6,|5,|0,|3]|
|--|--|--|-|-|-|
|-3|-2|-2|-2|0|0|
|-3|-2|-2|-1|4|4|
|-3|-2|-2|-1|4|4|

|||||||
|--|--|--|-|-|-|
|0|0|0|0|0|0|
|0|0|4|4|4|4|
|0|0|4|4|4|7|

- python implementation
~~~
class Solution:
    def maxProfit(self, k, prices):
        """
        :type k: int
        :type prices: List[int]
        :rtype: int
        """
        n = len(prices)
        if k == 0 or n == 0: return 0
        if k > n/2: return self.addProfit(prices)
        
        hold = [[0 for i in range(k+1)]for j in range(n)]         
        unhold = [[0 for i in range(k+1)] for j in range(n)]
        
        for j in range(k+1):
            hold[0][j] = -prices[0]
        for i in range(1,n):
            hold[i][0] = max(hold[i-1][0], -prices[i])
            
        for i in range(1, n):
            for j in range(1, k+1):
                hold[i][j] = max(unhold[i-1][j] - prices[i], hold[i-1][j])
                unhold[i][j] = max(hold[i-1][j-1] + prices[i], unhold[i-1][j])
        return max(hold[-1][-1], unhold[-1][-1])
    
    def addProfit(self, p):
        n = len(p)
        mProfit = 0
        for i in range(1,n):
            if p[i] > p[i-1]:
                mProfit += p[i] - p[i-1]
        return mProfit
~~~
