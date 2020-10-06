---
title: Azure NetApp Files のメトリック | Microsoft Docs
description: Azure NetApp Files では、割り当て済みストレージ、実際のストレージ使用量、ボリュームの IOPS、および待機時間に関するメトリックが提供されます。 これらのメトリックを使用して、使用状況とパフォーマンスを把握します。
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
ms.date: 09/10/2020
ms.author: b-juche
ms.openlocfilehash: 1690a844ff700a2975be8e972fd90ba71eeb937c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707783"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files のメトリック

Azure NetApp Files では、割り当て済みストレージ、実際のストレージ使用量、ボリュームの IOPS、および待機時間に関するメトリックが提供されます。 これらのメトリックを分析することで、NetApp アカウントの使用パターンとボリュームのパフォーマンスの理解を深めることができます。  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量プールの使用状況メトリック

- "*Pool Allocated Size*" (プールの割り当てサイズ)   
    プールのプロビジョニングされたサイズ。

- "*ボリュームに割り当てられたプールのサイズ*"  
    特定の容量プールのボリューム クォータの合計 (GiB) です (つまり、容量プールでのボリュームのプロビジョニング済みサイズの合計)。  
    このサイズは、ボリュームの作成中に選択したサイズです。  

- "*プールで消費されたサイズ*"  
    容量プールのボリューム全体で使用された論理領域の合計 (GiB) です。  

- "*Total Snapshot size for the pool*" (プールのスナップショット サイズの合計)    
    プール内のすべてのボリュームのスナップショット サイズの合計。

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>ボリュームの使用状況メトリック

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- "*ボリュームで消費されたサイズ*"   
    ボリュームで使用された論理領域の合計 (GiB) です。  
    このサイズには、アクティブなファイル システムとスナップショットで使用される論理領域が含まれます。  
- "*ボリューム スナップショットのサイズ*"   
   ボリューム内のスナップショットで使用される増分の論理領域です。  

## <a name="performance-metrics-for-volumes"></a>ボリュームのパフォーマンス メトリック

- "*読み取りの平均待機時間*"   
    ボリュームからの読み取りの平均時間 (ミリ秒) です。
- "*書き込みの平均待機時間*"   
    ボリュームからの書き込みの平均時間 (ミリ秒) です。
- "*読み取り IOPS*"   
    ボリュームへの 1 秒あたりの読み取り数です。
- "*書き込み IOPS*"   
    ボリュームへの 1 秒あたりの書き込み数です。

## <a name="volume-replication-metrics"></a><a name="replication"></a>ボリューム レプリケーション メトリック

- "*Is volume replication status healthy*" (ボリューム レプリケーションの状態は正常です)   
    レプリケーションのリレーションシップの条件。 

- "*Is volume replication transferring*" (ボリューム レプリケーションは転送中です)    
    ボリューム レプリケーションの状態が "転送中" かどうか。 
 
- "*Volume replication lag time*" (ボリューム レプリケーションのラグ タイム)   
    ミラーのデータがソースより遅れている時間 (秒単位)。 

- "*Volume replication last transfer duration*" (ボリューム レプリケーションの最後の転送期間)   
    最後の転送が完了するまでにかかった時間 (秒単位)。 

- "*Volume replication last transfer size*" (ボリューム レプリケーションの最後の転送サイズ)    
    最後の転送の一部として転送されたバイトの合計数。 

- "*Volume replication progress*"(ボリューム レプリケーションの進行状況\)    
    現在の転送操作で転送されたデータの総量。 

- "*Volume replication total transfer*" (ボリューム レプリケーションの転送の合計)   
    リレーションシップに対して転送された累積バイト数。 

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
* [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
