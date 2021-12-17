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
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: cc034689e2c3cd6846986680225ca7ca21ac41c8
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407534"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files のメトリック

Azure NetApp Files では、割り当て済みストレージ、実際のストレージ使用量、ボリュームの IOPS、および待機時間に関するメトリックが提供されます。 これらのメトリックを分析することで、NetApp アカウントの使用パターンとボリュームのパフォーマンスの理解を深めることができます。  

容量プールまたはボリュームのメトリックを見つけるには、 **[容量プール]** または **[ボリューム]** を選択します。  次に、 **[メトリック]** をクリックして、使用可能なメトリックを表示します。 

[ ![[メトリック] のプルダウンに移動する方法を示すスナップショット。](../media/azure-netapp-files/metrics-navigate-volume.png) ](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量プールの使用状況メトリック

- "*Pool Allocated Size*" (プールの割り当てサイズ)   
    プールのプロビジョニングされたサイズ。

- "*ボリュームに割り当てられたプールのサイズ*"  
    特定の容量プールのボリューム クォータの合計 (GiB) です (つまり、容量プールでのボリュームのプロビジョニング済みサイズの合計)。  
    このサイズは、ボリュームの作成中に選択したサイズです。  

- "*プールで消費されたサイズ*"  
    容量プールのボリューム全体で使用された論理領域の合計 (GiB) です。  

- "*プールのスナップショット サイズの合計*"    
    プール内のすべてのボリュームからのスナップショット サイズの合計。

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>ボリュームの使用状況メトリック

- *ボリュームで消費されたサイズの割合*    
    スナップショットを含む、消費されたボリュームの割合。  
- "*ボリュームの割り当てサイズ*"   
    ボリュームのプロビジョニングされたサイズ
- "*ボリュームのクォータ サイズ*"    
    ボリュームのプロビジョニングに使用されているクォータ サイズ (GiB)。   
- "*ボリュームで消費されたサイズ*"   
    ボリュームの論理サイズ (使用されているバイト)。  
    このサイズには、アクティブなファイル システムとスナップショットで使用される論理領域が含まれます。  
- "*ボリューム スナップショットのサイズ*"   
   ボリュームのすべてのスナップショットのサイズ。  

## <a name="performance-metrics-for-volumes"></a>ボリュームのパフォーマンス メトリック

> [!NOTE] 
> *読み取りの平均待機時間* および *書き込みの平均待機時間* のボリューム待機時間は、ストレージ サービスの内部で測定され、ネットワーク待機時間を含みません。

- "*読み取りの平均待機時間*"   
    ボリュームからの読み取りの平均時間 (ミリ秒) です。
- "*書き込みの平均待機時間*"   
    ボリュームからの書き込みの平均時間 (ミリ秒) です。
- "*読み取り IOPS*"   
    ボリュームへの 1 秒あたりの読み取り数です。
- "*書き込み IOPS*"   
    ボリュームへの 1 秒あたりの書き込み数です。

## <a name="volume-replication-metrics"></a><a name="replication"></a>ボリューム レプリケーション メトリック

> [!NOTE] 
> * ネットワーク転送サイズ (たとえば、*ボリューム レプリケーションの転送の合計* メトリック) が、リージョン間レプリケーションのソース ボリュームまたはターゲット ボリュームと異なる場合があります。 この動作は、ネットワーク転送コストを最小限に抑えるために効率的なレプリケーション エンジンが使用された結果です。
> * 現時点では、レプリケーションのリレーションシップのソースではなく、レプリケーション先のボリュームに対して、ボリューム レプリケーション メトリックが設定されています。

- "*Is volume replication status healthy*" (ボリューム レプリケーションの状態は正常です)   
    レプリケーションのリレーションシップの条件。 正常な状態は、`1` によって示されます。 異常な状態は、`0` によって示されます。

- "*Is volume replication transferring*" (ボリューム レプリケーションは転送中です)    
    ボリューム レプリケーションの状態が "転送中" かどうか。 

- "*Volume replication last transfer duration*" (ボリューム レプリケーションの最後の転送期間)   
    最後の転送が完了するまでにかかった時間 (秒単位)。 

- "*Volume replication last transfer size*" (ボリューム レプリケーションの最後の転送サイズ)    
    最後の転送の一部として転送されたバイトの合計数。 

- "*Volume replication progress*"(ボリューム レプリケーションの進行状況\)    
    現在の転送操作で転送されたデータの総量。 

- "*Volume replication total transfer*" (ボリューム レプリケーションの転送の合計)   
    リレーションシップに対して転送された累積バイト数。 

## <a name="throughput-metrics-for-capacity-pools"></a>容量プールのスループット メトリック   

* *プールの割り当てスループット*    
    プールに属するすべてのボリュームのスループットの合計。
    
* *プールに対してプロビジョニングされたスループット*   
    このプールのプロビジョニングされたスループット。


## <a name="throughput-metrics-for-volumes"></a>ボリュームのスループット メトリック   

* *読み取りスループット*   
    1 秒あたりの読み取りスループット (バイト単位)。
    
* *スループットの合計*   
    1 秒あたりのすべてのスループットの合計 (バイト単位)。

* *書き込みスループット*    
    1 秒あたりの書き込みスループット (バイト単位)。

* *その他のスループット*   
    1 秒あたりのバイト単位のその他のスループット (つまり、読み取りや書き込みではない)。

## <a name="volume-backup-metrics"></a>ボリュームのバックアップ メトリック  

* *ボリューム バックアップは有効か*   
    ボリュームのバックアップが有効になっているかどうかを示します。 `1` が有効です。 `0` は無効です。

* *ボリューム バックアップ操作が完了しているか*   
    前回のボリューム バックアップまたは復元操作が正常に完了したかどうかを示します。  `1` は成功しました。 `0` は失敗しました。

* *ボリューム バックアップが中断されているか*   
    ボリュームのバックアップ ポリシーが中断されているかどうかを示します。  `1` は中断されていません。 `0` は中断されました。

* *ボリューム バックアップのバイト数*   
    このボリュームに対してバックアップされた合計バイト数。

* *ボリューム バックアップの最後に転送されたバイト数*   
    最後のバックアップまたは復元操作のために転送された合計バイト数。  

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
* [容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
