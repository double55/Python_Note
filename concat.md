# pandas.concat

```python
pandas.concat(objs, axis=0, join='outer', ignore_index=False, keys=None, levels=None, names=None, verify_integrity=False, sort=False, copy=True)
```

沿特定轴连接 pandas 对象，并沿其他轴使用可选的设置逻辑。 

还可以在连接轴上添加一层分层索引，如果标签在传递的轴号上相同（或重叠），这可能很有用。

## 参数

***objs*** ： Series 或 DataFrame 对象的序列或映射

如果传递了映射，则排序的键将用作键 参数，除非传递，在这种情况下将选择值（见下文）。任何 None 对象都将被静默删除，除非它们都是 None 在这种情况下将引发 ValueError 。

***axis*** ：{0/'index', 1/'columns'}，默认 0

要连接的轴。

***join*** ：{'inner', 'outer'}，默认 'outer'

如何处理其他轴（或轴）上的索引。

***ignore_index*** ：布尔值，默认为 False

如果为 True，则不要沿连接轴使用索引值。生成的轴将标记为 0, ..., n - 1。如果您要连接的对象连接轴没有有意义的索引信息，这将很有用。请注意，连接中仍然尊重其他轴上的索引值。

***keys*** ： 序列，默认无

如果通过了多个级别，则应包含元组。使用传递的键作为最外层构建层次索引。

***levels*** ： 列表，默认无

用于构造 MultiIndex 的特定级别（唯一值）。否则，它们将从密钥中推断出来。

***names*** ：列表，默认无

生成的分层索引中的级别名称。

***verify_integrity*** ：bool，默认为 False

检查新的连接轴是否包含重复项。相对于实际的数据连接，这可能非常昂贵。

***sort*** : 布尔值，默认为 False

如果连接 为“外部”时未对齐，则对非连接轴进行排序。当join='inner'已经保留了非串联轴的顺序时，这不起作用。

> 在 1.0.0 版更改:默认情况下更改为不排序。

***copy*** : bool，默认 True

如果为 False，则不要不必要地复制数据。

## 返回值

object, type of objs

当Series沿着索引 (axis=0) 连接时， Series返回 a。当objs包含至少一个 DataFrame时，DataFrame返回 a。沿列连接时 (axis=1)，DataFrame返回 a。

## Examples

1. 结合两个Series。

```python
>>> s1 = pd.Series(['a', 'b'])
>>> s2 = pd.Series(['c', 'd'])
>>> pd.concat([s1, s2])

0    a
1    b
0    c
1    d
dtype: object
```

2. ignore_index通过将选项设置为 清除现有索引并在结果中将其重置True。

```python
>>> pd.concat([s1, s2], ignore_index=True)

0    a
1    b
2    c
3    d
dtype: object
```

3. keys使用该选项在数据的最外层添加层次索引。

```python
>>> pd.concat([s1, s2], keys=['s1', 's2'])

s1  0    a
    1    b
s2  0    c
    1    d
dtype: object
```

4. 使用该选项标记您创建的索引键names。

```python
>>> pd.concat([s1, s2], keys=['s1', 's2'],
          names=['Series name', 'Row ID'])

Series name  Row ID
s1           0         a
             1         b
s2           0         c
             1         d
dtype: object
```

5. 合并DataFrame具有相同列的两个对象。

```python
df1 = pd.DataFrame([['a', 1], ['b', 2]],
                   columns=['letter', 'number'])

df1

  letter  number
0      a       1
1      b       2

df2 = pd.DataFrame([['c', 3], ['d', 4]],
                   columns=['letter', 'number'])

df2

  letter  number
0      c       3
1      d       4

pd.concat([df1, df2])

  letter  number
0      a       1
1      b       2
0      c       3
1      d       4
```

6. DataFrame将具有重叠列的对象组合并返回所有内容。交叉点外的列将填充NaN值。

```python
df3 = pd.DataFrame([['c', 3, 'cat'], ['d', 4, 'dog']],
                   columns=['letter', 'number', 'animal'])

df3

  letter  number animal
0      c       3    cat
1      d       4    dog

pd.concat([df1, df3], sort=False)

  letter  number animal
0      a       1    NaN
1      b       2    NaN
0      c       3    cat
1      d       4    dog
```

7. 合并DataFrame具有重叠列的对象，并仅返回通过传递inner给join关键字参数共享的对象。

```python
pd.concat([df1, df3], join="inner")

  letter  number
0      a       1
1      b       2
0      c       3
1      d       4
```

8. DataFrame通过传入 沿 x 轴水平组合对象axis=1。

```python
df4 = pd.DataFrame([['bird', 'polly'], ['monkey', 'george']],
                   columns=['animal', 'name'])

pd.concat([df1, df4], axis=1)

  letter  number  animal    name
0      a       1    bird   polly
1      b       2  monkey  george
```

9. verify_integrity使用该选项防止结果包含重复的索引值 。

```python
df5 = pd.DataFrame([1], index=['a'])

df5

   0
a  1

df6 = pd.DataFrame([2], index=['a'])

df6

   0
a  2

pd.concat([df5, df6], verify_integrity=True)

Traceback (most recent call last):
    ...
ValueError: Indexes have overlapping values: ['a']
```