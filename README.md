# zerodha-statements


This project do analysis on zerodha statements(as csv files) and identifies all the types of charges, fees, unexpected credits done in the account.
It also group the expenses by Mutual funds purchase, stocks purchase.



## Here is my Zerodha's account statement's analysis

```python
import os, glob
import pandas as pd
```


```python
pd.set_option('display.float_format', lambda x: '%.3f' % x)
```


```python
input_file = glob.glob('data/*')[0]
```


```python
df = pd.read_csv(input_file)
```


```python
df = df.dropna()
```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>posting_date</th>
      <th>cost_center</th>
      <th>remarks</th>
      <th>voucher_type</th>
      <th>debit</th>
      <th>credit</th>
      <th>net_balance</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2018-07-31</td>
      <td>NSE-EQ - Z</td>
      <td>Received from GB8753-821214950902     online t...</td>
      <td>Bank Receipts</td>
      <td>0.000</td>
      <td>4000.000</td>
      <td>4000.000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2018-08-06</td>
      <td>NSE-EQ - Z</td>
      <td>Received from GB8753-821718899489     online t...</td>
      <td>Bank Receipts</td>
      <td>0.000</td>
      <td>20000.000</td>
      <td>24000.000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2018-08-06</td>
      <td>NSE-EQ - Z</td>
      <td>Settlement value for August 06, 2018 in NSE wi...</td>
      <td>Book Voucher</td>
      <td>23912.902</td>
      <td>0.000</td>
      <td>87.098</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2018-08-07</td>
      <td>NSE-EQ - Z</td>
      <td>Received from GB8753-821909118326     online t...</td>
      <td>Bank Receipts</td>
      <td>0.000</td>
      <td>15000.000</td>
      <td>15087.098</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2018-08-07</td>
      <td>NSE-EQ - Z</td>
      <td>Settlement value for August 07, 2018 in NSE wi...</td>
      <td>Book Voucher</td>
      <td>14772.825</td>
      <td>0.000</td>
      <td>314.272</td>
    </tr>
  </tbody>
</table>
</div>




```python
df[df['remarks'].str.contains("AMC for Demat")]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>posting_date</th>
      <th>cost_center</th>
      <th>remarks</th>
      <th>voucher_type</th>
      <th>debit</th>
      <th>credit</th>
      <th>net_balance</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>344</th>
      <td>2019-11-06</td>
      <td>NSE-EQ - Z</td>
      <td>AMC for Demat Account for 01-04-2019 to 01-07-...</td>
      <td>Journal Entry</td>
      <td>88.500</td>
      <td>0.000</td>
      <td>30691.482</td>
    </tr>
    <tr>
      <th>367</th>
      <td>2019-12-18</td>
      <td>NSE-EQ - Z</td>
      <td>AMC for Demat Account for 01-07-2019 to 30-09-...</td>
      <td>Journal Entry</td>
      <td>88.500</td>
      <td>0.000</td>
      <td>5700.002</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Unique cost_centers and their count
df.groupby('cost_center')['posting_date'].nunique()
```




    cost_center
    BSE-EQ - Z     30
    NSE-EQ - Z    206
    STARMF - Z     14
    Name: posting_date, dtype: int64




```python
def GetRemarkType(remark):
    if 'MF purchase' in remark:
        return 'MF purchase'
    elif 'OFS' in remark:
        return 'OFS'
    elif 'DP Charges' in remark:
        return 'DP Charges'
    elif 'Funds added' in remark:
        return 'Funds added'
    elif 'AMC for Demat' in remark:
        return 'AMC for Demat'
    elif 'Settlement value' in remark:
        return 'Settlement value monthly'
    elif 'Net settlement' in remark:
        return 'Net settlement for the Exchange'
    elif 'Payout of' in remark:
        return 'Payout of Funds'
    elif 'Received from' in remark:
        return 'Received from'
    elif 'Overnight liquid fund investment' in remark:
        return 'Overnight liquid fund investment'
    elif 'Overnight liquid fund redemption' in remark:
        return 'Overnight liquid fund redemption'
    else:
        return remark
```


```python
df.loc[:,'RemarkType'] = df['remarks'].apply(GetRemarkType)
```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>posting_date</th>
      <th>cost_center</th>
      <th>remarks</th>
      <th>voucher_type</th>
      <th>debit</th>
      <th>credit</th>
      <th>net_balance</th>
      <th>RemarkType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2018-07-31</td>
      <td>NSE-EQ - Z</td>
      <td>Received from GB8753-821214950902     online t...</td>
      <td>Bank Receipts</td>
      <td>0.000</td>
      <td>4000.000</td>
      <td>4000.000</td>
      <td>Received from</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2018-08-06</td>
      <td>NSE-EQ - Z</td>
      <td>Received from GB8753-821718899489     online t...</td>
      <td>Bank Receipts</td>
      <td>0.000</td>
      <td>20000.000</td>
      <td>24000.000</td>
      <td>Received from</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2018-08-06</td>
      <td>NSE-EQ - Z</td>
      <td>Settlement value for August 06, 2018 in NSE wi...</td>
      <td>Book Voucher</td>
      <td>23912.902</td>
      <td>0.000</td>
      <td>87.098</td>
      <td>Settlement value monthly</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2018-08-07</td>
      <td>NSE-EQ - Z</td>
      <td>Received from GB8753-821909118326     online t...</td>
      <td>Bank Receipts</td>
      <td>0.000</td>
      <td>15000.000</td>
      <td>15087.098</td>
      <td>Received from</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2018-08-07</td>
      <td>NSE-EQ - Z</td>
      <td>Settlement value for August 07, 2018 in NSE wi...</td>
      <td>Book Voucher</td>
      <td>14772.825</td>
      <td>0.000</td>
      <td>314.272</td>
      <td>Settlement value monthly</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.groupby('RemarkType')['debit', 'credit'].sum().head(n=30)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>debit</th>
      <th>credit</th>
    </tr>
    <tr>
      <th>RemarkType</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>AMC for Demat</th>
      <td>177.000</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>Annual Maintenance Charge for Demat Account for 2018-2019</th>
      <td>237.620</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>Being brokerage reversed for muhurat trading</th>
      <td>0.000</td>
      <td>0.010</td>
    </tr>
    <tr>
      <th>Being fee for smallcase</th>
      <td>5.610</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>Being fee for smallcase reversed</th>
      <td>0.000</td>
      <td>5.610</td>
    </tr>
    <tr>
      <th>Being payment gateway charges debited for GB8753</th>
      <td>31.860</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>DP Charges</th>
      <td>2485.080</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>Funds added</th>
      <td>0.000</td>
      <td>813201.000</td>
    </tr>
    <tr>
      <th>MF purchase</th>
      <td>200000.000</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>Net settlement for the Exchange</th>
      <td>1050635.467</td>
      <td>488380.786</td>
    </tr>
    <tr>
      <th>Overnight liquid fund investment</th>
      <td>27005.000</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>Overnight liquid fund redemption</th>
      <td>0.000</td>
      <td>27014.680</td>
    </tr>
    <tr>
      <th>Payout of Funds</th>
      <td>40000.000</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>Received from</th>
      <td>0.000</td>
      <td>39000.000</td>
    </tr>
    <tr>
      <th>Settlement value monthly</th>
      <td>38967.041</td>
      <td>89.726</td>
    </tr>
  </tbody>
</table>
</div>


From this, I observed high DP Charges(>2400 INR), which I am actively trying to reduce. The script also identified many unexpected and unkown charges like fee for smallcase, 2 different type of AMC charges, payment gateway charges, account modification charges, OFS charges, delayed payment charges, call and trade charges NSE-EQ - Z etc. Overall I learned about many hidden/unexpected costs they have, which Zerodha, being a discount brooker, don't list very transparently. [Zerodha charges page](https://zerodha.com/charges#tab-equities)

