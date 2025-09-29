---
title: "白群晖之 在“不受支持”的机型上启用btrfs和快照功能"
source: "https://wusiyu.me/synology-enable-brtfs-and-snapshot-replication-on-unsupported-models/"
author:
  - "[[WuSiYu]]"
published: 2024-07-04
created: 2025-09-22
description: "400多块买了个便宜的单盘位DS118白群晖当容灾备份机器用，到手发现这个机型居然不支持创建btrfs的存储空… 继续阅读白群晖之 在“不受支持”的机型上启用btrfs和快照功能"
tags:
  - "clippings"
---
400多块买了个便宜的单盘位DS118白群晖当容灾备份机器用，到手发现这个机型居然不支持创建btrfs的存储空间，这就十分难绷了，毕竟数据校验、快照等功能都用不了。

然后发现，尽管最近一代的新品在售的机器都支持btrfs了，但直到2020年群晖NAS的很多低端型号都不支持btrfs，其中20、19、18系列就包括：DS420j，DS220j，DS120j，DS419slim，DS119j，DS418j，DS218play，DS218j，DS118

尽管这些机型的配置确实不高，但btrfs其实也不是会费太多资源的东西，DS118这种机型完全能够支持，毕竟同为RTD1296 CPU的DS218或者同为1G内存的机器DS216+都是支持btrfs的，这么搞大概只是为了差别对待。

实际上，群晖在这些型号上用的系统都是差不多的，底层其实都带有btrfs功能，只是在Web UI上被隐藏了，这些功能的差异被定义在了 `/etc.defaults/synoinfo.conf` 中，因此只需要打开SSH功能，连接后执行 `sudo vim /etc.defaults/synoinfo.conf` ，加入以下内容即可：

```shell
support_btrfs="yes"
support_btrfs_dedupe="yes"
support_iscsi_btrfs_lun="yes"
support_share_snapshot="yes"
support_share_quota="yes"
support_share_user_quota="yes"
support_dr_snap="yes"
support_dr_replica="yes"
max_btrfs_snapshots="65536"
max_snapshot_per_lun="256"
max_snapshots_per_share="1024"
max_dr_replica="64"
max_sdr_replica="64"
```

若不会使用vim，也可以使用winscp等软件。此时btrfs存储池便可以被创建和使用，但若想使用群晖的快照套件还需要继续操作。

## 快照功能

DSM 7的快照功能需要使用套件SnapshotReplication和其依赖ReplicationService，然而这两个套件默认无法在不支持btrfs的机型上安装，但我们可以通过暂时修改机型的方式来强行安装，然后修改软件包信息。

1\. 首先找到一个相似架构且支持btrfs的机器，比如对于我的DS118，我找到了DS218，然后在 *群晖官网 -> DS218下载中心 -> DSM 套件* 中找到SnapshotReplication和ReplicationService的安装包，上传到home目录中。

2\. 编辑 `/etc.defaults/synoinfo.conf` ，修改synobios和unique字段中的机型，例如synobios=”ds118″和unique=”synology\_rtd1296\_ds118″中的“ds118”改为“ds218”，然后保存。 **注意：修改机型后，一定要在稍后改回来，期间不要重启，否则系统会无法启动。**

3\. 安装软件包，在终端中执行（.spk文件的文件名按实际修改）：

```shell
sudo synopkg install ReplicationService-ds218-1.3.0-0409.spk
sudo synopkg start ReplicationService
sudo synopkg install SnapshotReplication-ds218-7.4.5-1698.spk
sudo synopkg start SnapshotReplication
```

4\. 重要： **恢复 `synoinfo.conf` 中的机型修改**

5\. 此时套件虽然已经安装，但机型不匹配，会提示套件损坏，需要我们修改已安装软件包的信息，依次修改下面两个文件：

```shell
/var/packages/ReplicationService/INFO
/var/packages/SnapshotReplication/INFO
```

找到类似 `model="synology_rtd1296_ds218"` 的行，修改为机器的真实型号，然后重启，即可正常使用SnapshotReplication进行快照。

![[attachments/25260ac06e5ce7f486c110cf2db5ddc6_MD5.png]]

### 疑难解答

如果你这样做后依然无法创建快照，请尝试以下命令（homes为共享文件夹名称，可修改），观察快照能否被创建，或是否有报错：

```shell
sudo sh -x synosnapschedtask.sh local share homes
```

## 番外

玩过黑群晖的都知道，除了SA6400机型用的Linux内核版本是5.10.55（2021年发布）外，其他机型都还在使用上古4.4.302内核（2016年发布），然而在4.4老内核下，btrfs的压缩功能并不支持zstd算法，因此99%的白群晖NAS的文件系统压缩功能都是快速但效果较差的lzo，包括那些同样上万的机型（不100%确定，群晖毕竟对btrfs进行了不少魔改，也有可能backport了zstd的功能，其他机型的朋友可以看下synoinfo.conf是否包含 `btrfs_default_compression="zstd"` 的配置）。

对于黑群晖而言，也有多了一个用SA6400机型的理由。

+1

[1 快照功能](https://wusiyu.me/synology-enable-brtfs-and-snapshot-replication-on-unsupported-models/#kuai_zhao_gong_neng)

[1.1 疑难解答](https://wusiyu.me/synology-enable-brtfs-and-snapshot-replication-on-unsupported-models/#yi_nan_jie_da)

[2 番外](https://wusiyu.me/synology-enable-brtfs-and-snapshot-replication-on-unsupported-models/#fan_wai)

![[attachments/a822df2c6f8ef190376aa05885a92df0_MD5.jpg]]

## 作者：WuSiYu

学生，计算机/HPC/MLsys/LLM infra方向