# 第1章 强化学习概述

### 1.1 强化学习概念

​	**强化学习**（Reinforcement Learning，简称RL），来源自行为心理学，表示生物为了趋利避害而更频繁实施对自己有利的策略。 生物会根据之前的动作和获得相应的“感觉”选择之后采用可以获得更多利益的动作，这种特殊刺激使生物更趋向于采用某种策略的现象称为“强化”。强化行为的刺激可以称为“强化物”，因为强化物导致策略的改变称为“强化学习”。

### 1.2 强化学习与机器学习

​	强化学习作为机器学习的一个子领域，解决随时间的推移自动学习最优策略的问题。强化学习将机器学习（ML）问题隐藏的时间维度纳入学习，更接近人工智能的感知。

### 1.3 学习——监督、无监督和强化

监督学习：有标定的数据集，学习从数据集到标记的映射

无监督学习：无标定数据集，使用方法自标定数据集

强化学习：介于监督学习与无监督学习之间，自学习和标记学习共存

为什么我们关注强化学习，其中非常重要的一点就是强化学习得到的模型可以有超人类的表现。

* 监督学习获取的这些监督数据，其实是让人来标注的。比如说 ImageNet 的图片都是人类标注的。那么我们就可以确定这个算法的上限(upper bound)就是人类的表现，人类的这个标注结果决定了它永远不可能超越人类。
* 但是对于强化学习，它在环境里面自己探索，有非常大的潜力，它可以获得超越人的能力的这个表现，比如谷歌 DeepMind 的 AlphaGo 这样一个强化学习的算法可以把人类最强的棋手都打败。

### 1.4 强化学习方式

![](C:/Users/yqf/Desktop/notes/img/1.1.png ':size=450')

**强化学习讨论的问题是一个智能体(agent) 怎么在一个复杂不确定的环境(environment)里面去极大化它能获得的奖励。** 示意图由两部分组成：agent 和 environment。在强化学习过程中，agent 跟 environment 一直在交互。Agent 在环境里面获取到状态，agent 会利用这个状态输出一个动作(action)，一个决策。然后这个决策会放到环境之中去，环境会根据 agent 采取的决策，输出下一个状态以及当前的这个决策得到的奖励。Agent 的目的就是为了尽可能多地从环境中获取奖励。

### Reward

奖励是由环境给的一个标量的反馈信号(scalar feedback signal)，这个信号显示了 agent 在某一步采取了某个策略的表现如何。

强化学习的目的就是为了最大化 agent 可以获得的奖励，agent 在这个环境里面存在的目的就是为了极大化它的期望的累积奖励(expected cumulative reward)。

不同的环境，奖励也是不同的。这里给大家举一些奖励的例子。

* 比如说一个下象棋的选手，他的目的其实就为了赢棋。奖励是说在最后棋局结束的时候，他知道会得到一个正奖励或者负奖励。
* 羚羊站立也是一个强化学习过程，它得到的奖励就是它是否可以最后跟它妈妈一块离开或者它被吃掉。
* 在股票管理里面，奖励定义由你的股票获取的收益跟损失决定。
* 在玩雅达利游戏的时候，奖励就是你有没有在增加游戏的分数，奖励本身的稀疏程度决定了这个游戏的难度。

### Sequential Decision Making

![](D:\github\easy-rl\docs\chapter1\img\1.21.png ':size=500')

在一个强化学习环境里面，agent 的目的就是选取一系列的动作来极大化它的奖励，所以这些采取的动作必须有长期的影响。但在这个过程里面，它的奖励其实是被延迟了，就是说你现在采取的某一步决策可能要等到时间很久过后才知道这一步到底产生了什么样的影响。

这里一个示意图就是我们玩这个 Atari 的 Pong 游戏，你可能只有到最后游戏结束过后，才知道这个球到底有没有击打过去。中间你采取的 up 或 down 行为，并不会直接产生奖励。强化学习里面一个重要的课题就是近期奖励和远期奖励的一个权衡(trade-off)。怎么让 agent 取得更多的长期奖励是强化学习的问题。


在跟环境的交互过程中，agent 会获得很多观测。在每一个观测会采取一个动作，它也会得到一个奖励。**所以历史是观测(observation)、行为、奖励的序列：**
$$
H_{t}=O_{1}, R_{1}, A_{1}, \ldots, A_{t-1}, O_{t}, R_{t}
$$
Agent 在采取当前动作的时候会依赖于它之前得到的这个历史，**所以你可以把整个游戏的状态看成关于这个历史的函数：**
$$
S_{t}=f\left(H_{t}\right)
$$
Q: 状态和观测有什么关系



A: `状态(state)`    $s$  是对世界的完整描述，不会隐藏世界的信息。`观测(observation)` $o$ 是对状态的部分描述，可能会遗漏一些信息。在 deep RL 中，我们几乎总是用一个实值的向量、矩阵或者更高阶的张量来表示状态和观测。举个例子，我们可以用 RGB 像素值的矩阵来表示一个视觉的观测，我们可以用机器人关节的角度和速度来表示一个机器人的状态。

环境有自己的函数 $S_{t}^{e}=f^{e}\left(H_{t}\right)$ 来更新状态，在 agent 的内部也有一个函数 $S_{t}^{a}=f^{a}\left(H_{t}\right)$ 来更新状态。当 agent 的状态跟环境的状态等价的时候，我们就说这个环境是 `full observability`，就是全部可以观测。换句话说，当 agent 能够观察到环境的所有状态时，我们称这个环境是`完全可观测的(fully observed)`。在这种情况下面，强化学习通常被建模成一个 Markov decision process(MDP)的问题。在 MDP 中， $O_{t}=S_{t}^{e}=S_{t}^{a}$。

但是有一种情况是 agent 得到的观测并不能包含环境运作的所有状态，因为在这个强化学习的设定里面，环境的状态才是真正的所有状态。

* 比如 agent 在玩这个 black jack 这个游戏，它能看到的其实是牌面上的牌。
* 或者在玩雅达利游戏的时候，观测到的只是当前电视上面这一帧的信息，你并没有得到游戏内部里面所有的运作状态。

也就是说当 agent 只能看到部分的观测，我们就称这个环境是`部分可观测的(partially observed)`。在这种情况下面，强化学习通常被建模成一个 POMDP 的问题。

`部分可观测马尔可夫决策过程(Partially Observable Markov Decision Processes, POMDP)`是一个马尔可夫决策过程的泛化。POMDP 依然具有马尔可夫性质，但是假设智能体无法感知环境的状态 $s$，只能知道部分观测值 $o$。比如在自动驾驶中，智能体只能感知传感器采集的有限的环境信息。

POMDP 可以用一个 7 元组描述：$(S,A,T,R,\Omega,O,\gamma)$，其中 $S$ 表示状态空间，为隐变量，$A$ 为动作空间，$T(s'|s,a)$ 为状态转移概率，$R$ 为奖励函数，$\Omega(o|s,a)$ 为观测概率，$O$ 为观测空间，$\gamma$ 为折扣系数。

## Action Spaces

不同的环境允许不同种类的动作。在给定的环境中，有效动作的集合经常被称为`动作空间(action space)`。像 Atari 和 Go 这样的环境有`离散动作空间(discrete action spaces)`，在这个动作空间里，agent 的动作数量是有限的。在其他环境，比如在物理世界中控制一个 agent，在这个环境中就有`连续动作空间(continuous action spaces)` 。在连续空间中，动作是实值的向量。 

例如，

* 走迷宫机器人如果只有东南西北这 4 种移动方式，则其为离散动作空间；
* 如果机器人向 $360^{\circ}$ 中的任意角度都可以移动，则为连续动作空间。

## Major Components of an RL Agent 

对于一个强化学习 agent，它可能有一个或多个如下的组成成分：

*  `策略函数(policy function)`，agent 会用这个函数来选取下一步的动作。

*  `价值函数(value function)`，我们用价值函数来对当前状态进行估价，它就是说你进入现在这个状态，可以对你后面的收益带来多大的影响。当这个价值函数大的时候，说明你进入这个状态越有利。

*  `模型(model)`，模型表示了 agent 对这个环境的状态进行了理解，它决定了这个世界是如何进行的。



### Policy

我们深入看这三个组成成分的一些细节。

Policy 是 agent 的行为模型，它决定了这个 agent 的行为，它其实是一个函数，把输入的状态变成行为。这里有两种 policy：

* 一种是 `stochastic policy(随机性策略)`，它就是 $\pi$ 函数 $\pi(a | s)=P\left[A_{t}=a | S_{t}=s\right]$ 。当你输入一个状态 $s$ 的时候，输出是一个概率。这个概率就是你所有行为的一个概率，然后你可以进一步对这个概率分布进行采样，得到真实的你采取的行为。比如说这个概率可能是有 70% 的概率往左，30% 的概率往右，那么你通过采样就可以得到一个 action。
* 一种是 `deterministic policy(确定性策略)`，就是说你这里有可能只是采取它的极大化，采取最有可能的动作，即 $a^{*}=\arg \underset{a}{\max} \pi(a \mid s)$。 你现在这个概率就是事先决定好的。

从  Atari 游戏来看的话，策略函数的输入就是游戏的一帧，它的输出决定你是往左走或者是往右走。

通常情况下，强化学习一般使用`随机性策略`。随机性策略有很多优点：

* 在学习时可以通过引入一定随机性来更好地探索环境；

* 随机性策略的动作具有多样性，这一点在多个智能体博弈时也非常重要。采用确定性策略的智能体总是对同样的环境做出相同的动作，会导致它的策略很容易被对手预测。

### Value Function

**价值函数是未来奖励的一个预测，用来评估状态的好坏**。

价值函数里面有一个 `discount factor(折扣因子)`，我们希望尽可能在短的时间里面得到尽可能多的奖励。如果我们说十天过后，我给你 100 块钱，跟我现在给你 100 块钱，你肯定更希望我现在就给你 100 块钱，因为你可以把这 100 块钱存在银行里面，你就会有一些利息。所以我们就通过把这个折扣因子放到价值函数的定义里面，价值函数的定义其实是一个期望，如下式所示：


$$
v_{\pi}(s) \doteq \mathbb{E}_{\pi}\left[G_{t} \mid S_{t}=s\right]=\mathbb{E}_{\pi}\left[\sum_{k=0}^{\infty} \gamma^{k} R_{t+k+1} \mid S_{t}=s\right], \text { for all } s \in \mathcal{S}
$$
这里有一个期望 $\mathbb{E}_{\pi}$，这里有个小角标是 $\pi$ 函数，这个 $\pi$ 函数就是说在我们已知某一个策略函数的时候，到底可以得到多少的奖励。

我们还有一种价值函数：Q 函数。Q 函数里面包含两个变量：状态和动作，其定义如下式所示：
$$
q_{\pi}(s, a) \doteq \mathbb{E}_{\pi}\left[G_{t} \mid S_{t}=s, A_{t}=a\right]=\mathbb{E}_{\pi}\left[\sum_{k=0}^{\infty} \gamma^{k} R_{t+k+1} \mid S_{t}=s, A_{t}=a\right]
$$
所以你未来可以获得多少的奖励，它的这个期望取决于你当前的状态和当前的行为。这个 Q 函数是强化学习算法里面要学习的一个函数。因为当我们得到这个 Q 函数后，进入某一种状态，它最优的行为就可以通过这个 Q 函数来得到。

### Model

第三个组成部分是模型，**模型决定了下一个状态会是什么样的，就是说下一步的状态取决于你当前的状态以及你当前采取的行为。**它由两个部分组成，

* 概率：这个转移状态之间是怎么转移的，如下式所示：

$$
\mathcal{P}_{s s^{\prime}}^{a}=\mathbb{P}\left[S_{t+1}=s^{\prime} \mid S_{t}=s, A_{t}=a\right]
$$

* 奖励函数：当你在当前状态采取了某一个行为，可以得到多大的奖励，如下式所示：

$$
\mathcal{R}_{s}^{a}=\mathbb{E}\left[R_{t+1} \mid S_{t}=s, A_{t}=a\right]
$$

当我们有了这三个组成部分过后，就形成了一个 `马尔可夫决策过程(Markov Decision Process)`。这个决策过程可视化了状态之间的转移以及采取的行为。





## Types of RL Agents

**根据 agent 学习的东西不同，我们可以把 agent 进行归类。**

* `基于价值的 agent(value-based agent)`。
  * 这一类 agent 显式地学习的是价值函数，
  * 隐式地学习了它的策略。策略是从我们学到的价值函数里面推算出来的。
* `基于策略的 agent(policy-based agent)`。
  * 这一类 agent 直接去学习 policy，就是说你直接给它一个状态，它就会输出这个动作的概率。
  * 在基于策略的 agent 里面并没有去学习它的价值函数。
* 把 value-based 和 policy-based 结合起来就有了 `Actor-Critic agent`。这一类 agent 把它的策略函数和价值函数都学习了，然后通过两者的交互得到一个最佳的行为。

Q: 基于策略迭代和基于价值迭代的强化学习方法有什么区别?

A: 对于一个状态转移概率已知的马尔可夫决策过程，我们可以使用动态规划算法来求解；从决策方式来看，强化学习又可以划分为基于策略迭代的方法和基于价值迭代的方法。`决策方式`是智能体在给定状态下从动作集合中选择一个动作的依据，它是静态的，不随状态变化而变化。

在`基于策略迭代`的强化学习方法中，智能体会`制定一套动作策略`（确定在给定状态下需要采取何种动作），并根据这个策略进行操作。强化学习算法直接对策略进行优化，使制定的策略能够获得最大的奖励。

而在`基于价值迭代`的强化学习方法中，智能体不需要制定显式的策略，它`维护一个价值表格或价值函数`，并通过这个价值表格或价值函数来选取价值最大的动作。基于价值迭代的方法只能应用在不连续的、离散的环境下（如围棋或某些游戏领域），对于行为集合规模庞大、动作连续的场景（如机器人控制领域），其很难学习到较好的结果（此时基于策略迭代的方法能够根据设定的策略来选择连续的动作)。

基于价值迭代的强化学习算法有 Q-learning、 Sarsa 等，而基于策略迭代的强化学习算法有策略梯度算法等。此外， Actor-Critic 算法同时使用策略和价值评估来做出决策，其中，智能体会根据策略做出动作，而价值函数会对做出的动作给出价值，这样可以在原有的策略梯度算法的基础上加速学习过程，取得更好的效果。

**另外，我们是可以通过 agent 到底有没有学习这个环境模型来分类。**

* `model-based(有模型)` RL agent，它通过学习这个状态的转移来采取动作。
* ` model-free(免模型)` RL agent，它没有去直接估计这个状态的转移，也没有得到环境的具体转移变量。它通过学习价值函数和策略函数进行决策。Model-free 的模型里面没有一个环境转移的模型。

我们可以用马尔可夫决策过程来定义强化学习任务，并表示为四元组 $<S,A,P,R>$，即状态集合、动作集合、状态转移函数和奖励函数。如果这四元组中所有元素均已知，且状态集合和动作集合在有限步数内是有限集，则机器可以对真实环境进行建模，构建一个虚拟世界来模拟真实环境的状态和交互反应。

具体来说，当智能体知道状态转移函数 $P(s_{t+1}|s_t,a_t)$ 和奖励函数 $R(s_t,a_t)$ 后，它就能知道在某一状态下执行某一动作后能带来的奖励和环境的下一状态，这样智能体就不需要在真实环境中采取动作，直接在虚拟世界中学习和规划策略即可。这种学习方法称为`有模型学习`。

然而在实际应用中，智能体并不是那么容易就能知晓 MDP 中的所有元素的。**通常情况下，状态转移函数和奖励函数很难估计，甚至连环境中的状态都可能是未知的，这时就需要采用免模型学习。**免模型学习没有对真实环境进行建模，智能体只能在真实环境中通过一定的策略来执行动作，等待奖励和状态迁移，然后根据这些反馈信息来更新行为策略，这样反复迭代直到学习到最优策略。

Q: 有模型强化学习和免模型强化学习有什么区别？

A: 针对是否需要对真实环境建模，强化学习可以分为有模型学习和免模型学习。

* 有模型学习是指根据环境中的经验，构建一个虚拟世界，同时在真实环境和虚拟世界中学习；

* 免模型学习是指不对环境进行建模，直接与真实环境进行交互来学习到最优策略。

总的来说，有模型学习相比于免模型学习仅仅多出一个步骤，即对真实环境进行建模。因此，一些有模型的强化学习方法，也可以在免模型的强化学习方法中使用。在实际应用中，如果不清楚该用有模型强化学习还是免模型强化学习，可以先思考一下，在智能体执行动作前，是否能对下一步的状态和奖励进行预测，如果可以，就能够对环境进行建模，从而采用有模型学习。

免模型学习通常属于数据驱动型方法，需要大量的采样来估计状态、动作及奖励函数，从而优化动作策略。例如，在 Atari 平台上的 Space Invader 游戏中，免模型的深度强化学习需要大约 2 亿帧游戏画面才能学到比较理想的效果。相比之下，有模型学习可以在一定程度上缓解训练数据匮乏的问题，因为智能体可以在虚拟世界中行训练。

免模型学习的泛化性要优于有模型学习，原因是有模型学习算需要对真实环境进行建模，并且虚拟世界与真实环境之间可能还有差异，这限制了有模型学习算法的泛化性。

有模型的强化学习方法可以对环境建模，使得该类方法具有独特魅力，即“想象能力”。在免模型学习中，智能体只能一步一步地采取策略，等待真实环境的反馈；而有模型学习可以在虚拟世界中预测出所有将要发生的事，并采取对自己最有利的策略。

**目前，大部分深度强化学习方法都采用了免模型学习**，这是因为：

* 免模型学习更为简单直观且有丰富的开源资料，像 DQN、AlphaGo 系列等都采用免模型学习；
* 在目前的强化学习研究中，大部分情况下环境都是静态的、可描述的，智能体的状态是离散的、可观察的（如 Atari 游戏平台），这种相对简单确定的问题并不需要评估状态转移函数和奖励函数，直接采用免模型学习，使用大量的样本进行训练就能获得较好的效果。

![](C:/Users/yqf/Desktop/notes/img/1.36.png ':size=400')

把几类模型放到同一个饼图里面。饼图有三个组成部分：价值函数、策略和模型。按一个 agent 具不具有三者中的两者或者一者可以把它分成很多类

## Learning and Planning

Learning 和 Planning 是序列决策的两个基本问题。

在强化学习中，环境初始时是未知的，agent 不知道环境如何工作，agent 通过不断地与环境交互，逐渐改进策略。

在 plannning 中，环境是已知的，我们被告知了整个环境的运作规则的详细信息。Agent 能够计算出一个完美的模型，并且在不需要与环境进行任何交互的时候进行计算。Agent 不需要实时地与环境交互就能知道未来环境，只需要知道当前的状态，就能够开始思考，来寻找最优解。

在这个游戏中，规则是制定的，我们知道选择 left 之后环境将会产生什么变化。我们完全可以通过已知的变化规则，来在内部进行模拟整个决策过程，无需与环境交互。

一个常用的强化学习问题解决思路是，先学习环境如何工作，也就是了解环境工作的方式，即学习得到一个模型，然后利用这个模型进行规划。

## Exploration and Exploitation

在强化学习里面，`探索` 和`利用` 是两个很核心的问题。

* 探索是说我们怎么去探索这个环境，通过尝试不同的行为来得到一个最佳的策略，得到最大奖励的策略。

* 利用是说我们不去尝试新的东西，就采取已知的可以得到很大奖励的行为。

因为在刚开始的时候强化学习 agent 不知道它采取了某个行为会发生什么，所以它只能通过试错去探索。所以探索就是在试错来理解采取的这个行为到底可不可以得到好的奖励。利用是说我们直接采取已知的可以得到很好奖励的行为。所以这里就面临一个权衡，怎么通过牺牲一些短期的奖励来获得行为的理解，从而学习到更好的策略。

下面举一些探索和利用的例子。

* 以选择餐馆为例，
  * 利用：我们直接去你最喜欢的餐馆，因为你去过这个餐馆很多次了，所以你知道这里面的菜都非常可口。
  * 探索：你把手机拿出来，你直接搜索一个新的餐馆，然后去尝试它到底好不好吃。你有可能对这个新的餐馆非常不满意，钱就浪费了。

* 以做广告为例，
  * 利用：我们直接采取最优的这个广告策略。
  * 探索：我们换一种广告策略，看看这个新的广告策略到底可不可以得到奖励。

* 以挖油为例，
  * 利用：我们直接在已知的地方挖油，我们就可以确保挖到油。
  * 探索：我们在一个新的地方挖油，就有很大的概率，你可能不能发现任何油，但也可能有比较小的概率可以发现一个非常大的油田。
* 以玩游戏为例，
  * 利用：你总是采取某一种策略。比如说，你可能打街霸，你采取的策略可能是蹲在角落，然后一直触脚。这个策略很可能可以奏效，但可能遇到特定的对手就失效。
  * 探索：你可能尝试一些新的招式，有可能你会发出大招来，这样就可能一招毙命。

### K-armed Bandit

与监督学习不同，强化学习任务的最终奖赏是在多步动作之后才能观察到，这里我们不妨先考虑比较简单的情形：最大化单步奖赏，即仅考虑一步操作。需注意的是，即便在这样的简化情形下，强化学习仍与监督学习有显著不同，因为机器需通过尝试来发现各个动作产生的结果，而没有训练数据告诉机器应当做哪个动作。

想要最大化单步奖赏需考虑两个方面：一是需知道每个动作带来的奖赏，二是要执行奖赏最大的动作。若每个动作对应的奖赏是一个确定值，那么尝试遍所有的动作便能找出奖赏最大的动作。然而，更一般的情形是，一个动作的奖赏值是来自于一个概率分布，仅通过一次尝试并不能确切地获得平均奖赏值。

实际上，单步强化学习任务对应了一个理论模型，即` K-臂赌博机(K-armed bandit)`。K-臂赌博机也被称为 `多臂赌博机(Multi-armed bandit) `。如上图所示，K-摇臂赌博机有 K 个摇臂，赌徒在投入一个硬币后可选择按下其中一个摇臂，每个摇臂以一定的概率吐出硬币，但这个概率赌徒并不知道。赌徒的目标是通过一定的策略最大化自己的奖赏，即获得最多的硬币。

* 若仅为获知每个摇臂的期望奖赏，则可采用`仅探索(exploration-only)法`：将所有的尝试机会平均分配给每个摇臂(即轮流按下每个摇臂)，最后以每个摇臂各自的平均吐币概率作为其奖赏期望的近似估计。

* 若仅为执行奖赏最大的动作，则可采用`仅利用(exploitation-only)法`：按下目前最优的(即到目前为止平均奖赏最大的)摇臂，若有多个摇臂同为最优，则从中随机选取一个。

显然，仅探索法能很好地估计每个摇臂的奖赏，却会失去很多选择最优摇臂的机会；仅利用法则相反，它没有很好地估计摇臂期望奖赏，很可能经常选不到最优摇臂。因此，这两种方法都难以使最终的累积奖赏最大化。

事实上，探索(即估计摇臂的优劣)和利用(即选择当前最优摇臂)这两者是矛盾的，因为尝试次数(即总投币数)有限，加强了一方则会自然削弱另一方，这就是强化学习所面临的`探索-利用窘境(Exploration-Exploitation dilemma)`。显然，想要累积奖赏最大，则必须在探索与利用之间达成较好的折中。
