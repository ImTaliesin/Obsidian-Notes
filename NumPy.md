
## Normalization
Your mean is zero and your standard deviation is 1
```Python
data = np.array([1,2,3,4,5,6,7,8,9,10])
data[(data>=5) & (data<=7)] # = 5,6,7
mean = np.mean(data)
standard_deviation = np.std(data)
variance = np.var(data)

#Normalization
normalized_data = (data - mean) / standard_deviation
print(f'Normalized Data: {normalized_data}')

```