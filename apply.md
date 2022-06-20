# pandas.DataFrame.apply

```python
DataFrame.apply(func, axis=0, raw=False, result_type=None, args=(), **kwargs)
```

传递给函数的对象是 Series 对象，其索引是 DataFrame 的索引 (axis=0) 或 DataFrame 的列 (axis=1)。 默认情况下（result_type=None），最终返回类型是从应用函数的返回类型推断出来的。 否则，它取决于 result_type 参数。

## 参数

- func：function

应用于每一列或每一行的函数。

- axis：{0 or ‘index’, 1 or ‘columns’}, default 0

沿其应用函数的轴：<br>
0 或“索引”：将函数应用于每一列。 <br>
1 或“列”：将函数应用于每一行。<br>

- raw：bool, default False

确定行或列是否作为 Series 或 ndarray 对象传递： <br>
False ：将每一行或列作为 Series 传递给函数。 <br>
True ：传递的函数将改为接收 ndarray 对象。 如果您只是应用 NumPy 缩减功能，这将获得更好的性能。

- result_type：{‘expand’, ‘reduce’, ‘broadcast’, None}, default None

这些仅在axis = 1（列）时起作用：<br>
'expand'：类似列表的结果将被转换为列。 <br>
‘reduce’：如果可能，返回一个系列，而不是扩展类似列表的结果。 这与“扩展”相反。 <br>
‘broadcast’ : 结果将广播到DataFrame的原始形状，原始索引和列将被保留。 <br>
默认行为（无）取决于应用函数的返回值：类似列表的结果将作为这些结果的系列返回。 但是，如果应用函数返回一个系列，这些将扩展为列。

- args：tuple

除了数组/系列之外，要传递给 func 的位置参数。

- **kwargs

要作为关键字参数传递给 func 的附加关键字参数。

## 返回值

- Series or DataFrame

沿 DataFrame 的给定轴应用 func 的结果。

## 例

```python
> df = pd.DataFrame([[4, 9]] * 3, columns=['A', 'B'])
> df
   A  B
0  4  9
1  4  9
2  4  9
```

```python
> df.apply(np.sqrt)
     A    B
0  2.0  3.0
1  2.0  3.0
2  2.0  3.0
```

```python
> df.apply(np.sum, axis=0)
A    12
B    27
dtype: int64
```

```python
> df.apply(np.sum, axis=1)
0    13
1    13
2    13
dtype: int64
```

```python
> df.apply(lambda x: [1, 2], axis=1)
0    [1, 2]
1    [1, 2]
2    [1, 2]
dtype: object
```

```python
> df.apply(lambda x: [1, 2], axis=1, result_type='expand')
   0  1
0  1  2
1  1  2
2  1  2
```

```python
> df.apply(lambda x: pd.Series([1, 2], index=['foo', 'bar']), axis=1)
   foo  bar
0    1    2
1    1    2
2    1    2
```

```python
> df.apply(lambda x: [1, 2], axis=1, result_type='broadcast')
   A  B
0  1  2
1  1  2
2  1  2
```