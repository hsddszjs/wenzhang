---
layout: post
title: "zookeeper概要"
description: ""
comments: true
keywords: ""
---

zookeeper可看做文件系统，znode节点可类比为目录和文件，并提供节点变动订阅通知(观察触发器)，且每个znode都有记录变更的版本号，且提供临时节点(客户端断开时删除)

![观察触发器](/assets/images/观察触发器.png)


## 分布式锁

![分布式锁](/assets/images/zk分布式锁.png)

##  leader选举

* 所有节点创建具有相同路径 /app/leader_election/guid_ 的**顺序、临时节点**。
* ZooKeeper集合将附加10位序列号到路径，创建的znode将是 /app/leader_election/guid_0000000001，/app/leader_election/guid_0000000002等。
* 对于给定的实例，在znode中创建最小数字的节点成为leader，而所有其他节点是follower。
* 每个follower节点监视下一个具有最小数字的znode。例如，创建znode/app/leader_election/guid_0000000008的节点将监视znode/app/leader_election/guid_0000000007，创建znode/app/leader_election/guid_0000000007的节点将监视znode/app/leader_election/guid_0000000006。
* 如果leader关闭，则其相应的znode/app/leader_electionN会被删除。
* 下一个在线follower节点将通过监视器获得关于leader移除的通知。
* 下一个在线follower节点将检查是否存在其他具有最小数字的znode。如果没有，那么它将承担leader的角色。否则，它找到的创建具有最小数字的znode的节点将作为leader。
* 类似地，所有其他follower节点选举创建具有最小数字的znode的节点作为leader。

## ACL(权限控制)

针对子节点
* CREATE(child) 创建**子节点**
* DELETE(child) 删除**子节点**

针对该节点
* READ 获取节点数据和子节点列表
* WRITE 写数据
* ADMIN 设置ACL








