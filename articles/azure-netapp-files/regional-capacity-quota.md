---
title: Azure NetApp Files のリージョンの容量クォータ | Microsoft Docs
description: Azure NetApp Files のリージョンの容量クォータについて説明します。
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
ms.date: 10/11/2021
ms.author: b-juche
ms.openlocfilehash: 1ae89ff8b8593d96740b0140a1570ac19deacea3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270091"
---
# <a name="regional-capacity-quota-for-azure-netapp-files"></a>Azure NetApp Files のリージョンの容量クォータ

この記事では、Azure NetApp Files のリージョンの容量クォータについて説明します。

## <a name="display-regional-capacity-quota"></a>リージョンの容量クォータを表示する

Azure NetApp Files の [設定] にある **[クォータ]** をクリックすると、リージョンの現在のクォータ サイズと既定のクォータ サイズを表示することができます。 

例: 

![クォータの情報を表示する方法を示すスクリーンショット。](../media/azure-netapp-files/quota-display.png) 

## <a name="request-regional-capacity-quota-increase"></a>リージョンの容量クォータの引き上げをリクエストする

[サポート リクエストを送信](azure-netapp-files-resource-limits.md#request-limit-increase)することで、追加コストをかけることなく、リージョンの容量クォータを引き上げることができます。 送信したサポート リクエストは、処理のために Azure 容量管理チームに送信されます。 通常、2 営業日以内に返信が届きます。 大規模なリクエストの場合は、Azure 容量管理チームから連絡が届く場合があります。  

リージョンごとの容量クォータを増やしても、課金は増えません。 課金はこれまで通り、プロビジョニングされた容量プールに基づいて行われます。
たとえば、現在 25 TiB のプロビジョニングされた容量がある場合は、35 TiB へのクォータの引き上げをリクエストできます。  2 営業日以内に、クォータの引き上げが要求されたリージョンに適用されます。 クォータの引き上げが適用された場合でも、現在プロビジョニングされている容量 (25 TiB) に対してのみ料金を支払います。 ただし、実際に追加の 10 TiB をプロビジョニングすると、35 TiB に対して請求されます。

Azure NetApp Files の現在の[リソース制限](azure-netapp-files-resource-limits.md#resource-limits)は変わりません。 引き続き 500 TiB の容量プールをプロビジョニングできます。 しかし、その前に、リージョンの容量クォータを 500 TiB に増やす必要があります。

## <a name="next-steps"></a>次のステップ  

- [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
- [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
