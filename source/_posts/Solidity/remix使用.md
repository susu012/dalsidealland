---
title: remix使用
date: 2023-04-06 20:10:38
tags: 
categories:
  - - Solidity
auto_excerpt: "true"
---
## **左侧栏**

文件夹：

1. contracts：放智能合约
2. scripts：运行智能合约的脚本
3. tests：跑智能合约单元测试用

SOLIDITY COMPILER：编译器

1. COMPILER选项：选择编译器版本
2. LANGUAGE选项：选择计算机语言
3. EVM VERSION选项：运行环境的选择，有许多版本，每个都行，随便选，一般选default默认版本
4. Auto compile勾选：自动编译，修改代码之后会对新的代码自动进行编译
5. Enable optimization勾选：优化代码，一般不用改，默认200，但是如果智能合约使用的人很多建议改大一点到1000以上，太小的话gas会很高
6. Hide warnings勾选：是否隐藏警告，不用管
7. Compile按钮：编译。在文件中选一个智能合约文件，然后点这个按钮可以对它编译

DEPLOY&RUN TRANSACTIONS：智能合约所部署的环境的相关

1. ENVIRONMENT选项：有很多环境可选，JavaScrupt VM是JS虚拟机，运行在本机内存，而Injected Web3就是运行在以太坊测试网，Web3 Provider是本地或者其他网络
2. ACCOUNT选项：账户选项，默认会生成十几个账户，每个账户10个以太坊
3. GAS LIMIT填空栏：气体限制选择，不知道有啥用
4. Deplay按钮：部署合约的按钮，在部署之前，在按钮上面的CONTRACT上选择需要部署的合约，部署成功之后终端里会有一些信息
