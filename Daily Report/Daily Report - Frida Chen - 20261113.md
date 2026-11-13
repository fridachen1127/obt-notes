

# 作业
## 1 bmc重启

https://zenlayer.atlassian.net/wiki/spaces/GSC/pages/12234948793/BMC
### 1.1 bops查询失败

| 教程步骤                                                                    | 工具      | 截图                             | 结果                         |
| ----------------------------------------------------------------------- | ------- | ------------------------------ | -------------------------- |
| 如BOPS显示设备出现预下线提示，升级SRE询问预下线原因，是否可以取消预下线状态进行操作                           | bops    | ![[IMG-20261113101520586.png]] | 查询无结果，无预下线提示               |
| BMC重启为高危操作，反复核实确认设备信息（SN,IP）以及归属，防止人为事故产生。此操作时，建议同时长ping操作服务器IP，以确认操作进度 | 无       | 无                              | sn序列号在产品上，ip是确认客户提供的ip是否正确 |
|                                                                         | 命令行ping | ![[IMG-20261113102156476.png]] | time out 丢包100%            |
| 涉及重启需求，应通过BOPS进入IPMI查看服务器所处状态，截图客户确认后方可操作                               | bops    | 同1                             | bops无ipmi信息                |

### 1.2 其他替代查询
- grafana bmc - ip usage 

![[IMG-20261113103533301.png]]

- oss-bmc

![[IMG-20261113103647703.png]]


## 2 操作换机


- 需求处理类-操作流程-换机, https://zenlayer.atlassian.net/wiki/spaces/GSC/pages/12234195855/BMC
- 指导手册, https://zenlayer.atlassian.net/wiki/spaces/GSC/pages/10398826744/BMC?xpis=eyJicmlkZ2UiOiJzbWFydExpbmtzIiwiaWQiOiIxNzk0NTM1MjU0NzIxIiwic291cmNlIjoiY29uZmx1ZW5jZSJ9
- 需求处理-bmc查询类-设备硬件查询, https://zenlayer.atlassian.net/wiki/spaces/GSC/pages/12234883245/-BMC

### 2.1 bops查zone

![[IMG-20261113104333412.png]]

SEL2D


### 2.2 oss 开wo

oss-交付任务-右上角新建BMC VIP Post-sale Replacement/ BMC Self-Service Post-sale Replacement


![[IMG-20261113104740866.png]]

输入ip，zone，客户名称。查询没有结果

![[IMG-20261113105133012.png]]


### 2.3 查库存

- oss-idc资源-设备管理，根据换机具体类型和详细信息进一步筛选
- grafana bmc - bmc - pub tools - sever detail by label：区域库存，装机来源

![[IMG-20261113110010171.png]]

![[IMG-20261113110119515.png]]


![[IMG-20261113110512933.png]]


# 知识点: ipt 4个视频



