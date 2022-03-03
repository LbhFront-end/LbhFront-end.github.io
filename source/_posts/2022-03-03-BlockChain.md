---
title: 区块链开发入门
date: 2022-03-03 17:13:25
tags: 区块链
categories: 
	- 区块链
---

失踪人员回归，学习记录点区块链知识...

# 区块链开发入门：从0到1构建基于以太坊智能合约的ICO DApp

## 区块链简明发展史

### 比特币

区块链技术起源于2008年10月31日中本聪发表的比特币论文：[《Bitcoin: A Peer to Peer Electronic Cash System》](https://bitcoin.org/bitcoin.pdf)，即比特币白皮书，论文论述了基于P2P网络的电子现金系统的设计，系统中产生和流通的现金就是比特币(Bitcoin)。

### 白皮书中文

> 比特币：一种点对点的电子现金系统
> 一个完全的点对点版本的电子现金将允许一方不通过金融机构直接在线支付给另一方。电子签名提供了部分解决方案，但是如果还需要一个可信任的第三方来防止双花，那么这个最大的好处也就没有意义。我们提出一个用点对点网络来解决双花的方案。这个网络给每笔交易打上时间戳，并进行哈希计算，放进一条基于哈希工作量证明的链，这形成了一个不可改变的记录，除非重做这些工作量。最长的链不仅是见证序列的证明，还证明了它来自最大的CPU算力池。因为大部分的算力由诚实的节点控制，他们将会产生一条比攻击者要长的链。网络本身需要极小化结构。消息被尽力广播，并且节点可以随意离开或重新加入网络，接受最长的工作量证明的链作为它离开这段时间发生事情的证明

#### 1.介绍

互联网上的商业几乎完全依赖信任的第三方金融机构来处理电子支付。对于大多数交易来说，这套系统工作的足够好了，但是依然受到了基于信任模型的天然缺点的困扰。完全不能撤销的交易是不可能的，因为第三方金融机构不可避免的要调解纠纷。调解的代价增加了交易的成本，限制了最小实际交易的大小，切断了临时交易的可能性，丧失了对不可撤销服务提供不可撤销支付的可能性，这又是一个广义成本。因为撤销的可能性，信任的需求不断蔓延开来。商户必须提防他们的客户，越来越多的他们本不该需要的信息困扰他们。不得不接受一定比例的骗子。这些成本和支付的不确定问题可以用面对面使用现金避免，但是还没有机制存在使得通过通信信道支付而不需要信任的第三方。

需要的是一个电子支付系统，这个系统建立在密码学证明基础上而不是信任，运行任意有这个意愿的双方直接互相转账而不需要一个可信任的第三方。交易从计算上不可撤销的，这将保护卖方权利防止被骗，并且常规的托管机制很容易实现来保护买方权益。下面提出一个防止双花的解决方案，使用点对点分布式时间戳服务器来产生按时间排序的交易的计算证明。只要城市节点控制的CPU的算力大于攻击者节点的算力，这个系统就是安全的。

#### 2.交易

我们把一种电子币定义成一条数字签名链。每一个所有者把币转给下一个人的时候，是通过将前一个交易的哈希和下一个所有者的公钥进行数字签名，并把这些追加在币的后面。收款人可以通过验证签名来确定链的所有者。

![](/images/2022-03-03-blockChain-trade_coin.jpg)

当然，问题是收款人无法验证其中一个所有者是否同一个币花了两次。一个普遍的做法是引入一个信任的中央机关，或铸币厂，他们可以检查每一笔交易来防止双花问题。每次交易后，这个币必须返回到铸币厂，这样才能发行新币，只有直接从铸币厂发行的币才被相信是没有被双花的。这个方案的问题是，整个金钱系统的命运掌握在经营铸币厂的公司，每一笔交易都要经他们，就像银行一样。

我们需要一种方法，这种方法让收款人知道上一个所有者没有签署任何以前的交易。我们的目的是让最早的交易是可信的，我们不关心后面是不是有人企图进行双花。仅有的可以确认某一个交易存在的办法是要知道所有交易。在基于铸币厂的模型中，铸币厂知道所有交易，并且可以确定哪个交易先发生。为了在无信任第三方的情况下达到共识，这个共识就是认同同一个按接收的交易顺序排列的历史记录。收款人需要证据来说明在每一笔交易的时候，大多数节点一致认为这个交易是第一时间到达的。

#### 3.时间戳服务器

我们提出的解决方案是从一个时间戳服务开始，这个服务器工作的方式是，对条目所在的区块的哈希加盖时间戳，并且广泛地公开这些哈希，比如通过报纸或者新闻组邮件。显然，为了能进入这个哈希序列，时间戳证明的数据在那个时间必须存在。每一个时间戳和以前的时间戳，形成了一条链，每一个追加的时间戳都是对前一个时间戳进行加强。

![](/images/2022-03-03-blockChain-hash.jpg)

#### 4.工作量证明

为了基于点对点的基础实现一个分布式的时间戳服务器，我们将需要一个工作量证明的系统，这个系统和亚当贝克创造的“哈希现金”类似，而不是报纸或新闻组邮件。这个工作量包含寻找一个哈希值，比如用哈希算法SHA-256,这个哈希值以若干0开头。平均工作量和开头的0的个数是指数关系，并且验证很简单，只需要执行一次单独的哈希计算。

在我们的时间戳网络里，是这样实现工作量证明的，就是不断增加区块里的一个临时的数值，直到找到一个值使得区块的哈希值满足开头0的个数要求。一旦CPU花费计算满足了工作量证明的要求，这个区块链就无法修改，除非重新计算。随着后面的区块不断产生，想要改变这个区块，需要重做它后面所有区块的工作量。

![](/images/2022-03-03-blockChain-workload.jpg)

工作量证明还解决了在多数决策法中决定展示的问题。如果大多数是基于一个IP一票，这可能会被可以支配多个IP的人破坏。工作量证明本质上也是一个IP一票。最长的链代表了大多数人的决定，这个链投入了最大的工作量。如果大多数的CPU算力由诚实节点控制，诚实的链就会增长的很快，超过任何竞争链。为了修改过去的一个块，攻击者需要重新投入算力完成这个却快和它以后的区块的工作量，然后追上并超越诚实节点的工作量。我们后面讨论落后的攻击者追上的概率，这个概率随着后面区块的增加呈现指数级减少。

硬件的速度越来越快，参与运行的节点随着时间兴趣也随着经常变化，为了抵消这些因素的影响，工作量的难度是由每小时区块产生的数量的浮点型来决定的，如果区块产生的太快，难度就相应的增加。

#### 5.网络

运行这个网络的步骤如下：

1. 新交易给所有节点广播
2. 每个节点将新交易放到一个区块
3. 每个节点开始为这个区块寻找相应难度的工作量证明
4. 当一个节点找到了这个工作量证明，把这个区块广播给所有节点
5. 如果区块里所有的交易是有效的并且是没有被花费的，节点就会接受这个区块
6. 节点把这个区块的哈希作为上一个哈希，并开始进行工作以竞争创建下一个区块

节点总是认为最长的链是正确的，并且不断的工作去延长它。如果两个节点同时广播下一个区块，一些节点可能接受其中一个，也可能是另一个。这种情况下，他们在最先接收到的区块上工作，但是也会保存另外一个分支以防止它会变得更长。当下一个工作量被找到并且一个分支变得更长时，这种情况就会被打破，在另外一个分支上工作的节点就会切换到这个长的链上。

新交易广播不一定要广播到所有节点。只要他们能到达很多节点，这个交易很快就会进入下一个块。区块广播也能接受消息丢失。如果一个节点没有收到区块，当它收到下一个块时会发现自己少了一个区块，它就会请求来获得少的这个区块。

#### 6.激励

按照惯例，区块的第一个交易是一个特别的交易，这个交易会发行新币并且所有者是这个区块的创建者。这为节点支持网络引入了激励机制，并且这提供了一个初始发行货币进入流通的方式，因为没有一个中央机构去发行他们。不断增加新货币的过程类似于黄金矿工消耗资源来增加黄金的流通。在这里，消耗的是CPU的时间和电费。

激励还包括提供交易手续费。如果交易中输出值比输入值小，这个差值就是手续费，它被加入到这个区块激励值里。一旦预定数量的币全部进入流通，激励就全部转为交易手续费，完全没有通货膨胀。

激励有助于鼓励节点保持诚实，如果一个贪婪的攻击者掌握比所有诚实节点还要大的算力，他将面临一个选择，是通过偷回他支付的钱来诈骗人，还是用算力产生新的币。他应该会发现遵守规则有更多的好处，这个规则可以让他比其他人组合得到更多的新币，比破坏这个系统得到的更多，而且财产合法。

#### 7.回收磁盘空间

一旦一个币最新的交易被足够多的区块埋没，它之前的花费的交易就可以丢掉来节省空间。为了促成这个而不破坏区块的哈希，用这些交易生成一个默克尔树，仅仅根保存在区块的哈希里。那么旧区块可以通过去除树的一些分支进行压缩。这些内部的哈希就不用保存了。

![](/images/2022-03-03-blockChain-recycle.jpg)

一个区块头大概80字节，如果我们假设每十分钟产生一个区块，一年就是80字节x6x24x365=4.2兆字节。2008年出售的电脑典型的配置是2G内存，根据摩尔定律预测，每年增加1.2G，即使区块头全部放在内存里，存储也不是问题。

#### 8.简化支付验证

即使不允许网络节点，验证支付也是可能的。用户仅需要保存最长工作量证明链的区块头的拷贝，他通过查询网络节点直到确信它有最长的链来获取区块头，并且可以得到默尔克分支，分支连接了交易和这个打了时间戳的区块。他本身不能验证交易，但是通过连接到链上的一个地方，他可以看到网络节点已经接受了它，后面的区块进一步确定网络接受了它。

![](/images/2022-03-03-blockChain-confirm.jpg)

因此，如果诚实节点控制着网络，验证就是可靠的，如果网络被攻击者控制，验证就是很弱的。虽然网络节点本身可以验证交易，但这种简化验证的方法会被攻击者编造的交易欺骗，因为攻击者可能持续控制网络。防止这种情况的一种策略是接收网络节点的告警，当他们检测到无效块的时候，提示用户软件下载整个区块，并且提醒确认交易的一致性。频繁接收支付的企业可能仍然想运行他们自己的节点，为了更独立的安全性和更快的验证。

#### 9.组合和分割价值

虽然可以单独处理硬币，为转账的每一分单独交易是很不方便的。为了能是价值分割和组合，交易包含多个输入和输出。正常的会有一个单独的以前交易来的大额输入或多个小额输入组合在一起，最多两个输出：一个用来支付，一个用来找零，有零钱的话会返回给发送者。

![](/images/2022-03-03-blockChain-combine.jpg)

应该注意的是，一个交易依赖几个交易，这些交易依赖更多的交易，看起来很分散，但在这里不是一个问题，从不需要提取一个交易全部历史的独立的拷贝

#### 10.隐私

传统的银行通过限制向有关方和信任的第三方提供信息来达到一个保护隐私的目的。向公众广播所有交易的必须性将这个方法排除了。通过打破信息在其他地方流动性仍然可以保护隐私：通过保持公钥匿名性。公众可以看到一个人给其他人转钱了，但是没有信息可以把交易和某人联系起来。这类似于证券交易所公布的信息水平，交易时间和个人交易的规模是可以公开的，但不告诉当事人是谁。

![](/images/2022-03-03-blockChain-personal.jpg)

作为一个附加的防火墙，每次交易都是使用一个新的密钥对，防止和一个共同的所有者联系起来。对于多输入交易来说，这个联系无法避免，所有的输入必须表明由同一个人所有。风险是如果表明了某一个秘钥的所有者，这种联系将表明其他交易也属于同一个人。

#### 11.计算

我们想象一个场景，攻击者想要攻击用比诚实节点更快的速度产生一个替代链，即使成功了，也不会让系统能任意被修改，比如凭空产生价值或拿到不属于攻击者的钱。节点将不会接受一个无效的支付，并且诚实节点绝不会接受包含这种支付的区块。攻击者只能试着改变自己的交易来拿回本该花出去的钱。

诚实的链和攻击链的竞争可以说是二项式随机走动。成功事件是诚实链延长一个区块，领先优势加一，失败事件是攻击链延长一个区块，缩小一个差距。

一个攻击者从一个给定的赤字中追上的概率类似于赌徒破产问题。假设一个信用无限的赌徒从赤字开始，开始进行潜在次数无数的赌博，试图达到盈亏平衡。我们可以计算他达到盈亏平衡的概率，或者说从下次攻击链赶上诚实链的概率，如下：
p=诚实节点发现下一个区块的概率

q=攻击者找到下一个区块的概率

qz=攻击者在落后z个区块的情况下，追上的概率

![](/images/2022-03-03-blockChain-formula.jpg)

假设p>q,随着落后区块数量z增加，攻击者追上的概率呈指数下降。这个概率情况对攻击者不利，如果他没有幸运的提前向前冲刺，落后越多希望越渺茫。

我们现在考虑接收者在收到新的交易的时候，需要等待多长时间才能完全确定交易不能被发送者修改。我们假设发送者是攻击者，他想让接收者暂时相信他已经付款了，然后过了一段时间又换成是支付给自己。这事发生的时候接收者会受到警告，但是发送者希望一起都晚了。

接收者创建一个新的密钥对，签名之前很短的时间把公钥给发送者。这防止发送者提前准备一条链，持续在上面工作，直到他足够幸运达到了领先的程度，正好执行到这条交易。一旦这个交易发送了，不诚实的发送者开始在一个并行的链上秘密工作，这条链包含他的交易的另一个版本。

接收者一直等到知道交易被添加到一个区块中，并且后面已经追加了z个区块了。他并不知道攻击者的准确进展，但是可以假设诚实区块每个区块花费的时间是平均期望时间，攻击者潜在的进展将服从泊松分布，期望值：

![](/images/2022-03-03-blockChain-formula2.jpg)

为了得到目前的攻击者仍能追上的概率，我们将他所取得的每一步进展的泊松密度乘以他可能从那一点追上的概率。

![](/images/2022-03-03-blockChain-formula3.jpg)

变换一下避免对分布的无穷尾部求和...

![](/images/2022-03-03-blockChain-formula4.jpg)

转换为c代码

```c
#include <math.h>
double AttackerSuccessProbability(double q, int z)
{
    double p = 1.0 - q;
    double lambda = z * (q / p);
    double sum = 1.0;
    int i, k;
    for (k = 0; k <= z; k++)
    {
        double poisson = exp(-lambda);
        for (i = 1; i <= k; i++)
        poisson *= lambda / i;
        sum -= poisson * (1 - pow(q / p, z - k));
    }
    return sum;
}
```

运行结果， 可以看到概率随着z的增加呈指数下降

```shell
q=0.1
z=0 P=1.0000000
z=1 P=0.2045873
z=2 P=0.0509779
z=3 P=0.0131722
z=4 P=0.0034552
z=5 P=0.0009137
z=6 P=0.0002428
z=7 P=0.0000647
z=8 P=0.0000173
z=9 P=0.0000046
z=10 P=0.0000012
q=0.3
z=0 P=1.0000000
z=5 P=0.1773523
z=10 P=0.0416605
z=15 P=0.0101008
z=20 P=0.0024804
z=25 P=0.0006132
z=30 P=0.0001522
z=35 P=0.0000379
z=40 P=0.0000095
z=45 P=0.0000024
z=50 P=0.0000006
对于p<0.1%的求解…
p < 0.001
q=0.10 z=5
q=0.15 z=8
q=0.20 z=11
q=0.25 z=15
q=0.30 z=24
q=0.35 z=41
q=0.40 z=89
q=0.45 z=340
```

#### 12.结论

我们为无信任电子交易提出了一个系统，我们从数字签名币的常用框架开始，它对所有者有很强的控制，但是因为不能避免双花，所以还不完整。为了解决双花，我们提出了一个点对点网络，这个网络使用工作量证明记录一个公共的交易历史，只要诚实节点控制大部分的CPU算力，很快使得攻击者无法通过计算来改变交易历史。该网络的非结构化简单性使得它很稳健。节点同时工作，很少需要相互协调。他们不需要被识别，因为消息不需要路由到任何特定的位置，只需尽力传递就好。节点可以离开网络，也可以需要的时候重新加入网络，接受工作量链作为他离开的时候发生了什么的证据。他们用CPU算力投票，通过在有效的区块上工作并延续它来表达对区块的接受，通过不在新区块上工作表示拒绝无效区块。任何需要的规则和激励都可以在这种共识机制下进行。

基于论文的比特币网络于2009年初正式上线，其主要目的是存储该电子现金系统里所有账户间的转账交易，并且确保这些交易记录无法被篡改。

### 以太坊

比特币网络的功能仅限于让用户进行财务交易Finanicial Transaction，之后没有多久，很多就意识到可以用比特币创意中的区块链技术来进行金钱之外的价值交换，Vatalik Buterin 于 2013 年末发布了以太坊的白皮书：[《Ethereum: The Ultimate Smart Contract and Decentralized Application Platform》](http://web.archive.org/web/20131228111141/http://vbuterin.com/ethereum.html)

相比比特币网络的简洁，V提出了基于区块链技术创建更加复杂的去中心化应用Decentralized Application，简称为DApp的技术设计，典型的去中心化例子：

- 基于以太坊创建新的加密货币 CroptoCurrency,这种能力是2017年各种ICO泛滥的技术动因。
- 基于以太坊创建域名注册系统、博彩系统
- 基于以太坊开发去中心化游戏，2017的以太猫等

这些功能的实现都依赖于以太坊内建的智能合约Smart Contract，智能合约是存储在以太坊网络上的可以通过发送消息与之交互的代码片段。

基于以太坊智能合约可以方便的发行遵循ERC20规范的数字货币，这个过程常常被称为首次代币发行Initial Coin Offering，简称ICO,这给区块链企业融资提供了极大的便利，但由于成本极低被很多人滥用了，因为ICO过程是不受监管、且完全中心化的，项目方拿到募集来的以太坊随便花，而投资者拿到代币之后对项目运作和资金开销没有任何约束力。

2017年底V提出了 [DAICO](https://medium.com/@bonpay/daico-review-vitalik-buterins-new-model-of-ico-2091d5b1f873) 的理念，即筹集的资金会被智能合约锁定在账户里面，区块链创业团队在资金支出时先提出请求，多数投资者投票同意后，募集来的资金才可以被花出去，这在很大程度上保障了投资者的利益，也是在鼓励创业团队踏实做事。实战小册灵感源于此，不算是严格意义上的ICO App，因此不涉及发币过程，但现有众筹版本。

### 区块链 vs WEB

如果回顾区块链的发展历史，业内通常把区块链技术的发展分成3个阶段，作为前端工程师，我们可以将其和WEB技术的发展历史做如下类比：

- 区块链1.0，代表是[比特币](https://github.com/bitcoin/bitcoin)，只支持转账，类似于刚刚诞生的万维网，网页只支持静态内容的展示、链接等；
- 区块链2.0，代表是[以太坊](https://www.ethereum.org)，能够在转账的基础上支持一定复杂度的业务逻辑定制即智能合约，类似于有了JS的万维网，即在静态展示的基础上自定义动态内容，但是浏览器的JS执行效率还是比较弱，不能在浏览器做非常复杂的事情；
- 区块链3.0，代表是各种高性能底层公链，比如[EOS](https://eos.io),是目前整个社区努力的方向，但开发生态的成熟还需要时间，能够支持高并发商业应用的运行，类似于为能各种复杂的端应用提供运行环境的现代浏览器，比如Chrome;



## 区块链中的核心概念和原理

### 账户、交易、区块、区块链

如果说比特币是最早最成功的区块链应用，即基于区块链技术实现的银行系统，负责记账和发行货币，那么银行系统中存在的概念在区块链系统中也会存在，可以用银行账本中的概念来类比理解区块链的概念：

- 账户Account：是用户在银行的户头+密码的组合，在区块链世界中也是如此，无论是比特币还是以太坊的账户都是由地址、公钥、私钥3部分构成，其中地址相当于用户名，而公钥+私钥相当于密码，尤其是私钥，丢失或者泄露就意味着是去账户（敏感信息、资金）的控制权
- 交易Transaction：是账本中的任意一条收支记录，在区块链世界中可以指两个账户之间的转账交易、或者智能合约调用请求；
- 区块Block：是账本中的一页，账本的每页可能包含多笔收入和支出，同样，区块链中的每个区块都可能包含多笔交易
- 区块链Blockchain：是装订成册的多页账本，账本不同页按照记录时间先后顺序组织，区块链中不同区块按被矿工打包的时间先后组织

区块链技术通常被简单描述为：公开的、分布式、不可篡改的数据库技术或记账技术

### 块高度、出块时间

长时间记录的账本都会是多页的，在区块链世界也是如此，长时间运行的区块链网络记录下来的数据量也是巨大的。

块高度可以理解为账本的页数，在区块链世界里，块高度可以理解为自该区块链开始运行到现在共产生了多少区块，换个角度，我们可以认为块高度就是区块链时间的时间，每产生一个新的区块，快高度就会加1，[比特币最新块高度](https://blockexplorer.com/)，[以太坊最新快高度](https://etherscan.io/blocks)

出块时间可以类比为记完每页账户并在上面按完手印需要花多少时间，在区块链世界里，即区块链上相邻两个块产生出来的时间间隔，或者常说的交易确认时间Transaction Confirm time，这里说的交易确认时间指某笔交易从发起到被打包进区块链的时间，和部分钱包、交易所的交易确认时间小很多，比如比特币的区块时间通常控制在10分钟，而以太坊则是15秒左右。任何打包的交易在分布式网络上达成共识都需要时间，共识算法决定了时间的长短。[POW](https://mp.weixin.qq.com/s?__biz=MzU1NjYyNzE0MA==&mid=2247484622&idx=1&sn=34988179926bcf732d2e12ca1cb7fec9&source=41#wechat_redirect)、[POS](https://mp.weixin.qq.com/s?__biz=MzU1NjYyNzE0MA==&mid=2247484616&idx=1&sn=96bcc002720edfb705752269638c7e64&source=41#wechat_redirect)、[DPOS](https://mp.weixin.qq.com/s?__biz=MzU1NjYyNzE0MA==&mid=2247484615&idx=1&sn=7794501159e1da70117ed1dfd2fb88d9&source=41#wechat_redirect)

### 本章图解

![](/images/2022-03-03-blockChain-blockchain.jpg)

## 以太坊核心概念和原理

### 以太坊网络

分两个视角来看：

#### 整体视角

以太坊网络本质是P2P网络系统，其用途是发起交易、存储交易历史，这里的交易可以是转账或者是调用智能合约中的方法，而以太坊区块链是存储了以太坊网络上发生过的每笔交易的数据库。以太坊网络通常情况下是指主网，实际上社区中存在很多用途各异的以太坊网络，类比到Web工程里面3套环境，可以将其归类如下：

- 主网：Mainnet,就是以太坊的线上环境，记录、保存用户和智能合约的交易，主网中存储的代币才具有真正的价值；
- 测试网：Testnet，就是以太坊的测试环境，目的是方便社区和开发者测试智能合约、转账等功能，典型的测试网络有Rinkeby/Ropsten/Kovan等，其中代币不具有任何价值，[完整列表](https://testnet.etherscan.io/)
- 其他网：就是以太坊的开发环境，常通过开发者在本地运行以太坊节点组成，或者使用各种便捷的工具启动的本地测试网，以及以内部测试为目的而搭建的私有网络等，随着以太坊区块链数据的累计，运行全节点的时间和硬件的成本也越来越大，实战会用更加轻量的开发环境搭建方式

#### 个体视角

P2P网络通常包含多个节点，每个节点都需要运行以太坊客户端，而任何人都可以运行以太坊节点，每个以太坊网络上的节点都包含了以太坊区块链数据库的整体副本，每个以太坊网络节点都可以接收RPC交易请求并将请求广播给网络中的其他节点，每个以太坊节点都会尝试进行交易的校验、打包（常说的挖坑），即区块生产的任务，生产出的区块也会被广播给其他网络节点。

> 以太坊不同网络之间的账户可以完全相同，就好比我们可以把线上数据库的数据全部同步到我们测试环境数据库一样，但是不同测试网络之间、测试网络和主网之间、本地开发网络和主网之间是完全隔离的，即无法进行转账和智能合约功能调用



### 如何进行以太坊网络交互

交互的具体定义是：向以太坊网络发送转账请求，或调用智能合约函数，用我们熟悉的计算术语来说，就是发起读取或者修改以太坊区块链上数据的请求，并拿到反馈的过程。

类似于现有世界中支持开发者自行开发应用的平台，比如微博开发平台、微信小程序等，我们可以把与平台交互的用户分成两大类：开发者、普通用户，两者与平台本身的交互方式、交互途径不同。

比如微信小程序生态下，开发者。普通用户、平台之间的交互方式可以简化如下：

![](/images/2022-03-03-blockChain-miniwechat.jpg)

类似的，以太坊生态中，开发者、普通用户、平台的交互方式可简化为：

![](/images/2022-03-03-blockChain-etch.jpg)

其中，[web3.js](https://github.com/ethereum/web3.js)是前端工程师通过代码和以太坊网络交互的桥梁，而实际的DApp测试离不开使用钱包。

> 不论是开发者，还是普通用户，与以太坊网络的交互都会落地到具体的网络节点上，因为只有节点可以接收RPC请求并将其广播给其他节点，任何代码或者应用如果需要同以太坊网络交互，都需要通过某个具体的节点。

### 智能合约

智能合约指以太坊网络上被代码控制的一个账户，不同于我们使用各种钱包软件创建的账户（由创建账户的用户来控制），智能合约对应的账户是由代码控制的，其他账户（包括智能合约账户、普通用户账户）可以通过交易Transcation的方式与智能合约账户交互，社区中也会把智能合约账户称为内部账户，而普通用户称为外部账户。这两种账户的关系可以用下图描述：

![](/images/2022-03-03-blockChain-relation.jpg)

此外还需要注意的是，我们也常常用智能合约指代智能合约源代码或部署在以太坊网络上的智能合约账户，但本质上两者是有明显的区别的：如果把智能合约源代码比如设计图，那么智能合约账户就是根据蓝图造出来的车或者建筑。相同的源代码可以部署到不同的网络上，或者在相同的网络上部署很多次，都会产生不同的合约地址。整个过程可以用下图示意：

![](/images/2022-03-03-blockChain-deploy.jpg)

无论部署到哪个网络，源代码是完全相同的，而部署所产生的智能合约账户只存在于其被部署到的那个网络，比如部署到Mainnet主网的智能合约不能通过Rinkeby测试网络去访问，反过来亦然，普通账户则在不同网络间是通用的。

每个智能合约会有下面几个关键属性：

- balance,即该智能合约账户所控制的资产余额，比如某个抽奖智能合约中奖池的资金；
- storage,智能合约的相关数据会存储在这里，可粗暴的将其看做是DApp的数据库，比如抽奖智能合约里面存储参与人的地址；
- code,智能合约的字节码，由智能合约源代码编译儿而来的，存储在区块链上方方便任何节点接受智能合约的函数调用

## 使用Metamask创建第一个以太坊HD钱包

接触任何区块链网络都需要我们有自己的账户，管理账户的软件可称之为钱包，在创建钱包和账户之前，我们有必要了解以太坊网络中账户的组成：

![](/images/2022-03-03-blockChain-account.jpg)

如上图，以太坊网络中的账户和典型的区块链账户没有太大区别，都由地址、公钥、私钥3部分构成，不论使用何种钱包构建的以太坊账户，在不同的以太网网络之间都是可以通用的，比如我在主网构建了钱包账户，而切换到Rinkeby测试网络时依然可以使用同样的账户，这和传统的Web应用有很大的区别，比如我们在微信创建的账户就不能到百度上。这种跨网络的账户机制实际上是内置在以太坊客户端之内的，无需关心细节。

不论是在以太坊网络上发起转账交易，还是部署智能合约，亦或是调用智能合约中的函数，我们都需要账户，方便以太坊记录和验证谁、在什么时间、做了什么。

区块链世界里面的钱包其实借鉴自现实世界的钱包，区块链世界里面，每张银行卡对应一个账户，每家银行对应一个区块链网络，而能管理你所有银行卡的软件叫做钱包，对应关系如下:

![](/images/2022-03-03-blockChain-wallet.jpg)

### 钱包运行环境

Metamask是浏览器插件，虽然能运行在多个浏览器，但考虑到Solidity调试工具运行环境、前端调试工具的丰富程度建议在Google浏览器进行

### 创建钱包和账户

[Metamask](https://metamask.io/) 为我们提供了非常便捷的以太坊浏览器钱包插件，Google Chrome WebStore 的下载地址猛击[这里](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn/related?hl=en)

助记词是用来生成账户的公钥和私钥的，也就是用来恢复钱包里面所有的账户的。如果创建的是存在真实资产的账户，不要泄露助记词。

从密码学角度，地址、公钥、私钥本质都是非常大的数字，不论是转换为16进制或者base58格式，对普通用户来说看起来都是杂乱无章的随机字符串，如果区块链用户非要记住这些数字才能使用区块链的话，会非常麻烦。于是比特币社区提出了[BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)提议，技术上该提议可以在任意区块链中实现，比如使用完全相同的助记词在比特币和区块链上生成的地址可以是不同的，用户只需要记住满足一定规则的词组，钱包软件就可以基于该词组创建一些列的账户，并且保障无论是在什么硬件、什么时间创建出来的账户、公钥、私钥都完全相同，这样解决了账户识住的问题，也吧账户恢复的门槛降低了很多。支持BIP提议的钱包也可以归并为HD钱包，Hierarchical Deterministic Wallet,Metamask当属此类。

## 深入理解以太坊中的交易Transaction

从测试网络的充值不会马上成功，而是有15秒左右的延迟，正确理解区块链网络的交易Transaction是做区块链开发必要条件，正确理解以太坊网络上的交易是设计、开发出合理智能合约+DApp的基础条件。

交易详情中的几组关键信息：

- Transaction Hash：交易流水号，就像是去银行转账给你业务回折的那个流水号
- Block：说明这笔交易被打包进了编号（快高）为xxx的区块， xxx Block Confirmations 表示这笔交易被打包以后，以太坊的Rinkey 测试网有产生了149个区块
- Form/To/Value：交易的发起账户、接收账户、以及转账金额，如果是智能合约调用也会有这三个字段，但是会有说明调用了智能合约什么函数
- Gas Price：交易手续费，Gas是交易手续单位，Ether,Gwei是以太坊中代币的单位，
  - Limit表示在交易执行时最多消耗的汽油数量
  - Used 表示实际使用了多少汽油

以太坊交易中的汽油和现实中的汽油有异曲同工之妙，如果把以太坊交易比作开车去长途旅行，不难得出结论：

- 长途旅游必然消耗汽油，而旅行消耗汽油的单位为升，在以太坊里面汽油的单位就叫做Gas
- 加油询问油价，以太坊中汽油的价格是用自己的货币比好标识的，可以为0.0000000039 Ether/Gas，也可以说 3.9 Gwei/Gas，其中的 Ether 和 Gwei 是以太坊中的货币单位
- 现实中货币单位精度为2为小数，即精确到分，区块链世界中的货币单位精度可以多达18小数
- 现实世界中如果中途没有油了，可以停在原地请求支援，以太坊中Gas不够，交易就会被直接回滚
- 如果现实里面的汽车行驶不需要消耗任何能源，即不支付任何经济成本，就很容易堵车。如果把以太坊比作高速公路，而各种交易比作行驶的汽车，设计汽油机制以及可以调控的汽油价格就是很好的网络拥塞避免措施，如果想降低汽油车出行，发改委可以把油价提的很高

这里说的Ether就是在各种数字货币交易市场上的以太坊的交易单位，即ETH,以太坊的货币单位精度很高，所以不同精度货币单位的列表也很长，不同单位之间的换算可以参考：[etherconverter.online](https://etherconverter.online/)

### Gas LImit 和 Gas Price 详解

### Gas机制

为什么以太坊选择使用Gas去调节手续费，而没有选择像比特币那样使用比特币本身作为交易手续费

Gas是以太坊虚拟机EVM内部流通的货币，以太坊虚拟机用Gas来对交易打包、智能合约执行等操作收取费用。

调用某智能合约的接口需要执行如下操作，合约源代码编译出来的opcode就对应EVM中的操作，每个操作会产生特定的费用：

- 执行加法运算：1Gas
- 存储某个变量的值：100Gas
- 调用其他智能合约：20Gas

上会举例，完整的操作、手续费表在[这里](https://github.com/djrtwo/evm-opcode-gas-costs/blob/master/opcode-gas-costs_EIP-150_revision-1e18248_2017-04-12.csv)

在这种机制下，以太坊就可以对更加复杂的智能合约收取更多的费用，这样做很公平合理，因为需要更多存储和计算的智能合约消耗的资源更多，跟比特币不同的地方在于，比特币本身不支持在交易中包含复杂的计算逻辑，比特币是不支持智能合约的。

向以太坊提交一笔交易时，我们需要在交易中设定下面两个参数，虽然大多数时候只需要设定Gas Limit：

- Gas Price：指定我们愿意为每单位Gas支付的最高价格，Gas Price的单位才是以太坊中的单位，比如Wei,GWei
- Gas Limit：指定我们最多愿意为执行该笔交易支付多少个 Gas

### 家政服务公司的类比

假设有家家政服务公司，主营业务是个客户提供清洁服务，运行让雇员在开展业务的收入随着市场供需的变化而变化，为了实现这种灵活性，家政服务公司发行了一种代币叫做清洁币QJB,客户在支付清洁费用时不实际支付人民币，而是支付代币，而清洁不同类型的房间收取不同的QJB，数量由家政服务公司设定，并且不允许修改：

- 主卧，1QJB
- 客户，3
- 厨房，15
- 卫生间，10

客户只需要支付家政公司QJB，而雇员收入的是QJB,QJB可以随时换成发币

这种机制下，家政公司的雇员实际上就客户愿意为每个QJB支付多少钱进行谈判，选择对自己有利的需求去满足，比如雇员希望每个QJB能换算为100人民币，而客户却只愿意支付80元，这时雇员就可以选择放弃为该客户服务，而且服务愿意支付更高价格的客户，自由度和灵活性就这样产生了。

以太坊中的Gas Price有异曲同工之妙，雇员扮演矿工的角色，客户的清洁需求好比提交到网络中的转账交易，如果交易的Gas Price设置过低，矿工可以选择不理会这笔交易，只打包Gas Price满足自己设定的最低值交易。实际上以太坊网络上的矿工也是这么做的，他们在启动节点时会设置能接受的最小Gas Price，低于设定值的交易都会被过滤掉，不同的矿工对于把这个值设为多少是有自由的。

Gas Limit在家政服务中是这样的：

假如你租了个别墅，里面房间特别多，生日那天开了一个Party，结束后需要有人来清理，但是因为办完派对，能支付清洁费用的资金有限，这时会跟说家政公司雇员说，我最多愿意为整个清洁支付50QJB,每个QJB价值是100元，如果费用不够打扫整个别墅，扫到哪里是哪里吧，剩下的我自己搞定。家政公司雇员开始工作后会多退少就停止。

这就好比执行复杂智能合约时，填写了如下的参数：

- Gas Price：100GWei/Gas
- Gas Limit：50Gas

EVM在执行你的交易时如果实际消耗的费用在50个单位以内，交易就会成功被执行和打包，如果不够，消耗到50个单位汽油时就会停止并回滚交易，这点和家政服务不同，清洁工作停止的时候，已经打扫过的房间会保持干净的状态，但是在EVM里面，不存在执行一半的交易，要么成功，要么回滚，即使Gas不够，不成功的执行也是消耗了计算资源。

Gas是EVM对各种操作收取费用的机制，如果你需要发起交易，Gas Price是你实际报给矿工的Gas单价，而Gas Limit表示你最多愿意为这笔交易支出多少个单位的Gas。

EVM执行每笔交易时实际要消耗多个Gas计算过程相关文章[在此](https://hackernoon.com/ether-purchase-power-df40a38c5a2f)

### 几个重要的细节

#### 纯转账交易的手续费

以太坊纯转账交易的Gas消耗是21000个单位，这里的纯转账指转的是ETH,不包括各种ERC20的代币，因为代币本身是智能合约，转账的时候需要调用智能合约的接口。

#### Block Gas Limit

如果把Gas Limit设置过高，会直接抛出错误：Exceeds block gas limit，这是以太坊从经济角度的独特设计，如果大家的 Gas Limit都可以设置得无限大，那么最后需要回退的 Gas 就很多了。

#### 合理的Gas Price

etherscan.io 上有个实时变动的数据说明交易中合理的 Gas Price 应该设置为多少，猛击链接 [https://etherscan.io/gastracker) 查看，如果想查看历史变化趋势，有下面两个链接供参考：

- Gas Limit 均值变化趋势可参考：[https://etherscan.io/chart/gaslimit)。
- Gas Price 均值变化趋势可参考：[https://etherscan.io/chart/gasprice)。

## 智能合约编程语言 Solidity介绍以及开发入门

### 什么是Solidity?

[Solidity](https://docs.soliditylang.org/en/v0.8.12/)官方文档如是说：

>Solidity 是一种面向对象的高级语言，用于实现智能合约。智能合约是管理以太坊状态内账户行为的程序。
>
>Solidity 是一种[花括号语言](https://en.wikipedia.org/wiki/List_of_programming_languages_by_type#Curly-bracket_languages)。它受 C++、Python 和 JavaScript 的影响，旨在针对以太坊虚拟机 (EVM)。[您可以在语言影响](https://docs.soliditylang.org/en/v0.8.12/language-influences.html)部分找到有关 Solidity 受到哪些语言启发的更多详细信息。
>
>Solidity 是静态类型的，支持继承、库和复杂的用户定义类型等特性。
>
>使用 Solidity，您可以创建用于投票、众筹、盲拍和多重签名钱包等用途的合约。

Solidity属于强类型语言，内涵的类型除了常见的编程语言中的标准类型，还包括address等以太坊独有的类型，Solidity源码文件通常以.sol作为扩展名，

Solidity源代码要成为可以运行在以太坊的智能合约需要经历如下的步骤：

- 用Solidity编写的智能合约源代码徐还要先使用编译器编译为字节码Bytecode,编译过程中会同时产生智能合约的二进制接口规范 Application Binary Interface,简称为ABI
- 通过简易Transaction的方式将字节码部署到以太坊网络，每次成功部署都会产生一个新的智能合约账户；

而使用JavaScript编写的DApp通常通过web3.js+ABI去调用智能合约中的函数来实现数据的读取和修改，整个过程可以用下图示意：

![](/images/2022-03-03-blockChain-program.jpg)

Bytecode和ABI可以认为是智能合约源代码的两种外在表现形式，其中Bytecode是给机器执行的，而ABI是给DApp开发者用的自然语言描述的合约接口规范。

从整体上来看，开发智能合约+DApp 不需要我们精通Solidity,但需要理解他和我们所熟知的语言的最大区别，就可以顺利开发出正确的智能合约。

### 怎么开发Solidity?

Remix是以太坊社区开发出来的在线智能合约集成开发环境，包含开发、部署、调试支持，官方还提供了桌面版，但桌面版仍然需要网络才可以正确运行

在前端开发环境中构建自己的智能合约的工作流，则需要组合使用现有的工具实现智能合约的编写、编译、部署、测试等环节。

## 使用Solidity开发实现智能合约Hello World

使用Remix开发和调试第一个智能合约，浏览器打开https://remix.ethereum.org

```solidity
pragma solidity ^0.4.17;

contract Car {
    string public brand;

    constructor(string  initialBrand) public {
        brand = initialBrand;
    }

    function setBrand(string newBrand) public {
        brand = newBrand;
    }
    
    function getBrand() public view returns (string){
        return brand;
    }
}
```

Solidity遇到public变量时，会认为其实需要持久化存储在以太坊区块链上的合约数据，即不仅仅是public variable,还是storage variable:

- public variable 中的 public 和传统面向对象语言中的 public 作用完全相同，运行运行时任何代码读取它
- storage variable 中的 public 暗示着 Solidity 编译器智能合约生成 ABI 的时候自动为合约生成同名的 getter函数
- Car 合约中除了 brand 变量，其他变量比如 initialBrand/newBrand 都属于临时变量、本地变量，声明周期仅限于合约函数被调用时

Car合约中没有任何复杂的逻辑控制结构比如条件、循环，典型的函数声明格式如下：

```shell
function getBrand() public view returns (string){
	return brand;
}
# getBrand 函数名称
# public view 函数类型
# returns (string) 返回类型
```

其中典型的函数类型有以下几种：

- public,任何人都可以调用该函数，包括智能合约内部，或者外部账户、其他合约账户的调用
- private，只有合约内部可以调用的函数
- view，能够返回数据，并且不修改合约数据存储状态的函数
- constant,和view的含义完全相同，不修改合约数据的函数
- pure,纯粹的计算函数，和编程语言里面的 Pure Function含义完全相同，具体到智能合约领域，就是既不会读取，也不会修改合约数据
- payable,标记付款类函数，调用该函数的用户在完成交易时会支付实际的资金

合约部署后，最下面能看到新的合约账户，账户下面有按钮，每个按钮代表可以调用合约函数，如getBrand，如果按钮旁边有输入框，表示调用该函数的时候需要输入参数，如setBrand。我们直接点击brand或者getBrand按钮，会看到按钮右边立即显示了部署合约时设定的参数，点击了setBrand，没有输入内容则置空。这也是Remix的一个坑：不做参数校验。

我们合约没有声明brand方法，但部署后的合约里面有，这就是Solidity为公共访问的存储变量生成的getter方法，实际上源码的getBrand方法可以直接删除

## 部署智能合约时发生了什么？

合约部署Create和合约实例Contract Instance有几个问题需要理解：

- 智能合约部署的时候发生了什么，发送了什么？
- 部署合约用的是哪个账户，消耗的汽油哪里来的？
- 合约账户的形式是怎么样的，和普通用户的区别?
- 新创建合约实例3个方法对应的按钮，为什么set是红色，get和brand是不同颜色，有无区别，对后续DApp有什么影响？
- Metamask账户重置的时候以太坊单笔交易确认时间平均要15s左右，为什么Remix测试的时候那么快？
- 如果合约源代码修改了，能否直接在旧的合约实例上测试，如果不能是为什么，要测试新合约需要怎么做？
- 合约编译的时候虽然没有报红色的错误，但是报了Waning是什么？

### 合约部署的本质

合约部署实际上是发起了一笔交易，交易的接受者为空，以太坊将接受者为空的交易默认认为是合约创建请求，这类交易中会修改当前部署合约的机器码ByteCode，部署成功的话会返回新建的合约账户，合约部署本身需要消耗Gas，这个费用是发起者支付的。合约部署的交易细节在Remix上通过调试日志可以看到，打开Remix调试日志区域，通过点击合约创建右上角的Details按钮可以看到

```shell

[vm]from: 0x5B3...eddC4to: Car.(constructor)value: 0 weidata: 0x608...00000logs: 0hash: 0x975...9483b
status	true Transaction mined and execution succeed
transaction hash	0x975cea2d7f3173162027050..2489e7e419483b
from	0x5B38Da...3FcB875f56beddC4
to	Car.(constructor)
gas	80000000 gas
transaction cost	263067 gas 
execution cost	263067 gas 
hash	0x975cea2d7f3...a52489e7e419483b
input	0x608...00000
decoded input	{
	"string initialBrand": "A"
}
decoded output	 - 
logs	[]
val	0 wei

```

input是智能合约字节码bytecode 跟在car.json 里面的 data bytecode object一样

### 汽油哪里来的？

部署交易中的form字段跟JavaScript VM下出现的Account列表中的第一个账户完全相同，部署完之后选中的账户余额互少一点，少掉的这部分就是使用该账户部署智能合约时消耗的汽油

### 合约账户的本质

合约部署的直观结果是我们得到一个账户，但是我们仅仅有这个账户的地址，而没有账户的公钥、私钥、这就是合约的定义，contract instance is an account controlled by code 的准确含义，后续和合约的交互也只能通过代码，即智能合约的函数调用。

### Remix带来的幻觉

因为在测试时以太坊网络运行在内存中，并且是单节点，速度自然很快，但是缺陷是每次页面加载，这个测试网络中的合约实例、合约中的数据都会丢失，网络中的账户及余额也会重建

然后实际的以太坊网络，不论是测试网络还是主网，都不会那么快，在分布式网络上达成共识是个很复杂的过程，由于是对于使用POW共识算法的以太坊来说

### 如何重新发布？

如果智能合约的源代码被修改，同样需要部署智能合约的示例，并测试这个全新的合约实例。与前端页面开发不同是，浏览器刷新后，老的页面样式、DOM接口、应用状态都被销毁了，而以太坊老的合约实例是不会被销毁 的，即使部署了新的版本，如果愿意也可以和老版本继续交互。

### Solidity其实和JS区别很大

Car合约自动生成的brand函数可能会消耗无限的汽油，这是在智能合约设计时需要避免的，因为智能合约中的任何代码都是需要消耗汽油的，如果不加限制，很容易出现汽油不够而操作被回滚、调用失败的情况。具体到例子：brand属性为字符串，在静态类型语言中字符串本质是动态长度的字节数组dynamically-sized byte array，也就是说我们的brand属性可能是非常长的字符数组，如果长度超过某个临界值，就会消耗超出预期的汽油，只需要把brand类型从string设置byte32即可：

```solidity
pragma solidity ^0.4.17;

contract Car {
    bytes32 public brand;

    function Car(bytes32 initialBrand) public {
        brand = initialBrand;
    }

    function setBrand(bytes32 newBrand) public {
        brand = newBrand;
    }

    function getBrand() public view returns (bytes32) {
        return brand;
    }
}
```

## 调用智能合约时发生了什么？

setBrand被标记为红色，调用它实际发起的是transaction,任何交易都是异步的。getBrand被标记为蓝色，调用它就是简单的函数调用 call

智能合约中的函数要么是transaction类型，要么是call类型，两种类型函数的本质都是接口请求，但是又存在很大的区别，对比如下表：

| 函数类型           | call纯粹函数调用 | transaction通过交易调用 |
| ------------------ | ---------------- | ----------------------- |
| 是否能修改合约数据 | 不能修改         | 可以修改                |
| 是否能返回数据     | 能返回数据       | 只能返回交易地址 TxHash |
| 调用耗时长短       | 通常比较短       | 通常需要15s             |
| 是否需要花钱       | 免费调用         | 要花钱                  |

对于前端工程师，如果用同步、异步函数来类比则很好理解

- 合约中的纯粹函数调用相当于同步的，而通过交易去调用的函数相当于异步的
- 通常我们不指望异步函数马上返回结果，除非给他传入了回调，也就预示着智能合约中会修改合约数据的函数即使声明了返回值，也是无效，让人不解的是Rexmix不会因为这个报错，因为调用的时候只会返回交易哈希

## 自建智能合约工作流的动机和目标

### Remix的局限性

- 源代码没有版本控制，不存在编写一次就不用修改的代码，没有版本控制
- 智能合约只能在浏览器中运行，不能方便把合约部署到以太坊的主网或者测试网络
- 智能合约的测试都是手动的，手动的过程是不可靠的，如果有自动化的智能合约测试是最好的

### Truffle的困扰

[Truffle](https://trufflesuite.com/)s对自己的定位是以太坊开发的瑞士军刀，就好比前端领域的各种全家桶种子项目create-app,vue-cli等，帮你把各种关键模块react/webpack/vue/eslint组装好，只需要在上面开发应用逻辑即可，但对于刚入门区块链和以太坊的人来说，接触到的新概念、术语会比较多，不利于入门

### 工作流目标

在Node开发环境使用几个最少的必要工具实现自己的智能合约编译、部署、测试工作流，整个工作流过程可以用下图：

![](/images/2022-03-03-blockChain-node_work.jpg)

1. [solc](https://www.npmjs.com/package/solc)工具把Solidity源代码编译成 Bytecode 和 ABI
2. 把编译后的Bytecode 部署到本地测试网络[ganache-cli](https://www.npmjs.com/package/ganache-cli)，公共测试网络[rinkeby](https://www.npmjs.com/package/rinkeby)，使用到[web3.js](https://www.npmjs.com/package/web3)
3. 单元测试中和部署完的智能合约实例交互，需要组合使用web3.js和[mochajs](https://www.npmjs.com/package/mocha)

 其中ganache-cli是Truffle框架的一部分，能够让开发者快速启动本地测试网络，不需要在本地运行以太坊节点，web3.js在合约智能部署和自动化测试时会被大量使用，这也为后面开发DApp打下良好的基础。

做完上面的事情后，重新Remix，看看我们如何在Remix中加载和测试使用脚本部署的智能合约实例

## 编写智能合约编译脚本compile

scripts/compile.js

```js
const fs = require('fs');
const path = require('path');
const solc = require('solc');

const contractPath = path.resolve(__dirname, '../contracts', 'Car.sol');
const contractSource = fs.readFileSync(contractPath, 'utf8');

const result = solc.compile(contractSource, 1);
console.log(result);
```

运行Car.sol后得出下面的输出：

```shell
{
  contracts: {
    ':Car': {
      assembly: [Object],
      bytecode: '608060405234801...9d141bb3ee7074732b6b527fab31620b877c32f60029',
      functionHashes: [Object],
      gasEstimates: [Object],
      interface: '[{"constant":true,"inputs":[],"name":"brand","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newBrand","type":"string"}],"name":"setBrand","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"inputs":[{"name":"initialBrand","type":"string"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"}]',
      metadata: '{"compiler":{"version":"0.4.26+commit.4563c3fc"},"language":"Solidity","output":{"abi":[{"constant":true,"inputs":[],"name":"brand","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newBrand","type":"string"}],"name":"setBrand","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"inputs":[{"name":"initialBrand","type":"string"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"}],"devdoc":{"methods":{}},"userdoc":{"methods":{}}},"settings":{"compilationTarget":{"":"Car"},"evmVersion":"byzantium","libraries":{},"optimizer":{"enabled":true,"runs":200},"remappings":[]},"sources":{"":{"keccak256":"0x5cdc15332a2851cff3a4fb683f4779500af8a4e4c3f2456332d5643de8e9856b","urls":["bzzr://9e12529e71042b5ee545da27cab03c65921f2d4b69190f3e394589f32445913a"]}},"version":1}',
      opcodes: 'PUSH1 0x80 PUSH1 ...527FAB31620B877C32F6 STOP 0x29 ',
      runtimeBytecode: '608060405260043610...7074732b6b527fab31620b877c32f60029',        
      srcmap: '28:215:0:-;;;76:79;8:9:-...-;;;;;;;;;;;;;;;;;;;;:::o;:::-;;;;;;;',
      srcmapRuntime: '28:215:0:-;;;;;;...::i;:::-;;;:::o;:::-;;;;;;;;;;;;;;;;;;;;:::o'
    }
  },
  sourceList: [ '' ],
  sources: { '': { AST: [Object] } }
}
```

contracts属性包含了所有找到的合约，源代码中只有Car合约，每个合约下面都包括了assembly、bytecode、interface、metadata、opcodes等字段，目前阶段仅需要关心的字段有：

- bytecode，部署合约到以太坊测试网络需要使用
- interface,ABI,使用web3初始化智能合约交互实例的时候需要使用

interface

```json
[
	{
		"constant": true,
		"inputs": [],
		"name": "brand",
		"outputs": [ { "name": "", "type": "string" } ],
		"payable": false,
		"stateMutability": "view",
		"type": "function"
	},
	{
		"constant": false,
		"inputs": [ { "name": "newBrand", "type": "string" } ],
		"name": "setBrand",
		"outputs": [],
		"payable": false,
		"stateMutability": "nonpayable",
		"type": "function"
	},
	{
		"inputs": [ { "name": "initialBrand", "type": "string" } ],
		"payable": false,
		"stateMutability": "nonpayable",
		"type": "constructor"
	}
]

```

可以看到是内含三个元素的大数组：

- 每个元素要么是合约构造函数，要么是可以调用的合约接口
- 每个元素里面有注明函数的类型、接收的参数类型、返回值的类型

### 保存编译结果

使用 fs-extra,可以方便后续的部署和测试过程直接使用编译结果，需要把编译结果保存在文件系统中：

```js
const fs = require('fs-extra');
const path = require('path');
const solc = require('solc');

const contractPath = path.resolve(__dirname, '../contracts', 'Car.sol');
const contractSource = fs.readFileSync(contractPath, 'utf8');

const result = solc.compile(contractSource, 1);
console.log(result);

Object.keys(result.contracts).forEach(name => {
    const contractName = name.replace(/^:/, '');
    const filePath = path.resolve(__dirname, '../compiled', `${contractName}.json`);
    fs.outputJsonSync(filePath, result.contracts[name]);
    console.log(`save compiled contract ${contractName} to ${filePath}`);
})
```

然后重新编译脚本，确保complied目录下包含了新生成的Car.json

类似于前端构建流程中编译步骤，编译之前需要把之前的结果清空，然后把最新的编译结果保存下来，对编译脚本作如下改动：

```js
const fs = require('fs-extra');
const path = require('path');
const solc = require('solc');


// cleanup
const compiledDir = path.resolve(__dirname, '../compiled');
fs.removeSync(compiledDir);
fs.ensureDirSync(compiledDir);

// compile
const contractPath = path.resolve(__dirname, '../contracts', 'Car.sol');
const contractSource = fs.readFileSync(contractPath, 'utf8');
const result = solc.compile(contractSource, 1);

// check errors
if (Array.isArray(result.errors) && result.errors.length) {
    throw new Error(result.errors[0])
}

// save to disk
Object.keys(result.contracts).forEach(name => {
    const contractName = name.replace(/^:/, '');
    const filePath = path.resolve(__dirname, '../compiled', `${contractName}.json`);
    fs.outputJsonSync(filePath, result.contracts[name]);
    console.log(`save compiled contract ${contractName} to ${filePath}`);
})
```

## 编写智能合约部署脚本deploy

### web3.js简介

是把我们的前端世界和区块链世界连接起来的桥梁，web3js里面包含了多个以太坊生态中不同的模块，具体包含：

- web3-eth,方便js和以太坊区块链通信，部署、调用智能合约
- web3-utils,为DApp开发者提供了大量的工具函数，比如单位换算等，DApp开发时会使用里面的函数
- web3-shh,方便做基于whisper协议的P2P通信和广播
- web3-bzz,方便做基于swarm协议的去中心化文件存储

以太坊的主网、测试网络、本地私有网络非常多，以太坊的钱包应用也非常多，web3.js没有选择兼容所有的情况，而是自己定制了一个接口规范，让社区开发者为之贡献插件，插件在web3.js体系叫做provider，可以理解为webpack生态的plugin.

web3js通过插件机制和以太坊不通过网络通信的模式可以用下面的图示：

![](/images/2022-03-03-blockChain-bridge.jpg)

### 部署的必要条件

客户端和以太坊网络的任何交互都可以定性为接口调用和介意，智能部署属于后者，部署时除了必须要有bytecode。发起靠谱哦的必要条件也应算在内，具体来说包含如下几个方面：

#### 余额大于0的账户

以太坊的任何交易都需要账户发起，账户中必须要有足够多的余额来支付手续费Transcation Fee，我们之前创建并充值的Metamask账户可以派上用场

### 与目标网络的通信

区块链上的任何交易都会被发送到某个网络，并被这个网络中的节点打包确认，可以考虑把智能合约部署到Rinkeby网络中，这样我们的交易通过自己运行的节点广播给Rinkeby测试网络中的其他节点，就能被打包确认，但是实际上前端自己跑节点成本还是很高的，还在社区有人做了各种以太坊的入口节点，为广发开发者提供接口可以直接调用。

[Infura](https://infura.io/)就提供了这样的服务

### 合约部署脚本

scripts/deploy.js

```js
const fs = require('fs-extra');
const path = require('path');
const config = require('config');
const Web3 = require('web3');
const HDWalletProvider = require('truffle-hdwallet-provider');

// Get ByteCode
const contractPath = path.resolve(__dirname, '../compiled/Car.json');
const { interface, bytecode } = require(contractPath);

// Configuration Provider
const provider = new HDWalletProvider(
    config.get('hdwallet'),
    config.get('infuraUrl'),
)

// Initial Web3 Instance
const web3 = new Web3(provider);


(async () => {
    // Get Wallet Money
    const accounts = await web3.eth.getAccounts();
    console.log('部署合约的账户：', accounts[0])

    // Create Contract Instance And Deploy
    console.time('合约部署耗时')
    const result = await new web3.eth.Contract(JSON.parse(interface))
        .deploy({ data: bytecode, arguments: ['BINHONG'] })
        .send({
            from: accounts[0],
            gas:'1000000',
            gasPrice: web3.utils.toHex(web3.utils.toWei('10', 'gwei')),
            gasLimit: web3.utils.toHex(21000),
        })
    console.timeEnd('合约部署耗时');

    const contractAddress = result.options.address;
    console.log('合约部署成功', contractAddress);
    console.log('合约查看地址', `https://rinkeby.etherscan.io/address/${contractAddress}`);

    // Write Contract Address Into File System
    const addressFile = path.resolve(__dirname, '../address.json');
    fs.writeFileSync(addressFile, JSON.stringify(contractAddress));
    console.log('地址写入成功:', addressFile);

    process.exit();
})()

```

Configuration Provider 初始化了 Infura 提供的 HTTP 接口的测试网入口以及助记词钱包的provider，钱包助记词是 Metamask的钱包助记词

接着使用配置好的插件实例生成新的web3 实例

调用 web3.eth.getAccounts 方法解锁助记词钱包里面的第1个账户作为部署合约的账户，我们充值的也是Metamask钱包的第一个账户，而实际上这个助记词是可以派生出很多账户的，其他账户没有余额，也就无法使用

Create Contract Instance And Deploy 的代码也可以分开来写，链式的代码调用实际上做了合约初始化、交易初始化、交易发送3件事情：

```js
const contract = new web3.eth.Contract(JSON.parse(interface));
const transaction = contract.deploy({data:bytecode,arguments:['BINHONG']});
const result = await transaction.send({from:accounts[0],gas:100000})
```

## 使用etherscan 和 remix 查看和测试合约实例

智能合约实例是存储在以太坊网络上被代码控制的账户，部署完之后，可以通过echerscan/remix去访问它、和它交互

### 用etherscan查看合约

用什么网络就用xxtestnet.etherscan.io/tx/contractAddress,点击列表最左侧的TxHash字段，可以看到合约部署的交易细节：

![transation_detail](./public/images/transation_detail.jpg)

### 用Remix测试智能合约

可以使用Remix加载测试网络上特定的合约实例，并与之交互，下面是具体步骤

#### 配置合约源码

run transactions里面环境选择你的测试网络，编译原来的源码，At Address输入你的合约地址，然后进行调试，getbrand可以很快拿到值，但是setBrand却是处于pending状态，在账户那里会显示可能需要花费的汽油，确定后15s内返回结果，并消耗了汽油

```shell
status	true Transaction mined and execution succeed
transaction hash	0xa45...08e7eb67d003d936ad0d6591c83cf
from	0x95...14c
to	Car.setBrand(string) 0xc32eDBd9...C46
gas	29457 gas
transaction cost	29453 gas 
hash	0xa45e1443c2f61b3...cf
input	0xc1f...00000
decoded input	{
	"string newBrand": "Test"
}
decoded output	 - 
logs	[]
val	0 wei
```

再次点击brand，就是返回刚刚输入的"Test"了

## 使用mocha+web3.js+ganache编写合约测试

针对部署后的合约实例进行测试，而不是合约源代码，可以看为端到端测试

自动化测试可以跟测试网络上的合约实例交互，但是每次跑测试都需要花点几分钟时间，此外还要自己进行复杂的账户管理。

因为区块链网络交易确认的异步性，在工具选择时需要做些权衡，ganache-cli就像是Remix中运行在内容中的JavaScript VM测试网络，其方法调用，交易确认速度非常快，为开发者提供了成本极低的本地测试网络，很适合用来测试。

而ganache-cli为web3.js提供了兼容的provider，这样就可以通过web3.js把合约部署到ganache-cli提供的本地测试网络上，并且跟合约实例交互。

### 编写测试

测试时我们通常会把每次测试运行的环境隔离开，对应到智能合约测试，每次测试需要部署新的合约实例，然后针对新的实例做功能测试

Car合约的功能比较简答，设计如下2个测试用例：

- 合约部署时传入的brand属性被正确存储
- 调用setBrand之后合约的brand属性被正确更新

测试代码：

```js
const path = require('path');
const assert = require('assert');
const ganache = require('ganache-cli');
const Web3 = require('web3');

const contractPath = path.resolve(__dirname, '../compiled/Car.json');
const { interface, bytecode } = require(contractPath);
const web3 = new Web3(ganache.provider());

let accounts;
let contract;
const initialBrand = 'BinHong';

describe('contract', () => {
    beforeEach(() => {
        accounts = await web3.eth.getAccounts();
        console.log('合约部署账户：', accounts[0])

        contract = await new web3.eth.Contract(JSON.parse(interface))
            .deploy({ data: bytecode, argument: [initialBrand] })
            .send({ from: accounts[0] });

        console.log('合约部署成功：', contract.options.address)
    })

    it('deploy a contract', () => {
        assert.ok(contract.options.address);
    })

    it('has initial brand', async () => {
        const brand = await contract.methods.brand().call();
        assert.equal(brand, initialBrand)
    })

    it('can change the brand', async () => {
        const newBrand = 'LBH';
        await contract.methods.setBrand({ newBrand }).send({ form: accounts[0] })
        const brand = await contract.methods.brand().call();
        assert.equal(brand, newBrand);
    })
})
```

代码使用的断言库是nodejs内置的assert模块，ganache-cli的provider自己在内部管理了一些账户。web3.js智能合约实例交互的方法，在DApp开发时会大量使用：

- contract.methods.brand().call()，调用合约上的方法，通常是取数据，立即返回
- contract.methods.setBrand('xxx').send(),对合约发起交易，通常是修改数据，返回是交易Hash

send必须指定发起的账户地址，而call可以直接调用

### 运行测试

```bash
$ mocha tests


  contract
合约部署账户： 0x25E0EAE6B1A9D09Ad3B8f20E1945B6Aa03055275
合约部署成功： 0xC9640efE6f4AE6561BC5CE294C814f71f465E04f
    ✔ deploy a contract
合约部署账户： 0x25E0EAE6B1A9D09Ad3B8f20E1945B6Aa03055275
合约部署成功： 0x72af628dE4b4C09D82aA66846aD3317EA6B818f4
    ✔ has initial brand
合约部署账户： 0x25E0EAE6B1A9D09Ad3B8f20E1945B6Aa03055275
合约部署成功： 0xB572717DFc65236F6a07631A5b02a80FaAD29CB1
    ✔ can change the brand (295ms)


  3 passing (1s)

Done in 3.47s.
```

### 完整的工作流

```json
  "scripts": {
    "predeploy": "yarn compile",
    "pretest": "yarn compile",
    "compile": "node scripts/compile.js",
    "deploy": "node scripts/deploy.js",
    "test": "mocha tests/"
  },
```