---
title: Azure NetApp Files を使用してスナップショットを削除する| Microsoft Docs
description: Azure NetApp Files を使用してスナップショットを削除する方法について説明します。
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
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 19ae00a47dfee9fc86a3c5c42b81821934ad5999
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699263"
---
# <a name="delete-snapshots-using-azure-netapp-files"></a>Azure NetApp Files を使用してスナップショットを削除する 

保持する必要がなくなったスナップショットは削除できます。 

> [!IMPORTANT]
> スナップショットの削除操作を元に戻すことはできません。 削除されたスナップショットは復元できません。 

## <a name="steps"></a>手順

1. ボリュームの **[スナップショット]** メニューにアクセスします。 削除するスナップショットを右クリックします。 **[削除]** を選択します。

    ![スナップショットの右クリック メニューについて説明したスクリーンショット](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. [スナップショットの削除] ウィンドウで、 **[はい]** をクリックして、スナップショットを削除することを確定します。 

    ![スナップショットの削除を確認するスクリーンショット](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>次のステップ

* [スナップショットの詳細を表示](snapshots-introduction.md)
* [Azure NetApp Files のスナップショットの概要](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)