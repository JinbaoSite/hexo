---
title: Python机器学习应用 | 强化学习
date: 2017-07-02 17:56:21
categories: [机器学习]
---

## 1 强化学习

1、强化学习就是程序或智能体（agent）通过与环境不断地进行交互学习一个从环境到动作的映射，学习的目标就是使累计回报最大化。
2、强化学习是一种试错学习，因其在各种状态（环境）下需要尽量尝试所有可以选择的动作，通过环境给出的反馈（即奖励）来判断动作的优劣，最终获得环境和最优动作的映射关系（即策略）。

## 2 马尔可夫决策过程（MDP）

马尔可夫决策过程（Markov Decision Process）通常用来描述一个强化学习问题，智能体agent根据当前环境的观察采取动作获得环境的反馈，并使环境发生改变的循环过程。
![](http://oltfslql1.bkt.clouddn.com/mdp.jpg)

## 3 MDP基本元素

1、$s \in S$：有限状态state集合，s表示某个特定状态；
2、$a \in A$：有限动作action集合，a表示某个特定动作；
3、$T(S,a,S')~P_r(s'|s,a)$：状态转移模型，根据当前状态s和动作a预测下一个状态s，这里的$P_r$表示从s采取行动a转移到$s'$的概率；
4、$R(s,a)$：表示agent采取某个动作后的即时奖励，它还有$R(s,a,s'),R(s)$等表现形式；
5、Policy $\pi (s) \rightarrow a$：根据当前state来产生action，可表现为$a= \pi (s)$或$\pi (a|s) = P (a|s)$，后者表示某种状态下执行某个动作的概率。

## 4 值函数

状态值函数V表示执行策略$\pi$能得到的累计折扣奖励：

$$V^{\pi}(s) = E[R(s_0,a_0)+\gamma R(s_1,a_1) + \gamma ^2 R(s_2,a_2)+... | s=s_0]$$

即：

$$V^{\pi}(s) = R(s,a) + \gamma \sum_{s' \in S} p(s'|s,\pi (s)) V^{\pi} (s')$$

状态动作值函数$Q(s,a)$表示在状态s下执行动作a能得到的累计折扣奖励：

$$Q^{\pi}(s,a)= E[R(s_0,a_0)+\gamma R(s_1,a_1) + \gamma ^2 R(s_2,a_2)+... | s=s_0,a=a_0]$$

即：

$$Q^{\pi}(s,a) = R(s,a) + \gamma \sum_{s' \in S} p(s'|s,\pi (s)) Q^{\pi} (s',\pi (s'))$$

## 5 最优值函数

$$V^{*}(s)=max_{a \in A} R(s,a) + \gamma \sum_{s' \in S} p(s' | s,a) V^*(s') \\ 
Q^*(s,a) = R(s,a) + \gamma ( \sum_{s' \in S} p(s'|s,a)) max_{b \in A}Q^* (s',a)$$


## 6 最优控制

在得到最优值函数之后，可以通过值函数的值得到状态s时应该采取的动作a：

$$\pi (s) = argmax_{a \in A} [R(s,a) + \gamma \sum_{s' \in S} p(s'|s,a)V^*(s')] \\
\pi (s) = argmax_{a \in A} Q^*(s,a) \\
V^*(s) = max_{a \in A} Q^*(s,a)$$


## 7 蒙特卡洛强化学习

1、在现实的强化学习任务中，环境的转移概率、奖励函数往往很难得知，甚至很难得知环境中有多少状态。若学习算法不再依赖于环境建模，则称为免模型学习，蒙特卡洛强化学习就是其中的一种。
2、蒙特卡洛强化学习使用多次采样，然后求取平均累计奖赏作为期望累计奖赏的近似。
3、蒙特卡洛强化学习：直接对状态动作值函数Q(s,a)进行估计，每采样一条轨迹，就根据轨迹中的所有“状态-动作”利用下面的公式对来对值函数进行更新。

$$
Q(s,a) = \frac{Q(s,a)*count(s,a)+R}{count(s,a)+1}
$$

4、每次采样更新完所有的“状态-动作”对所对应的Q(s,a)，就需要更新采样策略$\pi$。但由于策略可能是确定性的，即一个状态对应一个动作，多次采样可能获得相同的采样轨迹，因此需要借助$\epsilon$贪心策略：

$$\pi (s,a)=
\begin{cases}
argmax_a Q(s,a) & \text{以概率1-$\epsilon$} \newline
随机从A中选取动作& \text{以概率$\epsilon$}
\end{cases}$$

5、蒙特卡洛强化学习算法需要采样一个完整的轨迹来更新值函数，效率较低，此外该算法没有充分利用强化学习任务的序贯决策结构。

## 8 Q-learning算法

1、Q-learning算法结合了动态规划与蒙特卡洛方法的思想，使得学习更加高效。
2、假设对于状态动作对(s,a)基于t次采样估算出其值函数为：

$$
Q^{\pi}_t(s,a) = \frac{1}{t} \sum_{i=1}^{t} r_i
$$

在进行t+1次采样后，依据增量更新得到：

$$
Q^{\pi}_{t+1}(s,a) = Q^{\pi}_t(s,a) + \frac{1}{t+1} (r_{t+1} - Q^{\pi}_t(s,a))
$$

然后，将$\frac{1}{t+1}$替换成系数$\alpha$（步长），得到：

$$
Q^{\pi}_{t+1}(s,a) = Q^{\pi}_t(s,a) + \alpha (r_{t+1} - Q^{\pi}_t(s,a))
$$

3、以$\gamma$折扣累计奖赏为例：

$$
r_{t+1} = R_s^a + \gamma Q^{\pi}_t(s',a')
$$

则值函数的更新方式如下：

$$
Q^{\pi}_{t+1}(s,a) = Q^{\pi}_t(s,a) + \alpha (R^a_s + \gamma Q^{\pi}_t(s',a') - Q^{\pi}_t(s,a))
$$

## 9 Q-learning算法流程

输入：环境$E$；动作空间$A$；起始状态$s_0$；奖励折扣$\gamma$；更新步长$\alpha$；
过程：
1:$Q(s,a)=0,\pi (s,a) = \frac{1}{|A|}$;
2:$s=s_0$;
3:$for t=1,2,...do$
4:&nbsp;&nbsp;&nbsp;&nbsp;$r,s'=$在E中执行动作$\pi ^{\epsilon} (s)$产生的奖赏和转移的状态;
5:&nbsp;&nbsp;&nbsp;&nbsp;$a=\pi(s')$;
6:&nbsp;&nbsp;&nbsp;&nbsp;$Q(s,a)=Q(s,a)+\alpha (r+\gamma Q(s',a')-Q(s,a))$;
7:&nbsp;&nbsp;&nbsp;&nbsp;$\pi(s)=argmax_{a"} Q(s,a")$;
8:&nbsp;&nbsp;&nbsp;&nbsp;$s=s',a=a'$;
9:$end$ $for$
输出：策略$\pi$

## 10 Deep Q Network（DQN）

1、Deep Q Network（DQN）：是将神经网络(neural network) 和Qlearning结合，利用神经网络近似模拟函数Q(s,a)，输入是问题的状态（e.g.,图形），输出是每个动作a对应的Q值，然后依据Q值大小选择对应状态执行的动作，以完成控制。
2、神经网络的参数：应用监督学习完成

## 11 DQN学习过程

![](http://oltfslql1.bkt.clouddn.com/dqn.jpg)
1.状态$s$输入，获得所有动作对应的$Q$值$Q(s,a)$；
2.选择对应$Q$值最大的动作$a′$并执行；
3.执行后环境发生改变，并能够获得环境的奖励$\gamma$；
4.利用奖励$\gamma$更新$Q(s,a′)$--强化学习利用新的$Q(s,a′)$更新网络参数—监督学习

## 12 DQN算法流程

Algorithm 1 Deep Q-learning with Experience Replay
&nbsp;&nbsp;&nbsp;&nbsp;Initialilze replay memory D to capacity N
&nbsp;&nbsp;&nbsp;&nbsp;Initialize action-value function Q with random weights
&nbsp;&nbsp;&nbsp;&nbsp;for episode = 1,M do
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Initialise sequence $s_1$={$x_1$} and preporcessed sequeced $\phi_1 = \phi(s_1)$
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;for t=1,T do
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;With probability $\epsilon$ select a random action $a_t$
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;otherwise select $a_t = max_a Q^*(\phi(s_t),a;\theta)$
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Execute action $a_t$ in emulator and observe reward $r_t$ and image $x_{t+1}$
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Set $s_{t+1} = s_t,a_t,x_{t+1}$ and preprocess $\phi_{t+1}=\phi(s_t+1)$
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Store transition ($\phi_t,a_t,r_t,\phi_{t+1}$) in D
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sample random minibatch of transitions ($\phi_j,a_j,r_j,\phi_{j+1}$) from D
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Set $y_j=
\begin{cases}
r_j& \text{for terminal $\phi_{j+1}$}\\
r_j + \gamma max_{a'} Q( \phi_{j+1},a';\theta )& \text{for non-terminal $\phi_{j+1}$}
\end{cases}$
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Perform a gradient descent step on $(y_i - Q(\phi_j,a_j;\theta))^2$ according to equation 3
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;end for
&nbsp;&nbsp;&nbsp;&nbsp;end for

部分流程解释：
1、Initialilze replay memory D to capacity N：初始化D：用于存放采集($S_t,a_t,r_t,S_{t+1}$)的状态转移过程，用于网络参数的训练
2、Initialize action-value function Q with random weights：随机初始化神经网络的参数
3、Initialise sequence $s_1$={$x_1$} and preporcessed sequeced $\phi_1 = \phi(s_1)$：获取环境的初始状态（x是采集的图像，使用图像作为agent的状态；预处理过程是说，使用4张图像代表当前状态，这里可以先忽略掉）
4、otherwise select $a_t = max_a Q^*(\phi(s_t),a;\theta)$：$epsilon$贪心策略：使用$epsilon$概率随机选取动作或1- $epsilon$的概率根据神经网络的输出选择动作
5、Execute action $a_t$ in emulator and observe reward $r_t$ and image $x_{t+1}$：在模拟器中执行选定的动作，获得奖励$\gamma_t$和一个观察$x_{t+1}$
6、Store transition ($\phi_t,a_t,r_t,\phi_{t+1}$) in D：设置$S_{t+1}$，并将状态($S_t,a_t,r_t,S_{t+1}$)转移过程存放在D中
7、Sample random minibatch of transitions ($\phi_j,a_j,r_j,\phi_{j+1}$) from D：从D中进行随机采样，获得一部分状态转移过程历史信息
8、Set $y_j=
\begin{cases}
r_j& \text{for terminal $\phi_{j+1}$}\\
r_j + \gamma max_{a'} Q( \phi_{j+1},a';\theta )& \text{for non-terminal $\phi_{j+1}$}
\end{cases}$：使用Q-learning方法更新状态值函数的值（终止与非终止状态的更新不同）
9、Perform a gradient descent step on $(y_i - Q(\phi_j,a_j;\theta))^2$ according to equation 3：使用监督学习方法更新网络的参数