---
title: 分布式文件系统综述
date: 2016-07-01 08:05:41
categories:
tags:
---

ceph, glusterfs, orangefs, tfs

# TFS #

TFS分配可写block时，简单的采用round robin策略分配，这种策略简单有效，其他根据dataserver负载来分配的策略，实现上比较复杂。
client会把读取过的block到dataserver的映射关系缓存到本地，由于block到dataserver的映射关系，一般只会在发生数据迁移的时候才会发生，所以一旦本地cache命中，大部分情况下都能从cache中获取的dataserver上访问到文件。
由于本地缓存容量有限，而映射关系数量庞大，从而使本地缓存命中率不高，可以配合远端缓存方案使用。

# Ceph #

为了确保集群中数据的分布式存储和良好的可扩展性，Ceph运用了著名的CRUSH (Controlled Replication Under Scalable Hashing) 算法。

# MISC #

当下流行的分布式文件系统存储主要是基于查表(lustre、龙存、蓝鲸)和计算(ceph, GlusterFS)两大类文件系统做的。

Linux4.6内核发布，引入OrangeFS
