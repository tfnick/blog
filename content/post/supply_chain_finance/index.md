---
title : "供应链金融模式设计"
description : "供应链金融模式设计"
date : "2021-03-12"
aliases : ["产品与架构"]
author : "飞天"
toc : true
---



## 场景-用户-需求



![公司间分工产生供应链](47fb51c4bf4548b388c86bebd25ff8b1_4882721495710800757.png)公司间分工产生供应链



核心企业利用自身在产业链中的强势地位，占用上游供应商企业的账期，同时对下游经销商实行先款后货的策略，通常会造成上下游企业资金紧张进而引发上下游企业的融资需求。

## 业务价值

不强依赖核心企业的情况下，依靠“独特”的风险控制变量，帮助银行识别可信任客户，帮助企业盘活其流动资产从而解决其融资问题。

## 解决方案

我们知道企业流动资产最重要的无外乎现金及等价物、应收账款和存货三大类。这就有了供应链金融的三种模式。

### 应收账款模式



![img](abed967ec6124c24ab4f9d7de3d584a3_12883267376303718167.png)



### 预付款模式



![img](a7ba0d8d25b247e6844389a7c578dfd4_8566184994420436600.png)



### 存货模式



![存货融资](84e958c8d5e24f69bba9aff7b4ce9b1d_10343914662376476591.png)存货融资



### 总结

大的模式总的来说与上面三张图大同小异，每个参与企业可以根据自身特点进行模式微创新，但非常重要的有两点：

- 一是以何种身份参与供应链金融？是以助贷方身份还是以核心企业身份
- 二是是否承担风险，风险承担与否会影响系统的复杂度与可复制性(决定模式的轻与重)，而且在未来，承担风险模式可能会被强监管，这一点是可以预期的。

所以对于物流企业，我个人倾向于以助贷身份进入供应链金融，且在不承担任何风险的情况（轻模式）下，提供助贷平台型服务。作为服务平台，需要我们一直努力的三个核心点，一是如何以高效的风控手段帮助资金方提升风控水平；二是如何帮助企业降低资金成本；三是如何规模化，持续降低自身运营成本。

目前，业内推出了很多所谓基于区块链的解决方案，但根本困难其实在于，供应链上企业对数据上区块链的成本、产出以及隐私的忧虑，特别是其中核心企业的意愿[较低](https://www.sohu.com/a/331981321_470085)，可以预计的是推广此类需要多方配合、协同的解决方案难度会非常大，不是一个合理的方向。如果一个商业模式不是基于参与方的“刚性”需求自然而然形成的，那么这个商业模式的实现与推广就会遇到非常多的[障碍](https://baijiahao.baidu.com/s?id=1688414811157008899&wfr=spider&for=pc)。

## 区块链技术价值分析

目前，在传统中心化金融里，以分布式账本为核心的区块链技术无法充分发挥其优势，引入区块链技术更多的是实现数据上链，从而实现数据多方更高效的共享。并且保证数据无法篡改，无法抵赖。但我们需要看到，区块链技术在去中心化金融领域，目前发展的如火如荼，其中可能会有不少值得传统金融学习与借鉴的地方。

## 目前供应链金融模式的主要问题

### 核心企业数量不多且参与意愿不强

由于诸多[原因](https://www.sohu.com/a/331981321_470085)，核心企业对参与供应链金融兴趣不大，且核心企业名单数量少，这是很多早期进入供应链金融的玩家失败出局的主要原因之一，比如X上金融。这就要求我们的模式设计，特别是风控管理与贷后回购上，需要有意识的创造条件甚至创新性的去除核心企业的强耦合影响。

### 系统性风险与联合欺诈

核心企业与上下游企业联合造假，套取银行资金，当整体经济形势趋冷，更容易引发这类问题。这类问题通常需要引入“人”进行风控防控，但引入人其实又引入了新的风险变量，所以又要求引入有新的风控手段。2019年供应链金融行业内大规模暴雷就是这类风险的集中体现。

### 上下游企业信息化程度不高

因为上下游企业数字化程度不高，想要获取上下游企业的经营数据比较困难，也有企业基于商业机密考虑不愿意开放核心的经营数据，所以进一步增加了风控的难度。目前，钉钉，腾讯企业微信，包括一些做企业信息化服务平台的创业型公司（比如有信）都在此领域发力，说明`企业数字化平台`已经成为toB领域兵家必争之地。

### 行业的差异

虽然模式上各个行业看上去差异不大，但实际业务涉及线下操作，必然会面临行业属性的差异，这就要求我们对模式细节与行业有所取舍，避免团队资源以摊大饼的方式过度分散于多个行业中。

## 目前头部的一些玩家

### 阿里巴巴

基于阿里巴巴平台优势与国际物流支持，阿里巴巴有能力打造信息流，资金流，物流的闭环商业模式，是未来国内国际供应链金融的重量级玩家。

### 腾讯

腾讯入局供应链金融等B端业务的抓手之一，是联易融；其主要思路是：

一，顺着供应链抓核心企业，了解产业链中的供应商、经销商，逐步建立行业的生态圈。这是强信用模式，核心企业能够对其客户确权，将核心企业信用不断地传导到供应商、供应商的供应商。“我们现在市场做了很多的ABS，ABS其实就是强信用传达，把核心企业的应收账款做成资产证券化在市场上发行。

二，做小微企业经营状况画像。从不同的渠道获取公开数据，包括企业经营和交易数据、税务发票的数据、企业征信信息，以此判断企业主特征。“我们现在采集有20多个不同的来源，数据之后是一个建模的过程，数据掌握得越多，数据验证的能力越强，最后产生一个信贷的模型，可能就会越贴近小微企业的真实经营状况，这是强数据驱动的经营状况画像。

### 京东数科

京东基于电商业务积累的资金流，信息流与物流闭环，比较适合打造销售端的供应链模式产品，比如京保贝。

### 怡亚通

怡亚通的供金模式使用了自己的资金承担了风险，缺点非常明显，一是占用自身现金，影响主业的资金使用效率，同时会限制规模的量级；二是当系统风险增加时，极易导致怡亚通自身陷入高不良的险境。

### UPS

是目前可以重点学习与借鉴的对象

## 引用

[供应链金融很难理解？五分钟让你看懂它的前世今生](https://www.sohu.com/a/140700570_762607)

[揭秘 | UPS是如何做供应链金融的](https://www.sohu.com/a/122850846_465938)

[想不到！核心企业开展供应链金融也这么难](https://www.sohu.com/a/331981321_470085)

[1+L+N，供应链金融去核心企业化的创新模式](http://www.cindmin.com/news_20282.html)

[腾讯互联网巨头们的供应链金融布局](https://www.iyiou.com/analysis/20191219120577)

[联易融招股书：毛利率高、研发占比低，近三年合计亏约30亿](https://baijiahao.baidu.com/s?id=1688414811157008899&wfr=spider&for=pc)

[供应链金融模式详解与创新](https://baijiahao.baidu.com/s?id=1662039874232911230&wfr=spider&for=pc)

[解读互联网+供应链金融的7种主流玩法](https://cloud.tencent.com/developer/article/1540382)

[金融业务净利下滑超82% 怡亚通收缩供应链金融](https://finance.sina.com.cn/stock/relnews/cn/2019-08-17/doc-ihytcern1420926.shtml)

[深圳怡亚通是一家怎样的公司？](https://www.zhihu.com/question/22548870)

[怡亚通模式分析](http://www.360doc.com/content/17/0627/18/33525822_667005334.shtml)

[供应链金融的十种模式](http://www.oh100.com/peixun/gongyinglian/233633.html)

[基于区块链技术的供应链金融分析](http://www.lianmenhu.com/blockchain-19190-1)

[旺链科技](http://www.vonechain.com/web/solution/page)

[“爆雷”事件频发　供应链金融何去何从](https://kbase.10000link.com/newsdetail.aspx?doc=2019081390003)

[供应链金融风险“套路深深”，本文带你破解风控之道](https://new.qq.com/omn/20200917/20200917A0DDJK00.html)

```
区块链本质是一个去中心化的分布式账本，具备防篡改、不可抵赖的特点
开发运行于区块链基础上的各类智能合约应用，比如Defi(分布式金融)是目前比较火的领域，数字资产上链，最大的挑战来自监管
```

[利用区块链技术提升传统金融的运行效率-基于强监管的联盟链](https://finance.sina.com.cn/money/bank/bank_hydt/2021-01-11/doc-iiznctkf1453100.shtml)

[利用区块链技术开发基于代币的各类分布式金融服务“DEFI”-基于无监管的以太坊](https://www.bishijie.com/shendu/40464.html)

[以太坊精选Defi应用](https://www.block123.com/zh-hans/feature/awesome-ethereum-defi-decentralized-finance-list/)

[以太坊精选Defi应用-wyre跨境支付应用](https://www.block123.com/zh-hans/nav/999142480266.htm)