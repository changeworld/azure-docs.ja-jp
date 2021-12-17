---
title: Azure NetApp Files 用の Linux NFS 先行読み取りベスト プラクティス - セッション スロットとスロット テーブル エントリ | Microsoft Docs
description: Azure NetApp Files 用のファイルシステム キャッシュおよび Linux NFS 先行読み取りベスト プラクティスについて説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: f87d2742ab34cdf5b6509e88b403240d388fa373
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233488"
---
# <a name="linux-nfs-read-ahead-best-practices-for-azure-netapp-files"></a>Azure NetApp Files 用の Linux NFS 先行読み取りベスト プラクティス

この記事は、Azure NetApp Files 用のファイルシステム キャッシュのベスト プラクティスを理解するのに役立ちます。  

NFS 先行読み取りは、アプリケーションによる I/O 要求に先立って、ファイルからのブロックを予測的に要求するものです。 これは、クライアントのシーケンシャル読み取りスループットを向上させることを目的として設計されています。  最近まで、最新のすべての Linux ディストリビューションでは先行読み取り値を、マウントされたファイルシステムの `rsize` の 15 倍に相当するように設定していました。  

次の表は、所定の `rsize` マウント オプションごとに、既定の先行読み取り値を示したものです。

| マウントされたファイルシステムの `rsize` | 先行読み取りするブロック |
|-|-|
| 64 KiB | 960 KiB |
| 256 KiB | 3,840 KiB |
| 1024 KiB | 15,360 KiB |

RHEL 8.3 および Ubuntu 18.04 によって、クライアントのシーケンシャル読み取りパフォーマンスに悪影響を及ぼすおそれのある変更が導入されました。  以前のリリースとは異なり、これらのディストリビューションでは、使用される `rsize` マウント オプションに関係なく、先行読み取りを既定値の 128 KiB に設定します。 より大きな先行読み取り値を持つリリースから、このような 128 KiB の既定値を持つものにアップグレードすると、シーケンシャル読み取りパフォーマンスが低下します。 ただし、先行読み取り値は、動的にも、永続的にも調整することが可能です。  たとえば、SAS GRID を使用したテストでは、15,360 KiB の読み取り値が 3,840 KiB、960 KiB、および 128 KiB と比較して最適であることがわかりました。  15,360 KiB を超える場合については、正または負の影響を判断するのに十分なテストが行われていません。

次の表に、現在利用可能なディストリビューションごとに先行読み取り値の既定値を示します。

|     Distribution    |     リリース    |     ブロック   先行読み取り    |
|-|-|-|
|     RHEL    |     8.3    |     128 KiB    |
|     RHEL    |     7.X、8.0、8.1、8.2    |     15 X `rsize`    |
|     SLES    |     12.X – 15SP2 以上    |     15 X `rsize`    |
|     Ubuntu    |     18.04 – 20.04 以上    |     128 KiB    |
|     Ubuntu    |     16.04    |     15 X `rsize`    |
|     Debian    |     最大 10 まで    |     15 x `rsize`    |


## <a name="how-to-work-with-per-nfs-filesystem-read-ahead"></a>NFS ごとのファイルシステムの先行読み取りを操作する方法   

NFS 先行読み取りは、NFS ファイルシステムのマウント ポイントで定義されます。 既定の設定を表示し、動的に、および永続的に設定することができます。  便宜上、Red Hat によって作成された次の bash スクリプトは、マウントされた NFS ファイルシステムの先行読み取りを表示または動的に設定するために提供されています。

先行読み取りは、次のスクリプトを使用して NFS マウントごとに動的に定義することも、このセクションで示すように、`udev` ルールを使用して永続的に定義することもできます。  マウントされた NFS ファイルシステムに対して先行読み取りを表示または設定するには、次のスクリプトを bash ファイルとして保存し、ファイルのアクセス許可を変更して実行可能ファイル (`chmod 544 readahead.sh`) にしてから、示されているように実行します。 

## <a name="how-to-show-or-set-read-ahead-values"></a>先読み値を表示または設定する方法   

現在の先行読み取り値 (戻り値の単位は KiB) を表示するには、次のコマンドを実行します。  

`$ ./readahead.sh  show <mount-point>`   

先行読み取りの新しい値を設定するには、次のコマンドを実行します。   

`$ ./readahead.sh  show <mount-point> [read-ahead-kb]`
 
### <a name="example"></a>例   

```
#!/bin/bash
# set | show readahead for a specific mount point
# Useful for things like NFS and if you do not know / care about the backing device
#
# To the extent possible under law, Red Hat, Inc. has dedicated all copyright
# to this software to the public domain worldwide, pursuant to the
# CC0 Public Domain Dedication. This software is distributed without any warranty.
# See <http://creativecommons.org/publicdomain/zero/1.0/>.
#

E_BADARGS=22
function myusage() {
echo "Usage: `basename $0` set|show <mount-point> [read-ahead-kb]"
}

if [ $# -gt 3 -o $# -lt 2 ]; then
   myusage
   exit $E_BADARGS
fi

MNT=${2%/}
BDEV=$(grep $MNT /proc/self/mountinfo | awk '{ print $3 }')

if [ $# -eq 3 -a $1 == "set" ]; then
   echo $3 > /sys/class/bdi/$BDEV/read_ahead_kb
elif [ $# -eq 2 -a $1 == "show" ]; then
   echo "$MNT $BDEV /sys/class/bdi/$BDEV/read_ahead_kb = "$(cat /sys/class/bdi/$BDEV/read_ahead_kb)
else
   myusage
   exit $E_BADARGS
fi
```

## <a name="how-to-persistently-set-read-ahead-for-nfs-mounts"></a>NFS マウントの先行読み取り永続的に設定する方法

NFS マウントの先行読み取りを永続的に設定するには、次のように `udev` ルールを記述します。    

1. `/etc/udev/rules.d/99-nfs.rules` を作成してテストします。

    `SUBSYSTEM=="bdi", ACTION=="add", PROGRAM="/bin/awk -v bdi=$kernel 'BEGIN{ret=1} {if ($4 == bdi) {ret=0}} END{exit ret}' /proc/fs/nfsfs/volumes", ATTR{read_ahead_kb}="15380"`

2. `udev` ルールを適用します。   

    `$udevadm control --reload`

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files 用の Linux のダイレクト I/O のベスト プラクティス](performance-linux-direct-io.md)
* [Azure NetApp Files の Linux ファイルシステム キャッシュのベスト プラクティス](performance-linux-filesystem-cache.md)
* [Azure NetApp Files 用の Linux NFS マウント オプションのベスト プラクティス](performance-linux-mount-options.md)
* [Linux コンカレンシーのベスト プラクティス](performance-linux-concurrency-session-slots.md)
* [Azure 仮想マシン SKU のベスト プラクティス](performance-virtual-machine-sku.md) 
* [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md) 
