# Python4DataAnalysis 2ed
# online book refer to: https://www.bookstack.cn/read/pyda-2e-zh/11.5.md

## 4 Numpy 基础
- numpy: ndarray 多维数组对象
- 数学运算
  - data = np.random.randn(2,3)
  - data + data, data * 10
- Creat ndarraay
  - `data1 = np.array([1, 2, 3,], [4, 5, 6])`
- Method
  - shape 代表维度
  - dtype 代表类型
  - array
  - arange
  - random.randn()
  - ones
  - zeros
  - empty

## 11 时间序列

- time Series
  - timestamp  特定的时刻
  - periods    如2007年1月或2010年全年
  - interval   由起始和结束时间戳表示
- pandas提供了许多内置的时间序列处理工具和数据算法。
- 可以高效处理非常大的时间序列，轻松地进行切片/切块、聚合、对定期/不定期的时间序列进行重采样等。
- 有些工具特别适合金融和经济应用，你当然也可以用它们来分析服务器日志数据。

### 11.1 
- from datetime import datetime
- date     以公历形式储存日历日期（年月日）
- time     时间储存为时分秒毫秒
- datetime 以毫秒形式存储日期和时间
- delta    两个datetime 值之间的差（日，秒， 毫秒）
- str <==> datetime
  - str
  - datetime.strftime("%Y-%m-%d")
  - datetime.strptime(strvalue, "%Y-%m-%d")

### 11.2
- 切片
  - 可以传入字符串日期、datetime或Timestamp 对pd.Series/DateFrame时间序列进行切片
  - 切片所产生的是原时间序列的视图，跟NumPy数组的切片运算是一样的；
  - 这意味着，没有数据被复制，对切片进行修改会反映到原始数据上
  
- 带有重复索引的时间序列
  - 对这个时间序列进行索引，要么产生标量值，要么产生切片
  - 对具有非唯一时间戳的数据进行聚合
    - 用groupby，并传入level=0 
    - pd.Series.groupby(level=0).mean()

### 11.3
- 生成日期范围
  - pandas.date_range(start=None, end=None, periods=None, freq=None, tz=None, normalize=False, name=None, closed=None, **kwargs)
  - 默认情况下，date_range会产生按天计算的时间点
  - 默认会保留起始和结束时间戳的时间信息
  - 产生一组被规范化（normalize=True）到午夜的时间戳
  - pandas.date_range('2012-04-01', '2012-06-01')
- 频率和日期偏移量
  - pd.date_range('2000-01-01', periods=10, freq='1h30min')
  - 传入频率字符串（如”1h30min”），这种字符串可以被高效地解析为等效的表达式
  - 11-4 时间序列的基础频率
- 用的频率类 WOM（Week Of Month）
  - pd.date_range('2012-01-01', '2012-09-01', freq='WOM-3FRI')
  - 每月第三个星期五
- 移动（超前和滞后）数据
  - 移动（shifting）指的是沿着时间轴将数据前移或后移
  ``` Python
  ts = pandas.Series()
  # 对数据位移
  ts.shift(2)
  ts.shift(-2)
  # 对时间戳位移
  ts.shift(2, freq='M')
  ts.shift(1, freq='90T')
  ```
  - shift通常用于计算一个时间序列或多个时间序列（如DataFrame的列）中的百分比变化
    - `ts / ts.shift(1) - 1` ?
- 通过偏移量对日期进行位移
  - pandas的日期偏移量还可以用在datetime或Timestamp对象上
  ``` Python
  from pandas.tseries.offsets import MonthEnd, Day
  offset = MonthEnd()
  ts = pd.Series(np.random.randn(20), 
                 index = pd.date_range('1/15/2000', periods=20, freq='4d')
  ts.groupby(offset.rollforward).mean()
  ts.resample('M').mean() # 效果同上
  ```
### 11.4 Time Zone
- pd.Series.tz_localize('UTC')
- pd.Series.tz_converse('Eroupe/Berlin')
- pandas 时间序列默认为单纯时间(native)，时区为 None,
- 将时间本地化(localize)后，就可以转变(converse)其他时区
- Timestamp
  - `stamp_utc = pd.Timestamp('2011-03-12 04:00', tz='UTC').tz_convert('Asia/Shanghai')`
  - `stamp_NY = pd.Timestamp('2011-03-12 04:00').tz_localize('UTC').tz_convert('America/New_York')`
  - Timestamp对象在内部保存了一个UTC时间戳值（自UNIX纪元（1970年1月1日）算起的纳秒数）,
  - 这个UTC值在时区转换过程中是不会发生变化的
    - `stamp_utc.value == stamp_NY # True`
  - 如果两个时间序列的时区不同，在将它们合并到一起时，最终结果就会是UTC,
  - 由于时间戳其实是以UTC存储的，所以这是一个很简单的运算，并不需要发生任何转换


### 11.5 时期及其算术运算
- periods
  - p = pd.period(2017, 'A-DEC') # Period('2007', 'A-DEC')
  - 从2007年1月1日到2007年12月31日之间的整段时间
  -  p + 5 # Period('2022', 'A-DEC')
  -  period_range
    - pd.period_range('2006-12-03', '2021-12-09', freq='M') # 产生 periodindex 类
    - PeriodIndex类保存了一组Period，它可以在任何pandas数据结构中被用作轴索引
- 频率转换
  - `pd.periods(2017, freq='A-DEC').asfreq('M', how='start') # Period('2007-01', 'M')'
- 按季度计算的时期频率 Q-JAN到Q-DEC


## 12 Advanced pandas
### 1 Categorical Type in pandas
- 
