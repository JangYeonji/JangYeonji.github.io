---
layout: splash
title:  "210914_Pandas_판다스의 멀티프로세싱"
---

# muitiprocessing


```python
import pickle 
```


```python
from kbo import crawling_kbo
```


```python
with open("./kbo.pkl", 'rb') as f:
    kbo = pickle.load(f)
```


```python
%%time
total = []
for x in kbo:
    total.append(crawling_kbo(x))
```

    C:\workspace\kbo.py:8: GuessedAtParserWarning: No parser was explicitly specified, so I'm using the best available HTML parser for this system ("lxml"). This usually isn't a problem, but if you run this code on another system, or in a different virtual environment, it may use a different parser and behave differently.
    
    The code that caused this warning is on line 8 of the file C:\workspace\kbo.py. To get rid of this warning, pass the additional argument 'features="lxml"' to the BeautifulSoup constructor.
    
      bs = BS(r.text)
    

    Wall time: 2min 4s
    


```python
import multiprocessing
```


```python
pool = multiprocessing.Pool(processes=4)
```


```python
%%time
result = pool.map(crawling_kbo, kbo)
```

    Wall time: 32 s
    


```python
import pandas as pd
```


```python
kbo_df = pd.DataFrame(result)
```


```python
kbo_df.to_csv("./kbo_20210914.csv",index=False, encoding='utf-8-sig')
```


```python
kbo_df = pd.read_csv("./kbo_20210914.csv")
```


```python
kbo_df2 = kbo_df[~kbo_df['연봉'].apply(lambda x : len(x) < 3)].copy()
```


```python
kbo_df2['연봉'].apply(lambda x: x[-2:]).value_counts()
```




    만원    819
    달러     37
    Name: 연봉, dtype: int64




```python
kbo_df2['연봉'] = kbo_df2['연봉'].apply(lambda x : int(x[:-2]) * 1151.5 if x[-2:] \
                                    == "달러" else int(x[:-2]) * 10000 )
```


```python
pd.options.display.float_format = '{:.5f}'.format
```


```python
report = kbo_df2.groupby(['Team'])[['연봉']].agg(['max', 'min', 'mean', 'median'])
```


```python
report.columns
```




    MultiIndex([('연봉',    'max'),
                ('연봉',    'min'),
                ('연봉',   'mean'),
                ('연봉', 'median')],
               )




```python
report.sort_values(by=[('연봉',   'median')])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="4" halign="left">연봉</th>
    </tr>
    <tr>
      <th></th>
      <th>max</th>
      <th>min</th>
      <th>mean</th>
      <th>median</th>
    </tr>
    <tr>
      <th>Team</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>SSG 랜더스</th>
      <td>2700000000.00000</td>
      <td>30000000.00000</td>
      <td>152659764.04494</td>
      <td>30000000.00000</td>
    </tr>
    <tr>
      <th>고양 히어로즈</th>
      <td>30000000.00000</td>
      <td>27000000.00000</td>
      <td>29785714.28571</td>
      <td>30000000.00000</td>
    </tr>
    <tr>
      <th>NC 다이노스</th>
      <td>1500000000.00000</td>
      <td>30000000.00000</td>
      <td>140291627.90698</td>
      <td>30500000.00000</td>
    </tr>
    <tr>
      <th>두산 베어스</th>
      <td>1000000000.00000</td>
      <td>30000000.00000</td>
      <td>116558505.95238</td>
      <td>32500000.00000</td>
    </tr>
    <tr>
      <th>한화 이글스</th>
      <td>800000000.00000</td>
      <td>30000000.00000</td>
      <td>76459593.02326</td>
      <td>33000000.00000</td>
    </tr>
    <tr>
      <th>KT 위즈</th>
      <td>800000000.00000</td>
      <td>27000000.00000</td>
      <td>96113994.56522</td>
      <td>34000000.00000</td>
    </tr>
    <tr>
      <th>롯데 자이언츠</th>
      <td>1036350000.00000</td>
      <td>27000000.00000</td>
      <td>101872954.54545</td>
      <td>34000000.00000</td>
    </tr>
    <tr>
      <th>KIA 타이거즈</th>
      <td>1151500000.00000</td>
      <td>30000000.00000</td>
      <td>93280196.62921</td>
      <td>35000000.00000</td>
    </tr>
    <tr>
      <th>LG 트윈스</th>
      <td>1000000000.00000</td>
      <td>27000000.00000</td>
      <td>114715909.09091</td>
      <td>36000000.00000</td>
    </tr>
    <tr>
      <th>키움 히어로즈</th>
      <td>1500000000.00000</td>
      <td>30000000.00000</td>
      <td>141994425.37313</td>
      <td>43000000.00000</td>
    </tr>
    <tr>
      <th>삼성 라이온즈</th>
      <td>1100000000.00000</td>
      <td>30000000.00000</td>
      <td>129119345.23810</td>
      <td>45000000.00000</td>
    </tr>
  </tbody>
</table>
</div>




