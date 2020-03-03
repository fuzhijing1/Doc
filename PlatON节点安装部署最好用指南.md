# PlatON节点安装部署手册

## 1. 节点安装部署

### 1.1 环境要求

- 硬件

| 需求项     | 建议配置       |
| ------    | ----------    |
| Cores     | \>=4        |
| CPU       | \>=2.4 GHz   |
| RAM       | \>=8GB       |
| DISK      | \>=500G      |

> 注意：
>
> - 表中DISK容量为基本配置，随着网络的长期运行，DISK实际需求量会逐步提高，建议采用可在线动态扩容的SSD磁盘

- 软件

PlatON节点目前完整测试过 Ubunt18.04 版本的操作系统，建议使用Ubunt18.04。本文档中的所有节点相关操作都以Ubunt18.04为基础。

- 网络

| 端口      | 服务名称       |
| ------    | ----------    |
| 443       | https         |
| 16789     | p2p           |

PlatON 的网络带宽要求大于等于20Mbps，同时云主机需要开放以上服务端口的外部访问权限。

- MTool 环境

MTool为节点管理工具，为方便操作，建议使用 Windows 10 操作系统或者ubuntu操作系统。本文档中的所有MTool相关操作分Windows和ubuntu两部分。

### 1.2 安装 PlatON

本文档主要介绍Ubuntu下安装部署节点的两种方式：ppa包和源码编译。

PlatON 的数据和日志默认保存在当前运行脚本的**用户的根目录下的platon-node目录**中，即：~/platon-node；请确保用户根目录有足够的空间存储这些文件；此目录用户可以自行修改，只需要打开platon_setup.sh脚本，修改一处：`node_dir=~/platon-node`为用户指定的目录即可；如果用户做了节点数据目录的修改，此后的相关脚本也需要做相应的修改。

#### 1.2.1 ppa方式安装

**step1.** 下载 platon_setup.sh 脚本

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/platon_setup.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/platon_setup.sh 
```

**step2.** 执行脚本

``` bash
chmod +x platon_setup.sh && ./platon_setup.sh --ppa 0.9.0 --testnet 
```

> 注意
>
> - 安装过程中会卸载旧版本的 platon，停止正在运行的 platon 进程，同时删除旧版本的数据。
> - --ppa表示使用ppa包进行安装部署。
> - 0.9.0为指定安装的ppa版本号，根据实际的版本号进行修改。
> - --testnet为测试网网络，不输入默认为主网网络。
> - 提示 `[sudo] password for` 时，请输入当前账户密码。
> - 提示 `Press [ENTER] to continue or Ctrl-c to cancel adding it` 时，请输入回车键。
> - 提示 `install platon and attach platon and get block number succeed` 时, 表示安装 PlatON 成功，未安装成功时，请通过我们的官方客户联系方式反馈具体问题。

#### 1.2.2 源码方式安装

**step1.** 安装编译环境（Ubuntu）：

- 系统版本：`Ubuntu 16.04.1 及以上`
- git：`2.19.1及以上`
- 编译器：`gcc(4.9.2+)`、`g++(5.0+)`
- go语言开发包：`go(1.7+)`
- cmake:`3.0+`

**step2.** 获取PlatON源码：

```bash
git clone https://github.com/PlatONnetwork/PlatON-Go.git --recursive
```

**step3.** 切到指定tag（实际需从公告获取）：

```bash
git checkout -b release-0.9.0 
```

>其中$tag_name为公告发布的tag名称。

**step4.** 安装依赖库：

```bash
sudo apt install llvm g++ libgmp-dev libssl-dev
```

**step5.** 编译：

```bash
cd PlatON-Go && make all
```

编译完成之后在`PlatON-Go/build/bin`目录下会生成`platon`等一系列可执行文件。

**step6.** 拷贝二进制：

```bash
sudo cp -f ./build/bin/platon /usr/bin/ && sudo cp -f ./build/bin/keytool /usr/bin/
```

**step7.** 下载 platon_setup.sh 脚本

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/platon_setup.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/platon_setup.sh
```

**step8.** 执行脚本

``` bash
chmod +x platon_setup.sh && ./platon_setup.sh --scc --testnet  
```

> 注意
>
> - 安装过程中会卸载旧版本的 platon，停止正在运行的 platon 进程，同时删除旧版本的数据。
> - --scc表示使用源码编译方式部署节点。
> - 提示 `[sudo] password for` 时，请输入当前账户密码。
> - 提示 `install platon and attach platon and get block number succeed` 时, 表示安装 PlatON 成功，未安装成功时，请通过我们的官方客户联系方式反馈具体问题。


当platon启动成功后，正常情况下会通过节点发现协议自动和距离自己最近的节点建立连接，连接成功后会启动区块同步，所以判断加入网络是否成功可以通过查看节点的peers同时确认当前节点块高是否增长来判断。

##### 进入`platon`控制台

通过`http`方式进入`platon`控制台

```
platon attach http://localhost:6789
```

##### 查看节点的peers

在`platon`控制台中通过执行以下命令查看当前节点连接的所有peers。

##### 查看当前块高

通过在`platon`控制台中执行以下命令查看当前节点ID。

```
> admin.nodeInfo.id
"5426b0daf7cc39c2b485415b627e61e7b0023bcba18e3c0e268731f33f97793e466c0d81dc4c466860726c5725a37b97c59de5ccb48ecd656d1854d810bbabb4"
>
```

##### 查看当前块高

通过在`platon`控制台中执行以下命令查看当前节点的块高。

```
> platon.blockNumber
2235
>
```

节点列表中出现一系列测试网络节点并且块高在不断增长，则表示连接成功！

### 1.3 配置 nginx

安全考虑，不建议节点 rpc 端口对外直接开放。可以考虑使用 Nginx 进行反向代理，并通过用户鉴权和 HTTPS 加强 Nginx 端口的安全性。如果用户在[安装PlatON](#1.2 安装 PlatON)做了节点数据目录的修改，nginx_conf.sh脚本也需要修改成相同的节点数据目录。 Nginx 配置步骤如下：

**step1.** 下载 nginx_conf.sh

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/nginx_conf.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/nginx_conf.sh
```

**step2.** 执行脚本

``` bash
chmod +x nginx_conf.sh && ./nginx_conf.sh
```

> 注意
>
> - 提示 `[sudo] password for` 时，输入当前账户密码。
> - 提示 `Enter your name:` 时，输入用户名，提示 `Enter your password:` 时，输入密码。务必牢记用户名和密码，后续MTool 配置验证节点信息时需要填写。
> - 提示 `ngnix conf succeed` 时，表示配置 nginx 成功，未配置成功时，请通过我们的客户联系方式反馈具体问题。

## 2. 接入测试网 

### 2.1 验证节点概述

PlatON 是实行民主治理的区块链项目，验证节点由所有 Energon 持有者共同推选，以维护和发展 PlatON 网络。得票数最多的101名节点将成为备选节点，从中用 VRF 随机选出25个验证节点，参与管理整个 PlatON 网络。

- 质押100万 Energon 成为备选节点候选人，并可接受委托
- 每个 Staking 周期期初，总票数（包括节点自质押和其他人的委托）排名前 101 名的节点，成为当前Staking周期的备选节点
- 每个共识周期（250个区块一轮）的第230个区块，从当前 Staking 周期的101名备选节点中, 按总得票数权重随机选出 25 名验证节点来生产区块

### 2.2  安装前准备

**MTool支持Windows和Ubuntu，用户可根据自己的资源进行选择，下面进行分别说明：**

#### 2.2.1  Windows下安装MTool前准备
 下载MTool安装包

官方提供的下载地址有2个：

1、https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/mtool-setup/0.8.0.0/mtool-setup.exe

2、 http://47.91.153.183/mtool/mtool-setup/0.8.0.0/mtool-setup.exe 

我测试均能下载成功。第一个适合海外用户或者科学上网用户，第二个适合国内用户，请大家谨慎选择。



 安装MTool

完成下载后双击mtool-setup.exe进行安装。

默认安装目录为 C:\tools，**建议不要更改此安装目录**。

弹出界面显示**Completing the mtool Setup Wizard**信息表示安装成功，点击**Finish**即可。



官方文档说要配置%MTOOLDIR%这个环境变量，我没有配，依然不影响使用，应该是安装程序默认进行了设置。


#### 2.2.2  Ubuntu下安装MTool前准备

执行命令：

```bash
mtool-client --version
```

执行结果显示`mtool-client: command not found`，表示没有安装旧版本不需要执行下面操作。

执行结果显示版本号，时间戳等信息表示安装了旧版本，此时需要备份重要信息，操作步骤：

**step1.** 执行以下命令，获取旧版本mtool的工作目录：

```bash
which mtool-client | xargs ls -l | awk -F'->' '{print$2}' | awk -F'/bin/mtool-client' '{print$1}'
```

返回旧版本的mtool的工作目录`$old_mtool`；新版本的mtool的工作目录假设为`$new_mtool`：

**step2.** 备份`$old_mtool/keystore`目录下的所有文件备份到其他地方，安装完新版本之后需要将备份文件拷贝回 `$new_mtool/keystore`目录下。

**step3.** 备份`$old_mtool/validator`目录下的所有文件备份到其他地方，安装完新版本之后需要将备份文件拷贝回 `$new_mtool/validator`目录下。

**step4.** 删除旧版本的mtool的工作目录`$old_mtool`。

### 2.3 安装 MTool

MTool 是一个命令行版本的节点管理工具，可以方便地发起质押等交易，MTool对质押等交易提供两种签名方式：在线签名和离线签名。

- 在线签名相对比较方便，本文档主要介绍在线签名操作过程
- 对安全性要求比较高的用户可以参考[离线MTool使用手册.md](./离线MTool使用手册.md)使用离线签名。

另外，本文档分别介绍 Windows 和Ubuntu环境下的MTool操作，用户可根据自己的资源进行选择；如果下载脚本失败，请设置DNS 服务器为8.8.8.8。

#### 2.3.1  Windows下安装MTool

步骤如下：

**step1.**  在[安装前准备](#22-安装前准备)步骤调出的管理员：powershell 窗口中，复制以下2条命令执行。

```bash
$env:chocolateyUseWindowsCompression = 'true'
Set-ExecutionPolicy -ExecutionPolicy Bypass
```

提示：

```plain
执行策略更改
执行策略可帮助你防止执行不信任的脚本。更改执行策略可能会产生安全风险，如 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies 帮助主题所述。是否要更改执行策略?
[Y] 是(Y)  [A] 全是(A)  [N] 否(N)  [L] 全否(L)  [S] 暂停(S)  [?] 帮助 (默认值为“N”):
```

请输入：y，并按回车键结束。

**step2.** 浏览器复制链接 <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/mtool_install.bat> 或者 <http://47.91.153.183/opensource/scripts/mtool_install.bat> 下载脚本

**step3.** 鼠标右键点击 mtool_install.bat， 选择以管理员身份运行

> 注意
>
> - 提示 `Please enter the version number of MTool:` 时，请输入安装MTool的版本号，具体参考公告发布的MTool的版本号。
>
> - 提示 `install MTool success` 时，表示 MTool 安装成功，未安装成功时，请通过我们的官方客户联系方式反馈具体问题。
> - 提示 `请按任意键继续. . .` 时，请输入回车键关闭当前 cmd 窗口。

#### 2.3.2  Ubuntu下安装MTool

步骤如下：

**step1.** 下载mtool工具包：(实际mtool版本的链接地址需从公告获取)

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/0.9.0.X/mtool-all.zip
```

或者

``` bash
wget http://47.91.153.183/mtool/0.9.0.X/mtool-all.zip
```

>其中0.9.0.X为mtool的版本号，实际版本号需从公告获取。

**step2.** 解压mtool工具包

``` bash
unzip mtool-all.zip && cd mtool-all
```

**step3.** 下载脚本

**注意：脚本下载到mtool_all目录下，否则脚本无法找到新版本mtool的路径；**

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/mtool_install.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/mtool_install.sh
```

**step4.** 执行命令

```
chmod +x mtool_install.sh && ./mtool_install.sh
```

> 注意：
>
> - 提示 `Install mtool succeed.` 时，表示 MTool 安装成功，未安装成功时，请通过我们的官方客户联系方式反馈具体问题。
> - 安装完成之后，需要**`重启终端`**，让新添加的环境变量生效。

### 2.4 创建钱包

PlatON中，参与验证节点进行出块要创建两个钱包。

其中Windows和Ubuntu下MTool的命令及目录有所区别：

- MTool命令

  - Windows：mtool-client.bat
  - Ubuntu：mtool-client

- MTool目录

  - Windows：%MTOOLDIR%

  - Ubuntu：$MTOOLDIR

>  说明：
>
>   - MTool命令用变量$mtool-client代替；
>   - MTool目录用变量$MTOOLDIR代替；
>
>  **`用户根据自己安装的系统进行选择。`**

- 质押钱包
  质押钱包用于质押 token，质押成功后才能成为备选节点候选人。
  打开终端，运行以下命令创建质押钱包：
  
  ``` shell
  $mtool-client account new staking
  ```

  输入一次密码，再输入一次确认密码，即可创建钱包文件，创建成功后会在目录`$MTOOLDIR/keystore`下生成质押钱包文件`staking.json`。

- 收益钱包
  用于收取区块奖励和Staking奖励，Staking奖励统一发放给验证节点，由验证节点自行分配。
  运行以下命令创建收益钱包：
  
  ``` shell
  $mtool-client account new reward
  ```

  输入一次密码，再输入一次确认密码，即可创建钱包文件，创建成功后会在目录`$MTOOLDIR/keystore`下生成质押钱包文件`reward.json`。

### 2.5 配置验证节点信息

根据用户在Windows或Ubuntu上安装的MTool，选择对应系统上的验证节点信息配置：

#### 2.5.1  Windows下配置验证节点信息

操作步骤如下：

**step1.** 浏览器复制链接 <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/validator_conf.bat> 或者 <http://47.91.153.183/opensource/scripts/validator_conf.bat> 下载脚本

**step2.** 鼠标右键点击 validator_conf.bat， 选择以管理员身份运行

> 注意
>
> - 提示 `Please enter the platon node IP address:` 时，请输入 PlatON 节点服务器 ip 地址。
> - 提示 `Please enter the platon chain id:` 时，请输入链ID。
> - 提示 `Enter your name:` 时，请输入配置 nginx 时输入的用户名。
> - 提示 `Enter your password:` 时，请输入配置 nginx 时输入的密码。
> - 提示 `Enter your platon node name:` 时，请输入 PlatON 节点的名称。
> - 提示 `Enter your platon node description:` 时，请输入 PlatON 节点描述。
> - 提示 `validator conf success` 时，表示脚本执行成功，未执行成功时，请通过我们的官方客户联系方式反馈具体问题。
> - 提示 `请按任意键继续. . .` 时，请输入回车键关闭当前 cmd 窗口。
> - 请到 `C:/tools/mtool/current/validator` 目录下，查看 validator_config.json 内容，是否有异常。

#### 2.5.2  Ubuntu下配置验证节点信息

操作步骤如下：

**step1.** 下载脚本：

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/validator_conf.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/validator_conf.sh
```

**step2.** 执行命令：

```bash
chmod +x validator_conf.sh && ./validator_conf.sh
```

> 注意
>
> - 提示 `Please enter the platon node IP address:` 时，请输入 PlatON 节点服务器 ip 地址。
> - 提示 `Please enter the platon chain id:` 时，请输入链ID。
> - 提示 `Enter your name:` 时，请输入配置 nginx 时输入的用户名。
> - 提示 `Enter your password:` 时，请输入配置 nginx 时输入的密码。
> - 提示 `Enter your platon node name:` 时，请输入 PlatON 节点的名称。
> - 提示 `Enter your platon node description:` 时，请输入 PlatON 节点描述。
> - 提示 `validator conf success` 并最后打印出的validator_config.json内容正常时，表示脚本执行成功，未执行成功时，请通过我们的官方客户联系方式反馈具体问题。

### 2.6 申请质押资金

质押资金 LAT 要向 PlatON 官方申请，请发送邮件到 PlatON 官方邮箱 <support@platon.network> ，格式如下：

``` plain
【测试网-LAT申请】

申请人/申请组织/机构：{XXX}

|  节点ID  |   节点名称  | 节点IP和端口  | 质押账户地址  | 收益账户地址  |
| -------- | ---------- | ------------ | ------------ | ------------ |
| {nodeId} | {nodeName} | {ip}:{port}  | {address}     | {address}    |
```

- nodeId：在 `$node_dir/data/nodeid` 中获取，$node_dir为用户自己设置的节点数据目录。
- nodeName：为 PlatON 节点所有者为节点取的名称
- ip：节点的IP地址
- port：节点端口号，默认值为 16789
- address：质押钱包地址和收益钱包地址，MTool 创建完钱包之后，可以在钱包文件中获取。钱包文件中 `address` 字段加上0x即为有效地址。

申请提交之后，请耐心等待，可在 PlatON 官方区块链浏览器 [PlatScan](https://platscan.platon.network) 主页面搜索框输入质押账户地址，确认官方发放的 token 是否已到账。

### 2.7 验证节点质押

利用节点工具 MTool 执行节点质押与委托等操作，对于有能力的开发者，可以在 Java SDK 和 Javascript SDK 基础上面开发自己的节点工具，SDK请参考文档。

- [Java-SDK](./Java-SDK.md)
- [JavaScript-API](./JavaScript-API.md)

质押资金申请到账后，确保质押账户余额充足，根据用户情况替换{质押金额}，质押最低门槛为100万LAT。

``` shell
$mtool-client staking --amount {质押金额} --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

例如：

``` shell
$mtool-client staking --amount 1000000 --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

> 注意
>
> - 提示 `please input keystore password:` 时，输入质押钱包的密码。
> - 提示 `operation finished` 并且提示 `SUCCESS` 表明质押成功。

### 2.8 验证节点确认

完成质押操作后，可在 PlatON 官方区块链浏览器 [PlatScan](https://platscan.platon.network) 上点击主页上方验证节点列表，查看所有验证节点，或者在输入框输入节点名称查询节点具体信息，查看是否成为验证节点。

## 3. 数据备份

当验证人节点的数据不一致导致链无法正常运行时，需要验证节点将数据回滚到最近达成共识状态的区块，验证节点可以使用社区提供的备份数据。

**从实时性和安全上考虑，建议有条件的节点自行搭建备份节点。数据备份配置步骤如下：**

**step1.** 按照[节点安装部署](#1-节点安装部署)另行安装一个备份节点

**step2.** 下载 timing_backup.sh 脚本

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/timing_backup.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/timing_backup.sh
```

**注意：如果用户在[安装PlatON](#1.2 安装 PlatON)做了节点数据目录的修改，timing_backup.sh脚本也需要修改成相同的节点数据目录。**

**step3.** 执行以下脚本

``` bash
chmod +x timing_backup.sh && ./timing_backup.sh --testnet
```

> 注意
>
> - --testnet为测试网网络，不输入默认为主网网络。
>
> - 提示 `[sudo] password for` 时，输入当前账户密码。
> - 提示 `add backup_task.sh to crontab succeed` 时，表示配置数据库备份定时任务成功，未配置成功时，请通过我们的官方客户联系方式反馈具体问题。
> - `crontab -l` 查看有 `0 0 * * * $node_dir/backup_task.sh`字段, 其中 `$node_dir` 替换为用户的节点目录，表示成功。
> - 数据库备份定时任务每日0点执行，备份文件生成在 `$node_dir/data`目录下。

## 4. 紧急情况处理

请实时关注社区最新消息，底层链的重大事情会第一时间在社区公布。
