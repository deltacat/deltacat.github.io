title: 简述区块链与加密货币
s: blockchaine-and-cryptocurrency
categories: 
- 软件开发
tags: 
- 软件开发
- 金融科技
- 区块链
date: 2017-01-30 12:00:00

---

# 区块链技术简述

![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017122701.jpg)

## 区块链的本质

一句话说明：不可逆的去中心化分布式数据库。

首先，区块链的主要作用是储存信息。任何需要保存的信息，都可以写入区块链，也可以从里面读取，所以它是数据库。

其次，任何人都可以架设服务器，加入区块链网络，成为一个节点。区块链的世界里面，没有中心节点，每个节点都是平等的，都保存着整个数据库。你可以向任何一个节点，写入/读取数据，因为所有节点最后都会同步，保证区块链一致。

![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017122702.png)

分布式数据库并非新技术。但是，区块链有一个革命性特点：没有管理员，彻底无中心。

如果有人想对区块链添加审核，也实现不了，因为它的设计目标就是防止出现居于中心地位的管理中枢。

正是因为无法管理，区块链才能做到无法被控制。否则一旦大机构控制了管理权，他们就会控制区块链。

但是，没有了管理员，人人都可以往里面写入数据，怎么才能保证数据是可信的呢？这就是区块链奇妙的地方。

##　核心技术

### 区块

![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017122703.png)

区块链由一个个区块（block）组成。区块很像数据库的记录，每次写入数据，就是创建一个区块。

### HASH

Hash 具有不可修改性。区块与 Hash 是一一对应的，每个区块的 Hash 都是针对"区块头"（Head）计算的。

前面说过，区块头包含很多内容，其中有当前区块体的 Hash（注意是"区块体"的 Hash，而不是整个区块），还有上一个区块的 Hash。这意味着，如果当前区块的内容变了，或者上一个区块的 Hash 变了，一定会引起当前区块的 Hash 改变。

这一点对区块链有重大意义。如果有人修改了一个区块，该区块的 Hash 就变了。为了让后面的区块还能连到它，该人必须同时修改后面所有的区块，否则被改掉的区块就脱离区块链了。由于后面要提到的原因，Hash 的计算很耗时，同时修改多个区块几乎不可能发生，除非有人掌握了全网51%以上的计算能力。

正是通过这种机制，区块链保证了自身的可靠性，数据一旦写入，就无法被篡改。这就像历史一样，发生了就是发生了，从此再无法改变。

![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017122705.png)

每个区块都连着上一个区块，这也是"区块链"这个名字的由来。


### 非对称加密

首先，理解比特币，必须理解非对称加密。

你可能听说过这个词，所谓非对称加密，其实很简单，就是加密和解密需要两把钥匙：一把公钥和一把私钥。

公钥是公开的，任何人都可以获取。私钥是保密的，只有拥有者才能使用。他人使用你的公钥加密信息，然后发送给你，你用私钥解密，取出信息。反过来，你也可以用私钥加密信息，别人用你的公钥解开，从而证明这个信息确实是你发出的，且未被篡改，这叫做数字签名（更详细的介绍请看《什么是数字签名》）。

现在请设想，如果公钥加密的不是普通的信息，而是加密了一笔钱，发送给你，这会怎样？

首先，你能解开加密包，取出里面的钱，因为私钥在你手里。其次，别人偷不走这笔钱，因为他们没有你的私钥。因此，支付可以成功。

这就是比特币（以及其他数字货币）的原理：非对称加密保证了支付的可靠性。

由于支付的钱必须通过私钥取出，所以你是谁并不重要，重要的是谁拥有私钥。只有拥有了私钥，才能取出支付给你的钱。（事实上，真实的交易流程稍有不同，私钥保证的不是取出支付给你的钱，而是保证只有你能把这些属于你的钱支付出去，详见后文。）

### 采矿

由于必须保证节点之间的同步，所以新区块的添加速度不能太快。试想一下，你刚刚同步了一个区块，准备基于它生成下一个区块，但这时别的节点又有新区块生成，你不得不放弃做了一半的计算，再次去同步。因为每个区块的后面，只能跟着一个区块，你永远只能在最新区块的后面，生成下一个区块。所以，你别无选择，一听到信号，就必须立刻同步。

所以，区块链的发明者中本聪（这是假名，真实身份至今未知）故意让添加新区块，变得很困难。他的设计是，平均每10分钟，全网才能生成一个新区块，一小时也就六个。

这种产出速度不是通过命令达成的，而是故意设置了海量的计算。也就是说，只有通过极其大量的计算，才能得到当前区块的有效 Hash，从而把新区块添加到区块链。由于计算量太大，所以快不起来。

这个过程就叫做采矿（mining），因为计算有效 Hash 的难度，好比在全世界的沙子里面，找到一粒符合条件的沙子。计算 Hash 的机器就叫做矿机，操作矿机的人就叫做矿工。

![](http://www.sanyarb.com.cn/images/attachement/jpg/site2/20170613/A121497290592668_change_cbx76140_b.jpg)

![](http://n.sinaimg.cn/translate/w580h363/20180113/Y4yC-fyqrewh6609396.jpg)

### 矿机、矿场、矿池

推荐矿池：鱼池、星火矿池

### 共识机制

区块链是去中心化的账本，没有中心节点，所以需要全网对账本达成共识。用于达成共识的规则就是“共识机制”。

目前常见的共识机制：PoW、PoS、DPoS、PBFT、dBFT、Pool

- PoW（Proof of Work）工作量证明，

就是大家熟悉的挖矿，通过与或运算，计算出一个满足规则的随机数，即获得本次记账权，发出本轮需要记录的数据，全网其它节点验证后一起存储；优点：完全去中心化，节点自由进出；缺点：目前bitcoin已经吸引全球大部分的算力，其它再用Pow共识机制的区块链应用很难获得相同的算力来保障自身的安全；挖矿造成大量的资源浪费；共识达成的周期较长，不适合商业应用

- PoS（Proof of Stake）权益证明

PoW 的一种升级共识机制；根据每个节点所占代币的比例和时间；等比例的降低挖矿难度，从而加快找随机数的速度。优点：在一定程度上缩短了共识达成的时间缺点：还是需要挖矿，本质上没有解决商业应用的痛点

## 代币（Token），ICO

许你经常看到ERC20和代币一同出现， ERC20是以太坊定义的一个代币标准。我们现在看到的很多代币，实际上是基于以太坊的智能合约发行。

一览：
https://coinmarketcap.com/tokens/

那么，如何发行一个新的代币，就会接触到概念 ICO（首次币发行）

### 先有比特币还是先有区块链？

比特币和区块链的概念同时诞生于中本聪2008年的论文。然而区块链的核心技术早已存在。

加密货币诞生于链圈（技术圈），成长于币圈。2017年深入影响了互联网圈和VC圈，又在做着颠覆金融圈的事情。

### 如何发行/参与ICO？

![](https://pic2.zhimg.com/80/v2-dcd00c592b7543a848cef81d926b907e_hd.jpg)

### ICO 历史


> 2013年7月募集了5000个比特币的Mastercoin (现名为Omni) 是收个有记录的ICO，而以太坊在2014年7月超过1500万美元的ICO开启了ICO快速发展进程。2015年The DAO实现高达1.5亿美元融资，但后因受黑客攻击失败。2016年以来,ICO热度不断提升。Lisk、ICONOMI等项目均获得百万美元融资，去中心化算力平台Golem几分钟就完成了860万美元融资，而后GNOSIS和BRAVE、WAVE等项目更是屡屡刷新全球募集记录。2017年3月，量子链ICO项目仅117个小时就累计筹集近亿人民币，创造了国产ICO项目最高纪录。

> ICO项目融资已超过VC融资金额。根据CoinDesk的数据显示，从2016年1月至2017年3月，全球主要ICO项目（25000美金以上的融资额）有91个，合计融资金额为3.37亿美金，ICO占风投资金比接近50%。2017年初至5月17日，区块链VC融资为1.07亿美金，而ICO为1.25亿美金，实现超越。下图为部分主要ICO项目信息。

>作者：觅罗宝
链接：https://www.zhihu.com/question/65739450/answer/235615173
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

![](https://pic4.zhimg.com/80/v2-173007f00bd64e0a218482014764e78e_hd.jpg)


## 智能合约

“智能合约”（smart contract）是由多产的跨领域法律学者 Nick Szabo 在1995年提出来的，他的定义为：“一个智能合约是一套以数字形式定义的承诺，包括合约参与方可以在上面执行这些承诺的协议。”

![](https://static.leiphone.com/uploads/new/article/740_740/201710/59dc674b1598f.jpg?imageMogr2/format/jpg/quality/90)

智能合约的最大价值在于全程自动化的交易，一旦某种条件达成，自动执行合约内容。

### 为什么要用区块链技术实现智能合约

基于以下原因使用区块链实现智能合约：

1. 数据不能修改、删除，只能查看和增加。
2. 去中心化。避免了中心化的“人”因素。

### 智能合约的开发越来越简单

最初，需要完整的区块链开发能力
以太坊搭建了一个生态平台，只需要简单的代码就可以实现自己的智能合约。
新的智能合约实现探索，如创业项目Fabric Token，对抗智能合约开发者稀缺。
https://www.leiphone.com/news/201801/yLVfTmwO3QFW7jd0.html

# 发展和展望

## 三代加密币的特征和尚存的问题

### 第一代加密币：比特币为代表。去中心化交易验证。

实质就是分布式去中心化账本

### 第二代加密币：以太坊为代表，引入智能合约

以太坊（Ethereum）是一个建立在区块链技术之上， 去中心化应用平台。它允许任何人在平台中建立和使用通过区块链技术运行的去中心化应用。

在没有以太坊之前，写区块链应用是这样的：拷贝一份比特币代码，然后去改底层代码如加密算法，共识机制，网络协议等等（很多山寨币就是这样，改改就出来一个新币）。

以太坊平台对底层区块链技术进行了封装，让区块链应用开发者可以直接基于以太坊平台进行开发，开发者只要专注于应用本身的开发，从而大大降低了难度。

###第三代加密币需要解决目前加密币的问题

可规模化（scalable）：TPS、网络带宽以及数据量
可互操作性（interoperability）：区块链与区块链之间，区块链与传统金融系统之间
和可持续发展性（sustainability）：比如如何支付、迭代、如何分叉等等

如： Cardano(ADA) 卡尔达诺

## 应用场景

目前真正有实际意义的应用场景仍然是加密货币

未来可能在物流、金融、法律、存证、医疗等诸多领域有广泛应用，但是首先需要解决商业应用的基本需要，以及数字化和实体的衔接问题。

抛开投资价值，仅从技术和应用角度来说，不要“为区块链而区块链”，一个产品是否要应用区块链技术，必须从多个角度来评估，是否真的需要一个去中心化的体系，系统运行在区块链上的成本和收益是否合理。

在决策前，先问几个问题：

1. 这个事情本质的问题是什么，信任，效率，安全？
2. 传统解决方案是否不能解决？
3. 如果传统方案不能解决，区块链是否能解决？会不会引入新的问题？
4. 如果都能完美解决，两者成本和效率的对比如何？

# 其他

## 加密币投资


## 一些安全说明

1. 找零机制 

http://www.8btc.com/bitcoin-change-addresses-explanation

![](http://bitzuma.com/images/posts/20140315/one-output-animation.gif)

![](http://bitzuma.com/images/posts/20140315/change-multiple-outputs-animation.gif)

2. 匿名性

匿名性并非表示不可追踪，只是把钱包地址和真实身份对应比较困难。但是在交易过程中，如果不懂得保护自己的钱包地址，就可能面临被追踪钱包地址甚至盗用的可能。

与他人交易时，一个地址尽量只使用一次。

3. 备份和冷钱包

私钥是唯一身份证明，一旦丢失无法找回。

冷钱包是最安全的保存方法。切勿将私钥保存在联网的电脑、手机等电子设备。

## 常见术语

- 非对称加密
- 钱包
  - 冷钱包
  - 热钱包
- 共识
- 面向区块链开发
- 智能合约
- DApps
- 代币
- ICO
- 矿池
- 公有链
- 私有链
- 联盟链

# 参考资料

- 廖雪峰的区块链入门教程：http://www.ruanyifeng.com/blog/2017/12/blockchain-tutorial.html
- ICO 是什么：https://www.zhihu.com/question/65739450
- 浅析比特币的找零机制：http://www.8btc.com/bitcoin-change-addresses-explanation
- 十分钟搞定 ICO：https://zhuanlan.zhihu.com/p/32796998
- 为什么说代币Token对区块链不可或缺？：http://www.techweb.com.cn/blockchain/2018-01-25/2632896.shtml
- 区块链研习 | 区块链里所说的“智能合约”是什么？https://www.leiphone.com/news/201710/MT0rQIVjpSVZ3ieH.html?ulu-rcmd=0_5021df_art_1_085c4d7a3c36494a9bc95779411ff277


