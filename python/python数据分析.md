# python 数据分析
## Numpy
    多维数组对象 ndarray
### 方法
    1. array = np.array([1,2,3]) 创建数组 用list 元组 等可迭代对象

    2. array = np.zeros(shape=(2,10))  创建全0数组 参数为形状

    3. array = np.ones((2,5))  创建全1数组 参数为形状

    4. array = np.empty((2,3,4))  填充随机数  (3维)2个 3行4列

    5. np.arange()  生成一组序列
       1. np.arange(10) -> [0,9]
       2. np.arange(3,10) -> [3,9]
       3. np.arange(3,10,2) #间隔为2 -> [3,5,7,9]

    6. np.reshape()    矩阵规格变换

    7. np.astype(dtype)  数据类型变换  float -> int 直接去掉小数点

    8. np.linspace(start, stop, num=50)  在间隔start和stop之间返回num个均匀间隔的数据

    9.  copy()  切片 复制数组

    10. a.T 转置

    11. np.concatenate((array1,array2),axis = 1)  数组合并，axis = 1 增加列 只增加某个维度的长度
         行方向 : 0 轴  列方向 : 1 轴

    12. np.stack((a1,a2))  增加了一个维度

    13. np.append(a1,5)  添加元素 高维数组应添加轴 否则展开为1维数组

    14. np.unique(array) 对array中元素进行去重

    15. np.cumsum(a,  axis=None)  累加
        1.  axis=0，按照行累加。
        2.  axis=1，按照列累加。
        3.  axis不给定具体值，就把numpy数组当成一个一维数组

    16. numpy.random()
        1. numpy.random.rand(ndim) rand函数根据给定维度生成[0,1)之间的数据，包含0，不包含1
        2. numpy.random.randn(ndim) randn函数返回一个或一组样本，具有标准正态分布

    17. np.where()
        1. np.where(condition,x,y) 当where内有三个参数时，第一个参数表示条件，当条件成立时where方法返回x，当条件不成立时where返回y
        2. 当where内只有一个参数时，那个参数表示条件，当条件成立时，where返回的是每个符合condition条件元素的坐标,返回的是以元组的形式
        3. data.loc[:,'y'] = np.where(data['y'] == 'no',0,1)  替换一列二值数据
### array属性
    1. a.ndim 查看数组维度

    2. a.shape 查看数组形状

    3. a.dtype 查看数组数据类型
       1.  int16 32 64
       2.  uint8 16 32 64
       3.  float64 16 32 64
       4.  complex 复数 64 128
       5.  True False
### 广播
    array + - * 10  : array所有元素+ - *10

    array + array : 对应位置元素相加
### 通用函数
    1. np.sqrt(array)  array中每个元素的平方根

    2. np.sin()

    3. np.cos()

    4. array ** 2  求2次幂  array ** 3 立方

    5. np.maximum(a, b)  a 与 b 逐位比较取其大者
    
    6. np.pi
### 统计方法
    1. np.mean() 求均值  可以沿轴运算

    2. np.sum()  求和

    3. np.max()  求最大值

    4. np.min()  求最小值

    5. np.std()  求方差

    6. np.median()  求中位值


## Matplotlib
    一般引入方式 : import matplotlib.pyplot as plt
    plt.figure(figsize=(10,10))#初始化一张画布  画布大小10*10  控制绘制图形形状
### 绘制线性图
    1. plt.plot(x,y, c='b',ls = '--' , lw = 5 , marker = '^')  绘制线段  
       1. 默认x轴为0,1,2,3
       2. c(color) = blue 颜色为蓝色
       3. ls(line style)   线段样式  '--'线段为虚线  '-.'线点
       4. lw(line width)   线段宽度  
       5. marker     标记数据点 '^'上三角  'o'圆形

    2. 在一个图形中画两条线  同时运行
        plt.plot(x,y)
        plt.plot(x,y1)

    3.  标签
        plt.plot(x,y,label = 'A')
        plt.plot(x,y1,label = 'B')
        plt.legend(loc) # 显示标签 loc : 标签显示位置

    4. plt.xlabel('age')  添加横坐标标题

    5. plt.ylabel('height')  添加纵坐标标题

    6. plt.title('student',y=1.01)  添加总标题
   
    7. 设置坐标范围
       1. plt.xlim(0,10)
       2. plt.ylim(-5,0)
   
    8. 给坐标设置标签
        1. plt.xticks([0,4,8],['born','baby','child'])#位置 内容
        2. plt.yticks([0,4,8],['born','baby','child'])
    
    9. plt.grid() 设置网格
       1.  plt.grid(True) # 显示背景的网格线
       2.  plt.grid(False) # 关闭背景的网格线
    
    10. plt.style.use('ggplot') 设置样式  
    
    11. plt.figure(figsize=(a,b))  绘图  a : 长   b : 宽 
    12. plt.subplot(a,b,c)
        1.  使用plt.subplot来创建小图
        2.  a 行 b 列 第c位置
### 条形图
    1. plt.bar(x,y)  纵向条形图
    2. plt.barh(x,y)  横向条形图
### 直方图
    1. plt.hist(data,bins = 20)  bins 分成几份
### 散点图
    1. plt.scatter(x,y) 
### 绘制图像
    1. plt.imshow(X, interpolation=None)
       1. X：图像数据
          1. (M, N)：标量数据的图像，灰度图
          2. (M, N, 3)：RGB图像
          3. (M, N, 4)：RGBA图像
       2. interpolation : 不同图像之间的插值方式
            methods = [None, 'none', 'nearest', 'bilinear', 'bicubic', 'spline16',
                       'spline36', 'hanning', 'hamming', 'hermite', 'kaiser', 'quadric',
                       'catrom', 'gaussian', 'bessel', 'mitchell', 'sinc', 'lanczos']




## Pandas
    一般导入方式 : import pandas as pd
### Pandas 数据结构
    1. Series   有一个index
    2. DataFrame   表格
### Series
    1. 构造Series
       1. s = pd.Series([1,2,3,4], index=['a','b','c','d'])
       2. s = pd.Series({'a' : 1 , 'b' : 2 , 'c' : 9})  利用字典

    2. s.index  查看索引

    3. s.values   查看数组

    4. 'b'  in s.index  判断值是否在index当中

    5. s.isnull()  找出null值  

    6. series 运算时 自动按照索引对齐  Null + 一个值 = Null

    7. 对index 切片 会包含前后两端

### DataFrame
    1.  xuhao = ['one','two','three','four']
        df = pd.DataFrame(np.random.randn(4,3) ,index = xuhao ,columns=['a','b','c'])

    2. df.columns 列索引

    3. df.index  行索引

    4. df[['a','c']] 取回多列  返回为DataFrame

    5. df.a  取回一列  返回为Series

    6. df.head(2)  取回前2行 # 默认显示前3行

    7. df.tail(2)  取回后2行# 默认显示后5行

    8. df.values  查看DataFrame的数据

    9.  df.loc['one']  默认为取回行 loc 按索引取值

    10. df.loc['one':'three',['a','b']]  取回3行2列  用切片时不需[]

    11. df.loc['two','b'] = 100  对(two , b)位置 赋值

    12. df.iloc[1,0] #按位取值  取回第2(1)行第1(0)列  此时切片不包含最后一位
### 添加删除行和列
    1.  df['e'] = pd.Series([1,2,3,4],index = ['one','three','four','two'])  增加一列  用Serios 会自动索引匹配

    2.  del df['d']  删除d列

    3.  df.loc['five']= 10 添加行

    4.  df.drop('five')  删除行  返回一个新的DataFrame

    5.  df.drop(columns=['c','e'])  删除多列
### 排序与数据对齐
    1. data.T 转置
    2. data.sort_index(axis=1,ascending=True,inplace =True)  根据索引排序
       1. axis : 1 列排序 0 行排序
       2. ascending : True 降序 False 升序
       3. inplace 是否返回一个值
    3. data.sort_values(by = 'c' ,ascending=False) 按照某一列的值排序
    4. data.reindex(columns=['b','c','a','d'],index = [])  重新排序索引
### 布尔索引
    data[data['y'] == 'yes'] = 1  对一列判断并赋值
### 其他
    1. data.a.value_counts() 对a列数据进行计数
    2. data.a.isin('10','20')  找出a列数据中10 和 20的项