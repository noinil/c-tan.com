+++ 
title = "在 Emacs Org-mode 中使用 Ledger 来记账"
description = "Ledger 和 org-mode 都是很厉害的软件， 本文给出一些简单的应用例子。"
date = "2016-07-28T15:47:00"
categories = ["technique"]
tags = ["emacs", "org-mode", "ledger"]

slug = "ledger-org-babel-example"
summary = "本文用一个简单的应用例子来展示如何在 emacs org-mode 中使用 ledger 来进行个人财务管理。"
isCJKLanguage = true
highlight_languages = ["lisp"]
+++ 

自从抛弃那些 mac app store 中买来的废柴记账软件之后， 我就一直在用
[ledger](http://ledger-cli.org/) 管理我的日常开销。  在 Emacs org-mode 中使用
ledger， 是一件享受自由的快乐事情。  未来我可能还会为它写一些数据可视化的工具，
因为实现起来实在是太方便了。

阅读 ledger 的用户手册并不比阅读其他自由软件的手册更轻松。  并且手册里面有大量内
容是实际应用中不会碰到的情形。  因此在这篇文章中我会描述一个日常生活中的场景，来
展示 org-mode 与 ledger 结合做个人金融管理的方式。


<br />
## 第一步： 设置
---

想要在 org-mode 中使用 `ledger`， 只需要在  `org-babel` 中添加支持即可。
在 Emacs 的配置文件中加入下面的内容：

```lisp
(setq-default
 org-babel-load-languages '((xxx . t)
                            (ledger . t)
                            ...
                            (yyy . t)))
```
                                
<br />
## 第二步： 为 ledger 建立一个文件
---

目前我还只用一个文件来纪录所有的收支。  三个月以来的纪录总共有将近 1000 行。  可
以预计若干年后文件将非常臃肿， 然而通过 org-mode 便可以方便地 archive 与 refile。

在这个文件中我们可以建立多个不同的 “块”， 用来分类管理收入、 支出等等。


<br />
## 第三步： 开户
---

`Ledger` 是所谓的 "double entry accounting system"， 这要求每一笔支付都有来源。
这在一些情况下会导致违反直觉的结果， 比如初始账户必需来源于一个虚拟的账户。  再
比如工资的来源也像是一个无限大热源。

我们来添加第一笔开户金额：

```org-mode
#+name: startup
#+BEGIN_SRC ledger :noweb yes
2016/07/01 * Opening Balance
    Assets:JP Bank                                      100000 JPY
    Assets:Cash                                          20000 JPY
    Assets:Itunes                                         5000 JPY
    Assets:Steam                                          1000 JPY
    Assets:中国银行                                        200 CNY
    Equity:Opening Balance
#+END_SRC
```
    
可以看到， 我们能在 `ledger` 中使用不同的货币。

<br />
## 第四步： 添加收入记录
---

记录收入十分简单：

```org-mode
#+name: income
#+BEGIN_SRC ledger :noweb yes
2016/07/15 * Kyoto University
    Assets:JP Bank                                      200000 JPY
    Income:Salary
#+END_SRC
```
    
如上所述， `Income:Salary`  是一个虚拟账户， 请不要过分追究其实际意义。

   
<br />
## 第五步： 添加支出记录
---

我的个人习惯是将每个月的所有支出都放到同一个区块中：

```org-mode
#+name: expenses
#+BEGIN_SRC ledger :noweb yes
...
#+END_SRC
```

区块的名字是随意指定的。  但是将所有支出都记为 "`expenses-yyyy-mm`" 之类的固定格式
也会更方便后期管理。

加入一些记录：

```org-mode
2016/07/03 * 生鮮館
    Expenses:Food                                         2500 JPY
    Assets:Cash
    
2016/07/06 * LAWSON
    Expenses:Food                                          580 JPY
    Expenses:Book                                         1200 JPY
    Assets:Cash

2016/07/12 * App Store
    Expenses:App                                           500 JPY
    Assets:Itunes

2016/07/15 * Steam Store
    Expenses:Game                                         2000 JPY
    Assets:Steam
```

### 转帐 （取现）

转帐也可以放到支出一起， 本质上都是从一种财富转变成另外一种。

```org-mode
2016/07/20 * Money transfer (from bank to cash)
    Assets:Cash                                         120000 JPY
    Assets:JP Bank
```
    
上面的记录表示从银行取出现金来塞进钱包。


<br />
## 第六步： 进行统计
---

我们再来写一个统计代码块：

```org-mode
#+name: balance
#+BEGIN_SRC ledger :cmdline bal :noweb yes
<<startup>>
<<income>>
<<expenses>>
#+END_SRC
```

这里写在 `<<>>` 中的就是上面我们定义的那些名字。

让 org-mode 调用 `ledger` 计算， 只需在 `#+name: balance` 一行上按 <kbd>C-c
 C-c</kbd> 或者<kbd>, ,</kbd> (如果使用 `Spacemacs`) 就可以了。
 
下面就是得到的结果：

```org-mode
#+RESULTS: balance
#+begin_example
           200 CNY
        319220 JPY  Assets
        135720 JPY    Cash
          4500 JPY    Itunes
        180000 JPY    JP Bank
         -1000 JPY    Steam
           200 CNY    中国银行
          -200 CNY
       -126000 JPY  Equity:Opening Balance
          6780 JPY  Expenses
           500 JPY    App
          1200 JPY    Book
          3080 JPY    Food
          2000 JPY    Game
       -200000 JPY  Income:Salary
  ----------------
                 0
#+end_example
```
    
    
<br />
## 小技巧
---

### 键绑定

- 在 org-mode 的 `ledger` 代码区， 按 <kbd>C-c ,</kbd> 可以快速进入一个临时的
  ledger-mode buffer。
- 在这个 ledger-mode buffer 中， 可以使用 ledger-mode 的键绑定：
    - <kbd>M-m m a</kbd> (`Spacemacs`) 添加记录
    - <kbd>SPC j =</kbd> (`Spacemacs`) 版面调整
    - <kbd>, c</kbd> (`Spacemacs`) 提交修改并返回 org-mode buffer
  
  
### 兑换以及信用卡

我将货币兑换及信用卡放在一起讨论， 因为在实际情况中我遇到的就是这样的情形： 
在网购时会使用信用卡的外币支付功能， 然后信用卡公司会帮我自动将消费金额兑换成稳
定货币， 在每个月的 “账单日”， 又会被兑换成人民币， 于是最后我使用人民币来支付账
单。  下面是一个实例 (请注意汇率的用法)：

```org-mode
2016/07/03 * Online Shopping
    Expenses:Game                                         3900 JPY
    Expenses:App                                          4184 JPY
    Liabilities:Credit Card

2016/06/25 * Currency exchange
    Liabilities:Credit Card                               3900 JPY @ 0.00991 USD
    Liabilities:Credit Card                               4184 JPY @ 0.00991 USD
    Liabilities:Credit Card

2016/06/25 * Repayment
    Liabilities:Credit Card                              80.11 USD @ 6.6866 CNY
    Assets:中国银行
```
