---
title: Azure NetApp Files のストレージ階層 | Microsoft Docs
description: Azure NetApp Files のアカウント、容量プール、ボリュームを含むストレージ階層について説明します。
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
ms.topic: overview
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 49ad214c8851546cb2340d7ad413f9369e9b8a01
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219946"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Azure NetApp Files のストレージ階層

Azure NetApp Files のボリュームを作成する前に、プロビジョニング容量のプールを購入して設定する必要があります。  容量プールを設定するには、NetApp アカウントが必要です。 ストレージ階層についての理解は、Azure NetApp Files のリソースを設定および管理するうえで役立ちます。

> [!IMPORTANT] 
> Azure NetApp Files は現在、サブスクリプション間でのリソースの移行をサポートしていません。

## <a name="netapp-accounts"></a><bpt id="p1"><a name="azure_netapp_files_account"></bpt><ept id="p1"></a></ept>NetApp アカウント

- NetApp アカウントは、構成容量プールの管理グループとしての役割を果たします。  
- NetApp アカウントは、一般的な Azure Storage アカウントとは異なります。 
- NetApp アカウントは、範囲がリージョン単位となります。   
- 1 つのリージョンで複数の NetApp アカウントを持つことはできますが、個々の NetApp アカウントが関連付けられるリージョンは 1 つだけです。

## <a name="capacity-pools"></a><bpt id="p1"><a name="capacity_pools"></bpt><ept id="p1"></a></ept>容量プール

容量プールのしくみを理解することは、ストレージのニーズに適した容量プールの種類の選択に役立ちます。 

### <a name="general-rules-of-capacity-pools"></a>容量プールの一般的な規則

- 容量プールは、そのプロビジョニング容量で測定されます。   
    追加情報については、<bpt id="p1">[</bpt>QoS の種類<ept id="p1">](#qos_types)</ept>に関する記事を参照してください。  
- 容量は、購入した固定 SKU 単位でプロビジョニングされます (4 TiB 容量など)。
- 容量プールに割り当てることができるサービス レベルは 1 つだけです。  
- 各容量プールが属することができる NetApp アカウントは 1 つだけです。 ただし、1 つの NetApp アカウント内に複数の容量プールを持つことができます。  
- 1 つの容量プールを複数の NetApp アカウントにまたがって移動することはできません。   
  たとえば、下の「<bpt id="p1">[</bpt>ストレージ階層の概念図<ept id="p1">](#conceptual_diagram_of_storage_hierarchy)</ept>」で、容量プール 1 を米国東部の NetApp アカウントから米国西部 2 の NetApp アカウントへ移動することはできません。  
- 容量プール内のすべてのボリュームが削除されるまで、容量プールを削除できません。

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><bpt id="p1"><a name="qos_types"></bpt><ept id="p1"></a></ept>容量プールのサービス品質 (QoS) の種類

QoS の種類は容量プールの属性です。 Azure NetApp Files では、"自動 (既定)" と "手動" の 2 種類の QoS 容量プールが提供されます。 

#### <a name="automatic-or-auto-qos-type"></a>"<bpt id="p1">*</bpt>自動<ept id="p1">*</ept>" の種類の QoS  

容量プールを作成する場合、既定の QoS の種類は [自動] になります。

自動 QoS 容量プールでは、ボリュームに割り当てられたサイズ クォータに比例して、スループットがプール内のボリュームに自動的に割り当てられます。 

ボリュームに割り当てられる最大スループットは、容量プールのサービス レベルとボリュームのサイズ クォータによって異なります。 計算例については、「<bpt id="p1">[</bpt>Azure NetApp Files のサービス レベル<ept id="p1">](azure-netapp-files-service-levels.md)</ept>」を参照してください。

QoS の種類の考慮事項については、「[Azure NetApp Files のパフォーマンスに関する考慮事項](azure-netapp-files-performance-considerations.md)」をご覧ください。

#### <a name="manual-qos-type"></a>"手動" の QoS の種類  

[容量プールを作成する](azure-netapp-files-set-up-capacity-pool.md)ときには、容量プールに対して、手動 QoS の種類を使用するように指定できます。 手動 QoS の種類を使用するように[既存の容量プールを変更する](manage-manual-qos-capacity-pool.md#change-to-qos)こともできます。 *容量の種類を手動 QoS に設定することは、永続的な変更です。* 種類が手動 QoS である容量プールを、自動 QoS 容量プール変換することはできません。 

手動 QoS 容量プールでは、ボリュームの容量とスループットを個別に割り当てることができます。 最小および最大のスループット レベルについては、「[Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md#resource-limits)」を参照してください。 手動 QoS 容量プールで作成されたすべてのボリュームの合計スループットは、プールの合計スループットによって制限されます。  これは、プール サイズとサービスレベルのスループットの組み合わせによって決まります。  たとえば、Ultra サービス レベルの 4 TiB の容量プールで、ボリュームに対して使用可能な合計スループット容量は 512 MiB/s (4 TiB x 128 MiB/s/TiB) です。

##### <a name="example-of-using-manual-qos"></a>手動 QoS の使用例

たとえば SAP HANA システム、Oracle データベース、または複数のボリュームを必要とするその他のワークロードと共に手動 QoS 容量プールを使用するときには、容量プールを使用してこれらのアプリケーションのボリュームを作成できます。  各ボリュームでは、アプリケーションの要件を満たすサイズとスループットを個々に提供できます。  メリットの詳細については、「[手動 QoS 容量プールのボリュームにおけるスループット制限の例](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool)」を参照してください。  

## <a name="volumes"></a><bpt id="p1"><a name="volumes"></bpt><ept id="p1"></a></ept>ボリューム

- ボリュームは、論理容量消費で測定され、スケーラブルです。 
- ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。
- ボリュームのスループット消費量は、そのプールで使用可能なスループットに対してカウントされます。 「<bpt id="p1">[</bpt>"手動" の種類の QoS<ept id="p1">](#manual_qos_type)</ept>」を参照してください。
- 各ボリュームが従属するプールは 1 つだけですが、1 つのプールは複数のボリュームを含むことができます。 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><bpt id="p1"><a name="conceptual_diagram_of_storage_hierarchy"></bpt><ept id="p1"></a></ept>ストレージ階層の概念図 
次の例は、Azure サブスクリプション、NetApp アカウント、容量プール、ボリュームの関係を示したものです。   

![ストレージ階層の概念図](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>次のステップ

- <bpt id="p1">[</bpt>Azure NetApp Files のリソース制限<ept id="p1">](azure-netapp-files-resource-limits.md)</ept>
- <bpt id="p1">[</bpt>Azure NetApp Files のサービス レベル<ept id="p1">](azure-netapp-files-service-levels.md)</ept>
- <bpt id="p1">[</bpt>Azure NetApp Files のパフォーマンスに関する考慮事項<ept id="p1">](azure-netapp-files-performance-considerations.md)</ept>
- <bpt id="p1">[</bpt>容量プールの作成<ept id="p1">](azure-netapp-files-set-up-capacity-pool.md)</ept>
- <bpt id="p1">[</bpt>手動 QoS 容量プールを管理する<ept id="p1">](manage-manual-qos-capacity-pool.md)</ept>
