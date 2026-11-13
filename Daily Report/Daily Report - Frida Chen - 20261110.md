# 学习内容

## 1 任务

| 任务        | 进度           | 备注                             | 待做             |
| --------- | ------------ | ------------------------------ | -------------- |
| console   | 文档看完，视频看完60% | vm-zec的文档也对应                   | 作业+ip变更之后的视频内容 |
| kentik    | 视频和文档已经看完    | 和kentik写邮件follow up，可以通过sso访问了 |                |
| nta       | N/A，资料不可用    |                                |                |
| sdn/sdwan | 文档粗看         | 找了下企微里面的最新负责人                  |                |

## 2 工具优化

1. 输入法：搜狗和默认的输入法切换不流畅。因为搜狗是云枢的下载，不能卸载，最终通过删除注册表搞定
2. obsidian：创建日报template，可以复用
3. Outlook：修改签名和正文字体和字号
4. teams meetings:  编辑-会议转录

## 3 学习内容

> 详细笔记在个人本地文档。3.2，3.3并非本地文档实际目录。

### 3.1 明确平台+产品

- 查看平台教程+新人培训下拉文件+看视频+提问明确平台定位

| 平台          | 产品，功能                                                              |
| ----------- | ------------------------------------------------------------------ |
| oss         | 订单，客户，销售信息                                                         |
| bops        | bmc zga。BOPS才是我们操作的，针对线下购买的客户我们可以这个平台帮忙操作，但是现在我们也没有权限做操作了，流程后面会看到。 |
| console     | 创建实例。Console是给客户的，类似淘宝的，给客户自助购买的平台。我们不操作客户的东西，需要客户自己操作             |
| zentour     |                                                                    |
| grafana     | ipt                                                                |
| grafana bmc | bmc, zec, all sw                                                   |
| kentik      | 流量分析，可以和grafana联动                                                  |
| nat         | 流量封堵                                                               |

vm
- vob: vm over bmc 
- zec: z elastic compute
	- ZEC 提供运行应用的云服务器，EIP 给这台服务器提供一个公网 IPv4 地址。

sdn, sdwan
- 控制设备
- 总部，分支，云之间互联
### 3.2 console zec instance

- 1 agent
  - 1.1 图表显示不全
  - 1.2 绑定辅助网卡需要先开 agent
- 2 磁盘
  - 2.1 NVMe
  - 2.2 标准、经济
  - 2.3 实例规格
  - 2.4 磁盘性能定制化
  - 2.5 容量
- 3 栈、协议族与 IPv4 / IPv6 双栈
- 4 网卡-内网 IP-EIP
- 5 三线 IP
  - 5.1 tips
  - 5.2 三大运营商简称
  - 5.3 配置示例
- 6 带宽上限
### 3.3 kentik case

- Kentik：网络架构与流量分析
  - 基础概念
    - Uplink：上联链路与上游运营商
    - Transit：Telia、PCCW
    - Ingress / Egress：流量方向
  - 网络架构设计
    - 多 CR 部署：冗余与带宽扩展
    - CR 互联：跨 CR 转发与上联共享
  - NetFlow 流量采集
    - Ingress 采集与跨 CR 流量去重
    - 结合 BGP 路由推算 Egress
  - Kentik 流量分析案例
    - PCCW 入向流量分析
    - Telia 出向接口过滤不完整
    - Destination AS 过滤的局限性
    - 通过 Next-Hop AS 识别 Transit 流量
  - 总结与注意事项
    - Ingress 与 Egress 的过滤方法
    - 跨 CR 转发及静态路由的影响
