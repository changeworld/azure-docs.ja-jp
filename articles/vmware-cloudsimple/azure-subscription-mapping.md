---
title: Azure サブスクリプション マッピングでリソース プールを作成する
titleSuffix: Azure VMware Solution by CloudSimple
description: Azure サブスクリプション マッピングを使用してプライベート クラウドのリソース プールを作成する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425795"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Azure サブスクリプション マッピングを使用してプライベート クラウドのリソース プールを作成する
Azure サブスクリプション マッピングでは、利用できる vSphere リソース プールからプライベート クラウドのリソース プールを作成できます。 CloudSimple ポータルで、プライベート クラウドの Azure サブスクリプションを表示および管理できます。

> [!NOTE]
> リソース プールをマップすると、子リソース プールもすべてマップされます。 子リソース プールが既にマップされている場合、親リソース プールはマップできません。

1. [CloudSimple ポータルにアクセスします](access-cloudsimple-portal.md)。
2. **[リソース]** ページを開き、 **[Azure subscriptions mapping]\(Azure サブスクリプション マッピング\)** を選択します。  
3. **[Edit Azure subscription mapping]\(Azure サブスクリプション マッピングの編集\)** をクリックします。  
4. 使用可能なリソース プールをマップするには、左側でそれらを選択し、右向きの矢印をクリックします。 
5. マッピングを削除するには、右側でそれらを選択し、左向きの矢印をクリックします。 

    ![Azure サブスクリプション](media/resources-azure-mapping.png)

6. **[OK]** をクリックします。
