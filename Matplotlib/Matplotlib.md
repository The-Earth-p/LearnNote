# Matplotlib

## 画柱状图

1.导入相关库

2.需要一定数量的数据，数据的具体的数量要和最终x轴的点位个数保持一直，数据的值要和y轴的数值相互适应匹配

3.根据我们已知的x轴的点位个数，用numpy生成一个位置数组，后面用于和x轴的各个点位一一对应，才能放入图像中

4.可以额外设置一下柱子的宽度，方便后面的布局

5.创建图形，通过subplot这个函数来指定大图中总共几张小图，怎么布局等等。

6.布局构建完之后，有各个子图的具体返回对象，可以针对具体对象进行操作。

7.操作完成之后就通过代码进行展示即可

柱状图1:一子图一点多柱子

```python
import matplotlib.pyplot as plt
import numpy as np

# 数据准备
categories = ['W/O', 'Res1', 'Res2', 'Res3', 'BN Module']
metrics = ['Precision@5', 'Recall@5', 'F1-score@5']

# 假设的数据 - 请替换为您的实际数据
precision = [0.18, 0.19, 0.20, 0.21, 0.22]  # Precision@5 数据
recall = [0.178, 0.176, 0.174, 0.172, 0.170]  # Recall@5 数据（使用您提供的部分）
f1 = [0.175, 0.177, 0.179, 0.181, 0.183]  # F1-score@5 数据

# 设置柱状图位置
x = np.arange(len(categories))  # 类别位置
width = 0.25  # 柱状图宽度

# 创建图形
fig, ax = plt.subplots(figsize=(10, 6))

# 绘制柱状图
rects1 = ax.bar(x - width, precision, width, label='Precision@5')
rects2 = ax.bar(x, recall, width, label='Recall@5')
rects3 = ax.bar(x + width, f1, width, label='F1-score@5')

# 添加标签、标题等
ax.set_ylabel('Scores')
ax.set_title('Model Performance Comparison')
ax.set_xticks(x)
ax.set_xticklabels(categories)
ax.legend()

# 在每个柱子上方添加数值标签
def autolabel(rects):
    for rect in rects:
        height = rect.get_height()
        ax.annotate(f'{height:.3f}',
                    xy=(rect.get_x() + rect.get_width() / 2, height),
                    xytext=(0, 3),  # 3 points vertical offset
                    textcoords="offset points",
                    ha='center', va='bottom')

autolabel(rects1)
autolabel(rects2)
autolabel(rects3)

# 调整布局
fig.tight_layout()

plt.show()
```

柱状图2:多子图一点单柱子

```python
import matplotlib.pyplot as plt
import numpy as np

# 数据准备
categories = ['W/O', 'Res1', 'Res2', 'Res3', 'BN']
metrics = ['Precision@5', 'Recall@5', 'F1-score@5']

# 假设的数据 - 请替换为您的实际数据
precision = [0.18, 0.19, 0.20, 0.21, 0.22]  # Precision@5 数据
recall = [0.178, 0.176, 0.174, 0.172, 0.170]  # Recall@5 数据（使用您提供的部分）
f1 = [0.175, 0.177, 0.179, 0.181, 0.183]  # F1-score@5 数据
adjusted_precision = [p - 0.17 for p in precision]#根据bottom来调整柱子的高度
# 设置柱状图位置
x = np.arange(len(categories))  # 类别位置
width = 0.25  # 柱状图宽度

# 创建图形，1行3列的子图布局
fig, (ax1, ax2, ax3) = plt.subplots(1, 3, figsize=(18, 6))

# 绘制第一个子图 - Precision@5
ax1.bar(x , adjusted_precision, width, label='Precision@5')#这里还可以加bottom，用来指示y轴最下面的坐标，但是需要注意，具体数值也要相应调整，还可以添加color参数，可以是单一颜色，可以是多个颜色组成的数组，数量要和柱子的数量一样
ax1.set_ylabel('Scores')
ax1.set_title('Precision@5 Comparison')
ax1.set_xticks(x)
ax1.set_xticklabels(categories)#如果没有类别标签当x轴的小点的话，就直接把这行注释掉，然后，改x就行
# autolabel(rects1)

# 绘制第二个子图 - Recall@5
ax2.bar(x - width/2, recall, width, label='Recall@5')
ax2.set_title('Recall@5 Comparison')
ax2.set_xticks(x)
ax2.set_xticklabels(categories)
# autolabel(rects2)

# 绘制第三个子图 - F1-score@5
ax3.bar(x - width/2, f1, width, label='F1-score@5')
ax3.set_title('F1-score@5 Comparison')
ax3.set_xticks(x)
ax3.set_xticklabels(categories)
# autolabel(rects3)

# 调整布局
fig.tight_layout()

plt.show()
```

## 画折线图

把bar函数改成plot就行，还有一些参数的减少和增加，其他具体流程和上述一样