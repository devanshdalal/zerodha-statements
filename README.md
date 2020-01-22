# zerodha-statements


This project does analysis on zerodha statements(as csv files) and identifies all the types of charges, fees, unexpected debits etc done in the account.
It also group the expenses by Mutual funds purchase, stocks purchase.



## [Here is my Zerodha's account statement's analysis](https://github.com/devanshdalal/zerodha-statements/blob/master/statement.ipynb)

## Insights

From this, I observed high DP Charges(>2400 INR), which I am actively trying to reduce. The script also identified many unexpected and unkown charges like fee for smallcase, 2 different type of AMC charges, payment gateway charges, account modification charges, OFS charges, delayed payment charges, call and trade charges NSE-EQ - Z etc. Overall I learned about many hidden/unexpected costs they have, which Zerodha, being a discount brooker, don't list very transparently. [Zerodha charges page](https://zerodha.com/charges#tab-equities)

