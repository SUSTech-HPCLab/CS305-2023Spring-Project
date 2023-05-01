# CS305 2023 Spring Project

**请仔细阅读本文档！** 我们非常努力地使本文档尽可能清晰，并尽可能涵盖我们在测试中遇到的所有问题。然而，我们仍然可能在本文档中遗漏重要的细节。如果有什么不清楚的地方，你应该在这个仓库中提交Issues，或者立即联系老师和SA，而不是猜测你需要做什么。
## 背景描述
**SDN**: SDN是一种新型的网络范式。正如你在本课程中所学到的，网络被分为控制平面和数据平面。控制平面是一组协议和配置，用于设置转发相关的设备（主机、交换机和路由器），以便它们能够转发数据包。例如，这包括ARP解析、DNS、DHCP、生成树协议、NAT，以及所有的路由协议。SDN最重要的特点就是控制平面和数据平面的分离。将控制逻辑集中在一个中心化的控制器中，由控制器对网络流量进行控制和管理。传统网络则将控制逻辑分布在各个网络设备中。而我们在Project中需要编写的就是一个中心化的控制器。
为了构建SDN的开发环境，我们需要使用以下两个组件。

**Mininet**: Mininet是一个广泛使用的网络仿真器，它可以在Linux主机上创建任意的虚拟网络环境。为了教学或软件验证的方便，开发者们通常使用Mininet构建虚拟的网络拓扑。开发者可以随意在网络中添加虚拟主机、虚拟交换机等网络资源并对自己的SDN控制器进行测试。

**Ryu**: Ryu是一个用于构建SDN控制器的开源框架。当我们通过mininet构建好虚拟SDN网络之后，我们利用Ryu来编写并部署SDN控制器。Ryu控制器可以与mininet中的虚拟交换机进行通信，从而控制虚拟网络的行为。下图展示出了Ryu和Mininet的架构。Ryu通过监控switch中的网络流量来做出相应动作（例如如何forward），而Mininet负责实际的网络流量的传输。

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/arch.png" width="30%"/>
</p>

在这个project中，我们编写一个支持两种主要功能的Ryu controller:
- 作为简易的DHCP服务器
- 实现最短路Switching算法

**注意:** 我们将使用mininet构建不同结构的网络拓扑来测试你所编写的Ryu controller。因此，你需要确保你的代码在使用自定的网络拓扑结构的情况下也能正常工作。

## 环境搭建
环境搭建主要分为两个步骤，首先是Mininet的安装，其次是安装我们提供的实验框架（包含Ryu)。

### 安装Mininet 
Mininet需要在Linux环境中运行。我们强烈建议在个人电脑上安装虚拟机，并在虚拟机中安装Mininet。
#### Windows和其他amd64结构用户配置指南
1. 安装VMware或者VirtualBox
2. 直接下载Mininet官方配置好Mininet组件的的Ubuntu镜像[mininet-2.3.0-210211-ubuntu-20.04.1](https://github.com/mininet/mininet/releases/download/2.3.0/mininet-2.3.0-210211-ubuntu-20.04.1-legacy-server-amd64-ovf.zip)
3. 下载完成后解压双击ovf即可自动呼叫VMware等虚拟机软件引导创建
4. 创建完成后启动虚拟机，输入用户名`mininet`和密码`mininet`即可登录
你也可以参考这篇指南中安装虚拟机的部分。

#### macOS ARM用户配置指南
如果您使用的是M1或者其他苹果芯片，请务必按照以下步骤配置：
1. 安装VMware Fusion或者Parallel Desktop
2. 安装Ubuntu 20.04.01 ARM版本(和芯片架构保持一致，建议搜索macOS m1安装Ubuntu Server 20.04)
3. 配置虚拟机并运行
4. 在虚拟机中安装Mininet
```
sudo apt-get update
sudo apt-get install mininet
```
5. 在虚拟机中安装python，pip和git
```
sudo apt-get install python3 python3-pip git
```
#### 检查Mininet是否安装成功
在虚拟机中打开你的终端(命令行)，输入如下命令，检测Mininet是否配置成功。
```
sudo mn --test pingall
```
当你看见类似的输出则证明Mininet的环境配置完成。

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/mininet_success.png" width="50%"/>
</p>

**Mininet必须在root身份下执行。务必保证使用的时候使用了sudo或直接在root身份下运行**

#### 安装实验框架
由于Ubuntu默认的Python版本过高，因此我们需要使用miniconda安装Python3.8的环境。
如果你是windows下的AMD 64Ubuntu用户，你可以直接使用以下指令安装miniconda。
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh -b -p ${HOME}/software/miniconda3
echo "export PATH=${HOME}/software/miniconda3/bin:\$PATH" >> ~/.bashrc
source ~/.bashrc
conda init bash
source ~/.bashrc
conda create -n cs305 python=3.8
conda activate cs305
python --version
```

如果你是macos下的ARM Ubuntu用户，你可以直接使用以下指令安装miniconda。
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh
sh Miniconda3-latest-Linux-aarch64.sh -b -p ${HOME}/software/miniconda3
echo "export PATH=${HOME}/software/miniconda3/bin:\$PATH" >> ~/.bashrc
source ~/.bashrc
conda init bash
source ~/.bashrc
conda create -n cs305 python=3.8
conda activate cs305
python --version
```
安装完Python环境后你需要安装本次Project的实验框架。

本次Project仓库位于[CS305-2023Spring-Project](https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project)。
你可以下载Zip文件或者clone这个仓库。
下载好源代码之后通过如下指令安装Python包依赖。
```
conda activate cs305
git clone https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project.git
cd CS305-2023Spring-Project
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple 

# Check if Ryu is installed successfully
ryu-manager --version
# If you see the version information of ryu-manager, the installation is successful.
```

## 任务描述
本次Project的基础部分包含两个部分:一个是简易的DHCP server。另一个是实现最短路switching算法。为了简化实验，我们对网络拓扑结构进行了如下的限制。
- Mininet中只包含L2交换机和Host（主机）。这意味着我们的网络是一个大型局域子网。无需考虑多子网的情况。
- Mininet中一个Host只会和一个交换机相连。
### 简易DHCP Server
DHCP: Dynamic Host Configuration Protocol,中文为动态主机设置协议。主要目的是满足内部网或网络服务供应商自动分配IP地址给用户的需求。

尽管Mininet在默认情况下会自动地给每一个host分配一个ip。我们会在测试脚本中关闭mininet的ip初始化。你可以参考DHCP的协议标准[RFC 2131](https://www.rfc-editor.org/rfc/rfc2131)来实现一个功能丰富完备DHCP server。无论如何，你只需要做到：

- **在host加入子网时，你设计的controller能够识别到dhcp packet并分配一个合法的IP地址给host**

在下个章节我们会介绍如何完成这个任务以及如何测试自己是否成功实现了DHCP server。

### 最短路Switching
你的任务是建立一个全局最短路径交换表，并在交换机上安装转发规则以实现这些路径。你将根据控制器controller收集的全局拓扑信息，在控制器上建立这个表。**以达到任意两个host之间的数据传输路径为最短路径。**

与传统的L2(Layer-2)交换机或L3(Layer-3)路由器不同，SDN交换机没有专门的MAC学习表(MAC-learning)或路由表。相反，SDN交换机使用一个更通用的流表结构，可以取代这些和其他结构。流表中的每个条目或规则都包含一组匹配标准（基于以太网、IP、TCP、UDP和其他标头的字段），选择特定的数据包，并包含对每个匹配规则的数据包应采取的一系列行动(action)。
你设计的Switching模块应该做到：首先匹配目标MAC地址(dest MAC), 根据匹配规则执行对应的Action，能够让数据包从正确的端口发送出去以到达目的地。

**如果你对action，flowtable等名词感觉陌生，请参考课程slides，教科书或查阅Ryu的文档和Openflow协议的相关信息。**

匹配规则的作用与传统路由表中的目的地和掩码字段相同，而action的作用与传统路由表中的接口(interface)字段相同，都表明了数据包该发到哪里去。需要注意的是你的拓扑结构不受限于树状结构，因为你收集到了全部交换机的信息，循环不应该是一个问题。事实上，你必须测试你的switching能不能在有环路的拓扑结构上有效。
为了计算最短路径，你应该使用Bellman-Ford算法或Djikstra算法来计算从每任意两个host之间的最短路径。确定了从host A到达host B的最短路径后，控制器必须向路径中的每个交换机安装流量表中的规则和相应的动作。当拓扑结构发生变化时，你应该更新受影响的路径规则。
## 实现与测试
在这个章节中，我们将结合实验框架代码给大家介绍实现上述功能的思路。并告诉大家如何进行测试。
### 实验框架
我们提供了一些初始文件来帮助你们快速开始开发功能。项目的结构如下所示
```
├── controller.py  # The main file of the controller
├── dhcp.py   # Implement DHCP server here
├── ofctl_utilis.py # Don't need to modify this file, it provides useful functions for building and sending packets
├── requirements.txt 
└── tests
    ├── dhcp_test
    │   └── test_network.py
    └── switching_test
        └── test_network.py
```

- controller.py：这个文件是项目的入口，你应该在这个文件中实现监控SDN网络中网络组件的增添，删除以及经过交换机的数据包流量。并根据收集到的信息触发DHCP功能或最短路switching功能
- dhcp.py: DHCP的实现细节应该在这个文件中被呈现。controller.py 通过调用dhcp.py的相关函数触发dhcp功能。
- tests: 为测试dhcp和switching功能编写的用于构建mininet网络的脚本。
### 实现简易DHCP
#### 过程描述
在SDN中实现简易的DHCP包括了如下过程:
1. Host在加入网络时广播发送DHCP DISCOVER packet
2. Controller接收到DHCP DISCOVER packet后，选择一个空闲IP，构建DHCP OFFER packet发送回Host
3. Host在收到OFFER packet后，广播DHCP REQUEST信息。确认所选择的DHCP server配置。
4. Controller收到DHCP REQUEST信息后，构建DHCP ACK packet并发送回Host。

**其中第一步和第三步由已经在测试脚本中实现了，你需要关注第二和第四步的实现。**

#### 接收DHCP协议包
在`controller.py`文件中我们提供了接收DHCP协议包的相关代码。这个函数会在数据包进入switch时被调用。`Datapath`在这里是接收到数据包的switch。`inPort`是数据包传入的端口。如果这个数据包可以被dhcp协议解析，我们调用`DHCPServer.handle_dhcp`函数进行处理。如果不能被dhcp解析，你应该进行判断是否是别的协议包，并针对不同的协议作出不同的处理。
```
@set_ev_cls(ofp_event.EventOFPPacketIn, MAIN_DISPATCHER)
    def packet_in_handler(self, ev):
        try:
            msg = ev.msg
            datapath = msg.datapath # switch
            pkt = packet.Packet(data=msg.data)
            pkt_dhcp = pkt.get_protocols(dhcp.dhcp)
            inPort = msg.in_port
            if not pkt_dhcp:
                # TODO: handle other protocols like ARP 
                pass
            else:
                DHCPServer.handle_dhcp(datapath, inPort, pkt)      
            return 
        except Exception as e:
            self.logger.error(e)
```
#### 构建DHCP协议包
你需要在`dhcp.py`文件中的`handle_dhcp`函数中分辨接收的DHCP数据包类型。根据传入的数据包类型决定发送DHCP OFFER packet还是DHCP ACK packet。在选择合法IP时，你需要结合`dhcp.py`文件中的`Config`类中规定的 `start_ip`，`end_ip`，`netmask`这三个属性。这三个属性的共同决定了子网的大小——你可以分配的IP的数量。详情可以查看dhcp.py文件中的注释。
#### 测试DHCP功能
假设在project的目录中，首先在一个terminal中执行如下命令
```
ryu-manager --observe-links controller.py 
```
新建另一个terminal，在新的terminal中执行如下命令
```
cd ./tests/dhcp_test/
sudo env "PATH=$PATH" python test_network.py # share the PATN env with sudo user
```
我们在dhcp.py文件的默认设置是从192.168.1.2开始分配IP。我们在执行test_network.py的terminal中输入`h1 ifconfig`和`h2 ifconfig`指令即可查看是否为这两台host分配好IP。只要出现了下图的内容，我们就认为基础的简易DHCP功能实现完成了。
<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/dhcp_success.png" width="50%"/>
</p>  

### 实现最短路Switching

我们可以利用SDN架构，在没有广播的情况下进行最短路径switching，具体如下：

- 当一个交换机被添加或删除以及交换机之间的链接被建立或删除时，网络拓扑结构将发生变化，这意味着最短路径也将发生变化。相应地，你应该更新受影响的交换机上的流表，以确保数据包总是沿着交换机之间的最短路径传输。为了实现这个功能，你可能需要创建一个抽象的数据结构来计算交换机之间的距离。
-  像普通的网络架构一样，当主机想发送一个数据包时，它会查询它的路由表，以确定目的地是否在同一个子网中（无需考虑这种情况，我们的Project中只有一个子网）。这意味着主机将把数据包作为一个以太网帧发送到IP目的地，目的地的MAC地址（而不是网关或路由器的MAC地址而是下一跳的交换器的MAC地址）。如果主机不知道目的地的MAC地址，它会发出一个ARP请求
- 当交换机收到ARP请求时，它将把请求作为PacketIn消息发送给controller，而不是广播它
- controller将收到PacketIn消息，并查找目标主机的MAC地址，然后生成一个响应（在PacketOut消息内），供交换机发回给发送方主机。
- 收到响应后，主机将发送IP数据包到目的地的MAC地址。
- 在指向目的地的路径上的每个交换机上，数据包将在目的地MAC地址上匹配，并在正确的端口上转发。

为了让controller知道每台主机的MAC地址，我们必须建立一个协议，让主机告知控制器其地址。对于这项任务，我们要求主机在连接时发送一个不请自来的ARP回复（也称为 "无偿ARP"，或arping），以告诉网络它的MAC和IP地址--我们已经配置Mininet在启动模拟网络时自动这样做（你可以在tests/switching_test/test_network.py中查看）。
最后，由于我们没有广播ARP消息，所有的ARP请求将被发送到控制器。当你收到一个ARP请求时，你应该产生一个适当的响应，以便主机可以填充它的ARP表。


#### 测试最短路Switching
我们在`tests/switching_test/test_network.py`中提供了一个测试网络。它的网络拓扑如下。

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/topo_example.png" width="50%"/>
</p>       

在`test_network.py`中构建了一个三角网络。它首先会在网络中添加host, switch, link， 你需要利用OpenFlow协议监控这些事件，当这些事件发生时，你需要在控制器中进行相应的处理来实现最短路switching。当所有的组件（host,switch,link）初始化完毕后，我们在每一个host上执行`arping`命令。你需要识别这些`arping` packet并告知host如何确定目的地MAC。在这个测试中，你可以使用mininet cli中的指令`pingall`来检测网络的连通性。
在这个网络中，h1到h2的最短路是h1->s1->s2->h2。h1到h3的最短路是h1->s1->s3->h3。任意两个host之间的数据传输所经过的switch数量应该不超过两个。

在project的目录中，首先在一个terminal中执行如下命令
```
ryu-manager --observe-links controller.py 
```
新建另一个terminal，在新的terminal中执行如下命令
```
cd ./tests/switching_test/
sudo env "PATH=$PATH" python test_network.py 
```
大约两秒之后，你会发现你在第二个terminal中进入了mininet cli。
**你应该在这里输入`pingall` command来测试你的网络的连通性。** **为了方便助教检查你们的代码，请在controller中实现展示最短路径的功能**。下图是一个展示最短路径的例子。它在`pingall`指令之后在第一个terminal中展示出了任意两个host之间的路径及其长度。这里的distance为3，指的是h1->s1->s3->h3的路径长度为3(3条边)。


<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/path_result.png" width="50%"/>
</p>   


你在第二个terminal中会看到下图的结果。这表明没有丢包出现，网络是连通的。

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/ping_result.png" width="50%"/>
</p>   

## 评分
你需要在第16周的实验课上演示你的项目。展示完你的项目后，你需要提交：

- `report.pdf` - 请清楚地说明你的项目的架构，并描述你所做的实施细节。如果需要，请添加截图或代码。你需要提供一个复杂的测试样例来证明你的程序的鲁棒性。
- `src.zip` - 一个名为src的目录，包含你的源代码。

下面是暂定的评分规则：
- Environment setup: 10 pts
- DHCP: 30 pts
- Shortest path switching: 50 pts
- Report: 10 pts
- Bonus: Up to 20 pts



### Bonus

你也许可以实现以下部分功能来获取Bonus分数。我们实现功能的完成度和难度来决定Bonus分数。你不需要完成下面所有的功能。


- 实现DHCP租约时长的功能。
- 根据RFC协议设计，确保DHCP不会重复分配IP。
- 实现不同的路由算法。
- 利用Ryu实现更多的功能比如DNS, 防火墙和NAT。
- 使用Mininet研究更多你在计算机网络课程中学到的网络功能，如TCP行为、TCP Reno与TCP Tahoe的比较、[Bufferbloat](https://en.wikipedia.org/wiki/Bufferbloat)问题。
- 更多你感兴趣的。请先和老师讨论你的想法。

请注意，你需要详细解释你做了什么，如何测试额外的功能，以及你在报告中发现了什么，以获得bonus分。你还需要设法如何在第16周的演示中很好地展示你的bonus功能。
## Hints

### 同步代码
你可以使用Visual Studio Code Remote extension通过SSH在虚拟机中编写代码

### 有用的Mininet Command
我们建议每次构建新的网络拓扑时，重启你的controller和mininet。你可能需要使用
```
sudo mn -c
```
来清理之前配置的网络。

以下是一些可能可以提供帮助的指令
```
MN>  arping h1  # 从h1发送一个arping，产生一个ARP请求，识别h1的MAC和IP地址。触发一个EventHostAdd事件
MN>  arping_all # 从所有主机发送一个arping。这个命令会在测试脚本中自动运行。你也可以自己重新运行它--如果你想重启控制器而不重启mininet，这非常有用
MN> h1 ping h2 -c 1 # 从h1向h2发送一个单一的ping包
MN> pingall # Ping所有的主机
MN> net # 查看当前的网络拓扑结构
MN> dpctl dump-flows # 展示所有交换机的流量表
```

### 如何添加Forwarding Rule

你可以阅读`ofctl_utils.py`的源码来了解更多细节。以下是一个简单的例子向你展示如何在switch中添加一个forwarding rule。
```
# Using function provided by ofctl_utils.py
from ofctl_utils import OfCtl, VLANID_NONE

def add_forwarding_rule(self, datapath, dl_dst, port):
    ofctl = OfCtl.factory(datapath, self.logger)
    actions = [datapath.ofproto_parser.OFPActionOutput(port)] 
    
    ofctl.set_flow(cookie=0, priority=0,
        dl_type=ether_types.ETH_TYPE_IP,
        dl_vlan=VLANID_NONE,
        dl_dst=dl_dst,
        actions=actions)
```

### 有用的文档
1. Ryu's API documentation https://ryu.readthedocs.io/en/latest/index.html
2. Mininet's document https://github.com/mininet/mininet/wiki/Documentation
3. Mininet source code https://github.com/mininet/mininet
4. Openflow quick start https://homepages.dcc.ufmg.br/~mmvieira/cc/OpenFlow%20Tutorial%20-%20OpenFlow%20Wiki.htm
