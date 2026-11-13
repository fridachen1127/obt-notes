
# 今日学习内容

## 任务

- [x] 3A账号pc设置快捷键：空白处右键
- [x] mobax导入session：昨日完成
- [x] 跳板机：昨日
- [x] salesforce+Outlook：熟悉了工单分类，提醒负责人的流程；账号签名和其他设置已经完成
- [x] 群组添加：后续

昨日收尾：
- kentik账号冲突收到了回复，确认只保留公司账号，注销个人账号。等待kentik操作

## 开会

开会：
- 邮件subject英文
- 响应速度
	- 15min escalating 升级供应商
	- 30min email

## 知识点

### bare metal

简言之就是选用bare metal的客户对低延迟等要求比较高，所以选择独占服务器而非共享

|对比维度|Bare Metal（裸金属）|Virtual Machine（虚拟机）|
|---|---|---|
|硬件资源|独占物理服务器|通常共享物理服务器|
|虚拟化层|无需 Hypervisor|通过 Hypervisor 虚拟化|
|性能|性能稳定、延迟低、抖动小|可能存在虚拟化开销和资源争用|
|硬件控制|可直接控制 CPU、NIC、BIOS 等|通常只能操作虚拟硬件|
|部署速度|较慢，需要物理资源分配|较快，可快速创建和删除|
|成本|通常较高|通常较低，按需分配|
|管理方式|IPMI、BMC、SSH|云控制台、Hypervisor、SSH|
|典型客户|量化交易、AI/HPC、大型数据库|Web 应用、开发测试、中小企业|

### A端，z端

链路两端用A，Z表示

### 95%

将一段时间内采集的所有流量数据从小到大排序，去掉最高的 5%，剩余数据中的最大值就是 P95。通常traffic越大，越有可能出现拥塞风险。

### golang

学golang语法
- [https://go-dev.shuijingwanwq.com/tour/basics/3](https://go-dev.shuijingwanwq.com/tour/basics/3)
- [https://www.coursera.org/learn/go-for-cloud-and-networks/lecture/PabMO/course-introduction](https://www.coursera.org/learn/go-for-cloud-and-networks/lecture/PabMO/course-introduction)

# 作业预习&问题

| 序号  | 任务 / 问题                                                         | 截图                                 | 操作步骤                             | 涉及系统       | 是否确定 | 原因                                                                                     |
| --- | --------------------------------------------------------------- | ---------------------------------- | -------------------------------- | ---------- | ---- | -------------------------------------------------------------------------------------- |
| 1   | 查询 CID2807 洛杉矶—东京专线近一个月流量                                       | ![[IMG-20261106173748194.png]]     | 客户管理-客户视图-勾选睿悠，选择老版-选近一个月        | oss        | 1    |                                                                                        |
|     |                                                                 | ![[IMG-20261106173859009.png]]     | 业务管理-fabric vll，根据字段搜索，输入cid，a和z | zen router | 1    |                                                                                        |
| 2   | 查询 IPT 95 计费流量图                                                 | ![[IMG-20261106173431398.png\|48]] | ipt资源-全球流量。                      | oss        | 0    | 任选都有95计费流量图，但是没法搜索具体的                                                                  |
| 3   | 定位 IP `156.59.181.54` 所属产品线                                     | ![[IMG-20261106174015749.png]]     | bmc resources，查ip                | bops       | 0.5  | 但是没有更近一步的产品信息                                                                          |
| 4   | 登录跳板机（Jump Server）                                              | 和昨天一样                              |                                  |            | 1    |                                                                                        |
| 5   | 根据 CID7777、德国资源、VLAN 3182 定位资源                                  | ![[IMG-20261106174807269.png]]     | 智能搜索，输入vlan，cid                  | zen router | 0.5  |                                                                                        |
| 6   | 在Grafana BMC中通过BMC Device Search by Label来找到设备156.59.181.54 的监控 | ![[IMG-20261106174534162.png]]     |                                  | m grafana  | 0    | 找不到search by label。在搜索栏搜了下search by label，出来了如图有限的信息。其他的可以filter label的，输入ip地址没有更详细的信息 |
| 7   | Salesforce 创建需求单并关单                                             | 无                                  |                                  |            | 0.5  | 设置账户信息的时候，看到case和new了。需求单可能是request？                                                   |
| 8   | 产品视频和文档                                                         | 简单看了一下                             |                                  |            | 0.5  | 我的学习找不到在哪个平台；视频2h没有文字稿                                                                 |




