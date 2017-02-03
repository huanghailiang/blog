---
title: Chapter 2
tags:
  - 机器学习系统设计
date: 2017-01-18 14:44:05
---

使用`anaconda`的话所有需要的库都已经装好了，或者自行安装以下库
- numpy
- matplotlib
- Scikit-learn

```python
from matplotlib import pyplot as plt
from sklearn.datasets import load_iris
import numpy as np

data=load_iris()
features=data['data']
feature_names = data['feature_names']
target = data['target']

for t,marker,c in zip(range(3),">ox","rgb"):
    # 画出每个类别，它们各自采用不同的颜色标识
    plt.scatter(features[target == t,0],
                features[target == t,1],
                marker=marker,
                c=c)
```
<!-- more -->
{% img /images/figure_1.png %}

Iris dateset包含有不同种类的Iris花朵的数据
`data`包含的属性有
- `data`
- `feature_names`    
[<abbr title="花萼长度">'sepal length (cm)'</abbr>, <abbr title="花萼宽度">'sepal width (cm)'</abbr>, <abbr title="花瓣长度">'petal length (cm)'</abbr>, <abbr title="花瓣宽度">'petal width (cm)'</abbr>]
- `target`    
`0`,`1`,`2`各50个，用于表示3种花不同的类别
- `target_names`    
[<abbr title="山鸢尾花">'setosa'</abbr>, <abbr title="变色鸢尾花">'versicolor'</abbr>, <abbr title="维吉尼亚鸢尾花">'virginica'</abbr>]
分别使用`>r`,`og`,`xb`在图中标出

{% img /images/figure_2.png %}
{% img /images/figure_3.png %}
类似的，画出另外几组图片。显然，根据花瓣长度很容易将山鸢尾花（Iris Setosa）与其他两种花区分出来。

```python
plength=features[:,2]                    #所有花瓣长度
is_setosa=(target==0)                    #所有的山鸢尾花
max_setosa=plength[is_setosa].max()      #1.9
min_non_setosa=plength[~is_setosa].min() #3.0
```
因此，我们可以构造一个简单的模型：如果花瓣长度小于2，那么它是山鸢尾花（Iris Setosa）；否则，它不是维吉尼亚鸢尾花（Iris Virginica）就是变色鸢尾花（Iris Versicolor）。
