# NumPy 数组对象

```py
# 来源：NumPy Essentials ch2
```

## 数组索引和切片

```py
# 创建 100x100 个 0~1 随机数
x = np.random.random((100, 100)) 

# 取第 42 行 87 列的元素（从零开始）
y = x[42, 87]

# 取第 k 行的所有元素
# 等价于 x[k] 和 x[k, ...]
print(x[k, :]) 

a = np.array([[10 * y + x for x in range(6)] for y in range(6)])
'''
+--+--+--+--+--+--+
| 0| 1| 2| 3| 4| 5|
+--+--+--+--+--+--+
|10|11|12|13|14|15|
+--+--+--+--+--+--+
|20|21|22|23|24|25|
+--+--+--+--+--+--+
|30|31|32|33|34|35|
+--+--+--+--+--+--+
|40|41|42|43|44|45|
+--+--+--+--+--+--+
|50|51|52|53|54|55|
+--+--+--+--+--+--+
'''

a[0, 3:5]
'''
array([3, 4])
+--+--+--+--+--+--+
|  |  |  | 3| 4|  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
'''

a[4: ,4:]
'''
array([[44, 45],
       [54, 55]])
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |44|45|
+--+--+--+--+--+--+
|  |  |  |  |54|55|
+--+--+--+--+--+--+
'''

a[:, 2]
'''
array([ 2, 12, 22, 32, 42, 52])
+--+--+--+--+--+--+
|  |  | 2|  |  |  |
+--+--+--+--+--+--+
|  |  |12|  |  |  |
+--+--+--+--+--+--+
|  |  |22|  |  |  |
+--+--+--+--+--+--+
|  |  |32|  |  |  |
+--+--+--+--+--+--+
|  |  |42|  |  |  |
+--+--+--+--+--+--+
|  |  |52|  |  |  |
+--+--+--+--+--+--+
'''

a[2::2, ::2]
'''
array([[20, 22, 24],
       [40, 42, 44]])
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|20|  |22|  |  |24|
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
|40|  |42|  |  |44|
+--+--+--+--+--+--+
|  |  |  |  |  |  |
+--+--+--+--+--+--+
'''
```

## 内存布局

```py
# flags 属性保存了数组的内存布局信息
print x.flags
'''
  C_CONTIGUOUS : True 
  F_CONTIGUOUS : False 
  OWNDATA : True 
  WRITEABLE : True 
  ALIGNED : True 
  UPDATEIFCOPY : False 
  
C_CONTIGUOUS：是否为 C 风格连续，也就是行为主，最后一个维度是连续的
F_CONTIGUOUS：是否为 F 风格连续，也就是列为主，第一个维度是连续的
OWNDATA：是否拥有数据，视图不拥有数据
WRITEABLE：是否可写
ALIGNED：是否对齐
UPDATEIFCOPY：
'''

# NumPy 默认是 C 风格连续
c_array = np.random.rand(10000, 10000) 
# 可以手动转换为 F 风格连续
f_array = np.asfortranarray(c_array) 

def sum_row(x):
    '''
    计算第零行的和
    '''
    return np.sum(x[0, :])
def sum_col(x):
    '''
    计算第零列的和
    '''
    return np.sum(x[:, 0])
    
'''
我们可以看到，C 风格数组按行访问比较快
F 风格数组按列访问比较快

%timeit sum_row(c_array) 
10000 loops, best of 3: 21.2 µs per loop 
 
%timeit sum_row(f_array) 
10000 loops, best of 3: 157 µs per loop 
 
%timeit sum_col(c_array) 
10000 loops, best of 3: 162 µs per loop 
 
%timeit sum_col(f_array) 
10000 loops, best of 3: 21.4 µs per loop 
'''
```

## 副本和视图

```py
# 视图不共享 NumPy 对象，共享底层数据
# 副本不共享 NumPy 对象，不共享底层数据

x = np.random.rand(100,10)

# 切片和索引都会产生视图
# 而不是副本
y = x[:5, :] 

# 看看底层内存是否一致
np.may_share_memory(x, y)
# True

# 我们将 y 所有元素清零
y[:] = 0

# 并打印 x 前 5 行
print(x[:5, :])
'''
[[ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.] 
 [ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.] 
 [ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.] 
 [ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.] 
 [ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.]] 
'''

# 但是这样不会产生视图
x = np.random.rand(100,10)
y = np.empty([5, 10])
y[:] = x[:5, :]
np.may_share_memory(x, y)
# False 

y[:] = 0 
print(x[:5, :])
```

## 数组创建

```py
# 最简单的方式就是从 Python 列表创建 NumPy 数组
x = np.array([1, 2, 3])
y = np.array(['hello', 'world']) 

# 但有时我们想创建范围内的数值数组
x = range(5)
y = np.array(x) 

# NumPy 有个辅助函数
# 等价于上面的操作
x = np.arange(5) 

# 多维数组也是一样的
x = np.array([[1, 2, 3],[4, 5, 6]]) 

x.ndim # 2
x.shape # (2, 3)

# rand 创建指定形状的数组，元素为 0~1 的随机数
x = np.random.rand(2, 2, 2) 
print(x.shape) 
# (2, 2, 2)

# random 和 rand 相似
# 只是接受元组形式的形状
shape_tuple = (2, 3, 4) 
y = np.random.random(shape_tuple)
print(y.shape) 
# (2, 3, 4) 

# randint(l, h, size=sz) 创建 l ~ h-1 的随机整数
# 默认是 10 个
LOW, HIGH = 1, 11 
SIZE = 10
x = np.random.randint(LOW, HIGH, size=SIZE) 
print(x) 
[ 6  9 10  7  9  5  8  8  9  3] 

# 还有一些其它的创建函数
# zeros(size) 和 ones(size) 创建指定形状的全零或全一数组
# eye(n) 创建 n 维单位矩阵
# full(size, n) 创建指定形状的纯量数组，所有元素都为 n
```

## 数据类型

```py

x = np.random.random((10,10)) 
 
# dtype 属性是数据类型
x.dtype 
# dtype('float64') 

x = np.array(range(10)) 
x.dtype 
# dtype('int32') 
 
x = np.array(['hello', 'world']) 
x.dtype 
# dtype('S5') 

# 创建数组时可以指定数据类型
# 我们可以传入 NumPy 类型
x = np.ones((10, 10), dtype=np.int) 
x.dtype 
# dtype('int32') 

# 也可以传入表示类型的字符串
x = np.zeros((10, 10), dtype='|S1')
x.dtype 
# dtype('S1') 

# NumPy 会使用它们来构造 dtype
# 完整列表请见
# http://docs.scipy.org/doc/numpy/user/basics.types.html
```
