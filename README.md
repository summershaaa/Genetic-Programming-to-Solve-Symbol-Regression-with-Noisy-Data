# Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data
## 使用遗传规划解决带噪声数据的符号回归问题

### 0.实验环境：
     Python3 , gplearn , numpy , sympy , matplotlib , seaborn

### 1 引言：
> **符号回归**(Symbolic Regression)是一种流行的**遗传规划**(Genetic Programming)应用。目的是根据给定的一组相关数据,找出拟合数据的函数表达式。与传统的线性回归和多项式回归相比，符号回归无需事先指定目标函数的形式和参数,函数的形式和参数均在回归的过程中确定。因此符号回归具有更广泛的应用范围。
> 对于样本量比较大且完整的数据，使用遗传规划算法来解决符号回归问题，总是能够很快且精确地拟合出潜在的函数表达式。但如果数据量比较少，而且存在噪声的情况下，拟合结果往往会偏向噪声数据。针对这一问题，结合RANSCA方法处理噪声的思想，本文提出了一种基于重采样的遗传规划算法**RSGP**(ReSample Genetic Programming)，对不同噪声率和噪声水平的数据进行符号回归拟合，查看改进算法的抗噪能力。

### 2 遗传规划原理
>  遗传规划基本原理是随机产生一个适合于给定环境的初始群体。每个群体的个体都有一个适应度值，用遗传算法处理得到高适应度的个体，产生下一代的群体。通过个体复制、交叉、变异等过程不断进化，直到出现给定问题的解或近似解为止。 遗传规划是一种相当有效的符号回归方法，它的优势源于其结构的灵活性，由于采用树形结构，可以描述层次化的问题，克服了传统方法中确定函数结构难的缺陷。<br>
下图时树结构的大致表达形式，前序遍历树可得到前缀表达式，可还原出表达式。曲线是表达式的表现型。
![树结构](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E6%A0%91%E7%BB%93%E6%9E%84.png)

##### 2.1 遗传规划的基本要素

> (1) **终止符集**(terminals): 终止符集是问题中最基本的元素,包括变量和常量. 
>
> (2) **函数符集**(functions): 函数符集可以是算术运算、函数运算、逻辑运算或程序运算等,  如+, - , × , ÷,sin,cos,log,exp 等.
>
> (3) **适应度值**(fitness): 适应度值函数给出了所研究环境下群体中每个个体好坏程度的评价方法。如$R^2$或$MAE$

##### 2.2 遗传规划的主要算子

 遗传规划的基本遗传算子主要包括**复制**、**交叉**和**变异**等

1)**复制**：基于适应度的个体选择方法, 从群体中选出一个父代个体,不对其做任何的变化便复制到新的一代中

2)**交叉**：随机从父代中选择出一些个体, 选择其中最好的个体进行交叉操作
 ![交叉](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E4%BA%A4%E5%8F%89.png)
3)**变异**： 从当前群体中选择一个单体,并随机选定一个(或多个)结点作为变异点,对变异点及其以下部分随机替换而生成新的单体

1. **子树变异**：
![子树变异](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E5%AD%90%E6%A0%91%E5%8F%98%E5%BC%82.png)
2.**hoist变异**：
![hoist变异](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/hoist%E5%8F%98%E5%BC%82.png)
3.**点变异**：
![点变异](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E7%82%B9%E5%8F%98%E5%BC%82.png)

### 3 添加噪声

##### 3.1 击穿点(Breakdown Point)

击穿点是一种鲁棒性度量，衡量回归器对噪声数据的最大容忍度。击穿点越大，估计器就越稳健。在统计上来说，如果总体样本中超过一半的数据都是较大的噪声，则无法将样本中的潜在分布和噪声数据的分布区分开。

##### 3.2 噪声率(NoiseRatio)和噪声水平(NoiseLevel)

在实验中，我们考虑对部分($\epsilon$)数据添加高斯噪声g(0, $\sigma$ ),其中g是均值为0，标准差为 $\sigma$ 的高斯随机数。
噪声率$\epsilon$ 考虑范围为[0.1,0.2,0.3,0.4,0.5]；噪声水平$\sigma$则取区间目标值范围约25%和50%的标准差水平。
即在不同噪声率$\epsilon$和噪声水平g(0,𝜎)取值下对每组数据做10次测试。

如下图：
- 左子图是原始无噪声的数据
- 中子图是在$\epsilon$=0.2，$\sigma$=25%取值下添加噪声后的数据
- 右子图是在$\epsilon$=0.5，$\sigma$=50%取值下添加噪声后的数据
![](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E5%99%AA%E5%A3%B0%E6%95%B0%E6%8D%AE%E6%BC%94%E7%A4%BA.png)


#### 3.实验结果对比：
![第一个函数的两种方法拟合效果对比](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E5%AF%B9%E6%AF%94%E5%9B%BE1.png)
![第二个函数的两种方法拟合效果对比](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E5%AF%B9%E6%AF%94%E5%9B%BE2.png)

#### 4.可视化不同噪声率和不同噪声水平下的拟合优度和均方误差
![第一个函数的两种方法拟合对比](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/x%5E4%2Bx%5E3%2Bx%5E2%2Bx__%E5%8F%AF%E8%A7%86%E5%8C%96%E7%9B%B8%E5%85%B3%E6%80%A7%E5%92%8C%E8%AF%AF%E5%B7%AE.png)
![第二个函数的两种方法拟合对比](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/1%2Bsin(3x)__%E5%8F%AF%E8%A7%86%E5%8C%96%E7%9B%B8%E5%85%B3%E6%80%A7%E5%92%8C%E8%AF%AF%E5%B7%AE.png)


#### 5.对比表格

                                          函数1的实验数据对比表
                                                      
![第一个函数的两种方法拟合对比](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E6%95%B0%E6%8D%AE%E8%A1%A8%E6%A0%BC1.png)

                                          函数2的实验数据对比表
                                                      
![第二个函数的两种方法拟合对比](https://github.com/summershaaa/Genetic-Programming-to-Solve-Symbol-Regression-with-Noisy-Data/blob/master/Image/%E6%95%B0%E6%8D%AE%E8%A1%A8%E6%A0%BC2.png)

