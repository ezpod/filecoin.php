__Filecoin.PHP__ 开发包适用于为PHP应用快速增加对Filecoin/FIL数字资产的支持能力，
即支持使用自有Filecoin区块链节点的应用场景，也支持基于第三方公共节点的
轻量级部署场景。Filecoin.PHP官方下载地址：[http://sc.hubwiz.com/codebag/filecoin-php-lib/](http://sc.hubwiz.com/codebag/filecoin-php-lib/)

<!--more-->

## 1、Filecoin.PHP开发包概述

Filecoin.PHP开发包主要包含以下特性：

- 支持离线生成Filecoin地址，方便管理维护
- 支持Filecoin消息的离线签名，有利于更好地保护私钥
- 自动估算Filecoin消息的GAS参数，避免手工调整
- 支持使用自有节点或第三方节点，例如使用Infura提供的公共节点
- 完善的Filecoin节点API封装，支持全部RPC API调用，例如查询地址地历史消息等

Filecoin.PHP软件包运行在 **Php 7.1+** 环境下，当前版本1.0.0，主要类/接口及关系如下图所示：

![filecoin.php uml](http://sc.hubwiz.com/codebag/filecoin-php-lib/img/uml.png)


Filecoin.PHP开发包的主要代码文件清单如下：

<table class="table table-striped">
  <thead>
    <tr><th>代码文件</th><th>说明</th></tr>
  </thead>
  <tbody>
    <tr><td>filecoin/src/FilKit.php</td><td>Filecoin.PHP开发包入口类</td></tr>
    <tr><td>filecoin/src/RpcClient.php</td><td>Filecoin节点RPC客户端类</td></tr>
    <tr><td>filecoin/src/Credential.php</td><td>Filecoin区块链身份凭证类</td></tr>
    <tr><td>filecoin/src/Address.php</td><td>Filecoin节点地址类</td></tr>
    <tr><td>filecoin/src/Leb128.php</td><td>Leb128编解码实现类</td></tr>
    <tr><td>filecoin/src/Base32.php</td><td>Base32编解码实现类</td></tr>
    <tr><td>filecoin/src/Blake2b.php</td><td>Blake2b哈希实现类</td></tr>
    <tr><td>demo/NewAddressDemo.php</td><td>演示代码，创建新的Filecoin区块链地址</td></tr>
    <tr><td>demo/RestoreAddressDemo.php</td><td>演示代码，使用已有私钥重新生成Filecoin地址</td></tr>
    <tr><td>demo/FilTransferDemo.php</td><td>演示代码，FILE转账和余额查询</td></tr>
    <tr><td>demo/RpcClientDemo.php</td><td>演示代码，查询指定地址的历史交易记录</td></tr>
    <tr><td>vendor</td><td>第三方依赖包目录</td></tr>
    <tr><td>composer.json</td><td>composer配置文件</td></tr>
  </tbody>
</table>

## 2、使用示例代码

### 2.1 创建新地址

在终端进入演示代码目录，执行如下命令：

```
~$ cd ~/filecoin.php/demo
~/filecoin.php/demo$ php NewAddressDemo.php
```

执行结果如下：

![](http://sc.hubwiz.com/codebag/filecoin-php-lib/img/demo-new-address.png)

### 2.2 利用私钥恢复地址

在终端进入演示代码目录，执行如下命令：

```
~$ cd ~/filecoin.php/demo
~/filecoin.php/demo$ php RestoreAddressDemo.php
```

执行结果如下：

![](http://sc.hubwiz.com/codebag/filecoin-php-lib/img/demo-restore-address.png)


### 2.3 FIL转账及余额查询

在终端进入演示代码目录，执行如下命令：

```
~$ cd ~/filecoin.php/demo
~/filecoin.php/demo$ php FilTransferDemo.php
```

执行结果如下：

![](http://sc.hubwiz.com/codebag/filecoin-php-lib/img/demo-fil-transfer.png)

### 2.4 RPC客户端调用示例

在终端进入演示代码目录，执行如下命令：

```
~$ cd ~/filecoin.php/demo
~/filecoin.php/demo$ php demo-rpc-client.php
```

执行结果如下：

![](http://sc.hubwiz.com/codebag/filecoin-php-lib/img/demo-rpc-client.png)

## 3、使用Filecoin.PHP

FilKit是开发包的入口，使用这个类可以快速实现FIL转账、交易确认等待和余额查询等功能。

### 2.1 实例化FilKit

FilKit实例化需要传入`RpcClient`对象和`Credential`对象，这两个
参数分别封装了Filecoin节点提供的API，以及进行交易签名的用户身份信息。

例如，下面的代码创建一个接入Infura的Filecoin节点的FilKit实例，并使用
指定的私钥进行交易签名：

```
use Filecoin\FilKit;
use Filecoin\RpcClient;
use Filecoin\Credential;

$client = new RpcClient(                          // 创建RPC客户端实例
  'https://filecoin.infura.io',                   // INFURA的filecoin节点URL    
  ['PROJECT_ID', 'PROJECT_SECRET']                // INFURA分配的项目ID和密码
);

$credential = Credential::fromKeyBase64(          // 利用已有私钥创建身份凭证
  'AacNySnfq9cdInB1ZUUvJJVTeqaI7LOW9EcX3UEDFfE='  // base64编码的私钥
);

$kit = new FilKit($client, $credential);          // 创建FilKit实例 
```

在创建FilKit实例时指定的Credential对象，将作为默认身份凭证执行
后续的转账交易等操作。

- __RpcClient / RPC客户端__

如果使用的Filecoin节点无需身份认证，那么在创建
RpcClient时只需传入RPC URL。例如使用本机的filecoin节点：

```
$client = new RpcClient('http://127.0.0.1/rpc/v0');  // 连接本机节点
```

如果使用的Filecoin节点启用了授权TOKEN的认证机制，那么在创建RpcClient
时需要传入授权TOKEN，例如：

```
$client = new RpcClient(
  'http://234.10.58.147/rpc/v0',                // 节点RPC API URL
  'Ynl0ZSBhcnJheQ=='                            // 节点分配的授权TOKEN
);
```

- __Credential / 身份凭证__

如果已有的私钥是16进制字符串形式，那么使用`Credential`类
的静态方法`fromKey()`来创建实例对象，例如：

```
$credential = Credential::fromKey(
  '01a70dc929dfabd71d22707565452f2495537aa688ecb396f44717dd410315f1'  // 16进制字符串格式的私钥
);
```


### 2.2 FIL转账交易

使用FilKit的`transfer()`方法进行FIL转账，例如发送 __1.23 FIL__ ：

```
$to = 'f1saxri7cpyz2cm767q77u3mqumrggljrmi5iqdty';          // 转账目标地址
$amount = '1230000000000000000';                            // 最小单位的转账数量，1 FIL = 10^18 UNIT
$cid = $kit->transfer($to,$amount);                         // 提交Trx转账交易
echo 'txid => ' . $cid->{'/'} .  PHP_EOL;                   // 显示交易ID
```

__注意__ ：

- 转账数量应转换为最小单位计量的整数字符串，1 FIL = 10^18 最小单位。
- 支持各种类型的接收地址，例如：
  - `f01729`：ID地址：
  - `f17uoq6tp427uzv7fztkbsnn64iwotfrristwpryy`：SECP256K1地址
  - `f24vg6ut43yw2h2jqydgbg2xq7x6f4kub3bg6as6i`：ACTOR地址
  - `f3q22fijmmlckhl56rn5nkyamkph3mcfu5ed6dheq53`：BLS地址

### 2.3 等待Filecoin消息确认

使用FilKit的`waitForReceipt()`方法等待交易确认，例如：

```
$receipt = $kit->waitForReceipt($cid);                      // 等待消息收据
echo 'exit code => ' . $receipt->ExitCode . PHP_EOL;        // 显示消息执行结果代码，0表示成功
```

默认的等待时间是60秒，在此时间内没有等到交易收据将提示错误。
可以传入第二个参数修改这一默认设置。例如等待10秒钟：

```
$receipt = $kit->waitForReceipt($cid, 10);                  // 等待10秒钟         
```

### 2.4 指定地址的FIL余额查询

使用`getBalance()`方法查询指定地址的FIL余额，例如：

```
$addr = 'f1saxri7cpyz2cm767q77u3mqumrggljrmi5iqdty';        // 要查询的Filecoin地址
$balance = $kit->getBlanace($addr);                         // 查询FIL余额，最小单位表示
echo 'balance => ' . $balance . PHP_EOL;                    // 显示FIL余额
```

__注意__ ：返回的余额为最小单位表示，1 FIL = 10^18最小单位。

### 2.5 使用RPC客户端

Filecoin节点透过其RPC API接口提供了很多有用的功能，使用Filecoin.PHP
开发包的RpcClient类可以访问所有的[Filecoin RPC API](http://sc.hubwiz.com/codebag/filecoin-php-lib/http://cw.hubwiz.com/card/c/filecoin-lotus-rpc/)。

例如查询当前的链头TipSet，对应的RPC API为[Filecoin.ChainHead](http://sc.hubwiz.com/codebag/filecoin-php-lib/http://cw.hubwiz.com/card/c/filecoin-lotus-rpc/1/2/12/)，
使用RpcClient对象的调用代码如下：

```
// $client = $kit->getClient();                           // 从FilKit得到RpcClient实例 
// or                                                     // 或者
// $client = new Client('http://127.0.0.1:1234/rpc/v0');  // 单独创建一个RpcClient对象

$ret = $client->chainHead();                              // 调用Filecoin.ChainHead API
echo 'height => ' . $ret->Height . PHP_EOL;               // 显示Height字段的值
```

从上面代码容易理解：

> Filecoin的RPC API名称去掉`Filecoin.`前缀，然后将剩余部分的首字符小写，就得到RpcClient的方法名。

如果RPC API的params数组包含多个参数，那么依次传入RpcClient的对应
方法即可。例如用Filecoin.ChainGetBlock调用[查询指定的区块](http://sc.hubwiz.com/codebag/filecoin-php-lib/http://cw.hubwiz.com/card/c/filecoin-lotus-rpc/1/2/3/)
时，需要传入区块的CID，使用RpcClient的调用如代码如下：

```
$cid = [                                                     // 要查询区块的CID               
  '/' => 'bafy2bzacea3wsdh6y3a36tb3skempjoxqpuyompjbmfeyf34fi3uy6uue42v4'
];                                                        
$block = $client->chainGetBlock($cid);                       // 调用 Filecoin.ChainGetBlock API    
echo 'miner => ' . $block->Miner . PHP_EOL;                  // 显示该区块的矿工地址 
```

----
原文链接：[Filecoin.PHP开发包 —— 汇智网](http://sc.hubwiz.com/codebag/filecoin-php-lib/)


