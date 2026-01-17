+++ 
title = "A Short Example of Using Ledger with Emacs Org-mode"
description = "Use ledger in Emacs as personal bookkeeping."
date = "2016-07-28T12:19:00"
categories = ["technique"]
tags = ["emacs", "org-mode", "ledger"]

slug = "ledger-org-babel-example"
summary = "Simple examples of using ledger in Emacs as personal bookkeeping."
highlight_languages = ["lisp", "org-mode"]
+++ 


It has been three months since I started to use [ledger](http://ledger-cli.org/)
in couple with `org-mode` as my personal finance tracking system.  I'm quite
satisfied with it.  Personally speeking, I think this combination is better than
any non-free apps I could find from the mac app store.

As usual, reading manual is painful.  Thus here I tried to give a very simple
example demonstrating practically how to make use of the very basic functions of
ledger within org-mode.

## Step 1. Enable ledger support in org-mode 

`Ledger` support in org-mode is provided by `org-babel`.  To enable it, simply
add the following code to the configuration file:


```lisp
(setq-default
 org-babel-load-languages '((xxx . t)
                            (ledger . t)
                            ...
                            (yyy . t)))
```
                                
## Step 2. Create a file for the finance logs

So far I kept all my financial reports in one org file.  Possibly after years of
logging, the file gets clumsy, then it's time to archive/refile or split it into
separate files.


## Step 3. Opening Balances

Well, I have to say the "opening balance" is one of the most complicated
concepts for anyone who is new to the so-called "double entry accounting
system".  I was so confused that the initial equity was negative!  But let's
face it and move on.

Create a block describing the initialization of our accounting:

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
    
As shown in the above list, we can use different currencies.

## Step 4. Add income

Great!  It's time to add some money to our account.  Suppose we receive salary
from the employer, which in my case is the university:

```org-mode
#+name: income
#+BEGIN_SRC ledger :noweb yes
2016/07/15 * Kyoto University
    Assets:JP Bank                                      200000 JPY
    Income:Salary
#+END_SRC
```
    
Again, the `Income:Salary` account is actually a pseudo account, in which the
money value is always negative.

   
## Step 5. Add expenses

Now let's spend some money!

Similar to the above two code blocks, create a block for expenses:

```org-mode
#+name: expenses
#+BEGIN_SRC ledger :noweb yes
...
#+END_SRC
```

We can also have several blocks like this, and name them as "expenses-2016-07",
"expenses-May" or "expenses-11-11".

Let's buy something:

```org-mode
2016/07/03 * 生鮮館
    Expenses:Food                                         2500 JPY
    Assets:Cash
    
2016/07/05 * LAWSON
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

### Money transfer

We can track money transfer in the same block as expenses.

```org-mode
2016/07/20 * Money transfer (from bank to cash)
    Assets:Cash                                         120000 JPY
    Assets:JP Bank
```
    
This means we withdraw some cash from our bank account and put them into wallet.


## Step 6. Statistics

Let's have a look at our balance:

```org-mode
#+name: balance
#+BEGIN_SRC ledger :cmdline bal :noweb yes
<<startup>>
<<income>>
<<expenses>>
#+END_SRC
```

Those in the `<<>>` brackets are exactly the names of blocks we defined before.

To ask Emacs to calculate the balance, simply press <kbd>C-c C-c</kbd>, or
<kbd>, ,</kbd> (in `Spacemacs`) on the "`#+name: balance`" line.

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
    
    
## Tips

### Keybindings

- From source code blocks in org-mode, <kbd>C-c ,</kbd> leads you to a temp
  ledger-mode buffer.
- In the temp ledger-mode buffer, ledger-mode keybindings are available.
    - <kbd>M-m m a</kbd> (`Spacemacs`) to add new record
    - <kbd>SPC j =</kbd> (`Spacemacs`) to tune indentation
    - <kbd>, c</kbd> (`Spacemacs`) to submit the modification and return to org-mode buffer
  
  
### About currency exchange and credit cards

Here I discuss about these two things together because in my case I use my
credit card for online shopping (usually in US dollar) and repay the loans in
Chinese Yuan.  Luckily the conversion from US dollar to Chinese Yuan was
automatically done by the bank of by credit card.  Here is an example:

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

In the above example, I first bought some games and apps online using the credit
card (see the first record).  Then the loans were exchanged from JPY into USD
and CNY, provided the exchange rates.  In the final step I payed back the money
using my Chinese bank account.

