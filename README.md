## 全网最全ERC20智能合约大全


如果你想在以太坊ETH上发行一个ERC20的代币，你需要了解ERC20代币合约，通读本文以后，你将获得一个比较厉害的技能，希望你能学会。下图是基于ETH发行的ERC20版的USDT，你可以大概了解一下一个基本的ERC20代币的一些信息：
[https://cn.etherscan.com/token/0xdac17f958d2ee523a2206206994597c13d831ec7](https://cn.etherscan.com/token/0xdac17f958d2ee523a2206206994597c13d831ec7)

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/152946_dae8db8a_8185976.png "iShot2020-10-15下午03.28.36.png")

 **什么是ERC20** 

可以把ERC20简单理解成以太坊上的一个代币协议，所有基于以太坊开发的代币合约都遵守这个协议。遵守这些协议的代币我们可以认为是标准化的代币，而标准化带来的好处是兼容性好。这些标准化的代币可以被各种以太坊钱包支持，用于不同的平台和项目。说白了，你要是想在以太坊上发行代币融资，必须要遵守ERC20标准。

ERC20的标准接口是这样的:

```
contract ERC20 {
      function name() constant returns (string name)
      function symbol() constant returns (string symbol)
      function decimals() constant returns (uint8 decimals)
      function totalSupply() constant returns (uint totalSupply);
      function balanceOf(address _owner) constant returns (uint balance);
      function transfer(address _to, uint _value) returns (bool success);
      function transferFrom(address _from, address _to, uint _value) returns (bool success);
      function approve(address _spender, uint _value) returns (bool success);
      function allowance(address _owner, address _spender) constant returns (uint remaining);
      event Transfer(address indexed _from, address indexed _to, uint _value);
      event Approval(address indexed _owner, address indexed _spender, uint _value);
    }
```

下面对上面的内容做一个简单的解释：

 **name**  
> 返回ERC20代币的名字，例如”My test token”。

 **symbol**  
> 返回代币的简称，例如：MTT，这个也是我们一般在代币交易所看到的名字。

 **decimals**  
> 返回token使用的小数点后几位。比如如果设置为3，就是支持0.001表示。

 **totalSupply**  
> 返回token的总供应量

 **balanceOf**  
> 返回某个地址(账户)的账户余额

 **transfer**  
> 从代币合约的调用者地址上转移_value的数量token到的地址_to，并且必须触发Transfer事件。

 **transferFrom**   
> 从地址_from发送数量为_value的token到地址_to,必须触发Transfer事件。transferFrom方法用于允许合同代理某人转移token。条件是from账户必须经过了approve。这个后面会举例说明。

 **approve**  
> 允许_spender多次取回您的帐户，最高达_value金额。 如果再次调用此函数，它将以_value覆盖当前的余量。

 **allowance**  
> 返回_spender仍然被允许从_owner提取的金额。

后面三个方法不好理解，这里还需要补充说明一下：
approve是授权第三方（比如某个服务合约）从发送者账户转移代币，然后通过 transferFrom() 函数来执行具体的转移操作。

举例说明，如果账户A有1000个ETH，想允许B账户随意调用他的100个ETH，过程如下：

```
1. A账户按照以下形式调用approve函数approve(B,100)  
2. B账户想用这100个ETH中的10个ETH给C账户，调用transferFrom(A, C, 10)  
3. 调用allowance(A, B)可以查看B账户还能够调用A账户多少个token  
```


 **发行ERC20代币步骤**  

1. 在 Chrome 插件商店搜索并安装 MetaMask。

> MetaMask是钱包的一种，在chrome浏览器中，安装MetaMask插件即可，安装完成后，右上角会出现一个“狐狸头”的标志，点击该标志，打开钱包，第一步，创建账户，（创建账户只需要输入面密码即可，名称创建后可以随便改，该账户就是一个hash值，如何给自己创建的账户冲以太币呢，你可以通过在交易所买入一些ETH，然后转入即可）创建成功后，记住密码还有产生的几个随机单词（一定要记录下来）。

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/153925_0e3a69d5_8185976.jpeg "v2-c7999f3d3e8840f6fa58a9575b8b7a09_b.jpg")

2. 运行后，用它来给我们初始化一个以太坊钱包。 
   
![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/154022_acd9ac97_8185976.jpeg "v2-69055834472658007aab01b0126a58f2_b.jpg")

3. 准备一份ERC20的智能合约源码（仓库里有很多），一个简单的合约如下：

```
pragma solidity ^0.4.12;
 
contract IMigrationContract {
    function migrate(address addr, uint256 nas) returns (bool success);
}
 
contract SafeMath {
 
 
    function safeAdd(uint256 x, uint256 y) internal returns(uint256) {
        uint256 z = x + y;
        assert((z >= x) && (z >= y));
        return z;
    }
 
    function safeSubtract(uint256 x, uint256 y) internal returns(uint256) {
        assert(x >= y);
        uint256 z = x - y;
        return z;
    }
 
    function safeMult(uint256 x, uint256 y) internal returns(uint256) {
        uint256 z = x * y;
        assert((x == 0)||(z/x == y));
        return z;
    }
 
}
 
contract Token {
    uint256 public totalSupply;
    function balanceOf(address _owner) constant returns (uint256 balance);
    function transfer(address _to, uint256 _value) returns (bool success);
    function transferFrom(address _from, address _to, uint256 _value) returns (bool success);
    function approve(address _spender, uint256 _value) returns (bool success);
    function allowance(address _owner, address _spender) constant returns (uint256 remaining);
    event Transfer(address indexed _from, address indexed _to, uint256 _value);
    event Approval(address indexed _owner, address indexed _spender, uint256 _value);
}
 
 
/*  ERC 20 token */
contract StandardToken is Token {
 
    function transfer(address _to, uint256 _value) returns (bool success) {
        if (balances[msg.sender] >= _value && _value > 0) {
            balances[msg.sender] -= _value;
            balances[_to] += _value;
            Transfer(msg.sender, _to, _value);
            return true;
        } else {
            return false;
        }
    }
 
    function transferFrom(address _from, address _to, uint256 _value) returns (bool success) {
        if (balances[_from] >= _value && allowed[_from][msg.sender] >= _value && _value > 0) {
            balances[_to] += _value;
            balances[_from] -= _value;
            allowed[_from][msg.sender] -= _value;
            Transfer(_from, _to, _value);
            return true;
        } else {
            return false;
        }
    }
 
    function balanceOf(address _owner) constant returns (uint256 balance) {
        return balances[_owner];
    }
 
    function approve(address _spender, uint256 _value) returns (bool success) {
        allowed[msg.sender][_spender] = _value;
        Approval(msg.sender, _spender, _value);
        return true;
    }
 
    function allowance(address _owner, address _spender) constant returns (uint256 remaining) {
        return allowed[_owner][_spender];
    }
 
    mapping (address => uint256) balances;
    mapping (address => mapping (address => uint256)) allowed;
}
 
contract BliBliToken is StandardToken, SafeMath {
 
    // metadata
    string  public constant name = "BliBli";
    string  public constant symbol = "BCoin";
    uint256 public constant decimals = 18;
    string  public version = "1.0";
 
    // contracts
    address public ethFundDeposit;          // ETH存放地址
    address public newContractAddr;         // token更新地址
 
    // crowdsale parameters
    bool    public isFunding;                // 状态切换到true
    uint256 public fundingStartBlock;
    uint256 public fundingStopBlock;
 
    uint256 public currentSupply;           // 正在售卖中的tokens数量
    uint256 public tokenRaised = 0;         // 总的售卖数量token
    uint256 public tokenMigrated = 0;     // 总的已经交易的 token
    uint256 public tokenExchangeRate = 625;             // 625 BILIBILI 兑换 1 ETH
 
    // events
    event AllocateToken(address indexed _to, uint256 _value);   // 分配的私有交易token;
    event IssueToken(address indexed _to, uint256 _value);      // 公开发行售卖的token;
    event IncreaseSupply(uint256 _value);
    event DecreaseSupply(uint256 _value);
    event Migrate(address indexed _to, uint256 _value);
 
    // 转换
    function formatDecimals(uint256 _value) internal returns (uint256 ) {
        return _value * 10 ** decimals;
    }
 
    // constructor
    function BliBliToken(
        address _ethFundDeposit,
        uint256 _currentSupply)
    {
        ethFundDeposit = _ethFundDeposit;
 
        isFunding = false;                           //通过控制预CrowdS ale状态
        fundingStartBlock = 0;
        fundingStopBlock = 0;
 
        currentSupply = formatDecimals(_currentSupply);
        totalSupply = formatDecimals(10000000);
        balances[msg.sender] = totalSupply;
        if(currentSupply > totalSupply) throw;
    }
 
    modifier isOwner()  { require(msg.sender == ethFundDeposit); _; }
 
    ///  设置token汇率
    function setTokenExchangeRate(uint256 _tokenExchangeRate) isOwner external {
        if (_tokenExchangeRate == 0) throw;
        if (_tokenExchangeRate == tokenExchangeRate) throw;
 
        tokenExchangeRate = _tokenExchangeRate;
    }
 
    /// @dev 超发token处理
    function increaseSupply (uint256 _value) isOwner external {
        uint256 value = formatDecimals(_value);
        if (value + currentSupply > totalSupply) throw;
        currentSupply = safeAdd(currentSupply, value);
        IncreaseSupply(value);
    }
 
    /// @dev 被盗token处理
    function decreaseSupply (uint256 _value) isOwner external {
        uint256 value = formatDecimals(_value);
        if (value + tokenRaised > currentSupply) throw;
 
        currentSupply = safeSubtract(currentSupply, value);
        DecreaseSupply(value);
    }
 
    ///  启动区块检测 异常的处理
    function startFunding (uint256 _fundingStartBlock, uint256 _fundingStopBlock) isOwner external {
        if (isFunding) throw;
        if (_fundingStartBlock >= _fundingStopBlock) throw;
        if (block.number >= _fundingStartBlock) throw;
 
        fundingStartBlock = _fundingStartBlock;
        fundingStopBlock = _fundingStopBlock;
        isFunding = true;
    }
 
    ///  关闭区块异常处理
    function stopFunding() isOwner external {
        if (!isFunding) throw;
        isFunding = false;
    }
 
    /// 开发了一个新的合同来接收token（或者更新token）
    function setMigrateContract(address _newContractAddr) isOwner external {
        if (_newContractAddr == newContractAddr) throw;
        newContractAddr = _newContractAddr;
    }
 
    /// 设置新的所有者地址
    function changeOwner(address _newFundDeposit) isOwner() external {
        if (_newFundDeposit == address(0x0)) throw;
        ethFundDeposit = _newFundDeposit;
    }
 
    ///转移token到新的合约
    function migrate() external {
        if(isFunding) throw;
        if(newContractAddr == address(0x0)) throw;
 
        uint256 tokens = balances[msg.sender];
        if (tokens == 0) throw;
 
        balances[msg.sender] = 0;
        tokenMigrated = safeAdd(tokenMigrated, tokens);
 
        IMigrationContract newContract = IMigrationContract(newContractAddr);
        if (!newContract.migrate(msg.sender, tokens)) throw;
 
        Migrate(msg.sender, tokens);               // log it
    }
 
    /// 转账ETH 到BILIBILI团队
    function transferETH() isOwner external {
        if (this.balance == 0) throw;
        if (!ethFundDeposit.send(this.balance)) throw;
    }
 
    ///  将BILIBILI token分配到预处理地址。
    function allocateToken (address _addr, uint256 _eth) isOwner external {
        if (_eth == 0) throw;
        if (_addr == address(0x0)) throw;
 
        uint256 tokens = safeMult(formatDecimals(_eth), tokenExchangeRate);
        if (tokens + tokenRaised > currentSupply) throw;
 
        tokenRaised = safeAdd(tokenRaised, tokens);
        balances[_addr] += tokens;
 
        AllocateToken(_addr, tokens);  // 记录token日志
    }
 
    /// 购买token
    function () payable {
        if (!isFunding) throw;
        if (msg.value == 0) throw;
 
        if (block.number < fundingStartBlock) throw;
        if (block.number > fundingStopBlock) throw;
 
        uint256 tokens = safeMult(msg.value, tokenExchangeRate);
        if (tokens + tokenRaised > currentSupply) throw;
 
        tokenRaised = safeAdd(tokenRaised, tokens);
        balances[msg.sender] += tokens;
 
        IssueToken(msg.sender, tokens);  //记录日志
    }
}
```


4. 有了智能合约，然后把它发布到以太坊网络中。  
我们使用 Remix - Solidity IDE 网站来发布智能合约。MetaMask会把当前账户相关的信息填写到网站上，我们只需要把智能合约的代码粘贴进去，简单的改一下配置就可以了：

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/154232_47ba4c6b_8185976.jpeg "v2-0571838dabae082d9c6b3901884df1dd_b.jpg")

5. 把网站当前使用的 solidity 编译器版本号改成和文件头一致，把Enable Optimization 去掉。然后切换到 Compile 页面，点击Start Compile。

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/154306_53b6a01d_8185976.jpeg "v2-d63ea2387ae78db3ba7d38bcf2eb21aa_b.jpg")

6. 在Run页面，点击 Deploy

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/154404_db7ce8d8_8185976.jpeg "v2-262feb25fbac6221006fc34b2b4811de_b.jpg")

7. 弹出 MetaMask 确认页面，输入一个 Gas 数量即可，点击Submit。

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/154436_a8e3ab66_8185976.jpeg "v2-e16742e250cac707a8858a9990be2f1c_b.jpg")

8. 可以看到合约开始部署～

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/154512_0f45c48e_8185976.jpeg "v2-69055834472658007aab01b0126a58f2_b.jpg")

9. 部署完毕后，点击那个合约，会帮你打开一个网站，查看合约详情：

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/154616_0d40e4ed_8185976.jpeg "v2-2784a05bf1630241ca1fb9ff512715e3_b.jpg")

10. 点击合约地址，会跳转到合约校验发布页面，点击Verify And Publish

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/154643_794a9b55_8185976.jpeg "v2-ecce6571b28b905235561fd517cceb8d_b.jpg")

11. 填写好下面信息，同时粘贴代码：

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/155014_6105b89c_8185976.jpeg "v2-60377360bc72f4f4f01f631477f6abbe_b.jpg")

12. 拉到最下面，然后确认即可。发布成功后，会看到如下页面：

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/155045_3ec4ffa1_8185976.jpeg "v2-34e589f54efe5be1eabae94d56549548_b.jpg")

13. 然后在账户中，就可以看到如下内容了：

![输入图片说明](https://images.gitee.com/uploads/images/2020/1015/155109_749200ca_8185976.jpeg "v2-6b0a5134adccbefd6cac82b22a2e6249_b.jpg")

到此为止，ERC20的合约已经部署完成！
