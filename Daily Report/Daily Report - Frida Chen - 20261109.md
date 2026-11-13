
# 学习内容

## 1 任务完成

- [x] 改Outlook signature: 去掉复制的原始格式
- [x] 基础网络架构分享2h：截图做笔记
- [x] 产品培训1h

## 2 学习要点

### 2.1 拓扑图
#### 2.1.1 核心产品关系

![[IMG-20261109163227899.png]]

- 口字型，非全互联
- isp公网和专线服务质量差异

#### 2.1.2 单节点收发关系


![[IMG-20261109163312265.png]]
- CR-CR：isis


#### 2.1.3 网关落在哪里

![[IMG-20261109135158008.png]]


![[IMG-20261109135209304.png]]


#### 2.1.4 多节点

![[IMG-20261109163513330.png]]

### 2.2 监控：网络互联 transit pldt，pni，ix


![[IMG-20261109163830458.png]]

- tansit：向上游 ISP 购买互联网可达性
	- cons：流量收费，不易排查
- peering
	- pni: as1-as2
		- pros：更好排查
		- cons：当地运营商多，可分配端口不足
	- ix：一个 IX 端口可服务多个 Peer。通过共享二层交换平台连接多个 AS
		- pros：省端口费

### 2.3 RR

1. 定义：RR（Route Reflector，路由反射器） 主要用于解决 iBGP Full Mesh（全互联）带来的邻居数量、配置复杂度和扩展性问题。

2. 公式：以4个路由为例，推导公式 n(n-1)/2

3. ibgp防止环路，水平分割示意图；rr反射学习

关键在于 iBGP Split Horizon（iBGP 水平分割规则），
普通 iBGP 路由器从一个 iBGP 邻居学到的路由，默认不会再通告给其他 iBGP 邻居。

      CR1 -------- CR2 -------- CR3
          iBGP         iBGP

      CR1 学到路由 8.8.8.0/24
                  |
                  v
                 CR2
                  X
          不会继续通告给 CR3


如果将 CR2 配置为 RR，并将 CR1、CR3 配置为 RR Client：

```
      CR1 -------- RR -------- CR3
            iBGP       iBGP
                  |
          反射 CR1 的路由
                  |
                  v
                 CR3
```

RR 可以按照路由反射规则将 Client 学到的路由通告给其他 Client。

什么时候引入

pop
PoP（Point of Presence，网络接入节点）
- 单个路由，单个接入节点：0
- 单个接入节点，多个路由：1
- 多个接入节点，多个路由：1

### 2.4 配置修改优先级

![[IMG-20261109162752774.png]]

强制改优先级
ISIS：20
bgp：200 

直连>静态>isis>bgp


### 2.5 bgp community


![[Pasted image 20261109171504.png]]



### 2.6 mobax 命令

| 任务         | Linux / Jump Server  | Huawei VRP                   | Juniper Junos           |
| ---------- | -------------------- | ---------------------------- | ----------------------- |
| 查看 IP      | `ip addr`            | `display ip interface brief` | `show interfaces terse` |
| 查看路由       | `ip route`           | `display ip routing-table`   | `show route`            |
| 查看 BGP     | 依路由软件而定              | `display bgp peer`           | `show bgp summary`      |
| Ping       | `ping 8.8.8.8`       | `ping 8.8.8.8`               | `ping 8.8.8.8`          |
| Traceroute | `traceroute 8.8.8.8` | `tracert 8.8.8.8`            | `traceroute 8.8.8.8`    |
| 过滤输出       | `grep`               | `include`                    | `match`                 |
注意：MobaXterm 只是终端客户端，命令语法由远端设备的操作系统决定。

演示用的match
转义符
## 3 general

### 3.1 常用拓扑图缩写

| 缩写  | 全称                           | 备注   | 缩写  | 全称                    | 备注                    |
| --- | ---------------------------- | ---- | --- | --------------------- | --------------------- |
| CR  | core router                  |      |     |                       |                       |
| CS  | core sw                      |      | VPC | virtual private cloud |                       |
| ES  | edge sw                      |      | TOR | top of rack           | idc里，下面都是服务器，上面有一台交换机 |
| OFS | openflow sw                  |      |     |                       |                       |
| NNI | network to network interface | 扩展接口 |     |                       |                       |

### 3.2 产品线汇总

![[IMG-20261109163112528.png]]


| 缩写   | 英文全称                          | 备注              | 核心功能                                            | 典型客户 / 场景                    |
| ---- | ----------------------------- | --------------- | ----------------------------------------------- | ---------------------------- |
| Colo | Colocation=co+location        | 机柜              | 机柜托管，提供机房空间、供电、制冷、物理安全、网络接入和交叉连接（Cross Connect） | UCloud 等云服务商、需要海外机房资源的企业     |
| IPT  | IP Transit                    | 带宽              | 互联网接入及带宽服务，通过 BGP 和多个 ISP 实现全球公网互联、路由优化及冗余      | CDN 厂商、云服务商、游戏公司、IDC 客户      |
| SDN  | Software-Defined Networking   | 专线              | 软件定义网络，利用全球骨干网提供二层专线、三层互联、云连接等服务                | 跨国企业、在线教育、跨境业务、多云互联          |
| BMC  | Bare Metal Cloud              | 服务器             | 裸金属云，向客户提供独占物理服务器，支持按需部署、操作系统安装、远程管理            | 游戏厂商、AI 计算、视频平台、莉莉丝等出海游戏企业场景 |
| MHS  | Managed Hosting Services      |                 | 托管式服务器服务，通常包含服务器部署、硬件维护、机房及网络支持；具体服务范围依合同       | 需要海外服务器但不希望自行管理硬件的企业         |
| ZGA  | Zenlayer Global Accelerator   | 动态加速，在海外新增一个服务器 | 全球应用加速，通过就近接入节点、智能路由和全球骨干网改善跨境访问延迟、丢包及抖动        | 跨境应用、海外用户访问国内业务、全球 SaaS      |
| CDN  | Content Delivery Network      | NVIDIA驱动下载      | 内容分发网络，通过边缘节点缓存内容，降低源站负载、提高下载和访问速度              | 软件及驱动下载、视频、游戏更新、网站静态资源       |
| DDoS | Distributed Denial-of-Service | 防止被攻击           | 分布式拒绝服务攻击防护，通过流量检测、清洗和过滤保护业务可用性                 | 游戏、金融                        |

### 3.3 95计费

- 峰值限时：允许客户有突发峰值，但是要在36h内。不然会额外计费。
- 百分位概念

![[Pasted image 20261109165105.png]]

### 3.4 端口收费

![[IMG-20261109160741607.png]]
1块板卡1-2w美金，16个端口，单个端口1k美金

### 3.5 路由器板卡

| 板卡                   | 通俗类比       | 核心功能                   | 实际场景（Zenlayer）              |
| -------------------- | ---------- | ---------------------- | --------------------------- |
| 主控板（RE / MPU）        | 大脑 / CPU   | 运行 BGP、OSPF，计算路由并下发转发表 | 计算经过 PLDT、Globe、NTT 的最佳路由   |
| 业务板（Line Card / MPC） | 网卡 + 数据处理器 | 接收、处理和转发数据包            | 业务板 1 接 PLDT，业务板 2 接 Globe  |
| 交换网板（Switch Fabric）  | 内部高速公路     | 在不同业务板之间高速传输数据         | 将 PLDT 进入的数据传送到 Globe 所在业务板 |
| 接口卡（PIC / MIC）       | 网口扩展卡      | 提供 10G、100G 等不同类型的物理接口 | 接入 ISP、PNI、IX 链路            |
| 电源模块（PSU）            | 电源         | 为设备及板卡供电               | 电源故障时可能切换到冗余电源              |
| 风扇模块（Fan Tray）       | 散热风扇       | 为设备散热                  | 风扇故障可能导致高温告警或板卡关闭           |


**数据转发示意**

```
           主控板（大脑）
          BGP / OSPF 选路
                │
          下发转发表
                ↓
PLDT → 业务板 1 ──┐
                  │
               交换网板
                  │
Globe ← 业务板 2 ─┘
```

主控板负责计算路由，业务板负责实际转发，交换网板负责板卡之间的数据交换。正常转发的数据包通常不需要经过主控板 CPU。

### 3.6 常见厂商缩写

| 厂商                   | 中文名称     | 国家/地区起源 | 主要产品                          | 常见场景                 |
| -------------------- | -------- | ------- | ----------------------------- | -------------------- |
| Cisco                | 思科       | 美国      | Catalyst、Nexus、ASR、Cisco 8000 | 企业网、ISP、数据中心         |
| Huawei               | 华为       | 中国      | NE40E、NE8000、CloudEngine      | ISP、骨干网、数据中心         |
| **Juniper Networks** | 瞻博网络     | 美国      | MX、PTX、EX、QFX                 | ISP、BGP Peering、数据中心 |
| Arista Networks      | 阿里斯塔     | 美国      | 7000、7200、7500 系列             | 数据中心、云计算、低延迟网络       |
| H3C                  | 新华三      | 中国      | S、CR、SR 系列                    | 企业网、园区网、数据中心         |
| Nokia                | 诺基亚      | 芬兰      | 7750 SR、7250 IXR              | ISP、运营商骨干网           |
| ZTE                  | 中兴通讯     | 中国      | ZXR10、ZXR 系列                  | ISP、电信运营商            |
| Extreme Networks     | 极进网络     | 美国      | ExtremeSwitching              | 企业网、园区网              |
| Fortinet             | 飞塔       | 美国      | FortiGate、FortiSwitch         | 防火墙、SD-WAN、安全网络      |
| MikroTik             | MikroTik | 拉脱维亚    | CCR、CRS、RouterBOARD           | 中小 ISP、企业网           |

### 3.7 供应商，运营商区别

|维度|供应商（Vendor / Supplier）|运营商（Carrier / Operator）|
|---|---|---|
|核心角色|提供设备、技术、资源或服务|建设、运营或管理通信网络|
|主要产品|路由器、交换机、光纤资源、带宽、云服务|Internet、IP Transit、专线、移动通信|
|是否拥有网络|不一定|通常拥有或控制一定网络资源|
|是否拥有 ASN|不一定|IP 网络运营商通常有|
|典型客户|运营商、云厂商、企业|企业、ISP、云厂商、个人|
|收入来源|产品销售、软件授权、资源租赁、服务合同|网络接入费、带宽费、专线费、通信服务费|
# 疑问（待深入）

## 1 堆叠

![[IMG-20261109102332081.png]]
- gpt给的有些抽象
## 2 isis

- 类比ospf
## 3 mobax
- 找教程设置好
- 自己找配置命令练习

## 4 收发关系和原因

- 从头再听

