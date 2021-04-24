---
title: 科学计算之NumPy
date: 2019-07-22 23:31:54
tags:
- Python
- Numpy
categories:
- Python
---

NumPy库学习

---

## 数组

NumPy的主要对象为同类型的多维数组
维度成为轴，轴的数目为rank

1. 一维数组

    ```Python
    [1, 2, 3]
    ```

2. 二维数组

    ```Python
    [[1, 2, 3],
    [2, 3, 4]]
    ```

    > 逻辑分割

NumPy的数组命名ndarray，简称array
> 与标准Python库array.array不同，标准库内容少

### 数组属性

+ `ndarray.ndim`  判断数组的轴的个数

    ```Python
    import numpy as np
    x = np.array([[1, 2, 3], [4, 5, 6]])
    print(x)  # array([1, 2, 3], [4, 5, 6])
    print(x.ndim)  # 2
    ```

+ `ndarray.shape`  对n行m列的矩阵，shape返回(n, m)

    ```Python
    import numpy as np
    x = np.array([[1, 2, 3], [4, 5, 6]])
    print(x.shape)  # (2, 3)
    ```

+ `ndarry.size` 数组元素的总数等于shape的乘积

    ```Python
    import numpy as np
    x = np.array([[1, 2, 3], [4, 5, 6]])
    print(x.size)  # 6
    ```

+ `ndarry.dtype` 描述数组中元素类型的对象

    ```Python
    import numpy as np
    x = np.array([[1, 2, 3], [4, 5, 6]])
    print(x.dtype)  # dtype('int64')
    y = np.array([1., 2, 3])  # 向上转化浮点数优先级高
    print(y)  # array([1., 2., 3.])
    print(y.dtype)  # dtype('float64')
    ```

+ `ndarry.itemsize` 数组中每个元素的字节大小

    ```Python
    import numpy as np
    x = np.array([[1, 2, 3], [4, 5, 6]])
    print(x.itemsize)  # 8
    ```

    > float64类型数组的itemsize为8(=64/8)
    > complex32类型数组的itemsize为4(=32/4)

+ `ndarray.strides` 步长幅度(有疑问，后续需要查资料)

    ```Python
    import numpy as np
    x = np.array([[1, 2, 3], [4, 5, 6]])
    print(x.strides)  # (14, 4)
    y = np.array([[1, 2], [3, 4], [5, 6]])
    print(y.strides)  # (8, 4)
    z = np.array([1, 2, 3])
    print(z.strides)  # (4,)
    ```

+ `ndarray.data` 指针(不用)

+ `ndarray.flags` 存放数组自身相关属性

    ```Python
    import numpy as np
    x = np.array([[1, 2, 3], [4, 5, 6]])
    print(x.flags)
    '''
    F_CONTIGUOUS : False  #
    OWNDATA : True  # 是否属于自己
    WRITEABLE : True  # 是否可写
    ALIGNED : True  # 是否对齐
    WRITEBACKIFCOPY : False  
    UPDATEIFCOPY : False
    '''
    # 修改属性
    x.flags.writable = False
    ```

### 创建数组

1. `numpy.array` 使用array函数从常规Python**列表或元组**中创建数组

    ```Python
    import numpy as np
    print(np.array(1))  # array(1)  0维占位用
    ```

2. `numpy.zeros()` 创建一个由0组成的数组
   `numpy.ones()`  创建一个由1组成的数组

    ```Python
    import numpy as np
    print(np.zeros((3, 4)))  # 3x4
    print(np.ones(3, 4))
    print(np.zeros(3, 4), dtype = bool)  # 指定类型
    ```

3. `numpy.empty()` 生成无用数据

    ```Python
    import numpy as np
    print(np.empty(2, 3))
    ```

4. `numpy.random.random()` 创建随机

    ```Python
    import numpy as np
    print(np.random.random((2, 3)))
    ```

5. `numpy.arange()` 创建数字序列

    ```Python
    import numpy as np
    print(np.arange(0, 10, 2))  # 始，终，步长
    ```

6. `numpy.linspace()` 创建等步长的数字序列

    ```Python
    import numpy as np
    print(np.linspce(0, 2, 9))  # 始，终，个数
    ```

7. `numpy.fromfunction()` 从给定函数中创建数组

    ```Python
    import numpy as np
    def f(x, y):
        return 2*x+y
    print(np.fromfunction(f,(3, 3)))  
    ```

8. `numpy.eye()`和`numpy.identity()` 创建正方形矩阵，对角线是1，其余为零

    ```Python
    import numpy as np
    print(np.eye(2))
    print(np.identity(3))
    print(np.eye(4), k = 1)   # 向右偏移
    print(np.eye(4), k = -1)  # 向左偏移
    ```

9. `numpy.fromfile()`和`numpy.tofile()` 从文件中存取

10. `numpy.load()`和`numpy.save()` NumPy专用二级制存储文件
