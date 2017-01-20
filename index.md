## Kaggle Titanic Competition

##### Import the Pandas library.


```python
import numpy as np
import pandas as pd
```

##### Load the train and test datasets to create two DataFrames.


```python
train_url = "http://s3.amazonaws.com/assets.datacamp.com/course/Kaggle/train.csv"
train = pd.read_csv(train_url)

test_url = "http://s3.amazonaws.com/assets.datacamp.com/course/Kaggle/test.csv"
test = pd.read_csv(test_url)
```

##### Print the head of the train and test dataframes.


```python
print(train.head())
print('----------------------------------------------------------------------------------')
print(test.head())
```

       PassengerId  Survived  Pclass  \
    0            1         0       3   
    1            2         1       1   
    2            3         1       3   
    3            4         1       1   
    4            5         0       3   
    
                                                    Name     Sex   Age  SibSp  \
    0                            Braund, Mr. Owen Harris    male  22.0      1   
    1  Cumings, Mrs. John Bradley (Florence Briggs Th...  female  38.0      1   
    2                             Heikkinen, Miss. Laina  female  26.0      0   
    3       Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      1   
    4                           Allen, Mr. William Henry    male  35.0      0   
    
       Parch            Ticket     Fare Cabin Embarked  
    0      0         A/5 21171   7.2500   NaN        S  
    1      0          PC 17599  71.2833   C85        C  
    2      0  STON/O2. 3101282   7.9250   NaN        S  
    3      0            113803  53.1000  C123        S  
    4      0            373450   8.0500   NaN        S  
    ----------------------------------------------------------------------------------
       PassengerId  Pclass                                          Name     Sex  \
    0          892       3                              Kelly, Mr. James    male   
    1          893       3              Wilkes, Mrs. James (Ellen Needs)  female   
    2          894       2                     Myles, Mr. Thomas Francis    male   
    3          895       3                              Wirz, Mr. Albert    male   
    4          896       3  Hirvonen, Mrs. Alexander (Helga E Lindqvist)  female   
    
        Age  SibSp  Parch   Ticket     Fare Cabin Embarked  
    0  34.5      0      0   330911   7.8292   NaN        Q  
    1  47.0      1      0   363272   7.0000   NaN        S  
    2  62.0      0      0   240276   9.6875   NaN        Q  
    3  27.0      0      0   315154   8.6625   NaN        S  
    4  22.0      1      1  3101298  12.2875   NaN        S  


##### Properties of the training set.


```python
train["Age"] = train["Age"].fillna(train["Age"].median())

train.describe()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>446.000000</td>
      <td>0.383838</td>
      <td>2.308642</td>
      <td>29.361582</td>
      <td>0.523008</td>
      <td>0.381594</td>
      <td>32.204208</td>
    </tr>
    <tr>
      <th>std</th>
      <td>257.353842</td>
      <td>0.486592</td>
      <td>0.836071</td>
      <td>13.019697</td>
      <td>1.102743</td>
      <td>0.806057</td>
      <td>49.693429</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.420000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>223.500000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>22.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>7.910400</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>446.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>14.454200</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>668.500000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>35.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>31.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>891.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>80.000000</td>
      <td>8.000000</td>
      <td>6.000000</td>
      <td>512.329200</td>
    </tr>
  </tbody>
</table>
</div>




```python
train.shape
```




    (891, 12)



##### Influence of gender.

Passengers that survived vs passengers that passed away.


```python
print(train['Survived'].value_counts())
```

    0    549
    1    342
    Name: Survived, dtype: int64


As proportions.


```python
print(train['Survived'].value_counts(normalize = True))
```

    0    0.616162
    1    0.383838
    Name: Survived, dtype: float64


Males that survived vs males that passed away.


```python
print(train['Survived'][train['Sex'] == 'male'].value_counts())
```

    0    468
    1    109
    Name: Survived, dtype: int64


Females that survived vs Females that passed away.


```python
print(train['Survived'][train['Sex'] == 'female'].value_counts())
```

    1    233
    0     81
    Name: Survived, dtype: int64


Normalized male survival.


```python
print(train['Survived'][train['Sex'] == 'male'].value_counts(normalize = True))
```

    0    0.811092
    1    0.188908
    Name: Survived, dtype: float64


Normalized female survival.


```python
print(train['Survived'][train['Sex'] == 'female'].value_counts(normalize = True))
```

    1    0.742038
    0    0.257962
    Name: Survived, dtype: float64


##### Influence of age.

Create the column Child and assign to 'NaN'.


```python
train["Child"] = float('NaN')
```

Assign 1 to passengers under 18, 0 to those 18 or older. Print the new column.


```python
train['Child'][train['Age'] < 18] = 1
train['Child'][train['Age'] >= 18] = 0
```

    /Users/pedroterrondastis/anaconda/lib/python3.5/site-packages/ipykernel/__main__.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      if __name__ == '__main__':
    /Users/pedroterrondastis/anaconda/lib/python3.5/site-packages/ipykernel/__main__.py:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      from ipykernel import kernelapp as app


Print normalized Survival Rates for passengers under 18.


```python
print(train["Survived"][train["Child"] == 1].value_counts(normalize = True))
```

    1    0.539823
    0    0.460177
    Name: Survived, dtype: float64


Print normalized Survival Rates for passengers 18 or older.


```python
print(train["Survived"][train["Child"] == 0].value_counts(normalize = True))
```

    0    0.638817
    1    0.361183
    Name: Survived, dtype: float64


### First prediction. 

##### We predict that if the passenger is a woman, Survived will equal 1. (All women survive)

Create a copy of test: test_one.


```python
test_one = test
```

Initialize a Survived column to 0.


```python
test_one['Survived'] = 0
```

Set Survived to 1 if Sex equals "female" and print the 'Survived' column from 'test_one'.


```python
test_one['Survived'][test_one['Sex'] == 'female'] = 1
```

    /Users/pedroterrondastis/anaconda/lib/python3.5/site-packages/ipykernel/__main__.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      if __name__ == '__main__':



```python
print(test_one['Survived'])
```

    0      0
    1      1
    2      0
    3      0
    4      1
    5      0
    6      1
    7      0
    8      1
    9      0
    10     0
    11     0
    12     1
    13     0
    14     1
    15     1
    16     0
    17     0
    18     1
    19     1
    20     0
    21     0
    22     1
    23     0
    24     1
    25     0
    26     1
    27     0
    28     0
    29     0
          ..
    388    0
    389    0
    390    0
    391    1
    392    0
    393    0
    394    0
    395    1
    396    0
    397    1
    398    0
    399    0
    400    1
    401    0
    402    1
    403    0
    404    0
    405    0
    406    0
    407    0
    408    1
    409    1
    410    1
    411    1
    412    1
    413    0
    414    1
    415    0
    416    0
    417    0
    Name: Survived, dtype: int64



```python

```
