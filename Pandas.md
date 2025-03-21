## Series
```python
data={'a':1,'b':2,'c':3}
series=pd.series(data)
df = pd.df(series)
```

## Dataframe
```python
data={
	'Name':['talie','jack','john'],
	'Age':[25,30,45],
	'City':['Hell', 'Paradise','Narnia']
}
df=pd.DataFrame(data)

# show the first column
print(df.loc[0])

# show all rows for a column
print(df.loc[:, "Name"])

# access info from a specific element
df.at[1,'Name']
df.at[1,'Age']

# Remove a column/row, axis=0 for row, axis=1 for column
df.drop('City', axis=1)


```
