

```python
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib
import matplotlib.pyplot as plt
import pickle as pkl

from scipy.stats import ttest_ind
from sklearn.preprocessing import RobustScaler
from sklearn.manifold import TSNE
from sklearn.mixture import GaussianMixture

font= matplotlib.font_manager.FontProperties(fname= 'C:\Windows\\Fonts\\조선일보명조.ttf').get_name()
matplotlib.rc('font', family=font)  
```

### 데이터를 로드하고 분석에 적합한 형태로 변환함


```python
df_bymonth= pd.read_csv('./Pre_processed_data/groupby_ID_YEARMONTH.csv')
```


```python
pt_bytsne= pd.read_pickle('./pkl_result/data_tSNE_dim2_perplex30.pkl')
```


```python
df_raw= pd.read_csv('./Pre_processed_data/merged_dataframe_shopping.csv', delimiter= ',', encoding= 'euc-kr')
```


```python
df_grouped= df_raw.sort_values('ID')
```


```python
agg_func= {'RCT_NO': 'count', 'BIZ_UNIT': lambda x: x.nunique(), 'PD_S_C': lambda x: x.nunique(), \
           'BR_C': lambda x: x.nunique(), 'BUY_AM': 'sum'}
```


```python
df_grouped= df_grouped.groupby('ID').agg(agg_func).reset_index(drop= False)
```


```python
df_demo= df_raw[['ID', 'AGE_PRD', 'IS_MALE', 'IS_FEMALE', 'PRVIN']].drop_duplicates(subset= 'ID').sort_values('ID').reset_index(drop= True)
```


```python
df_byitem= pd.read_pickle('./pkl_result/item_selected_by_middleline.pkl')
```


```python
df_byitem= df_byitem.reset_index(drop=False)
```


```python
df_item_consumed= pd.read_pickle('./pkl_result/item_selected_by_middleline.pkl')
```


```python
df_item_consumed.reset_index(drop= False, inplace= True)
```


```python
def plot_results(X, Y_= None):
    try:
        if Y_.any():
            for i in range(Y_.nunique()):
                grp= sns.regplot(x= X.loc[Y_==i, 0], y= X.loc[Y_==i, 1], fit_reg= False, scatter_kws= {'s': 2})
        plt.show()
    except AttributeError:
        grp= sns.regplot(x= X.iloc[:,0], y= X.iloc[:, 1], fit_reg= False, scatter_kws= {'s': 2})
        plt.show()
```


```python
def plot_clusters(X, Y_):
    for i in sorted(Y_.unique().tolist()):
        grp= sns.regplot(x= X.iloc[:,0], y= X.iloc[:, 1], fit_reg= False, scatter_kws= {'s': 2})
        grp= sns.regplot(x= X.loc[Y_==i, 0], y= X.loc[Y_==i, 1], fit_reg= False, scatter_kws= {"color": "red", 's': 2})
        plt.show()
```


```python
prefered_item_dict_05= {}
ft_dict= {}
def find_prefered_item(df, label, p_val= 0.05):
    for i in sorted(label.unique().tolist()):
        ft_dict[i]= df.loc[label==i, :]
        print('\nfor cluster {}:\n'.format(str(i)))
        prefered_item_dict_05[i]= []
        for item in df.columns.values.tolist():
            (stat_item, p_val_item)=  ttest_ind(ft_dict[i][item], df[item], equal_var= False)
            if p_val_item< p_val and stat_item>0:
                print('item {} has different mean with total dataset; ({}, {})'.format(item, stat_item, p_val_item))
                prefered_item_dict_05[i].append(item)
                
def find_prefered_by_clusters(df, label, i, j, p_val= 0.05):
    for item in df.columns.values.tolist():
        (stat_item, p_val_item)=  ttest_ind(df.loc[label==i, item], df.loc[label==j, item], equal_var= False)
        if p_val_item< p_val:
            print('item {} has different mean with total dataset; ({}, {})'.format(item, stat_item, p_val_item))
```


```python
plot_results(pt_bytsne[[0,1]], pt_bytsne['y_14_clst'])
```


![png](/public/img/jupyternb_example/output_16_0.png)



```python
plot_clusters(pt_bytsne[[0,1]], pt_bytsne['y_14_clst'])
```


![png](/public/img/jupyternb_example/output_17_0.png)



![png](/public/img/jupyternb_example/output_17_1.png)



![png](/public/img/jupyternb_example/output_17_2.png)



![png](/public/img/jupyternb_example/output_17_3.png)



![png](/public/img/jupyternb_example/output_17_4.png)



![png](/public/img/jupyternb_example/output_17_5.png)



![png](/public/img/jupyternb_example/output_17_6.png)



![png](/public/img/jupyternb_example/output_17_7.png)



![png](/public/img/jupyternb_example/output_17_8.png)



![png](/public/img/jupyternb_example/output_17_9.png)



![png](/public/img/jupyternb_example/output_17_10.png)



![png](/public/img/jupyternb_example/output_17_11.png)



![png](/public/img/jupyternb_example/output_17_12.png)



![png](/public/img/jupyternb_example/output_17_13.png)



```python
plot_clusters(pt_bytsne[[0,1]], df_demo['IS_FEMALE'])
#성별이 클러스터와 연관이 있는 인구통계 정보인지 확인함. 아래의 시각화 결과에 따르면, 성별은 클러스터를 구분짓는 요소는 아니지만 클러스터 내에서 소비 품목의 차이는 드러났음.
```


![png](/public/img/jupyternb_example/output_18_0.png)



![png](/public/img/jupyternb_example/output_18_1.png)



```python
plot_clusters(pt_bytsne[[0,1]], df_demo['AGE_PRD'])
# 연령대의 경우도 마찬가지로 연령대와 관계 없이 폭넓게 분포하고 있었음.
```


![png](/public/img/jupyternb_example/output_19_0.png)



![png](/public/img/jupyternb_example/output_19_1.png)



![png](/public/img/jupyternb_example/output_19_2.png)



![png](/public/img/jupyternb_example/output_19_3.png)



![png](/public/img/jupyternb_example/output_19_4.png)


### 성별, 연령대 또는 나이에 따라 사용자를 나누어 클러스터링해 보았음. 아래는 여성 사용자만을 대상으로 클러스터링한 결과임


```python
pt_bytsne= pd.concat([df_demo['ID'], pt_bytsne], axis= 1)
```


```python
pt_female= pt_bytsne.loc[df_demo['IS_FEMALE']==1, :].reset_index(drop= True)
```


```python
pt_female.head()
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
      <th>ID</th>
      <th>0</th>
      <th>1</th>
      <th>y_14_clst</th>
      <th>y_28_clst</th>
      <th>y_24_clst</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>53.016911</td>
      <td>-32.975819</td>
      <td>10</td>
      <td>8</td>
      <td>8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>-22.183092</td>
      <td>52.640892</td>
      <td>3</td>
      <td>9</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6</td>
      <td>42.187847</td>
      <td>32.573856</td>
      <td>5</td>
      <td>17</td>
      <td>17</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7</td>
      <td>-27.630230</td>
      <td>52.580269</td>
      <td>3</td>
      <td>12</td>
      <td>12</td>
    </tr>
    <tr>
      <th>4</th>
      <td>8</td>
      <td>-5.850988</td>
      <td>27.713455</td>
      <td>12</td>
      <td>3</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
pt_male= pt_bytsne.loc[df_demo['IS_FEMALE']==0, :].reset_index(drop= True)
```


```python
gmm = GaussianMixture(n_components=14, covariance_type='full', random_state= 42).fit(pt_female[[0,1]])
label_dict[(2,i)]= pd.Series(gmm.predict(pt_female[[0,1]]))
plot_results(pt_female[[0,1]], label_dict[(2,14)])

# 여성들만을 군집화한 경우에도 유사한 클러스터가 형성되는 것을 확인함
```


![png](/public/img/jupyternb_example/output_25_0.png)



```python
df_female= df_item_consumed.loc[df_demo['IS_FEMALE']==1, :].drop('ID', axis= 1).reset_index(drop= True)
```


```python
df_female.head()
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
      <th>H&amp;B선물세트</th>
      <th>VIDEOGAME</th>
      <th>가공식품</th>
      <th>가공우유</th>
      <th>가구</th>
      <th>가방브랜드</th>
      <th>계절완구</th>
      <th>고급</th>
      <th>고양이용품</th>
      <th>골프</th>
      <th>...</th>
      <th>필기용품</th>
      <th>한방차</th>
      <th>한우선물세트</th>
      <th>해초</th>
      <th>헬스용품</th>
      <th>호주산소고기</th>
      <th>홍인삼</th>
      <th>화과자</th>
      <th>황태</th>
      <th>훼이셜케어</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>6.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>14.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>18.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>23.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 149 columns</p>
</div>



### 남성 사용자를 클러스터링해 보았음. 이 때 6번 클러스터와 7번 클러스터 간의 차이를 파악하였음.


```python
pt_male= pt_bytsne.loc[df_demo['IS_FEMALE']==0, :].reset_index(drop= True)
df_male= df_item_consumed.loc[df_demo['IS_FEMALE']==0, :].drop('ID', axis= 1).reset_index(drop= True)
id_male= df_raw.loc[df_raw['IS_MALE']==1, 'ID'].drop_duplicates().sort_values().reset_index(drop= True)
df_demo_male= df_demo.loc[df_demo['ID'].isin(id_male), :].reset_index(drop= True)
df_male.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 7003 entries, 0 to 7002
    Columns: 149 entries, H&B선물세트 to 훼이셜케어
    dtypes: float64(149)
    memory usage: 8.0 MB
    


```python
gmm = GaussianMixture(n_components=14, covariance_type='full', random_state= 42).fit(pt_male[[0,1]])
label_dict[(2, 14)]= pd.Series(gmm.predict(pt_male[[0,1]]))
plot_results(pt_male[[0,1]], label_dict[(2,14)])
```


![png](/public/img/jupyternb_example/output_30_0.png)



```python
df_byitem.loc[df_demo['IS_MALE']==1].reset_index().loc[label_dict[(2,14)]==6, :].head()
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
      <th>index</th>
      <th>ID</th>
      <th>H&amp;B선물세트</th>
      <th>VIDEOGAME</th>
      <th>가공식품</th>
      <th>가공우유</th>
      <th>가구</th>
      <th>가방브랜드</th>
      <th>계절완구</th>
      <th>고급</th>
      <th>...</th>
      <th>필기용품</th>
      <th>한방차</th>
      <th>한우선물세트</th>
      <th>해초</th>
      <th>헬스용품</th>
      <th>호주산소고기</th>
      <th>홍인삼</th>
      <th>화과자</th>
      <th>황태</th>
      <th>훼이셜케어</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>4</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>38.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>26</td>
      <td>29</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>11.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>39</td>
      <td>42</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>22.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>46</td>
      <td>50</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>9.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>15.0</td>
      <td>14.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>52</td>
      <td>56</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>13.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 151 columns</p>
</div>



#### 6번 클러스터와 7번 클러스터 간의 지표 차이를 파악하였음.


```python
df_clst6= df_demo_male.loc[label_dict[(2,14)]==6, :]
```


```python
df_clst7= df_demo_male.loc[label_dict[(2,14)]==7, :]
```


```python
df_info6= df_grouped.loc[df_grouped['ID'].isin(df_clst6['ID']), :]
df_info7= df_grouped.loc[df_grouped['ID'].isin(df_clst7['ID']), :]
```


```python
df_info6.head()
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
      <th>ID</th>
      <th>RCT_NO</th>
      <th>BIZ_UNIT</th>
      <th>PD_S_C</th>
      <th>BR_C</th>
      <th>BUY_AM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>308</td>
      <td>3</td>
      <td>136</td>
      <td>7</td>
      <td>7970116</td>
    </tr>
    <tr>
      <th>26</th>
      <td>29</td>
      <td>408</td>
      <td>2</td>
      <td>159</td>
      <td>5</td>
      <td>3756730</td>
    </tr>
    <tr>
      <th>39</th>
      <td>42</td>
      <td>492</td>
      <td>2</td>
      <td>138</td>
      <td>3</td>
      <td>5425186</td>
    </tr>
    <tr>
      <th>46</th>
      <td>50</td>
      <td>566</td>
      <td>3</td>
      <td>238</td>
      <td>11</td>
      <td>10626666</td>
    </tr>
    <tr>
      <th>52</th>
      <td>56</td>
      <td>107</td>
      <td>3</td>
      <td>58</td>
      <td>6</td>
      <td>2197496</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_clst6.AGE_PRD.value_counts().sort_index()
```




    20     55
    30    156
    40    249
    50    212
    60    129
    Name: AGE_PRD, dtype: int64




```python
df_value6= df_clst6.PRVIN.value_counts().sort_values()
df_value7= df_clst7.PRVIN.value_counts().sort_values()
df_ratio6= (df_value6/df_clst6.PRVIN.count()).apply(lambda x: round(x, 3))
df_ratio7= (df_value7/df_clst7.PRVIN.count()).apply(lambda x: round(x, 3))

num_of_range= range(1, df_value6.count()+1)
grp= plt.figure(figsize= (9,12))
ax= grp.add_subplot(211)
plt.hlines(y=num_of_range, xmin=0, xmax=df_ratio6, color='skyblue')
for i in num_of_range:
    ax.annotate(df_ratio6.values.tolist()[i-1], xy=(df_ratio6.values.tolist()[i-1]+0.01, i+0.01), textcoords='data')
plt.plot(df_ratio6, num_of_range, "o")
plt.yticks(num_of_range, df_value6.index)

num_of_range= range(1, df_value7.count()+1)
grp= plt.figure(figsize= (9,9))
ax= grp.add_subplot(212)
plt.hlines(y=num_of_range, xmin=0, xmax=df_ratio7, color='tomato')
for i in num_of_range:
    ax.annotate(df_ratio7.values.tolist()[i-1], xy=(df_ratio7.values.tolist()[i-1]+0.01, i+0.01), textcoords='data')
plt.plot(df_ratio7, num_of_range, "o", color= 'red')
plt.yticks(num_of_range, df_value6.index)
plt.show()
```


![png](/public/img/jupyternb_example/output_38_0.png)



![png](/public/img/jupyternb_example/output_38_1.png)



```python
df_value6= df_clst6.AGE_PRD.value_counts().sort_index()
df_value7= df_clst7.AGE_PRD.value_counts().sort_index()
df_ratio6= (df_value6/df_clst6.AGE_PRD.count()).apply(lambda x: round(x, 3))
df_ratio7= (df_value7/df_clst7.AGE_PRD.count()).apply(lambda x: round(x, 3))

num_of_range= range(1, df_value6.count()+1)
grp= plt.figure(figsize= (9,12))
ax= grp.add_subplot(211)
plt.hlines(y=num_of_range, xmin=0, xmax=df_ratio6, color='skyblue')
for i in num_of_range:
    ax.annotate(df_ratio6.values.tolist()[i-1], xy=(df_ratio6.values.tolist()[i-1]+0.01, i+0.01), textcoords='data')
plt.plot(df_ratio6, num_of_range, "o")
plt.yticks(num_of_range, df_value6.index)

num_of_range= range(1, df_value7.count()+1)
grp= plt.figure(figsize= (9,9))
ax= grp.add_subplot(212)
plt.hlines(y=num_of_range, xmin=0, xmax=df_ratio7, color='tomato')
for i in num_of_range:
    ax.annotate(df_ratio7.values.tolist()[i-1], xy=(df_ratio7.values.tolist()[i-1]+0.01, i+0.01), textcoords='data')
plt.plot(df_ratio7, num_of_range, "o", color= 'red')
plt.yticks(num_of_range, df_value6.index)
plt.show()
```


![png](/public/img/jupyternb_example/output_39_0.png)



![png](/public/img/jupyternb_example/output_39_1.png)



```python
grp= sns.distplot(df_info6['BUY_AM'], label= 'cluster 6', bins= 30, kde= False, norm_hist= True)
grp= sns.distplot(df_info7['BUY_AM'], label= 'cluster 7', kde= False, norm_hist= True)
plt.legend()
plt.show()
```


![png](/public/img/jupyternb_example/output_40_0.png)



```python
grp= sns.distplot(df_info6['RCT_NO'], label= 'cluster 6', bins= 30, kde= False, norm_hist= True)
grp= sns.distplot(df_info7['RCT_NO'], label= 'cluster 7', kde= False, norm_hist= True)
plt.legend()
plt.show()
```


![png](/public/img/jupyternb_example/output_41_0.png)



```python
grp= sns.distplot(df_info6['PD_S_C'], label= 'cluster 6', bins= 30, kde= True)
grp= sns.distplot(df_info7['PD_S_C'], label= 'cluster 7', kde= True)
plt.legend()
plt.show()
```


![png](/public/img/jupyternb_example/output_42_0.png)



```python
grp= sns.distplot(df_info6['BR_C'], label= 'cluster 6', bins= 30, kde= True)
grp= sns.distplot(df_info7['BR_C'], label= 'cluster 7', kde= True)
plt.legend()
plt.show()
```


![png](/public/img/jupyternb_example/output_43_0.png)


### 20-40대 서울 거주 여성을 클러스터링함


```python
pt_specified= pt_bytsne.loc[(df_demo['IS_FEMALE']==1)&((df_demo['AGE_PRD']==20)|(df_demo['AGE_PRD']==30)|(df_demo['AGE_PRD']==40))&(df_demo['PRVIN']=='서울특별시'), :].reset_index(drop= True)
df_specified= df_item_consumed.loc[(df_demo['IS_FEMALE']==1)&((df_demo['AGE_PRD']==20)|(df_demo['AGE_PRD']==30)|(df_demo['AGE_PRD']==40))&(df_demo['PRVIN']=='서울특별시'), :].drop('ID', axis= 1).reset_index(drop= True)
df_specified.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 2554 entries, 0 to 2553
    Columns: 149 entries, H&B선물세트 to 훼이셜케어
    dtypes: float64(149)
    memory usage: 2.9 MB
    


```python
plot_results(pt_specified[[0,1]])
```


![png](/public/img/jupyternb_example/output_46_0.png)



```python
gmm = GaussianMixture(n_components=10, covariance_type='full', random_state= 42).fit(pt_specified[[0,1]])
label_dict[(2,i)]= pd.Series(gmm.predict(pt_specified[[0,1]]))
plot_results(pt_specified[[0,1]], label_dict[(2,10)])
```


![png](/public/img/jupyternb_example/output_47_0.png)



```python
plot_clusters(pt_specified[[0,1]], label_dict[(2,10)])
```


![png](/public/img/jupyternb_example/output_48_0.png)



![png](/public/img/jupyternb_example/output_48_1.png)



![png](/public/img/jupyternb_example/output_48_2.png)



![png](/public/img/jupyternb_example/output_48_3.png)



![png](/public/img/jupyternb_example/output_48_4.png)



![png](/public/img/jupyternb_example/output_48_5.png)



![png](/public/img/jupyternb_example/output_48_6.png)



![png](/public/img/jupyternb_example/output_48_7.png)



![png](/public/img/jupyternb_example/output_48_8.png)



![png](/public/img/jupyternb_example/output_48_9.png)

