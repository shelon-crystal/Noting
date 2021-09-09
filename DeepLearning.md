



### Python基础库



#### Numpy 使用



##### 1.1 生成Numpy数组

~~~python
# numpy数组
x = np.array([1.0, 2.0, 3.0])
print(x)
# <class 'numpy.ndarray'>
print(type(x))
~~~



###### 1.1.1 算数运算

1. 四则运算，数组对应项相运算，例：数组与数组，数组与常数
2. 

~~~python
y = np.array([3.0, 4.3, 5.0])
print(x * y)
print(y / 2.0)
~~~



##### 1.2 Numpy多维数组

1. 只接收规则矩阵定义，定义不规则会报错
2. 

~~~python
# 多维数组
A = np.array([[1, 2], [3, 4]])
print(A)
B = np.array([[4, 2], [3, 10]])
print(A * B)
~~~



###### 1.2.1 Numpy多维数组计算

**要点：都是对应项相乘**

一维数组：向量

三维以及三维以上数组：张量

1. 多维乘标量

   ~~~python
   print(A * 2)
   ~~~

2. 多维矩阵相乘

   ~~~python
   print(A * B)
   ~~~

   

###### 1.2.2 矩阵广播



实质：将乘号右边的乘数扩展成与被乘数形状相同的矩阵

![1603271488041](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603271488041.png)



复杂案例：C是一个三维数组

~~~python
C = np.array([[[1, 2], [3, 4], [1, 2], [3, 4]],
              [[1, 2], [3, 4], [1, 2], [3, 4]],
              [[1, 2], [3, 4], [1, 2], [3, 4]]])
print(C)
print(C.shape)

D = np.array([3, 4])
print(C * D)
~~~

结果：

![1603272452647](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603272452647.png)



##### 1.3 元素访问



基本访问：X[0]，X[0] [1]



###### 1.3.1 for遍历

~~~python
for row in np.array([[51, 55], [14, 19], [0, 4]]):
    print(row)
~~~



###### 1.3.2 标记法获取



1. 通过不等号运算符得到布尔数组 X > 15
2. 取出布尔数组中对应True的元素 X[X > 15]
3. 化一维 flatten()

**在基本标记法的`[]`运算中加入表达式/指定标记，根据表达式结果获取指定范围数据**

~~~python
X = np.array([[51, 55], [14, 19], [0, 4]])
# 转化为一维
X = X.flatten()
print(X)

# 获取索引为0 2 4元素
print(X[np.array([0, 2, 4])])
# 获取 X 数组中大于 15 的元素布尔数组
print(X > 15)
# 获取大于 15 的元素布尔数组中为 True 对应的元素
print(X[X > 15])

~~~





#### Matplotlib库使用



##### 2.1 绘制图形



###### 2.1.1 pyplot



1. 画图的线`plot()`

   参数：

   - 第一个：画图要取的点（横坐标）
   - 第二个：画图参考的函数，由横坐标和参考函数产生的纵坐标例如Sin()
   - lable：左下角用图例显示标识该图像
   - linestyle：画图用的参考线，默认实线
   - linewidth：画图的线的宽度

2. 各轴标签：

   - xlable()、ylable()：分别表示横轴和纵轴的名称
   - title()：表示这整个图像的标签



~~~python
# sin()函数
# 生成数据,以0.1为单位,生成0-6的数据(0.1 0.2 0.3 ... 6.0)
x = np.arange(0, 6, 0.1)
y1 = np.sin(x)
y2 = np.cos(x)


# 绘图
plt.plot(x, y1, label="sin", linewidth=4.0)
plt.plot(x, y2, linestyle="--", label="sin")
plt.xlabel("x")     # x轴标签
plt.ylabel("y")     # y轴标签
plt.title('sin & cos')  # 标题
plt.legend()
plt.show()
~~~

**注：**

1. **在plt.show()之前一定要plt.legend()**
2. **plt 代表 matplotlib.pylot 的对象**



图像：

![1603295160343](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603295160343.png)





###### 2.1.2 imread.imshow()



~~~python
# 显示图像
img = imread('D:/tools/CodingTools/Python/PycharmProjects/deepLearning/img/kawaii.jpg')
plt.imshow(img)
plt.show()
~~~



图像：

![1603295518573](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603295518573.png)



---



### 感知机



##### 3.1 概念

接收信号到达设定阈值产生输出的

![1603466596056](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603466596056.png)



##### 3.2 感知机的实现

###### 3.2.1 与门

~~~python
与门
def AND (x1, x2):
    w1, w2, theta = 0.5, 0.5, 0.7
    tmp = x1 * w1 + x2 * w2
    if tmp < theta:
        return 0
    elif tmp > theta:
        return 1
~~~



###### 3.2.2 导入权重和偏置

行为描述：

![1603466741443](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603466741443.png)

实际上就是把θ移动到了等式左边



###### 3.2.3 偏置与门、或门、与非门



**类别：单层感知机**



~~~python
# 引入偏置
def AND(x1, x2):
    x = np.array([x1, x2])  # 输入
    w = np.array([0.5, 0.5])  # 权重
    b = -0.7
    tmp = np.sum(w * x) + b
    if tmp <= 0:
        return 0
    else:
        return 1



# 与非门：实质上就是负权的与门
def NAND (x1, x2):
    x = np.array([x1, x2])  # 输入
    w = np.array([-0.5, -0.5])  # 权重
    b = 0.7
    tmp = np.sum(w * x) + b
    if tmp <= 0:
        return 0
    else:
        return 1

def OR(x1, x2):
    x = np.array([x1, x2])  # 输入
    w = np.array([0.5, 0.5])  # 权重
    b = -0.2
    tmp = np.sum(w * x) + b
    if tmp <= 0:
        return 0
    else:
        return 1
~~~



###### 3.2.4 异或门



异或门用直线无法划分成线性的真值

![1603466979465](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603466979465.png)

![1603466996395](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603466996395.png)



使用多层感知机可以实现异或（10/01=1，00=0，11=0）

![1603467052082](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603467052082.png)

真值表

![1603467067893](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603467067893.png)



实现：

~~~python
# 组建异或门

def XOR(x1, x2):
    s1 = NAND(x1, x2)
    s2 = OR(x1, x2)
    y = AND(s1, s2)
    return y
~~~



##### 3.3 激活函数



###### 3.3.1 阶跃函数

以阈值为界，一旦超过阈值，就切换输出这样的函数为阶跃函数



###### 3.3.2 sigmoid函数



转换输出，实际上就是对上一层神经元的输出进行`1/(e^(-x))`的转换，指数转换

函数原型：

![1603615166912](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603615166912.png)



实现：

~~~python

# 参数x为NumPy数组时，结果也能被正确计算
# exp(-x) == e^(-x)，e的指数函数
def sigmoid(x):
    return 1 / (1 + np.exp(-x))


# x = np.array([-1.0, 1.0, 2.0])
# print(sigmoid(x))


x = np.arange(-5.0, 5.0, 0.1)
y = sigmoid(x)
plt.plot(x, y)
plt.ylim(-0.1, 1.1)
plt.show()
~~~

![1603444362893](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603444362893.png)





###### 3.3.3 阶跃函数的实现



~~~python
# 阶跃函数，超过设定的阈值，就切换输出的函数
def step_function(x):
    # np.array(x>0):可以得到一个布尔数组，对应x中元素x>0==true x<0==false
    # 第二个参数则是将此数组转换成int类型，即false=0，true=1
    return np.array(x > 0, dtype=np.int)


# 获得从-0.5 到 5.0的点作为坐标，每0.1取一个点
x = np.arange(-5.0, 5.0, 0.1)
y = step_function(x)

plt.plot(x, y)
# 设置图像的上限和下限，即绘图显示的值域范围
plt.ylim(-0.1, 1.1)
plt.show()
~~~

![1603615306842](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603615306842.png)





###### 3.3.4 sigmoid与阶跃对比

![1603444331668](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603444331668.png)

阶跃函数与sigmoid函数相比：

1. sigmoid与阶跃平滑
2. sigmoid的可以返回0-1之间的实数，阶跃只能0或者1



###### 3.3.5 ReLU函数



ReLU函数在输入大于0时，直接输出该值；在输入小于等于0时，输 

出0



原型：

![1603615553077](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603615553077.png)

函数实现：

~~~python
import numpy as np
import matplotlib.pyplot as plt

def relu(x):
    return np.maximum(0, x)


x = np.arange(-5.0, 5.0, 0.1)
y = relu(x)
plt.plot(x, y)
plt.ylim(-0.1, 5)
plt.show()
~~~

![1603615590775](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603615590775.png)







##### 3.4 多维数组的运算：



###### 3.4.1 常用方法



1. np.ndim(x)：求维度

2. np.shape(x)：求矩阵形状，返回元组，如(4,3,2)

3. np.dot(x,y)：矩阵相乘，**要求x行数=y列数，或者x列数=y行数**



案例：

~~~python
import numpy as np

C = np.array([[[1, 2], [3, 4], [1, 2], [3, 4]],
              [[1, 2], [3, 4], [1, 2], [3, 4]],
              [[1, 2], [3, 4], [1, 2], [3, 4]]])

# 维度
print(np.ndim(C))
# 形状
print(C.shape)

# 乘法(4,2) * (2,3)
A = [[1, 2], [3, 4], [5, 8], [9, 6]]
B = [[5, 6, 9], [7, 8, 4]]
print(np.dot(A, B))

~~~

![1603615792235](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603615792235.png)



##### 3.5 神经元传递化为矩阵运算



###### 3.5.1 传递计算模式

公式：

$$
A = XW + B
$$



公式说明：

A = XW + B
A向量为后一层神经元权重
W表示前一层神经元到后一层神经元的传递方向，表示前一层到后一层的权重
B则为偏置值



###### 3.5.2 传递的计算描述

 

1. 图例

   ![1603615948244](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603615948244.png)

2. 神经元之间的传递

   ![1603615979565](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603615979565.png)

3. 传递转换全过程

   ![1603615928378](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603615928378.png)



###### 3.5.3 传递实现



~~~python
def sigmoid(x):
    return 1 / (1 + np.exp(-x))

# A = XW + B
# A向量为后一层神经元权重
# W表示前一层神经元到后一层神经元的传递方向，表示前一层到后一层的权重
# B则为偏置值


X = np.array([1.0, 0.5])
W1 = np.array([[0.1, 0.3, 0.5], [0.2, 0.4, 0.6]])
B1 = np.array([0.1, 0.2, 0.3])

A1 = np.dot(X, W1) + B1     # [0.3 0.7 1.1]
print(A1)

# 被激活后的信号，通过激活函数sigmoid将信号转化为下一层的输出
Z = sigmoid(A1)     # [0.57444252 0.66818777 0.75026011]
print(Z)
~~~



###### 3.5.4 案例实现



**一般输出层也要经历一层函数，称为输出层函数，案例中的输出层函数默认返回输入本身**

**输出层所用的激活函数，要根据求解问题的性质决定。一般地，回** 

**归问题可以使用恒等函数，二元分类问题可以使用 sigmoid函数，** 

**多元分类问题可以使用 softmax函数。**



~~~python
import numpy as np

def identity_function(a3):
    return a3

def sigmoid(x):
    return 1 / (1 + np.exp(-x))

# 生成节点和节点对应的偏置值(权重和偏置的初始化)
def init_network():
    network = {}
    network['W1'] = np.array([[0.1, 0.3, 0.5], [0.2, 0.4, 0.6]])
    network['b1'] = np.array([0.1, 0.2, 0.3])
    network['W2'] = np.array([[0.1, 0.4], [0.2, 0.5], [0.3, 0.6]])
    network['b2'] = np.array([0.1, 0.2])
    network['W3'] = np.array([[0.1, 0.3], [0.2, 0.4]])
    network['b3'] = np.array([0.1, 0.2])
    return network


# 将输入信号转换成输出信号(forward 前向处理：输入->输出，backward)
def forward(network, x):
    W1, W2, W3 = network['W1'], network['W2'], network['W3']
    b1, b2, b3 = network['b1'], network['b2'], network['b3']

    a1 = np.dot(x, W1) + b1
    # 通过激活函数sigmoid转换为下一层节点传递的输入，以下同理
    Z1 = sigmoid(a1)
    a2 = np.dot(Z1, W2) + b2
    Z2 = sigmoid(a2)
    a3 = np.dot(Z2, W3) + b3

    # 进入输出层激活函数
    y = identity_function(a3)
    return y

~~~



##### 3.7 Mnist数据集识别



###### 3.7.1 使用描述

MNIST数据集的一般使用方法是，先用训练图像进行学习，再用学习到的模型度量能在多大程度上对测试图像进行正确的分类（即计算学习模型与计算测试数据的正确率）



MNIST数据集是由0到9的数字图像构成的。训练图像有6万张，测试图像有1万张，这些图像可以用于学习和推理

![1603726272074](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603726272074.png)



###### 3.7.2 案例



说明：

1. load_mnist函数以**“(训练图像 ,训练标签 )，(测试图像，测试标签 )”**的 形式返回读入的MNIST数据

2. `img.reshape()`将Numpy数组转换成指定的形状，然后利用fromarray()转换成PIL数据对象，如(在案例中为形成(28，28)像素的形状)

   ![1603722737122](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603722737122.png)

3. Python有 pickle这个便利的功能。这个功能可以将程序运行中的对象保存为文件。如果加载保存过的 pickle文件，可以立刻复原之前程序运行中的对象。

~~~python
import sys, os
sys.path.append(os.pardir)
from dataset.mnist import load_mnist
from PIL import Image
import numpy as np

(x_train, t_train), (x_test, t_test) = load_mnist(flatten=True, normalize=False)

# print(x_train.shape)    # (60000, 784)
# print(t_train.shape)    # (60000,)
# print(x_test.shape)     # (10000, 784)
# print(t_test.shape)     # (10000,)

def img_show(img):
    pli_img = Image.fromarray(np.uint8(img))
    pli_img.show()


img = x_train[0]
label = t_train[0]

img = img.reshape(28, 28)
img_show(img)
~~~



###### 3.8 神经网络推理处理



###### 3.8.1 几个概念

1. 正规化：load_mnist函数的参数normalize设置成了True。将normalize设置成True后，函数内部会进行转换，将图像的各个像素值除以255，使得数据的值在0*.*0～1*.*0的范围内。**像这样把数据限定到某个范围内的处理称为正规化（normalization）**

   

2. 预处理：对神经网络的输入数据进行某种既定的转换为预处理。

   很多预处理都会考虑到数据的整体分布。比如，利用数据整体的均值或标准差，移动数据，使数据整体以 0为中心分布，或者进行正规化，把数据的延展控制在一定范围内。

3. 







###### 3.8.2 案例实现

~~~python
import numpy as np
import sys, os
sys.path.append(os.pardir)
from dataset.mnist import load_mnist
import pickle



# 激活函数
def sigmoid(x):
    return 1 / (1 + np.exp(-x))

# 输出层函数
def softmax(a):
    c = np.max(a)
    # 减去a(输入信号)中最大的值再进行指数运算，以解决溢出问题
    exp_a = np.exp(a - c)
    # 计算减去最大值之后的exp_a的和
    sum_exp_a = np.sum(exp_a)

    y = exp_a / sum_exp_a

    return y


def get_data():
    (x_train, t_train), (x_test, t_test) = load_mnist(flatten=True, one_hot_label=False)
    return x_test, t_test

def init_network():
    with open("sample_weight.pkl", 'rb') as f:
        network = pickle.load(f)
    return network

# 用于以Numpy的形式输出各个标签对应的概率[0.1,0.3...0.01]
def predict(network, x):
    W1, W2, W3 = network['W1'], network['W2'], network['W3']
    b1, b2, b3 = network['b1'], network['b2'], network['b3']

    a1 = np.dot(x, W1) + b1
    # 通过激活函数sigmoid转换为下一层节点传递的输入，以下同理
    Z1 = sigmoid(a1)
    a2 = np.dot(Z1, W2) + b2
    Z2 = sigmoid(a2)
    a3 = np.dot(Z2, W3) + b3

    # 进入输出层激活函数
    y = softmax(a3)
    return y



x, t = get_data()
network = init_network()

accuracy_cnt = 0
for i in range(len(x)):
    y = predict(network, x[i])
    p = np.argmax(y)
    # 比较神经网络所预测的答案和正确解标签，将回答正确的概率作为识别精度。
    if p == t[i]:
        accuracy_cnt += 1


print("Accuracy:" + str(float(accuracy_cnt) / len(x)))


~~~



---



#### 神经网络



##### 4.1 神经网络的特征



###### 4.1.1 数据决定权重