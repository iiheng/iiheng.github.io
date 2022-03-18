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
	
```commandline
import numpy as np

a = np.array([[0,0,0],[10,10,10],[20,20,20],[30,30,30]])
b = np.array([1,2,3])
print(a + b)
```
    
    [[ 1  2  3]
     [11 12 13]
     [21 22 23]
     [31 32 33]]

```commandline
import numpy as np

a = np.array([[0,0,0],[10,10,10],[20,20,20],[30,30,30]])
b = np.array([1,2,3])
bb = np.tile(b,(4,1))
print(a + bb)
```

  [[ 1  2  3]
   [11 12 13]
   [21 22 23]
   [31 32 33]]

## NumPy 迭代数组
```commandline
import numpy as np

a = np.arange(6).reshape(2,3)
print(a)
for x in np.nditer(a.T):
    print(x,end=",")
print('\n')

for x in np.nditer(a.T.copy(order='C')):
    print(x,end=",")
print('\n')
```

   [[0 1 2]
    [3 4 5]]
   0,1,2,3,4,5,
   
   0,3,1,4,2,5,
   
### 控制遍历顺序
> for x in np.nditer(a,order="F"): Fortran order,即是列表优先
> for x in np.nditer(a.T,order='C'):C order ,即是行序优先

```commandline
import numpy as np

a = np.arange(0,60,5)
a = a.reshape(3,4)
print("原始数组是：")
print(a)
print("\n")
print('原始数组的转置是：')
b = a.T
print(b)
print("\n")
print('以C风格顺序排序：')
c = b.copy(order="C")
print(c)
for x in np.nditer(c):
    print(x,end=",")

print("\n")
print("以F风格顺序排序：")
c = b.copy(order="F")
print(c)
for x in np.nditer(c):
    print(x,end=",")
```
    原始数组是：
    [[ 0  5 10 15]
     [20 25 30 35]
     [40 45 50 55]]
    
    
    原始数组的转置是：
    [[ 0 20 40]
     [ 5 25 45]
     [10 30 50]
     [15 35 55]]
    
    
    以C风格顺序排序：
    [[ 0 20 40]
     [ 5 25 45]
     [10 30 50]
     [15 35 55]]
    0,20,40,5,25,45,10,30,50,15,35,55,
    
    以F风格顺序排序：
    [[ 0 20 40]
     [ 5 25 45]
     [10 30 50]
     [15 35 55]]
    0,5,10,15,20,25,30,35,40,45,50,55,

```commandline
import numpy as np

a = np.arange(0,60,5)
a = a.reshape(3,4)
print("原始数组是：")
print(a)
print("\n")
print("以C风格顺序排序")
for x in np.nditer(a,order="C"):
    print(x,end=",")
print("\n")
print("以F风格顺序排序：")
for x in np.nditer(a,order="F"):
    print(x,end=",")
```

    原始数组是：
    [[ 0  5 10 15]
     [20 25 30 35]
     [40 45 50 55]]
    
    
    以C风格顺序排序
    0,5,10,15,20,25,30,35,40,45,50,55,
    
    以F风格顺序排序：
    0,20,40,5,25,45,10,30,50,15,35,55,
    Process finished with exit code 0

```commandline
import numpy as np

a = np.arange(0,60,5)
a = a.reshape(3,4)
print("原始数组是：")
print(a)
print('\n')
for x in np.nditer(a,op_flags=['readwrite']):
    x[...] = 2*x
print("修改后的数组是：")
print(a)
```

    原始数组是：
    [[ 0  5 10 15]
     [20 25 30 35]
     [40 45 50 55]]
    
    
    修改后的数组是：
    [[  0  10  20  30]
     [ 40  50  60  70]
     [ 80  90 100 110]]

```commandline
import numpy as np

a = np.arange(0,60,5)
a = a.reshape(3,4)
print("原始数组是：")
print(a)
print('\n')
for x in np.nditer(a,flags = ['external_loop'],order='F'):
    print(x,end=",")
```

    [[ 0  5 10 15]
     [20 25 30 35]
     [40 45 50 55]]
    
    
    [ 0 20 40],[ 5 25 45],[10 30 50],[15 35 55],

```commandline
import numpy as np

a = np.arange(0,60,5)
a = a.reshape(3,4)
print("第一个数组为：")
print(a)
print("\n")
print("第二个数组为：")
b = np.array([1,2,3,4],dtype=int)
print(b)
print("\n")
print("修改后的数组为：")
for x,y in np.nditer([a,b]):
    print("%d:%d"%(x,y),end=",")
```

    第一个数组为：
    [[ 0  5 10 15]
     [20 25 30 35]
     [40 45 50 55]]
    
    
    第二个数组为：
    [1 2 3 4]
    
    
    修改后的数组为：
    0:1,5:2,10:3,15:4,20:1,25:2,30:3,35:4,40:1,45:2,50:3,55:4,

## Numpy数组操作
### 修改数组形状

|函数| 描述                        |
|-----|---------------------------|
|reshape| 不改变数据的条件下修改形状             |
|flat| 数组元素迭代器                   |
|flatten| 返回一份数组拷贝，对拷贝所做的修改不会影响原始数组 |
|ravel|返回展开数组|

#### numpy.reshape
```commandline
import numpy as np

a = np.arange(8)
print("原始数组：")
print(a)
print("\n")

b = a.reshape(4,2)
print("修改后的数组")
print(b)
```
    
    原始数组：
    [0 1 2 3 4 5 6 7]
    
    
    修改后的数组
    [[0 1]
     [2 3]
     [4 5]
     [6 7]]

#### numpy.ndarray.flat
```
import numpy as np

a = np.arange(9).reshape(3,3)
print("原始数组：")
for row in a:
    print(row)

print("迭代后的数组：")
for element in a.flat:
    print(element)
```

    原始数组：
    [0 1 2]
    [3 4 5]
    [6 7 8]
    迭代后的数组：
    0
    1
    2
    3
    4
    5
    6
    7
    8
#### numpy.ndarray.flatten
`ndarray.flatten(order="C")`
> order:'C'--按行，'F'--按列，'A'--原顺序，'K'--元素在内存中的出现顺序。
```
import numpy as np

a = np.arange(8).reshape(2,4)

print("原数组：")
print(a)
print("\n")
# 默认按行

print("展开的数组：")
print(a.flatten())
print("\n")

print("以 F 风格顺序展开的数组：")
print(a.flatten(order='F'))
```

	原数组：
	[[0 1 2 3]
	 [4 5 6 7]]


	展开的数组：
	[0 1 2 3 4 5 6 7]


	以 F 风格顺序展开的数组：
	[0 4 1 5 2 6 3 7]
	
#### numpy.ravel
`numpy.ravel(a,order="C")`

> order:"C" --按行,"F"--按列,"A"--原顺序,"K"--元素在内存中的出现顺序。
```
import numpy as np

a = np.arange(8).reshape(2,4)

print("原数组：")
print(a)
print("\n")

print("调用ravel函数之后：")
print(a.ravel())
print('\n')

print('以F风格顺序调用ravel函数之后：')
print(a.ravel(order="F"))
```

	原数组：
	[[0 1 2 3]
	 [4 5 6 7]]


	调用ravel函数之后：
	[0 1 2 3 4 5 6 7]


	以F风格顺序调用ravel函数之后：
	[0 4 1 5 2 6 3 7]
	
### 翻转数组

|函数|描述|
|----|----|
|transpose|对换数组的维度|
|ndarray.T|和self.transpose()相同|
|rollaxis|向后滚动指定的轴|
|swapaxes|对换数组的两个轴|

#### numpy.transpose

`numpy.transpose(arr,axes)`

```
import numpy as np

a = np.arange(12).reshape(3,4)

print("原数组：")
print(a)
print("\n")

print("对换数组：")
print(np.transpose(a))
```

	原数组：
	[[ 0  1  2  3]
	 [ 4  5  6  7]
	 [ 8  9 10 11]]


	对换数组：
	[[ 0  4  8]
	 [ 1  5  9]
	 [ 2  6 10]
	 [ 3  7 11]]

#### numpy.ndarray.T
 
```
import numpy as np

a = np.arange(12).reshape(3,4)

print("原数组：")
print(a)
print("\n")

print("转置数组：")
print(a.T)
```

	原数组：
	[[ 0  1  2  3]
	 [ 4  5  6  7]
	 [ 8  9 10 11]]


	转置数组：
	[[ 0  4  8]
	 [ 1  5  9]
	 [ 2  6 10]
	 [ 3  7 11]]
	 
#### numpy.rollaxis
`numpy.rollaxis(arr,axis,start)`

```
import numpy as np

# 创建了三维的 ndarray
a = np.arange(8).reshape(2,2,2)

print('原数组：')
print(a)
print("获取数组中一个值：")
print(np.where(a==6))
print(np.where(a==4))
print(a[1,1,0])
print('\n')

# 将轴 2 滚动到轴 0
print("调用rollaxis函数1：")
b = np.rollaxis(a,2,0)
print(b)
# 查看元素a[1,1,0],即 6 的坐标，变成[0,1,1]
# 最后一个 0 移动到最前面
print(np.where(b==6))
print('\n')

# 将轴 2 滚动到轴 1

print("调用rollaxis函数2：")
c = np.rollaxis(a,2,1)
print(c)
# 查看元素a[1,1,0],即 6 的坐标，变成[1,0,1]
# 最后的 0 和它前面的 1 对换位置
print(np.where(c==6))
print("\n")
```

	原数组：
	[[[0 1]
	  [2 3]]

	 [[4 5]
	  [6 7]]]
	获取数组中一个值：
	(array([1], dtype=int64), array([1], dtype=int64), array([0], dtype=int64))
	(array([1], dtype=int64), array([0], dtype=int64), array([0], dtype=int64))
	6


	调用rollaxis函数1：
	[[[0 2]
	  [4 6]]

	 [[1 3]
	  [5 7]]]
	(array([0], dtype=int64), array([1], dtype=int64), array([1], dtype=int64))


	调用rollaxis函数2：
	[[[0 2]
	  [1 3]]

	 [[4 6]
	  [5 7]]]
	(array([1], dtype=int64), array([0], dtype=int64), array([1], dtype=int64))
	
#### numpy.swapaxes
`numpy.swapaxes(arr,axis1,axis2)`

```
import numpy as np

# 创建了三维的 ndarray
a = np.arange(8).reshape(2,2,2)

print("原数组：")
print(a)
print("\n")
# 现在交换 0 到轴 2

print("调用 swapaxes 函数后的数组：")
print(np.swapaxes(a,2,0))
```

	[[[0 4]
	  [2 6]]

	 [[1 5]
	  [3 7]]]
	  
### 修改数组维度
|维度|描述|
|broadcast|产生模仿广播的对象|
|broadcast_to|将数组广播到新形状|
|expand_dims|扩展数组的形状|
|squeeze|从数组的形状中删除一维条目|

#### numpy.broadcast

#### numpy.broadcast_to
`numpy.broadcast_to(array, shape, subok)`

```
import numpy as np

a = np.arange(4).reshape(1,4)

print("原数组")
print(a)
print('\n')

print('调用broadcast_to函数之后：')
print(np.broadcast_to(a,(4,4)))
```

	原数组
	[[0 1 2 3]]


	调用broadcast_to函数之后：
	[[0 1 2 3]
	 [0 1 2 3]
	 [0 1 2 3]
	 [0 1 2 3]]
	
### 连接数组
#### numpy.concatenate
`numpy.concatenate((a1, a2, ...), axis)`
```
import numpy as np

a = np.array([[1,2],[3,4]])

print('第一个数组：')
print(a)
print('\n')
b = np.array([[5,6],[7,8]])

print('第二个数组：')
print(b)
print('\n')

print('延轴 0 连接两个数组')
print(np.concatenate((a,b)))
print('\n')

print('沿轴 1 连接两个数组：')
print(np.concatenate((a,b),axis=1))
```

	第一个数组：
	[[1 2]
	 [3 4]]


	第二个数组：
	[[5 6]
	 [7 8]]


	延轴 0 连接两个数组
	[[1 2]
	 [3 4]
	 [5 6]
	 [7 8]]


	沿轴 1 连接两个数组：
	[[1 2 5 6]
	 [3 4 7 8]]
	 
#### numpy.stack
`numpy.stack(arrays, axis)`
```
import numpy as np

a = np.array([[1,2],[3,4]])

print('第一个数组：')
print(a)
print('\n')
b = np.array([[5,6],[7,8]])

print('第二个数组：')
print(b)
print('\n')

print('延轴 0 堆叠两个数组：')
print(np.stack((a,b)))
print('\n')

print('沿轴 1 堆叠两个数组：')
print(np.stack((a,b),axis=1))
```

	第一个数组：
	[[1 2]
	 [3 4]]


	第二个数组：
	[[5 6]
	 [7 8]]


	延轴 0 连接两个数组
	[[[1 2]
	  [3 4]]

	 [[5 6]
	  [7 8]]]


	沿轴 1 连接两个数组：
	[[[1 2]
	  [5 6]]

	 [[3 4]
	  [7 8]]]

#### numpy.hstack
```
import numpy as np

a = np.array([[1,2],[3,4]])

print('第一个数组')
print(a)
print('\n')
b = np.array([[5,6],[7,8]])

print('第二个数组：')
print(b)
print('\n')

print('水平堆叠：')
c = np.hstack((a,b))
print(c)
print('\n')
```

	第一个数组
	[[1 2]
	 [3 4]]


	第二个数组：
	[[5 6]
	 [7 8]]


	水平堆叠：
	[[1 2 5 6]
	 [3 4 7 8]]
	 
#### numpy.vstack
```
import numpy as np

a = np.array([[1,2],[3,4]])

print('第一个数组')
print(a)
print('\n')
b = np.array([[5,6],[7,8]])

print('第二个数组：')
print(b)
print('\n')

print('垂直堆叠：')
c = np.vstack((a,b))
print(c)
```

	第一个数组
	[[1 2]
	 [3 4]]


	第二个数组：
	[[5 6]
	 [7 8]]


	垂直堆叠：
	[[1 2]
	 [3 4]
	 [5 6]
	 [7 8]]
	 
### 分割数组
#### numpy.split
`numpy.split(ary, indices_or_sections, axis)`
```
import numpy as np

a = np.arange(9)

print('第一个数组：')
print(a)
print('\n')

print("将数组分为三个大小相等的子数组：")
b = np.split(a,3)
print(b)
print('\n')

print('将数组在一维数组中表明的位置分割：')
b = np.split(a,[4,7])
print(b)
```

	第一个数组：
	[0 1 2 3 4 5 6 7 8]


	将数组分为三个大小相等的子数组：
	[array([0, 1, 2]), array([3, 4, 5]), array([6, 7, 8])]


	将数组在一维数组中表明的位置分割：
	[array([0, 1, 2, 3]), array([4, 5, 6]), array([7, 8])]
	
```
import numpy as np

a = np.arange(16).reshape(4,4)
print('第一个数组：')
print(a)
print('\n')
print('默认分割（0轴）:')
b = np.split(a,2)
print(b)
print('\n')

print('沿垂直方向分割：')
c = np.split(a,2,1)
print(c)
print('\n')

print('沿水平方向分割：')
d = np.hsplit(a,2)
print(d)
```

	第一个数组：
	[[ 0  1  2  3]
	 [ 4  5  6  7]
	 [ 8  9 10 11]
	 [12 13 14 15]]


	默认分割（0轴）：
	[array([[0, 1, 2, 3],
		   [4, 5, 6, 7]]), array([[ 8,  9, 10, 11],
		   [12, 13, 14, 15]])]


	沿垂直方向分割：
	[array([[ 0,  1],
		   [ 4,  5],
		   [ 8,  9],
		   [12, 13]]), array([[ 2,  3],
		   [ 6,  7],
		   [10, 11],
		   [14, 15]])]


	沿水平方向分割：
	[array([[ 0,  1],
		   [ 4,  5],
		   [ 8,  9],
		   [12, 13]]), array([[ 2,  3],
		   [ 6,  7],
		   [10, 11],
		   [14, 15]])]
		   
#### numpy.hsplit
```
import numpy as np

harr = np.floor(10 * np.random.random((2,6)))
print('原array')
print(harr)

print('拆分后：')
print(np.hsplit(harr,3))
```

	原array
	[[3. 2. 3. 0. 3. 7.]
	 [3. 5. 8. 3. 3. 1.]]
	拆分后：
	[array([[3., 2.],
		   [3., 5.]]), array([[3., 0.],
		   [8., 3.]]), array([[3., 7.],
		   [3., 1.]])]
		   
#### numpy.vsplit
```
import numpy as np

a = np.arange(16).reshape(4,4)

print('第一个数组：')
print(a)
print('\n')

print('垂直分割：')
b = np.vsplit(a,2)
print(b)
```

	第一个数组：
	[[ 0  1  2  3]
	 [ 4  5  6  7]
	 [ 8  9 10 11]
	 [12 13 14 15]]


	竖直分割：
	[array([[0, 1, 2, 3],
		   [4, 5, 6, 7]]), array([[ 8,  9, 10, 11],
		   [12, 13, 14, 15]])]
		   
### 数组元素的添加与删除
```
import numpy as np

a = np.arange(6).reshape(2,3)

print('第一个数组：')
print(a)
print('\n')

print('第一个数组的形状：')
print(a.shape)
print('\n')
b = np.resize(a,(3,2))

print('第二个数组：')
print(b)
print('\n')

print('第二个数组的形状：')
print(b.shape)
print('\n')

print("修改第二个数组的大小：")
b = np.resize(a,(3,3))
print(b)
```

	第一个数组：
	[[0 1 2]
	 [3 4 5]]


	第一个数组的形状：
	(2, 3)


	第二个数组：
	[[0 1]
	 [2 3]
	 [4 5]]


	第二个数组的形状：
	(3, 2)


	修改第二个数组的大小：
	[[0 1 2]
	 [3 4 5]
	 [0 1 2]]
	 
#### numpy.append
```
import numpy as np

a = np.array([[1,2,3],[4,5,6]])

print('第一个数组：')
print(a)
print('\n')

print('向数组添加元素：')
print(np.append(a,[7,8,9]))
print('\n')

print('沿轴 0 添加元素：')
print(np.append(a,[[7,8,9]],axis = 0))
print('\n')

print('沿轴 1 添加元素：')
print(np.append(a,[[5,5,5],[7,8,9]],axis=1))
```

	第一个数组：
	[[1 2 3]
	 [4 5 6]]


	向数组添加元素：
	[1 2 3 4 5 6 7 8 9]


	沿轴 0 添加元素：
	[[1 2 3]
	 [4 5 6]
	 [7 8 9]]


	沿轴 1 添加元素：
	[[1 2 3 5 5 5]
	 [4 5 6 7 8 9]]
	 
#### numpy.insert
