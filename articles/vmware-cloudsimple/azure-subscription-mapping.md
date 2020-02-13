---
title: Azure サブスクリプション マッピングでリソース プールを作成する
description: Azure サブスクリプション マッピングを使用して AVS プライベート クラウドのリソース プールを作成する方法について説明します
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014965"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Azure サブスクリプション マッピングを使用して AVS プライベート クラウドのリソース プールを作成する
Azure サブスクリプション マッピングでは、利用できる vSphere リソース プールから AVS プライベート クラウドのリソース プールを作成できます。 AVS ポータルで、AVS プライベート クラウドの Azure サブスクリプションを表示および管理できます。

> [!NOTE]
> リソース プールをマップすると、子リソース プールもすべてマップされます。 子リソース プールが既にマップされている場合、親リソース プールはマップできません。

1. [AVS ポータルにアクセスします](access-cloudsimple-portal.md)。
2. **[リソース]** ページを開き、 **[Azure subscriptions mapping]\(Azure サブスクリプション マッピング\)** を選択します。  
3. **[Edit Azure subscription mapping]\(Azure サブスクリプション マッピングの編集\)** をクリックします。  
4. 使用可能なリソース プールをマップするには、左側でそれらを選択し、右向きの矢印をクリックします。 
5. マッピングを削除するには、右側でそれらを選択し、左向きの矢印をクリックします。 

    ![Azure サブスクリプション](media/resources-azure-mapping.png)

6. **[OK]** をクリックします。
