---
title: Numpy的使用
date: 2022-03-01 15:57:54
tags: Numpy的学习
---
# NumPy使用教程
## 安装

`pip3 install numpy`

> 验证安装成功
```commandline
from numpy import *
print(eye(4))
```

## NumPy Ndarray 对象
`numpy.array(object, dtype = None, copy = True, order = None, subok = False, ndmin = 0)`

## NumPy 数据类型
| 属性           | 说明            |
|--------------|---------------|
| ndarray.ndim | 秩，即轴的数量或维度的数量 |
|ndarray.shape|数组的维度，对于矩阵，n行m列|
|narray.size|数组元素的总个数，相当于.shape中n*m的值|
|ndarray.itemsize|ndarray对象中每个元素的大小，以字节为单位|
|ndarray.flags|ndarray对象的内存信息|
|ndarray.real|ndarray元素的实部|
|ndarray.imag|ndarray元素的虚部|
|ndarray.data|包含实际数组元素的缓冲区，由于一般通过数组的索引获取元素，所以通常不需要使用这个属性|

## 创建数组
### numpy.empty
`numpy.empty(shape, dtype = float, order = 'C')`
#### 参数说明
| 参数    | 描述   |
|-------|------|
| shape | 数组类型 |
|dtype|数据类型，可选|
|order|有"C"和"F" 两个选项，分别代表，行优先和列优先，在计算机内存中的存储元素的顺序|

#### 尝试
```commandline
import numpy as np

x = np.empty([3,2],dtype=int)
print(x)
```

    [[ 6917529027641081856  5764616291768666155]
     [ 6917529027641081859 -5764598754299804209]
     [          4497473538      844429428932120]]

> 数组元素为随机值，因此他们未初始化。
### numpy.zeros
`numpy.zeros(shape,dtype=float,order = 'C'')`

|参数|描述|
|---|---|
|shape|数组类型|
|dtype|数组类型，可选|
|order|'C'用于C的行数组，或者'F'用于FORTRAN的列数组|

#### 尝试
```commandline
import numpy as np

# 默认为浮点数
x = np.zeros(5)
print(x)

# 设置类型为整数
y = np.zeros((5,),dtype=np.int_)
print(y)

# 自定义类型
z = np.zeros((2,2),dtype=[('x','i4'),('y','i4')])
print(z)
```


    [0. 0. 0. 0. 0.]
    [0 0 0 0 0]
    [[(0, 0) (0, 0)]
     [(0, 0) (0, 0)]]

### numpy.ones
> 和numpy.zeros类似，不过将里面的的数改为1。

## NumPy 从已有的数组创建数组
### numpy.asarray
`numpy.asarray(a, dtype = None, order = None)`

|参数|描述|
|---|---|
|a|任意形式的输入参数，可以是列表、列表的元组、元组、元组的元组、元组的列表、多维元组|
|dtype|数据类型，可选|
|order|可选，有"C"和"F"两个选项，分别戴白哦，行优先和列优先，在计算机内存中的存储元素的顺序|
#### 尝试
```commandline
import numpy as np

x = [1,2,3]
a = np.asarray(x)
print(type(a),a)
```
    
    <class 'numpy.ndarray'> [1 2 3]

```commandline
import numpy as np

x = [1,2,3]
y = (1,2,3)
m = [(1,2,3),(4,5)]
a = np.asarray(x) # 将列表转化为ndarray
b = np.asarray(y) # 将元组转化为ndarray
c = np.asarray(m,dtype=object) # 将元组列表转换为ndarray
d = np.asarray(a,dtype = float) # 设置了dtype参数
print([{type(p):p} for p in [a,b,c,d]])
```
### numpy.frombuffer
`numpy.frombuffer(buffer, dtype = float, count = -1, offset = 0)`
> *注意：* buffer 是字符串的时候，Python3 默认 str是Unicode类型，所以要转成bytestring在原str前加上b。

| 参数       | 描述 |
|----------|------------------|
| buffer   | 可以是任意对象，会以流的形式读入 |
| dtype    |返回数组的数据类型，可选|
| count    |读取的数据数量，默认为-1，读取所有数据。|
| offset   |读取的起始位置，默认为0|

#### 尝试
```commandline
import numpy as np

s = b"Hello World"
a = np.frombuffer(s,dtype='S1')
print(a)
```
    
    [b'H' b'e' b'l' b'l' b'o' b' ' b'W' b'o' b'r' b'l' b'd']

### numpty.fromiter
`numpy.fromiter(iterable, dtype, count=-1)`

| 参数       | 描述                    |
|----------|-----------------------|
| iterable | 可迭代对象                 |
| dtype    | 返回数组的数据类型             |
| count    | 读取的数据数量，默认为-1，读取所有数据。 |
#### 尝试
```commandline
import numpy as np

# 使用 range 函数出啊见列表对象
list = range(5)
it = iter(list)

# 使用迭代器创建 ndarray
x = np.fromiter(it,dtype=float)
print(type(x),x)
```
    
    [0. 1. 2. 3. 4.]

## NumPy从数值范围创建数组
### numpy.arange

#### 尝试
```commandline
import numpy as np

# 生成0到5的数组
x = np.arange(5)
# 设置了 dtype
y = np.arange(5,dtype = float)
print([{type(p):p}for p in [x,y]])
```
    
    [{<class 'numpy.ndarray'>: array([0, 1, 2, 3, 4])}, {<class 'numpy.ndarray'>: array([0., 1., 2., 3., 4.])}]

### numpy.linspace
`np.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None)`
#### 尝试
```commandline
import numpy as np

# 设置起始点为1，终止点为10，数列个数为10
a = np.linspace(1,10,10)

# 设置元素全部是1的等差数列
b = np.linspace(1,1,10)

# 将 endpoint 设为 false，不包含终止值
c = np.linspace(10,20,5,endpoint=False)

# 如果将 endpoint 设为 true，则会包含 20
d = np.linspace(10,20,5,endpoint=True)

print(a,b,c,d,sep='\n')
```
    [ 1.  2.  3.  4.  5.  6.  7.  8.  9. 10.]
    [1. 1. 1. 1. 1. 1. 1. 1. 1. 1.]
    [10. 12. 14. 16. 18.]
    [10.  12.5 15.  17.5 20. ]

### numpy.logspace
`np.logspace(start, stop, num=50, endpoint=True, base=10.0, dtype=None)`

```commandline
import numpy as np

# 默认底数是10
a = np.logspace(1.0,2.0,num=10)

# 将对数的底数设置为2
b = np.logspace(0,9,10,base=2)
print(a,b,sep='\n')
```

    [ 10.          12.91549665  16.68100537  21.5443469   27.82559402
      35.93813664  46.41588834  59.94842503  77.42636827 100.        ]
    [  1.   2.   4.   8.  16.  32.  64. 128. 256. 512.]

```commandline
import numpy as np

a = np.arange(10)
s = slice(2,7,2)
print(a[s])
```
    
    [2 4 6]
|例子|解释|
|---|---|
|`[2]`|返回与该索引相对应的单个元素|
|`[2:]`|表示从该索引开始以后的所有项都将被提取|
|`[2:7]`|提取两个索引（不包括停止索引）之间的项|
|`[...,1]`|返回第二列元素。省略号`...`，选择元组的长度与数组的维度相同|

```commandline
import numpy as np

a = np.arange(10)
d = np.array([[1,2,3],[3,4,5],[4,5,6]])
print(a[2:],a[2:5],d,d[1:],d[...,1],d[1,...],d[...,1:],sep='\n')
```

    [2 3 4 5 6 7 8 9] 
    [2 3 4]
    [[1 2 3]
     [3 4 5]
     [4 5 6]]
    [[3 4 5]
     [4 5 6]]
    [2 4 5]
    [3 4 5]
    [[2 3]
     [4 5]
     [5 6]] 

## NumPy 高级索引
### 整数数组索引
```
import numpy as np

# 获取数组中（0,0）,（1,1）,(2,0)位置处的元素
x = np.array([[1,2],[3,4],[5,6]])
y = x[[0,1,2],[0,1,0]] #第一个数组代表行，第二个数组代表列。
print("x:",x,"y:",y,sep='\n')
```

	x:
	[[1 2]
	 [3 4]
	 [5 6]]
	y:
	[1 4 5]

```
import numpy as np

x = np.array([[0,1,2],[3,4,5],[6,7,8],[9,10,11]])
print('我们的数组是：')
print(x)
print('\n')
rows = np.array([[0,0],[3,3]])
cols = np.array([[0,2],[0,2]])
y = x[rows,cols]
print("这个数组的四个角元素是：")
print(y)
```

	我们的数组是：
	[[ 0  1  2]
	 [ 3  4  5]
	 [ 6  7  8]
	 [ 9 10 11]]


	这个数组的四个角元素是：
	[[ 0  2]
	 [ 9 11]]
	 
```
import numpy as np

a = np.array([[1,2,3],[4,5,6],[7,8,9]])
b = a[1:3,1:3]
c = a[1:3,[1,2]]
d = a[...,1:]
print(b)
print(c)
print(d)
```

	[[5 6]
	 [8 9]]
	[[5 6]
	 [8 9]]
	[[2 3]
	 [5 6]
	 [8 9]]
	 
### 布尔索引
```
import numpy as np

x = np.array([[0,1,2],[3,4,5],[6,7,8],[9,10,11]])
print("我们的数组是：")
print(x)
print('\n')
# 现在我们会打印出大于5的元素
print("大于5的元素是：")
print(x[x > 5])
```

	我们的数组是：
	[[ 0  1  2]
	 [ 3  4  5]
	 [ 6  7  8]
	 [ 9 10 11]]


	大于5的元素是：
	[ 6  7  8  9 10 11]
	
	
```
import numpy as np

a = np.array([np.nan,1,2,np.nan,3,4,5])
print(a)
print(a[~np.isnan(a)])
```


	[nan  1.  2. nan  3.  4.  5.]
	[1. 2. 3. 4. 5.]

```
import numpy as np

a = np.array([1,2+6j,5,3.5+5j])
print(np.iscomplex(a))
print(a[np.iscomplex(a)])
```

	[False  True False  True]
	[2. +6.j 3.5+5.j]
	
### 花絮索引
```
import numpy as np

x = np.arange(32).reshape((8,4))
print(x[[4,2,1,7]])
```

	[[16 17 18 19]
	 [ 8  9 10 11]
	 [ 4  5  6  7]
	 [28 29 30 31]]
	 
```
import numpy as np

x = np.arange(32).reshape((8,4))
print(x[[-4,-1,-1,-7]])
```

	[[16 17 18 19]
	 [28 29 30 31]
	 [28 29 30 31]
	 [ 4  5  6  7]]
	 
```
import numpy as np

x = np.arange(32).reshape((8,4))
print(x[np.ix_([1,5,7,2],[0,3,1,2])])
```

	[[ 4  7  5  6]
	 [20 23 21 22]
	 [28 31 29 30]
	 [ 8 11  9 10]]
	 
## NumPy 广播（Broadcast）
```
import numpy as np

a = np.array([1,2,3,4])
b = np.array([10,20,30,40])
c = a * b
print(c)
```

	[ 10  40  90 160]
	
