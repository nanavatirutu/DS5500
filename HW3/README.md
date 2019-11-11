

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import re
```


```python
# Fiscal Data : https://nces.ed.gov/ccd/f33agency.asp
fiscal_data = pd.read_csv('./Data/Sdf16_1a.txt', sep='\t', low_memory=False)
```

### Problem 1
#### Import and explore the district-level fiscal data from 2015-16. Rank and visualize the states that take in the most federal funding (revenue). Which states spend the most federal funding per student?


```python
fiscal_data = fiscal_data[fiscal_data.TFEDREV > 0]
state_fiscal_data = fiscal_data.groupby('STNAME').sum().sort_values(by='TFEDREV', ascending=False)
state_fiscal_data.head()
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
      <th>FIPST</th>
      <th>YEAR</th>
      <th>CCDNF</th>
      <th>CENFILE</th>
      <th>V33</th>
      <th>MEMBERSCH</th>
      <th>TOTALREV</th>
      <th>TFEDREV</th>
      <th>C14</th>
      <th>C15</th>
      <th>...</th>
      <th>_66V</th>
      <th>W01</th>
      <th>W31</th>
      <th>W61</th>
      <th>V95</th>
      <th>V02</th>
      <th>K14</th>
      <th>CE1</th>
      <th>CE2</th>
      <th>WEIGHT</th>
    </tr>
    <tr>
      <th>STNAME</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>California</th>
      <td>6108</td>
      <td>16288</td>
      <td>1018</td>
      <td>1017</td>
      <td>6203559</td>
      <td>6187037</td>
      <td>89110947000</td>
      <td>7709275000</td>
      <td>1798207000</td>
      <td>1821197000</td>
      <td>...</td>
      <td>0</td>
      <td>4946663000</td>
      <td>10871931000</td>
      <td>29391495000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-1018</td>
      <td>-1018</td>
      <td>1018</td>
    </tr>
    <tr>
      <th>Texas</th>
      <td>58368</td>
      <td>19456</td>
      <td>1216</td>
      <td>1044</td>
      <td>5296442</td>
      <td>5296378</td>
      <td>60768409000</td>
      <td>6194317000</td>
      <td>1347799000</td>
      <td>847121000</td>
      <td>...</td>
      <td>3618330000</td>
      <td>2947662000</td>
      <td>12040448000</td>
      <td>18156976000</td>
      <td>1196458000</td>
      <td>0</td>
      <td>0</td>
      <td>41141377000</td>
      <td>4911673000</td>
      <td>1216</td>
    </tr>
    <tr>
      <th>New York</th>
      <td>24732</td>
      <td>10992</td>
      <td>687</td>
      <td>677</td>
      <td>2591958</td>
      <td>2572154</td>
      <td>67051220000</td>
      <td>3374794000</td>
      <td>1035611000</td>
      <td>699120000</td>
      <td>...</td>
      <td>2032288000</td>
      <td>428102000</td>
      <td>1425604000</td>
      <td>9234538000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-687</td>
      <td>-687</td>
      <td>687</td>
    </tr>
    <tr>
      <th>Florida</th>
      <td>804</td>
      <td>1072</td>
      <td>67</td>
      <td>67</td>
      <td>2776933</td>
      <td>2776067</td>
      <td>28125598000</td>
      <td>3147329000</td>
      <td>808874000</td>
      <td>606538000</td>
      <td>...</td>
      <td>0</td>
      <td>633122000</td>
      <td>1136097000</td>
      <td>6836238000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>21654985000</td>
      <td>3115137000</td>
      <td>67</td>
    </tr>
    <tr>
      <th>Illinois</th>
      <td>16524</td>
      <td>15552</td>
      <td>972</td>
      <td>970</td>
      <td>2029830</td>
      <td>2007587</td>
      <td>32884195000</td>
      <td>2334945000</td>
      <td>681879000</td>
      <td>544336000</td>
      <td>...</td>
      <td>920029000</td>
      <td>1366956000</td>
      <td>1211789000</td>
      <td>12929989000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-971</td>
      <td>-971</td>
      <td>972</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 132 columns</p>
</div>




```python
plt.figure(figsize=(20,10))
plt.bar(state_fiscal_data.index, state_fiscal_data.TFEDREV)
plt.xlabel('States', fontsize=12)
plt.ylabel('Total Federal Revenue ($)', fontsize=12)
_ = plt.xticks(
    rotation=45, 
    horizontalalignment='right',
    fontweight='light' 
)
_ = plt.title('State-wise federal funding.', fontsize = 16)
```


![png](output_4_0.png)



```python
# state spending per student

state_fiscal_data['TFEDREVperStudent'] = state_fiscal_data['TFEDREV']/state_fiscal_data['V33']
state_fiscal_data_sorted = state_fiscal_data.sort_values(by='TFEDREVperStudent', ascending=False)

plt.figure(figsize=(10,5))
plt.bar(state_fiscal_data_sorted.head().index, state_fiscal_data_sorted.head().TFEDREVperStudent)
plt.xlabel('States', fontsize=12)
plt.ylabel('Total Federal Revenue per Student ($)', fontsize=12)
_ = plt.xticks(
    rotation=45, 
    horizontalalignment='right',
    fontweight='light' 
)
_ = plt.title('States with high federal funding per student.', fontsize = 16)
```


![png](output_5_0.png)


* We observe that District of Columbia has maximum per student Federal Funding

### Problem 2
#### Visualize the relationship between school districts’ total revenue and expenditures. Which states have the most debt per student?


```python
#state_fiscal_data[['TOTALREV','TOTALEXP','V33']]
plt.figure(figsize=(20,5))
plt.xticks(
    rotation=45, 
    horizontalalignment='right',
    fontweight='light' 
)
plt.title("Total Revenue vs Total Expenditure", fontsize = 16)
plt.xlabel('Total Revenue', fontsize=12)
plt.ylabel('Total Expenditure', fontsize=12)
_ = sns.scatterplot(data = state_fiscal_data, x = 'TOTALREV', y = 'TOTALEXP') 
```


![png](output_8_0.png)



```python
sorted_debt = state_fiscal_data\
            .assign(debt_per_student = (state_fiscal_data['TOTALEXP'] - state_fiscal_data['TOTALREV'])/state_fiscal_data['V33'])


sorted_debt = sorted_debt.sort_values(by = 'debt_per_student', ascending = False)

```


```python
plt.figure(figsize=(10,5))
plt.bar(sorted_debt.head().index, sorted_debt.head().debt_per_student)
plt.xlabel('States', fontsize=12)
plt.ylabel('Debt per Student ($)', fontsize=12)
_ = plt.xticks(
    rotation=45, 
    horizontalalignment='right',
    fontweight='light' 
)
_ = plt.title('States with high federal funding per student.', fontsize = 16)
```


![png](output_10_0.png)


* We observe North Dakota has maximum Debt per student

### Problem 3
#### The district-level performance metrics from EDFacts may be useful in your decision. However, to protect student privacy, the data in these datasets has been heavily “blurred” to prevent students from being identified. Therefore, most of the numeric metrics are presented as ranges in string format. In addition, censored and missing data must be imputed.
#### Write and explain a function for processing a single column of “blurred” metrics into usable numeric values. Use it to process and then visualize the distribution of a performance metric of your choice.


```python
# EDFacts Data :  
# LEA level

math = pd.read_csv('./Data/math-achievement-lea-sy2015-16.csv', low_memory=False)
rla = pd.read_csv('./Data/rla-achievement-lea-sy2015-16.csv', low_memory=False)   
```


```python
'''
## FUNCTIONS ##
preprocessing: Takes the values from the column of “blurred” metrics into usable numeric values
               * The values here are passed to the helper function that converts the messy value into range:
                       - minimum value
                       - maximum value 
               * The average of minimum and maximum value is considered as final output.       
                
findFuncValue: This function is a helper function for preprocessing.
               It converts the value to minimum and maximum value.
               
               -`PS` value is converted to 0 (for both min and max values) here.
               - Single numeric value is saved to minimum and maximum value as it is.
               - For GE or GT minimum is value itself and maximum value is 100.
               - For LE or LT minimum is 0 and maximum value is the value itself.
               
'''

def findFuncValue(value): 

    
    if re.findall("^GE|^LE|^GT|^LT", value):
        function = re.findall("^GE|^LE|^GT|^LT", value)[0]
        min_value = re.sub("^GE|^LE|^GT|^LT", "", value)
        if function in ['GE','GT']:
            max_value = 100
        
        if function in ['LE','LT']:
            max_value = min_value
            min_value = 0
               
    
    elif re.search("-", value):
        min_value = value.split("-")[0]
        max_value = value.split("-")[1]
    
    elif value == 'PS':
        min_value = '0'
        max_value = '0'
    
    else: 
        min_value = value
        max_value = value
        
    return min_value, max_value
    
def preprocessing(value):
    min_value, max_value = findFuncValue(value)
    value = (int(min_value) + int(max_value))/2
    return value
        
    
```


```python
# imputing mean value for values hidden for privacy
math['math_proficiency'] = [preprocessing(i) for i in math.ALL_MTH00PCTPROF_1516]
math[math.math_proficiency == 0] = math.math_proficiency.mean()
```


```python
# Density plot shows distribution of proficiency of the students in Mathematics
plt.figure(figsize=(15,5))
plt.title('Distribution of Performance in Mathematics', fontsize = 16)
_ = sns.distplot(math.math_proficiency,hist=True, kde=True, 
             bins=int(180/5), color = 'darkblue', 
             hist_kws={'edgecolor':'black'},
             kde_kws={'linewidth': 4})
```


![png](output_16_0.png)


### Problem 4
#### You are tasked with cutting 15% of the U.S. federal budget currently being spent on funding school districts. How much money is this? 


```python
print(f"Total Fedral Revenue is: {int(fiscal_data.TFEDREV.sum())}")
print(f"15% of Total Fedral Revenue is: {int(fiscal_data.TFEDREV.sum()*0.15)}")
```

    Total Fedral Revenue is: 55602742000
    15% of Total Fedral Revenue is: 8340411300


#### Choose which school districts will have their funding cut and how this will be done. (You should produce a table of LEA IDs and the dollar amount by which their federal funding will be cut you do not need print the entire table.)


```python
LEAID_debts = fiscal_data\
      .assign(district_value_ex = fiscal_data['TOTALREV'] - fiscal_data['TOTALEXP']) \
      .assign(district_value_prop = (fiscal_data['TOTALREV'] - fiscal_data['TOTALEXP'])/fiscal_data['TOTALREV'])\
      .assign(dep_fed_rev = fiscal_data['TFEDREV']/fiscal_data['TOTALREV'])

```


```python
cutoff_metric = LEAID_debts[(LEAID_debts.district_value_prop > 0)  & (LEAID_debts.dep_fed_rev<0.8) & (LEAID_debts.district_value_ex < LEAID_debts.TFEDREV)].sort_values(by='district_value_prop',ascending = False)


```


```python
# % of budget to be saved from amount that can be overall saved
8340411300/cutoff_metric['district_value_ex'].sum()
```




    0.7758224079391183




```python
Cuts = cutoff_metric\
      .assign(cutAmount = cutoff_metric['district_value_ex'] * 0.7758224079391183 )\
      .assign(percTFEDREV = (cutoff_metric['district_value_ex'] * 0.7758224079391183)/ cutoff_metric['TFEDREV'] )
```


```python
# Amount to be cut per LEAID is given by:
Cuts[['LEAID','cutAmount','percTFEDREV']]
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
      <th>LEAID</th>
      <th>cutAmount</th>
      <th>percTFEDREV</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>17199</th>
      <td>5000007</td>
      <td>1.990760e+06</td>
      <td>0.731091</td>
    </tr>
    <tr>
      <th>10857</th>
      <td>3500158</td>
      <td>6.229854e+05</td>
      <td>0.561754</td>
    </tr>
    <tr>
      <th>475</th>
      <td>0400403</td>
      <td>1.187008e+05</td>
      <td>0.442914</td>
    </tr>
    <tr>
      <th>17791</th>
      <td>5300013</td>
      <td>7.298161e+06</td>
      <td>0.673076</td>
    </tr>
    <tr>
      <th>15879</th>
      <td>4800164</td>
      <td>5.873751e+06</td>
      <td>0.518974</td>
    </tr>
    <tr>
      <th>3349</th>
      <td>1600163</td>
      <td>6.059173e+05</td>
      <td>0.504091</td>
    </tr>
    <tr>
      <th>15887</th>
      <td>4800176</td>
      <td>3.596713e+06</td>
      <td>0.518856</td>
    </tr>
    <tr>
      <th>436</th>
      <td>0400351</td>
      <td>2.048171e+05</td>
      <td>0.658576</td>
    </tr>
    <tr>
      <th>9151</th>
      <td>3004500</td>
      <td>8.549563e+05</td>
      <td>0.509509</td>
    </tr>
    <tr>
      <th>17529</th>
      <td>5099931</td>
      <td>2.648658e+06</td>
      <td>0.521287</td>
    </tr>
    <tr>
      <th>15845</th>
      <td>4800104</td>
      <td>2.105117e+07</td>
      <td>0.409373</td>
    </tr>
    <tr>
      <th>17525</th>
      <td>5099927</td>
      <td>9.612440e+05</td>
      <td>0.489432</td>
    </tr>
    <tr>
      <th>352</th>
      <td>0400213</td>
      <td>7.424620e+05</td>
      <td>0.406383</td>
    </tr>
    <tr>
      <th>9340</th>
      <td>3017040</td>
      <td>1.083048e+06</td>
      <td>0.425225</td>
    </tr>
    <tr>
      <th>711</th>
      <td>0400915</td>
      <td>5.050604e+05</td>
      <td>0.704408</td>
    </tr>
    <tr>
      <th>7656</th>
      <td>2680750</td>
      <td>5.398172e+06</td>
      <td>0.587013</td>
    </tr>
    <tr>
      <th>9159</th>
      <td>3005010</td>
      <td>4.887681e+05</td>
      <td>0.496211</td>
    </tr>
    <tr>
      <th>15870</th>
      <td>4800150</td>
      <td>7.101878e+06</td>
      <td>0.329998</td>
    </tr>
    <tr>
      <th>17519</th>
      <td>5099921</td>
      <td>2.651761e+06</td>
      <td>0.665937</td>
    </tr>
    <tr>
      <th>15874</th>
      <td>4800156</td>
      <td>8.343194e+06</td>
      <td>0.366058</td>
    </tr>
    <tr>
      <th>9089</th>
      <td>3000095</td>
      <td>8.596112e+05</td>
      <td>0.380359</td>
    </tr>
    <tr>
      <th>790</th>
      <td>0403150</td>
      <td>3.159925e+06</td>
      <td>0.410433</td>
    </tr>
    <tr>
      <th>317</th>
      <td>0400147</td>
      <td>4.608385e+05</td>
      <td>0.494462</td>
    </tr>
    <tr>
      <th>12454</th>
      <td>3814520</td>
      <td>2.878301e+05</td>
      <td>0.666273</td>
    </tr>
    <tr>
      <th>7638</th>
      <td>2680420</td>
      <td>2.616849e+06</td>
      <td>0.354155</td>
    </tr>
    <tr>
      <th>17507</th>
      <td>5099909</td>
      <td>7.122050e+05</td>
      <td>0.536703</td>
    </tr>
    <tr>
      <th>2112</th>
      <td>0632340</td>
      <td>8.146135e+04</td>
      <td>0.678845</td>
    </tr>
    <tr>
      <th>15880</th>
      <td>4800165</td>
      <td>1.905420e+06</td>
      <td>0.668334</td>
    </tr>
    <tr>
      <th>719</th>
      <td>0400923</td>
      <td>3.529992e+05</td>
      <td>0.711692</td>
    </tr>
    <tr>
      <th>15514</th>
      <td>4621300</td>
      <td>7.447895e+04</td>
      <td>0.528220</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>10462</th>
      <td>3408430</td>
      <td>7.758224e+02</td>
      <td>0.015516</td>
    </tr>
    <tr>
      <th>9856</th>
      <td>3301860</td>
      <td>5.430757e+03</td>
      <td>0.001989</td>
    </tr>
    <tr>
      <th>11913</th>
      <td>3630540</td>
      <td>2.792961e+04</td>
      <td>0.011992</td>
    </tr>
    <tr>
      <th>17418</th>
      <td>5007470</td>
      <td>1.318898e+04</td>
      <td>0.010328</td>
    </tr>
    <tr>
      <th>5819</th>
      <td>2200030</td>
      <td>2.405049e+04</td>
      <td>0.001609</td>
    </tr>
    <tr>
      <th>7007</th>
      <td>2601019</td>
      <td>7.758224e+02</td>
      <td>0.008342</td>
    </tr>
    <tr>
      <th>12629</th>
      <td>3900203</td>
      <td>3.103290e+03</td>
      <td>0.001825</td>
    </tr>
    <tr>
      <th>12010</th>
      <td>3700045</td>
      <td>7.758224e+02</td>
      <td>0.009698</td>
    </tr>
    <tr>
      <th>13792</th>
      <td>4004230</td>
      <td>2.327467e+03</td>
      <td>0.002761</td>
    </tr>
    <tr>
      <th>15647</th>
      <td>4700330</td>
      <td>1.784392e+04</td>
      <td>0.001744</td>
    </tr>
    <tr>
      <th>12683</th>
      <td>3900350</td>
      <td>7.758224e+02</td>
      <td>0.001259</td>
    </tr>
    <tr>
      <th>4456</th>
      <td>1740140</td>
      <td>3.879112e+03</td>
      <td>0.002998</td>
    </tr>
    <tr>
      <th>76</th>
      <td>0100660</td>
      <td>1.241316e+04</td>
      <td>0.001578</td>
    </tr>
    <tr>
      <th>18474</th>
      <td>5510350</td>
      <td>2.327467e+03</td>
      <td>0.002541</td>
    </tr>
    <tr>
      <th>4913</th>
      <td>1810770</td>
      <td>1.551645e+03</td>
      <td>0.004498</td>
    </tr>
    <tr>
      <th>3609</th>
      <td>1700246</td>
      <td>7.758224e+02</td>
      <td>0.023510</td>
    </tr>
    <tr>
      <th>15125</th>
      <td>4222920</td>
      <td>2.327467e+03</td>
      <td>0.005290</td>
    </tr>
    <tr>
      <th>8212</th>
      <td>2733450</td>
      <td>1.551645e+03</td>
      <td>0.002821</td>
    </tr>
    <tr>
      <th>15350</th>
      <td>4401170</td>
      <td>8.534046e+03</td>
      <td>0.002566</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>0627900</td>
      <td>7.758224e+02</td>
      <td>0.001319</td>
    </tr>
    <tr>
      <th>12300</th>
      <td>3705020</td>
      <td>1.241316e+04</td>
      <td>0.000835</td>
    </tr>
    <tr>
      <th>15263</th>
      <td>4289150</td>
      <td>4.654934e+03</td>
      <td>0.000272</td>
    </tr>
    <tr>
      <th>4375</th>
      <td>1735940</td>
      <td>7.758224e+02</td>
      <td>0.001405</td>
    </tr>
    <tr>
      <th>6519</th>
      <td>2504980</td>
      <td>1.861974e+04</td>
      <td>0.003293</td>
    </tr>
    <tr>
      <th>18110</th>
      <td>5400030</td>
      <td>2.327467e+03</td>
      <td>0.000695</td>
    </tr>
    <tr>
      <th>9551</th>
      <td>3100118</td>
      <td>7.758224e+02</td>
      <td>0.001558</td>
    </tr>
    <tr>
      <th>10681</th>
      <td>3415330</td>
      <td>9.309869e+03</td>
      <td>0.002923</td>
    </tr>
    <tr>
      <th>18364</th>
      <td>5505670</td>
      <td>7.758224e+02</td>
      <td>0.000926</td>
    </tr>
    <tr>
      <th>18127</th>
      <td>5400511</td>
      <td>7.758224e+02</td>
      <td>0.000465</td>
    </tr>
    <tr>
      <th>5833</th>
      <td>2200047</td>
      <td>7.758224e+02</td>
      <td>0.001911</td>
    </tr>
  </tbody>
</table>
<p>5732 rows × 3 columns</p>
</div>




```python
Cuts.cutAmount.sum()
```




    8340411300.000001



### Problem 5
#### Provide a statement for your supervisor justifying your decisions on which school districts will lose funding.

* 5732 unique LEAID's would loose their budget with minimum cutoff budget upto 0.02%. Many of the schools would be hardly affected. I have particularly targeted those districts in which schools save money each year (i.e revenue is more than expenditure) out of which those school whose dependence of federal revenue is less than 80% are selected. Hence the schools with less or no revenue outside the Federal revenue are not affected. 
* 1385  out of 5732 LEAIDs would loose less than 15% of the budget


```python
plt.figure(figsize=(15,5))
plt.title('Distribution of Performance in Mathematics', fontsize = 16)
_ = sns.distplot(Cuts.percTFEDREV,hist=True, kde=True, 
             bins=int(180/5), color = 'darkblue', 
             hist_kws={'edgecolor':'black'},
             kde_kws={'linewidth': 4})
```


![png](output_28_0.png)

