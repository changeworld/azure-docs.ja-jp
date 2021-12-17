---
title: Azure NetApp Files のボリューム クォータについて | Microsoft Docs
description: ボリューム クォータの概要について説明します。 また、ボリュームとプールの容量の監視と管理に関するリファレンスも提供します。
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
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: ffcc36cecc4dbba32a8f3927ab88a5632018a9bb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219756"
---
# <a name="understand-volume-quota"></a>ボリューム クォータについて

この記事では、Azure NetApp Files のボリューム クォータに関する概要について説明します。 また、ボリュームまたは容量プールの容量の監視と管理に役立つ詳細情報のリファレンスも提供します。  

## <a name="behaviors-of-volume-quota"></a>ボリューム クォータの動作 

* Azure NetApp Files ボリュームのストレージ容量は、ボリュームのセット サイズ (クォータ) に制限されます。 

* ボリューム消費量が最大になると、ボリュームも基になる容量プールも自動的には増加しません。 代わりに、ボリュームは "領域不足" という状態を受け取ります。 ただし、必要に応じて[容量プールまたはボリュームのサイズを変更](azure-netapp-files-resize-capacity-pools-or-volumes.md)することができます。 基になる容量プールと[ボリュームの容量を積極的に監視](monitor-volume-capacity.md)する必要があります。

* 容量プールの種類によっては、Azure NetApp Files ボリュームのサイズ (クォータ) が、帯域幅のパフォーマンスとプロビジョニングされた容量に影響を与えます。  詳細については、[自動 QoS プールの種類](azure-netapp-files-understand-storage-hierarchy.md#qos_types)に関するページを参照してください。 

* ボリューム [スナップショット](snapshots-introduction.md)によって消費された容量は、ボリューム内のプロビジョニングされたスペースから減算されます。 

* ボリューム クォータは、[レプリケーションの宛先ボリューム](cross-region-replication-introduction.md)には適用されません。

* 容量消費の計算と容量消費の超過については、「[Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
* [ボリュームの容量を監視する](monitor-volume-capacity.md)
* [容量プールまたはボリュームのサイズを変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [容量管理に関する FAQ](faq-capacity-management.md)