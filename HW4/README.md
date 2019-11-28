

```python
import pandas as pd
import matplotlib.pyplot as plt
```

### Problem 1
##### For the districts you selected for budget cuts in HW 3 Problem 4, calculate and visualize the proportion of each district’s total funding that will be lost. Which districts will be affected by your budget cuts the most?


```python
Cuts = pd.read_csv('./Data/cuts.csv', low_memory=False)
```


```python
Cuts['perc_tot'] = Cuts['cutAmount']/Cuts['TOTALREV'] 
```

####  Below is the list of top 10 districts affected by budget cut.


```python
Cuts\
.sort_values(by = 'perc_tot', ascending = False)\
[['LEAID', 'NAME','STNAME','cutAmount','TOTALREV','perc_tot']]\
.head(n=10)
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
      <th>NAME</th>
      <th>STNAME</th>
      <th>cutAmount</th>
      <th>TOTALREV</th>
      <th>perc_tot</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5000007</td>
      <td>Barre Supervisory Union</td>
      <td>Vermont</td>
      <td>1.990760e+06</td>
      <td>5209000</td>
      <td>0.382177</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3500158</td>
      <td>WALATOWA CHARTER HIGH</td>
      <td>New Mexico</td>
      <td>6.229854e+05</td>
      <td>1890000</td>
      <td>0.329622</td>
    </tr>
    <tr>
      <th>2</th>
      <td>400403</td>
      <td>Akimel O'Otham Pee Posh Charter School Inc. 1</td>
      <td>Arizona</td>
      <td>1.187008e+05</td>
      <td>385000</td>
      <td>0.308314</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5300013</td>
      <td>Educational Service District 105</td>
      <td>Washington</td>
      <td>7.298161e+06</td>
      <td>23709000</td>
      <td>0.307822</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4800164</td>
      <td>REG 14 EDUCATION SERVICE CENTER</td>
      <td>Texas</td>
      <td>5.873751e+06</td>
      <td>20254000</td>
      <td>0.290005</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1600163</td>
      <td>CHIEF TAHGEE ELEMENTARY ACADEMY DISTRICT</td>
      <td>Idaho</td>
      <td>6.059173e+05</td>
      <td>2100000</td>
      <td>0.288532</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4800176</td>
      <td>REG 9 EDUCATION SERVICE CENTER</td>
      <td>Texas</td>
      <td>3.596713e+06</td>
      <td>12703000</td>
      <td>0.283139</td>
    </tr>
    <tr>
      <th>7</th>
      <td>400351</td>
      <td>Kaizen Education Foundation dba Vista Grove Pr...</td>
      <td>Arizona</td>
      <td>2.048171e+05</td>
      <td>737000</td>
      <td>0.277907</td>
    </tr>
    <tr>
      <th>8</th>
      <td>3004500</td>
      <td>Box Elder H S</td>
      <td>Montana</td>
      <td>8.549563e+05</td>
      <td>3081000</td>
      <td>0.277493</td>
    </tr>
    <tr>
      <th>9</th>
      <td>5099931</td>
      <td>North Country Supervisory Union</td>
      <td>Vermont</td>
      <td>2.648658e+06</td>
      <td>9573000</td>
      <td>0.276680</td>
    </tr>
  </tbody>
</table>
</div>




```python
cut_plot = Cuts.sort_values(by = 'perc_tot', ascending = False).head(n=10)
plt.barh(cut_plot.NAME, 
         cut_plot.TOTALREV,left = cut_plot.cutAmount, label='Total Revenue')

plt.barh(cut_plot.NAME, 
         cut_plot.cutAmount, label='Cut Amount')

plt.ylabel("Districts that have maximum proportion cut.")
plt.xlabel("Amount ($)")
plt.legend(loc="upper right")
plt.title("Amount cut out of Total Revenue")

plt.show()
```


![png](Images/output_6_0.png)



```python
plt.barh(cut_plot.NAME, 
         cut_plot.perc_tot, label= 'Proportion Cut for each district')

plt.ylabel("Districts that have maximum proportion cut.")
plt.xlabel("Amount ($)")
plt.legend(loc="upper right")
plt.title("Proportion cut from total Revenue")

plt.show()
```


![png](Images/output_7_0.png)


##### We can observe maximum cut proportion is 0.38 that is 38%. Barre Supervisory union is most affected by budget cut. 


```python
demographics = pd.read_csv('./Data/ccd_lea_052_1516_w_1a_011717.csv', low_memory=False)
demographics.LEAID = demographics.LEAID.astype('str')
```

### Problem 2
#### A common problem with purely data-driven solutions is that they can inadvertently perpetuate hidden pre-existing biases in the data, and further disadvantage groups that are already disadvantaged. Calculate the proportion of enrolled students by race for each district, then visualize the distributions of these for districts that received budget cuts versus districts that did not receive budget cuts.


```python
district_prop = demographics[['LEAID']][demographics['TOTAL'] >= 0].copy()

district_prop['American Indian/Alaska Native'] = demographics['AM'] / demographics['TOTAL']
district_prop['Asian'] = demographics['AS'] / demographics['TOTAL']
district_prop['Hispanic'] = demographics['HI'] / demographics['TOTAL']
district_prop['Black'] = demographics['BL'] / demographics['TOTAL']
district_prop['White'] = demographics['WH'] / demographics['TOTAL']
district_prop['Hawaiian Native / Pacific Islander'] = demographics['HP'] / demographics['TOTAL']
district_prop['Two or More Races'] = demographics['TR'] / demographics['TOTAL']


district_prop.head()
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
      <th>American Indian/Alaska Native</th>
      <th>Asian</th>
      <th>Hispanic</th>
      <th>Black</th>
      <th>White</th>
      <th>Hawaiian Native / Pacific Islander</th>
      <th>Two or More Races</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>100005</td>
      <td>0.002685</td>
      <td>0.003260</td>
      <td>0.439010</td>
      <td>0.029152</td>
      <td>0.502301</td>
      <td>0.000767</td>
      <td>0.022823</td>
    </tr>
    <tr>
      <th>2</th>
      <td>100006</td>
      <td>0.004614</td>
      <td>0.003017</td>
      <td>0.205856</td>
      <td>0.008873</td>
      <td>0.768589</td>
      <td>0.000355</td>
      <td>0.008696</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100007</td>
      <td>0.000719</td>
      <td>0.066930</td>
      <td>0.072466</td>
      <td>0.254565</td>
      <td>0.584543</td>
      <td>0.001006</td>
      <td>0.019770</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100008</td>
      <td>0.006193</td>
      <td>0.086105</td>
      <td>0.043852</td>
      <td>0.203976</td>
      <td>0.617920</td>
      <td>0.002697</td>
      <td>0.039257</td>
    </tr>
    <tr>
      <th>6</th>
      <td>100011</td>
      <td>0.001050</td>
      <td>0.006824</td>
      <td>0.126509</td>
      <td>0.227297</td>
      <td>0.602625</td>
      <td>0.002625</td>
      <td>0.033071</td>
    </tr>
  </tbody>
</table>
</div>




```python
Cuts_demographics = Cuts[['LEAID']].merge(demographics[['LEAID','AM','AS','HI','BL','WH','HP','TR','TOTAL']], how = 'inner' ,on = 'LEAID')

# Removing negative values:
num = Cuts_demographics._get_numeric_data()
num[num < 0] = 0

Race_count = pd.DataFrame(Cuts_demographics[['AM','AS','HI','BL','WH','HP','TR']].sum()).rename(columns = {0:'Count'})

not_cut_demographics = Cuts[['LEAID']].merge(demographics[['LEAID','AM','AS','HI','BL','WH','HP','TR','TOTAL']], how='outer', indicator=True)

not_cut_demographics = not_cut_demographics[not_cut_demographics._merge == 'right_only']

# Removing negative values:
num = not_cut_demographics._get_numeric_data()
num[num < 0] = 0
Race_count_notcut = pd.DataFrame(not_cut_demographics[['AM','AS','HI','BL','WH','HP','TR']].sum()).rename(columns = {0:'Count'})
```


```python
Races = {'AM': 'American Indian/Alaska Native',
 'AS': 'Asian',
 'HI': 'Hispanic',
 'BL': 'Black',
 'WH': 'White',
 'HP': 'Hawaiian Native / Pacific Islander',
 'TR': 'Two or More Races'}
Race_count = Race_count.rename(index = Races).reset_index().rename(columns = {'index':'Races'}) 
Race_count['Percent'] =   Race_count['Count']/Race_count['Count'].sum()*100
```


```python
Race_count_notcut = Race_count_notcut.rename(index = Races).reset_index().rename(columns = {'index':'Races'}) 
Race_count_notcut['Percent'] =   Race_count_notcut['Count']/Race_count_notcut['Count'].sum()*100
Race_count_notcut
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
      <th>Races</th>
      <th>Count</th>
      <th>Percent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>American Indian/Alaska Native</td>
      <td>286372.0</td>
      <td>0.922366</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Asian</td>
      <td>1774324.0</td>
      <td>5.714862</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Hispanic</td>
      <td>7365882.0</td>
      <td>23.724527</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Black</td>
      <td>4310695.0</td>
      <td>13.884176</td>
    </tr>
    <tr>
      <th>4</th>
      <td>White</td>
      <td>16091223.0</td>
      <td>51.827691</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hawaiian Native / Pacific Islander</td>
      <td>147183.0</td>
      <td>0.474057</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Two or More Races</td>
      <td>1071861.0</td>
      <td>3.452322</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.figure(figsize=(10,10))
plt.subplot(2,1,1)
plt.barh(Race_count_notcut.Races,Race_count_notcut.Percent)
plt.title('No Revenue Cut')
plt.xlabel('Race')
plt.ylabel('Percentage of race with no revenue cut')
plt.subplot(2,1,2)
plt.barh(Race_count.Races,Race_count.Percent)
plt.title('Revenue Cut')
plt.xlabel('Race')
plt.ylabel('Percentage of race with revenue cut')
plt.show()
```


![png](Images/output_15_0.png)


#### Comment on whether the the distributions appear to be the same or different. Did your selection include any hidden biases, or manage to avoid them?

* An implicit bias is observed in Hispanic students enrolled as percentage of Hispanic students in district that lost revenue is 30% where as enrollment in districts that havent lost funding has 23% enrollment of Hispanic students. Most of the distribution is quite similar for the districts that would lose revenue as supposed to districts that would not face cut in the revenue. 

* We can observe a little negative bias in the solution towards hispanic students however it is minimal.

### Problem 3
#### Calculate the proportion of enrolled students by disability status (students with an IEP under IDEA) for each district, then visualize the distributions of these proportions for districts that received budget cuts versus districts that did not receive budget cuts.


```python
disabilities = pd.read_csv('./Data/ccd_lea_002089_1516_w_1a_011717.csv', low_memory=False)
disabilities[['LEAID']] = disabilities['LEAID'].astype('str')
```


```python
disabilities_prop = disabilities[['LEAID', 'SPECED']]\
                    .merge(demographics[['LEAID', 'TOTAL']])
disabilities_prop = Cuts[['LEAID']].merge(disabilities_prop, how='outer', indicator=True)
disabilities_prop = disabilities_prop[disabilities_prop.TOTAL.notna()]
disabilities_prop['disable_prop'] = disabilities_prop['SPECED']/disabilities_prop['TOTAL']
disabilities_prop.head()
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
      <th>SPECED</th>
      <th>TOTAL</th>
      <th>_merge</th>
      <th>disable_prop</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>3500158</td>
      <td>6.0</td>
      <td>54.0</td>
      <td>both</td>
      <td>0.111111</td>
    </tr>
    <tr>
      <th>2</th>
      <td>400403</td>
      <td>3.0</td>
      <td>14.0</td>
      <td>both</td>
      <td>0.214286</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1600163</td>
      <td>26.0</td>
      <td>101.0</td>
      <td>both</td>
      <td>0.257426</td>
    </tr>
    <tr>
      <th>7</th>
      <td>400351</td>
      <td>8.0</td>
      <td>61.0</td>
      <td>both</td>
      <td>0.131148</td>
    </tr>
    <tr>
      <th>8</th>
      <td>3004500</td>
      <td>14.0</td>
      <td>113.0</td>
      <td>both</td>
      <td>0.123894</td>
    </tr>
  </tbody>
</table>
</div>




```python
overall_proportion = disabilities_prop.groupby('_merge').sum()
overall_proportion['disable_prop'] = overall_proportion['SPECED']/overall_proportion['TOTAL'] 
overall_proportion = overall_proportion.drop('left_only')\
                    .rename(index = {'both':'revenue_cut','right_only':'revenue_not_cut'})
overall_proportion['not_disable_prop'] = 1 - overall_proportion['disable_prop']
overall_proportion

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
      <th>SPECED</th>
      <th>TOTAL</th>
      <th>disable_prop</th>
      <th>not_disable_prop</th>
    </tr>
    <tr>
      <th>_merge</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>revenue_not_cut</th>
      <td>4134294.0</td>
      <td>31049551.0</td>
      <td>0.133151</td>
      <td>0.866849</td>
    </tr>
    <tr>
      <th>revenue_cut</th>
      <td>2540898.0</td>
      <td>19762259.0</td>
      <td>0.128573</td>
      <td>0.871427</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt_prop = overall_proportion[['disable_prop','not_disable_prop']].stack().reset_index().rename(columns = {'level_1':'disability',0:'prop'})
```


```python
plt.figure(figsize=(10,5))
plt.subplot(1,2,1)
plt.title('Revenue Cut')

plt.bar(plt_prop[plt_prop._merge == 'revenue_cut'].disability,
        plt_prop[plt_prop._merge == 'revenue_cut'].prop)

plt.xlabel('Disability')
plt.ylabel('Proportion of disabled students with revenue cut')
plt.subplot(1,2,2)


plt.bar(plt_prop[plt_prop._merge == 'revenue_not_cut'].disability,
        plt_prop[plt_prop._merge == 'revenue_not_cut'].prop)

plt.title('No Revenue Cut')
plt.xlabel('Disability')
plt.ylabel('Proportion of disabled students with no revenue cut')


```




    Text(0, 0.5, 'Proportion of disabled students with no revenue cut')




![png](Images/output_23_1.png)


#### Comment on whether the the distributions appear to be the same or different. Did your selection include any hidden biases, or manage to avoid them?

* The distribution for proportion of disabled and not disable student is very similar in both the cases. Hence, the results are not biased towards disbled students negatively or positively.

### Problem 4

#### Choose and critique one of your fellow classmates’ selection of schools for budget cuts in HW 3 Problem 4 and Problem 5. What was the justification of their selection? Discuss any advantages or disadvantages of their approach.

* I would critique on Prathwish Shetty's Answer:
https://colab.research.google.com/drive/1TD7hQ2NMVWYvLg7bIZv0ay46fOoOhfpi#scrollTo=IxYCQg8UrCCZ

* Prathwish chose to do multi-level funding cut. Students with lower scores gets a lower cut in revenue.

* Pros:
    * The method promotes to push education standards for lower performing school. It does make sense that more resources should be put on low performing students to bring them up to average level. So cutting their revenues could impact negatively.
    
* Cons: 
    * However, I feel monetary funds could not be the only reason. There could be other factors too. Also, if the students are performing exceptionally well their schools' revenue would get a cut that could affect the schools in debt with students performing well. This method could be unfair for those students and schools working really hard. 
    
* Suggestion: 
    * Scores and proficiency in subjects could be one of the factors to be considered. Aditionally, total revenue and debt could be also considered so that the schools aren't negatively affected. 

### Problem 5
#### Summarize and comment on what you learned from one the special topics lectures (MapReduce + Hadoop, Visualization, Causal Inference, or the Industry Panel) of your choice.

#### INDUSTRY PANEL
* The industry panel which included Faye Zheng (Gamalon), Michelle Tat (Haven Healthcare), Daniel Hannah (Vectra AI), Katie Porter (Tamr) and Luke Winslow (Wayfair) was very helpful as they discussed more about the industry. How the different roles are they are not called Data Science roles however they do have work relavent to Data Science. They emphasized on the critical points we need to keep mind during the process of job search. The panel also discussed their own journey and career paths about their PHDs. DS 5500 being the last course of the program having panel on one of the lecture days is very helpful as we are looking towards finding new jobs or decideing wether to go for PHD or not. 

* This lecture was very benificial as it gave first hand information from people in industry who have been in Academia for a while also as some of them were Post Docs. 
