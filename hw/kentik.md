# Kentik 案例：网络设计与流量分析

## 1 网络架构：为什么部署多台 CR 并互联？

目的： 实现冗余、共享上联运营商资源，以及跨 CR 转发。

```
    Telia AS1299       PCCW AS3491
          |                 |
       Uplink           Uplink
          |                 |
         CR-1 ============ CR-2
          |    互联链路     |
          |                 |
         CS-1              CS-2
          |                 |
        客户 A             客户 B
```

- 多台 CR：避免单点故障，扩展端口和带宽容量。
    
- CR 互联：CR-1 可以通过 CR-2 使用 PCCW 上联。
    
- BGP 选路：根据路由信息确定出向路径。
    

培训采用两台 CR 的节点举例；部署 3–4 台 CR 属于扩展设计，并非培训要求。

## 2 Case 1：如何完整采集节点流量？

问题： 需要分析整个节点的流量，但多台 CR 之间存在横向转发。

|方案|问题|
|---|---|
|只采集 CR-1|遗漏 CR-2 的部分流量|
|两台 CR 同时采集 Ingress + Egress|重复统计|
|两台 CR 采集全部 Ingress|互联流量重复|
|两台 CR 采集 Ingress，排除 CR 互联口|培训最终方案|

最终设计：

```
客户 → CR-1 → CR-2 → Telia
         ↑       ↑
       采集     不重复采集
```

只采集节点内两台 CR 的非互联口入向 NetFlow，避免同一股流量被重复计算。

## 3 Case 2：为什么采集 Ingress 就能分析 Egress？

问题： 没有直接采集出向流量，如何知道流量从哪个接口出去？

方案：NetFlow + BGP 路由信息。

```
Ingress NetFlow
       |
       v
获取 Destination IP
       |
       v
查询 BGP 路由表
       |
       v
Next-Hop 递归解析
       |
       v
推算 Egress Interface
```

培训中通过建立 BGP 邻居获取路由信息。

关键： 出向流量是根据已采集的入向流量和路由信息推算出来的。

## 4 Case 3：PCCW 入向端口跑满，如何分析？

问题： PCCW 上联流量异常增大，需要定位主要来源。

方案：按入口接口过滤。

```
Source = 香港节点 CR
        |
Filter = Ingress Interface Description
         contains "CW"
        |
Group By = Source IP / Source AS
           Destination IP / Destination AS
        |
定位主要流量来源
```

培训演示结果：

- 香港节点总入向流量约 186 G。
    
- PCCW 过滤后约 8 G。
    
- 可以继续按源 IP、目的 IP 和 AS 分析。
    

结论： 入向流量直接在对应接口采集，过滤相对简单。

## 5 Case 4：为什么出向流量不能直接按接口描述过滤？

问题： Telia 实际出向约 6 G，按接口描述过滤仅得到 4 G。

原因：跨 CR 转发。

```
客户 → CR-1 → CR-2 → Telia
         |
         v
  CR-1 的出接口
  是通往 CR-2 的互联口
  而不是 Telia
```

|方案|结果|原因|
|---|---|---|
|Egress Interface Description = Telia|约 4 G|遗漏跨 CR 流量|
|Destination AS = PCCW AS3491|约 60 M|Transit 并非最终目的 AS|
|采集 CR 互联口入向|可补充缺失流量|但可能导致重复统计|

因此，培训否定了这三种直接解决方案。

## 6 Case 5：如何正确分析 Transit 出向流量？

最终方案：通过 BGP Next-Hop AS 过滤。

```
Zenlayer AS21859
       |
       v
Telia AS1299
       |
       v
其他 AS
       |
       v
Destination AS
```

对于经 Telia 出网的 BGP 路由，培训使用：

`Next-Hop AS = 1299`

识别经过 Telia 的流量，即使流量先经过另一台 CR。

|字段|表示什么|
|---|---|
|Destination AS|流量最终到达哪个 AS|
|Egress Interface|当前 CR 推算出的出接口|
|Next-Hop AS|BGP 路径上的相邻 AS|

特殊情况： 静态路由可能导致基于 BGP 的推算不完整。培训提到使用 `902` 标签及重分布策略，使相关路由信息可供分析。

## 7 整体逻辑总结

```
多台 CR + CR 互联
        |
        v
需要完整分析节点流量
        |
        v
NetFlow 采集 Ingress
排除 CR 互联口避免重复
        |
        v
结合 BGP 路由推算 Egress
        |
        +---------------------+
        |                     |
        v                     v
    分析入向               分析出向
        |                     |
        v                     v
  Ingress Interface      Transit 上联
        |                     |
        v                     v
  按入口描述过滤         按 Next-Hop AS 过滤
        |                     |
        +----------+----------+
                   |
                   v
       Source / Destination IP
       Source / Destination AS
                   |
                   v
           定位主要流量
                   |
                   v
          辅助 BGP 流量调度
```

一句话记忆：

多 CR 互联 → NetFlow 入向采集并去重 → BGP 推算出向 → 入向按接口过滤、Transit 出向按 Next-Hop AS 过滤 → 分析 IP/AS 并辅助流量调度。

以上是培训中的方案逻辑。实际使用 Next-Hop AS 时，还需验证静态路由、ECMP 和路由信息是否与真实转发路径一致。

# uplink

上联（Uplink）严格来说指中间的连接链路或接口，而不是运营商本身。

以 Zenlayer 连接 Telia、PCCW 为例：

```
    Telia AS1299          PCCW AS3491
    上游运营商             上游运营商
         |                     |
         |                     |
      Uplink 1             Uplink 2
      上联链路              上联链路
         |                     |
         +----------+----------+
                    |
               Zenlayer CR
                    |
                 Downlink
                 下联链路
                    |
               Zenlayer CS
                    |
                   客户
```


```
          Internet
              |
       Transit Provider
          (PCCW)
              |
          ↑ 上联
              |
       CR（核心路由器）
              |
          ↓ 下联
              |
       CS（核心交换机）
              |
          ↓ 下联
              |
         客户服务器
```

## 1 概念

|名称|指代对象|举例|
|---|---|---|
|Upstream Provider|上游运营商|Telia、PCCW、Tata|
|Uplink|上联链路或接口|CR 连接 PCCW 的 100GE 接口|
|Uplink Port|上联端口|CR 上的 `100GE1/0/1`|
|Transit|上游提供的网络服务|PCCW 提供 IP Transit|
|BGP Peer|BGP 对等体|Zenlayer CR 与 PCCW 路由器建立的 BGP 邻居|

|对比|上联 Uplink|下联 Downlink|
|---|---|---|
|方向|连接上一级网络|连接下一级网络|
|常见对象|Transit、IX、PNI、上层交换机|客户、服务器、下层交换机|
|作用|接入其他网络|为下层网络提供连接|
|流量|可以双向传输|可以双向传输|
## 2 运营商 telia, pccw

|对比|Telia Company|PCCW Global|
|---|---|---|
|所属国家/地区|瑞典|中国香港|
|总部|瑞典斯德哥尔摩|香港（PCCW Global 国际业务总部设于香港）|
|主要业务|电信、移动通信、企业网络|国际网络连接、IP Transit、企业通信|
|主要市场|北欧、波罗的海地区|全球，尤其是亚洲及国际连接|
|常见 AS|AS1299（原 Telia Carrier）|AS3491|
|国际骨干网品牌|Arelion（原 Telia Carrier）|PCCW Global|