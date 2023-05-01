# CS305-2023Spring-Project

**Please read this project specification carefully and keep track of the updates!!!** 

**IMPORTANT NOTE**: We try our best to make this specification as clear as possible and cover all the problems we met during our testing. However, it is not uncommon that we could still miss important details in this specification. If there is anything unclear, you should submit issues in this repository or contact the instructors and SAs immediately, rather than guessing what you are required to do.

## Introduction
**SDN**: Software-defined networking (SDN) is a new network paradigm. A network can be divided into control and data planes. The control plane is a set of protocols and configurations used to set up forwarding-related devices (hosts, switches, and routers) so that they can forward packets properly. This includes ARP resolution, DNS, DHCP, spanning tree protocol, NAT, and all routing protocols, many of which are covered in our CS305 course. The most important feature of SDN is the separation of the control plane and the data plane. By centralizing the control logic in a centralized controller, the controller can control and manage network traffic in a **programmable** manner. In contrast, traditional networks distribute control logic across network devices. In this project, we will write a centralized controller. To build a local SDN development environment, we use the following two software tools.

**Mininet**:  Mininet is a widely-used network emulator which enables creating arbitrary virtual network environments on a Linux host. For teaching or software verification purposes, developers often use Mininet to build virtual network topologies. Developers can emulate networks with virtual hosts, virtual switches, and other network components and test their SDN controllers.

**Ryu**: Ryu is an open-source framework for building SDN controllers. After we build a virtual SDN network using Mininet, we use Ryu to write and deploy the SDN controller. A Ryu controller can communicate with the virtual switches in Mininet to control the behaviors of the virtual network. The figure below shows the overall architecture of Ryu and Mininet. Ryu monitors network traffic in switches to take corresponding actions (such as how to forward), while Mininet is responsible for the actual transmission of network traffic.

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/arch.png" width="30%"/>
</p>
In this project, we will write a Ryu controller to support two main functions:

- Serve as a simple DHCP server
- Implement the shortest path switching algorithm

**NOTE:** We will use Mininet to build different network topologies to test the correctness of the Ryu controller you write. You are thus required to ensure your code works properly using customized network topologies.

## Environment Setup
The environment setup consists of two main steps. First, install Mininet, and second, install the experimental framework we provide (including Ryu).

### Install Mininet
Mininet needs to run in a Linux environment. We strongly recommend installing a virtual machine on a personal computer and then installing Mininet in the virtual machine.

#### Windows and Other amd64 Users' Configuration Guide
1. Install VMware or VirtualBox.
2. Download the official Ubuntu image with mininet [mininet-2.3.0-210211-ubuntu-20.04.1](https://github.com/mininet/mininet/releases/download/2.3.0/mininet-2.3.0-210211-ubuntu-20.04.1-legacy-server-amd64-ovf.zip).
3. After downloading the image, unzip and double-click on the ovf file to automatically call the VMware or other virtual machine software to create it.
4. login to the virtual machine with the username `mininet` and paasword `mininet`.
5. You can also refer to the installation of the virtual machine in this [guide](https://naiv.fun/Dev/41.html).
#### macOS ARM Users' Configuration Guide
If you are using an M1 or other Apple chips, be sure to configure it as follows:

1. Install VMware Fusion or Parallel Desktop.
2. Install Ubuntu 20.04.01 ARM version (consistent with the chip architecture, it is recommended to search for macOS m1 installation of Ubuntu Server 20.04).
3. Configure the virtual machine and run it.
4. Install Mininet.
```
sudo apt-get update
sudo apt-get install mininet
```
5. Install Python, Pip and git
```
sudo apt-get install python3 python3-pip git
```

#### Check whether Mininet is installed correctly
Open your terminal (command line) in the virtual machine and enter the following command to check if Mininet is configured correctly.
```
sudo mn --test pingall
```
If you see output similar to the following, it means that the Mininet environment is configured correctly.

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/mininet_success.png" width="50%"/>
</p>

**Mininet must be executed as root. Be sure to use sudo or run it directly as root when using it.**

#### Experiment Framework Installation
Since Ubuntu's default Python version is too high, we need to install the Python 3.8 environment using miniconda.
If you are an AMD64 Ubuntu user under windows, you can install miniconda directly using the following command.
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

If you are an ARM Ubuntu user under macos, you can install miniconda directly using the following command.
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
After installing the Python environment you need to install the experimental framework for this Project.

The project repository is located at [CS305-2023Spring-Project](https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project). You can download the source code by downloading the ZIP file or cloning the repository. After downloading the source code, install the Python package dependencies with the following command.
```
conda activate cs305
git clone https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project.git
cd CS305-2023Spring-Project
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple 


# Check if Ryu is installed successfully
ryu-manager --version
# If you see the version information of ryu-manager, the installation is successful.
```

## Tasks
The basic part of this project includes two parts: a simple DHCP server and implementation of the shortest path switching algorithm. To simplify the experiment, we have imposed the following restrictions on the network topology structure.
- The Mininet only contains L2 switches and hosts. This means that our network is a large local subnet, and there is no need to consider multi-subnet scenarios.
- Each host in Mininet is only connected to one switch.

### Simple DHCP Server
DHCP, Dynamic Host Configuration Protocol, is mainly used for automatically assigning IP addresses to users in an internal network or network service provider.

Although Mininet automatically assigns an IP address to each host by default, we will turn off the IP initialization of Mininet in the test script. You can refer to the DHCP protocol standard [RFC 2131](https://www.rfc-editor.org/rfc/rfc2131) to implement a feature-rich and complete DHCP server. In any case, you only need to:

- **When the host joins the subnet, the controller you design can recognize the DHCP packet and assign a valid IP address to the host.**

In the next section, we will introduce how to complete this task and how to test whether you have successfully implemented the DHCP server.

### Shortest Path Switching
Your task is to establish a global shortest path switching table and install forwarding rules on the switches to implement these paths. You will build this table on the controller based on the global topology information collected by the controller. **The purpose is to achieve the shortest path between any two hosts.**

Unlike traditional Layer-2 switches or Layer-3 routers, SDN switches do not have a dedicated MAC learning table (MAC-learning) or routing table. Instead, SDN switches use a more general *flow table* structure, which can replace these and other structures. Each entry or rule in the flow table contains a set of matching criteria (based on fields in Ethernet, IP, TCP, UDP, and other headers), selects specific packets, and contains a series of actions to be taken for each matching rule.

Your switching module should match the destination MAC address and execute the corresponding action based on the matching rule to send the packet to the correct port to reach its destination.

**If you are unfamiliar with the terms such as action and flow table, please refer to our slides, the course textbook, and the documentation of Ryu and the relevant information of the Openflow protocol.**

The purpose of matching rules is the same as the destination and mask fields in traditional routing tables, while the purpose of actions is the same as the interface field in traditional routing tables, indicating where the packet should be sent. It should be noted that your topology is not limited to a tree structure, because you have collected information from all switches, and loops should not be a problem. In fact, you must test whether your switching is effective in topologies with loops.

To calculate the shortest path, you should use the Bellman-Ford algorithm or Dijkstra's algorithm to calculate the shortest path between any two hosts. After determining the shortest path from host A to host B, the controller must install the rules and corresponding actions in the flow table to each switch in the path. When the topology changes, you should update the affected path rules.

## Implementation and Testing
In this section, we will combine the experimental framework code to introduce the implementation ideas of the above functions and tell you how to test them.
### Experimental Framework
We provide some basic starter programs to help you start with this project. The project structure is as follows.
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

- `controller.py`: This file is the entry point of the project. You should implement monitoring of network components in the SDN network, addition and deletion, data flow through switches, and trigger DHCP or shortest path switching functions based on collected information.
- `dhcp.py`: The implementation details of DHCP should be presented in this file. controller.py calls relevant functions in dhcp.py to trigger the DHCP function.
- `tests`: Scripts for building mininet networks to test dhcp and switching functions.
### Implementing Simple DHCP
Implementing simple DHCP in SDN includes the following steps:
1. When a host joins the network, it broadcasts a DHCP DISCOVER packet.
2. After the controller receives the DHCP DISCOVER packet, it selects a free IP and constructs a DHCP OFFER packet to send back to the host.
3. After the host receives the OFFER packet, it broadcasts the DHCP REQUEST information to confirm the DHCP server configuration it has selected.
4. After the controller receives the DHCP REQUEST information, it constructs a DHCP ACK packet and sends it back to the host.

**The first and the third steps are implemented in the test script, and you should focus on implementing the second and fourth steps.**

#### Receiving DHCP Protocol Packets
In the `controller.py` file, we have provided relevant code for receiving DHCP protocol packets. This function is called when a packet enters the switch. `Datapath` here is the switch that receives the packet, and `inPort` is the port through which the packet enters. If this packet can be parsed by the DHCP protocol, we call the `DHCPServer.handle_dhcp` function to process it. If it cannot be parsed by DHCP, you should determine whether it is another protocol packet and make different treatments for different protocols.
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

#### Building DHCP Protocol Packets

You need to distinguish the received DHCP packet type in the `handle_dhcp` function in `dhcp.py`. Based on the received packet type, decide whether to send a DHCP OFFER packet or a DHCP ACK packet. When selecting a legal IP address, you need to combine the `start_ip`, `end_ip`, and `netmask` properties defined in the `Config` class in `dhcp.py`. These three properties together determine the size of the subnet—the number of IP addresses you can allocate. See the comments in `dhcp.py` for details.

#### Testing DHCP Functionality

Assuming that you are in the directory of the project, first execute the following command in a terminal:

```
ryu-manager --observe-links controller.py 
```

Open another terminal, and execute the following command:

```
cd ./tests/dhcp_test/
sudo env "PATH=$PATH" python test_network.py # share the PATN env with sudo user
```

We have set the default IP allocation to start from `192.168.1.2` in `dhcp.py`. You can check whether the two hosts have been assigned IP addresses by using command `h1 ifconfig` and `h2 ifconfig`.
As long as the following result appears, we consider the basic simple DHCP function implementation is completed.

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/dhcp_success.png" width="50%"/>
</p>   

#### Implementing The Shortest Path Switching

We can leverage the centralized SDN architecture to perform the shortest path switching without broadcasts, as follows:
### Implementing Shortest Path Switching

- When a switch is added or removed and a link between switches is established or removed, the network topology will change, which means the shortest path will also change. Correspondingly, you should update the flow table on the affected switch to ensure that data packets are always transmitted along the shortest path between switches. In order to implement this function you may need to create an abstract data structure to calculate the distance between switches.

- As usual, when a host wants to send a packet, it consults its routing table to determine if the destination is in the same subnet (will always be true in this project). This means the host will send the packet to the IP destination as an Ethernet frame destined to the MAC address of the destination (as opposed to the MAC address of a gateway or router). If the host does not know the MAC address for the destination, it issues an ARP request

- When a switch receives the ARP request, it will send the request to the controller as a PacketIn message, rather than broadcasting it
- The controller will receive the PacketIn message and look up the MAC address of the destination host, then generate a response (inside a PacketOut message) for the switch to send back to the sender host
- Upon receiving the response, the host will send the IP packet to the destination’s MAC address
- At each switch along the path to the destination (as determined previously by your code), the packet will match on the destination MAC address and be forwarded on the correct port.

In order for the controller to know the MAC address of each host, we must establish a protocol for hosts to inform the controller of its address. For this project, we require that hosts send an unsolicited ARP reply (also called a “gratuitous ARP”, or an arping) when connecting to tell the network its MAC and IP address—we have configured Mininet to do this automatically when starting the emulated network.
Finally, since we are not broadcasting ARP messages, all ARP requests will be sent to the controller instead. When you receive an ARP request, you should generate an appropriate response so a host can populate its ARP table.

#### Testing Shortest Path Switching
We provide a test network in `tests/switching_test/test_network.py`. Its network topology is as follows.

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/topo_example.png" width="50%"/>
</p>       

In `test_network.py`, a triangle network is constructed by adding hosts, switches, and links to the network. You need to monitor these events using the OpenFlow protocol and perform corresponding processing in the controller to achieve the shortest path switching. After all components (hosts, switches, links) are initialized, we execute the `arping` command on each host. You need to identify these `arping` packets and inform the hosts how to determine the destination MAC address. In this test, you can use the `pingall` command in the mininet CLI to test network connectivity.
In this network, the shortest path from h1 to h2 is h1->s1->s2->h2, and the shortest path from h1 to h3 is h1->s1->s3->h3: the number of switches that data transmission between any two hosts passes through should not exceed two.

In the project's directory, first execute the following command in one terminal:
```
ryu-manager --observe-links controller.py 
```
In another terminal, execute the following command:
```
cd ./tests/switching_test/
sudo env "PATH=$PATH" python test_network.py # share the PATN env with sudo user
```
After about two seconds, you will find that you have entered the mininet CLI in the second terminal.
**You should enter the `pingall` command here to test the connectivity of your network.** **To facilitate checking on your code, please implement the function of displaying the shortest path in the controller.** The following figure shows an example of displaying the shortest path. After the `pingall` command, it displays the path and its length between any two hosts in the first terminal. Here, the distance is 3, which means that the path length from h1->s1->s3->h3 is 3 (3 edges).

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/path_result.png" width="50%"/>
</p>   

You will see the result in the following figure in the second terminal. This indicates that there is no packet loss and the network is connected.

<p align="center">
  <img src="https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/img/ping_result.png" width="50%"/>
</p>   

## Grading and Submissions

You will need to demonstrate your project on Week 16 during your lab section. After demonstrating your project, you are required to submit:

- `report.pdf` — Please clearly illustrate the architecture of your project and describe the implementation details of what you've done. Add screenshots or codes if needed. You need to provide a complex testcase to demonstrate the robustness of your program.
- `src.zip` — A directory named src containing your source code.

Here is a TENTATIVE grading rule for your project:

- Environment setup: 10 pts
- DHCP: 30 pts
- Shortest path switching: 50 pts
- Report: 10 pts
- Bonus: Up to 20 pts

### Bonus (Up to 20 points)

You may implement some of the following features to get bonus points. We will decide your bonus points based on the completeness, complexity, and difficulty of your implemented functions. No need to implement all the features.

- Implement the function of DHCP lease duration.
- Design the DHCP function according to the RFC protocol to ensure that DHCP does not duplicate IP allocation.
- Implement different routing algorithms.
- Implement more functions using Ryu, such as DNS, firewall, and NAT.
- Use Mininet to study more network features you have learned in the computer network course, such as TCP behaviors, TCP Reno versus TCP Tahoe, and [Bufferbloat](https://en.wikipedia.org/wiki/Bufferbloat) problem.
- More that you can think of. Please discuss with the instructors first.

Note that you need to provide a detailed explanation of what you do, how to test the extra functions, and what you discover in the report for the bonus points. You also need to think of a way to demonstrate your bonus functions during your Demo on Week 16.

## Hints

**A Chinese version of this document is provided at [README-zh.md](https://github.com/SUSTech-HPCLab/CS305-2023Spring-Project/blob/main/README-zh.md)**.

### Synchronize Code

You can use the Visual Studio Code Remote extension to write code in the virtual machine via SSH.

### Useful Mininet Command
We recommend restarting your controller and Mininet every time you build a new network topology. You may need to use
```
sudo mn -c
```
to clean up previously configured networks.

Here are some commands that may be helpful:
```
MN> arping h1  # Send an arping from h1, generates an ARP request, identifies the MAC and IP address of h1. Triggers an EventHostAdd event
MN> arping_all # Send an arping from all hosts. This command will be run automatically in the test script. You can also run it yourself -- useful if you want to restart the controller without restarting Mininet.
MN> h1 ping h2 -c 1 # Send a single ping packet from h1 to h2
MN> pingall # Ping all hosts
MN> net # View the current network topology
MN> dpctl dump-flows # Show flow tables for all switches
```

### How to add a forwarding rule

You can read the code in `ofctl_utils.py` to learn more details.
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


### Useful Documents
1. Ryu's API documentation https://ryu.readthedocs.io/en/latest/index.html
2. Mininet's document https://github.com/mininet/mininet/wiki/Documentation
3. Mininet source code https://github.com/mininet/mininet
4. Openflow quick start https://homepages.dcc.ufmg.br/~mmvieira/cc/OpenFlow%20Tutorial%20-%20OpenFlow%20Wiki.htm


## Acknowledgments
The project is modified based on an assignment from Prof. Aditya Akella for CS640 Computer Networks at the University of Wisconsin, Madison, and from Prof. Rodrigo Fonseca for CS168 Computer Networks at Brown university.
