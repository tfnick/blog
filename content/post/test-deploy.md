---
share: "true"
toc: "true"
date: 2024-09-09
title: 文章标题
description: 文章描述
author: "飞天"
tags:
  - ddd
---


## 二级

领域中存在实体、值对象。

如果两个实体之间如果存在强一致性的要求，则他们之间可能存在聚合关系。比如订单与订单项之间的关系。

如果两个实体之间不存在强一致性的要求，则他们之间只是普通的实体之间的关系。比如人员与项目之间的关系或者用户与角色之间的关系。

虽然两个实体之间存在强一致性的要求，但如果聚合过大的话，不宜设计成一个聚合，要规避大聚合带来的大事务的问题（本质是要考虑CPU、内存、网络的限制）

由于基础设施层持久化聚合的限制，一些DDD实践者甚至弱化聚合的概念，实践中更多使用领域服务来替代基础设施层的聚合的级联更新或删除。这类实践可以称为DDD Lite。

## 二级

领域之间，聚合与聚合之间通过事件进行通信。
this is test page
