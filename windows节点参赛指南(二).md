## windows节点参赛指南(二)

### 安装节点工具

我这里只试了这一种方式，其他方式，请大家去参阅官方文档

#### 下载MTool安装包

官方提供的下载地址有2个：

1、https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/mtool-setup/0.8.0.0/mtool-setup.exe

2、 http://47.91.153.183/mtool/mtool-setup/0.8.0.0/mtool-setup.exe 

我测试均能下载成功。第一个适合海外用户或者科学上网用户，第二个适合国内用户，请大家谨慎选择。



#### 安装MTool

完成下载后双击mtool-setup.exe进行安装。

默认安装目录为 C:\tools，**建议不要更改此安装目录**。

弹出界面显示**Completing the mtool Setup Wizard**信息表示安装成功，点击**Finish**即可。



官方文档说要配置%MTOOLDIR%这个环境变量，我没有配，依然不影响使用，应该是安装程序默认进行了设置。



<!--以下命令均需在`Git-bash`环境运行！-->


### 生成质押和收益钱包文件

####       生成质押钱包

用于质押 token，节点质押成功之后才能成为备选节点候选人，有机会被选中成为验证人参与共识。

   在命令行下运行以下命令生成质押钱包：

  ```
  $mtool-client account new staking
  ```

  输入一次密码，再输入一次确认密码，即可创建钱包文件，创建成功后会在目录`C:\tools\mtool\current\keystore`下生成质押钱包文件`staking.json`。

  

  #### 生成收益钱包

  

用于收取区块奖励和质押奖励，质押奖励统一发放给节点，由节点自行分配。

在命令行下运行以下命令生成收益钱包：

  ```
  $mtool-client account new reward
  ```

  输入一次密码，再输入一次确认密码，即可创建钱包文件，创建成功后会在目录`C:\tools\mtool\current\keystore`下生成质押钱包文件`reward.json`。

  *注意：千万要保存好私钥和不要忘记密码！！！*

  

  ### 配置验证节点信息

官方提供了自动配置节点信息的脚本和说明，但是里面涉及到Nginx的配置，而且Nginx的配置只提供了ubuntu的说明，所以我这边就只补充手动配置节点信息的说明如下；

#### 手动配置节点信息

安装完节点工具MTool之后，自带了一个节点配置文件的示例/c/tools/mtool/current/validator/validator_config.json.example

内容如下：

```
{
  "chainId": "101",
  "nodePublicKey": "d02b9ab6aabbb086501eac03e43c32e341f13633e6e26313c25f83c9d54baa9ab43e316c58bc1b8ccf4378ec54b22e2d1a18f45345825b694aae759a43106b38",
  "blsPubKey": "347e64a50be45850563c5dcc155fb36e8642c8f077464d147974551558a119ab9d78cc69457e67d00254da8147743b006da1c33609cb8fb15e214b59ef602300878d614403da50f58b44e1ba2f36741b0762bc6da55c02e76cc1ec4d0c983305",
  "benefitAddress": "0xe9ac6a4b219d2a98d624ffee86e2df60527e1f2b",
  "nodeAddress": "http://18.238.183.12",
  "nodePort": "16789",
  "nodeRpcPort": "6789",
  "nodeName": "Satoshi-Nakamoto",
  "details": "My Node Description",
  "externalId": "MyKeyBaseId",
  "webSite": "http://www.mycompany.com",
  "certificate": "/path/to/ssl_ca",
  "delegatedRewardRate": 10000
}
```
这个文件后面所有对于节点的操作都非常重要，现在对里面的关键字段进行说明：

**贝莱世界的chainId为101**

**nodePublicKey就是上篇生成的节点公钥**

**blsPubKey就是上篇生成的BLS公钥**

**benefitAddress就是刚刚生成的收益钱包地址**

**nodeAddress就是上篇启动节点的时候本机的公网IP地址，可以在百度搜索里面输入：“IP地址”来查看**

**nodePort，nodeRpcPort这两个默认不需要改变**

**nodeName、details、externalId、webSite按需填写**

**certificate是为了nginx配置使用的，如果没有可以删除**

**delegatedRewardRate用来设置给委托人分红的比例**



### 通过质押成为验证人

#### 发起质押操作

 如果节点部署完成，并且已经同步区块成功，就可以进行质押操作了。

注意需要确保质押账户余额足够，根据用户情况替换{质押金额}，官方给出的质押最低门槛为100万LAT。

- 在命令行执行以下命令

```
$mtool-client staking --amount 1000000 --keystore /c/tools/mtool/current/keystore/staking.json --config /c/tools/mtool/current/validator/validator_config.json
```

提示：**please input keystore password:** 输入质押钱包的密码，然后回车，如果显示如下信息则代表质押成功：

```
operation finished
transaction hash:
0x89b964d27d0caf1d8bf268f721eb123c4af57aed36187bea90b262f4769eeb9b
SUCCESS
```

- 参数说明

> amount: 质押数，不少于1000000lat-质押门槛，小数点不超过8位
>
> restrictedamount: 不少于1000000lat-质押门槛，小数点不超过8位（使用锁仓余额质押）



如果上述步骤都完成，恭喜你，你现在可以去官方的区块链浏览器查看你的节点信息了：

https://platscan.test.platon.network/node



未完待续！