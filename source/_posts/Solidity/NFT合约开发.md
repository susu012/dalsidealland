---
title: NFT合约开发
date: 2023-04-17 19:59:00
tags: 
categories:
  - - Solidity
auto_excerpt: "true"
---



## 前言

这篇文章写得比较早，那时候连WTF都是一本野鸡教程（我还是比较讨厌其商业化的，毕竟其实大家都觉得它写得很烂不是吗，solodity届谭浩强，只是那时候中文资料少填补了空白），而中文资料几乎都是过期的，全靠啃稀少的最新英文文档。

不过好在solidity是一个简单的语言，唯一复杂一点是内联汇编，它的语法是有限的，它的过程是有限的（代码长度直接和gas fee相关），状态是有限的（变量的类型少，实际代码中，个数也要尽量少），毕竟以太坊虚拟机性能有限。

最后，安全，安全就是一切。



# NFT合约开发

  

**开发经验**

  

1. 所有的函数都是围绕着修修改改那几个你事先规定的状态变量

2. 状态变量修改之前要校验其它所有相关状态变量是否合法

3. 状态变量修改之后要检查对等修改其它所有相关状态变量是否需要改变

  

---

  

**怎么写铸币**

  

铸币函数的形参都是 输入铸币地址和令牌编号，重写铸币函数的时候，先校验一些参数合法性：

  

1. 外部地址：`require(tx.origin == msg.sender, "Only EOA");`

2. 令牌总数：`require(totalSupply() + _numberOfTokens <= MAX_SUPPLY,"Max Limit To Presale");`

3. 铸币限额：`require(_numberOfTokens <= maxByMint, "Exceeds Amount");`

4. 铸币金额：`require(mintPrice * _numberOfTokens <= msg.value, "Low Price To Mint");`

  

然后写一个for循环，铸币几次就循环几次

  

如果使用了ERC721URIStorage法控制tokenURI，那么mint函数铸币之后需要紧接着调用_setTokenURI函数，详见怎么写URI

  

铸币函数如果要写返回值，最好能返回铸币id

  

```solidity

for (uint8 i = 0; i < _numberOfTokens; i += 1) {

uint16 tokenId = uint16(totalSupply() + i);

_safeMint(msg.sender, tokenId);

}

```

  

如果想要让白名单铸币和公售同时进行，且保留白名单铸币的权利，可以写一个maxsupply的状态变量，让公售函数对maxsupply进行检查，而白名单不检查即可（可能在开图之前要设置一个图片让所有铸币者都获得同一张图片，然后铸币完成之后慢慢传所有图）

  

---

  

**怎么写构造函数**

  

1. 在合约内部写`constructor(xxx)public{xxx}`

2. 一般来说，构造函数的首要任务是要把本合约的状态变量全部初始化一遍

3. 构造函数的次要人物是创建合约同时运行一些需要特殊任务定制函数，例如提前铸造一些令牌之类的，此时需要在构造函数的形参位置传入一个地址参数，以供内部msg.sender使用

4. 和其它计算机语言不一样的是solidity是先有的合约代码后有的构造函数，构造函数就真是个初始化器，先存在着，然后用构造函数初始化

5. 对继承关系的合约，写本合约的构造函数时是在“写一个函数”，对形参要标注数据类型，但是对父合约的构造函数要填的是实参，父合约构造函数通常是ERC721(”项目名称”,”项目简记符”)

6. 令牌编号从1开始比从0开始更节省gas费，所以可以在构造的时候就把令牌id计数器从0自增到1

  

```solidity

constructor(address _admin) ERC721("Bandits In The Metaverse", "BITM") {

MAX_SUPPLY = 3333;

RESEVE_AMOUNT = 133;

mintPrice = 0.025 ether;

maxByMint = 3;

admin = _admin;

uint16 tokenId = totalSupply();

_safeMint(msg.sender, tokenId);

mintedCount = mintedCount + 1;

}

```

  

---

  

**怎么写URI**

  

IPFS：

  

1. 合约里存的tokenURI是存一大批.json文件的ipfs地址，这些json用文件夹上传功能组织起来

2. 每个.json里存一大批图片地址，图片们也是另一个ipfs文件夹

  

ERC721URIStorage法：

  

1. 合成TokenURI：先写一个getTokenURI函数，这个函数对baseURI和TokenId进行合成然后返回URI。

2. 函数体先输入令牌id，强制把令牌id转换为String格式，然后设置String格式的HeadString和FooterString，前者是metadata数据网址的前面部分（ipfs://xxx之类的），后者是”.json”，然后可以用solidity自带的字符串合成函数`string.concat(xxx)`把HeadString+tokenid+FooterString合成tokenURI并返回，然后查找并返回这个id的TokenURI地址。对三个字符串进行合成并返回可以使用string.concat()函数，格式string.concat([字符串1],[字符串2],[字符串3],…)，这个函数是solidity自带字符串库里面的，强制转换可以用Openzeppelin的utils的String.sol里的toString函数

3. 设置TokenURI：铸币的时候，如果用了openzeppelin的ERC721URIStorage，此文件会定义一个函数_setTokenURI(uint256 tokenId, string memory _tokenURI)，输入TokenId和TokenURI可以设置URI

4. 注意string.concat函数编译器版本必须大于等于0.8.12，坑死

  

---

  

**怎么写函数封装（铸币价格、单次铸币总数）**

  

1. 对每个加减运算符、力所能及使用库然后using for的状态变量，都要用专门的函数、专门的类、专门的库进行封装

2. 不管是引用类型还是值类型的数据，所有数据的身份唯一，传入函数之后要在函数中再创建一个新的memory变量然后赋值给它，在函数中专门用这个memory变量即可，不要让一参分饰多角

  

---

  

**怎么写铸币开关（白名单、公售、阶段1阶段2等）**

  

1. 写一个pause变量，最好是paused以表明到底暂停没暂停，然后每个铸币函数都对这个布尔值进行校验即可

  

```solidity

bool public paused = true;

```

  

---

  

**怎么写白名单**

  

**映射法**

  

1. 定义一个地址到布尔值的映射数据结构，以这个映射类型定义一个白名单地址变量，把所有白名单都写进去，再写个修改器，每次铸币都校验是否为true

  

```solidity

mapping(address => bool) whitelist;

```

  

1. 再写一个地址到布尔值的映射数据结构，定义一个白名单地址已经铸币的变量，每次铸币校验一下这个白单地址是否已经铸币

  

```solidity

mapping(address => bool) whitelistMinted;

```

  

**默克尔树法**

  

使用默克尔树法和分为两部分代码，开发者制作默克尔树和铸币者使用默克尔树

  

1. 制作默克尔树：第一步对所有白名单地址单独进行哈希运算，算出每个白名单地址的哈希值，作为默克尔树的叶，第二步用构造默克尔树的函数和叶数据，进行构造默克尔树对象，第三步调用默克尔树对象的成员方法把根哈希值算出来，根哈希值明文存到合约状态变量中

2. 白名单的验证：第一步把当前用户地址使用算法算出哈希值，即取出树叶，第二步把树叶传入函数判断是否和树根匹配

3. 在网站mint页面，对用户地址进行哈希计算，也就是这个地址的叶节点值，把叶节点传入智能合约调用函数进行验证，这个验证函数是openzeppelin的MerkleProof.sol的成员函数MerkleProof.verify()，可以用布尔值返回树叶是否属于树根

  

---

  

**怎么写限制每个人铸币的个数**

  

1. 写一个地址到布尔值的映射，铸币的时候储存调用者地址，每次铸币的时候校验有没有铸币

  

```solidity

mapping(address => bool) publicMinted;

```

  

---

  

**怎么上传代码到etherscan**

  
这个很简单不过etherscan改版了所以删掉了

---

  

**怎么写燃烧**

这个也是以后有机会写了，不过实现比较简单靠自己的智慧吧，纯逻辑方面的一个事情。

**怎么写撤销函数**

这个也是以后有机会写了，不过实现比较简单靠自己的智慧吧，纯逻辑方面的一个事情。

**怎么写开图**

这个也是以后有机会写了，根据图片的存储方式一般有两种（cryptopunk那种情况是一枝独秀，我们都很欣赏它）。

第一种是ipfs的，除非你自己搭建ipfs存储（而且效果一般很不好），否则一般建议两种实现方式，首先是吉祥物很魔性的Pinata（一个好的吉祥物会让你忘不掉这个公司），其提供一定存储容量的免费空间，文件上传之后几乎立等可用；其次是对NFT而言，用OpenSea投资的nft.storage比较方便（几乎一键，该公司对资源的投入和给予力度很大和很慷慨）。

第二种是自己搭个服务器，猴子那种，人话说自己盖图床。

有一定编码水平的程序员看了思路应该就知道怎么搭了，剩下的是细节问题。

**怎么写合约自毁**

这个也是以后有机会写了，不过实现比较简单靠自己的智慧吧，纯逻辑方面的一个事情。

**怎么写工厂模式**

唯一有点技术含量的，这个也是以后有机会写了，这利用了solodity的一个特性，youtube上有教程。

**怎么写版税**

这个也是以后有机会写了。