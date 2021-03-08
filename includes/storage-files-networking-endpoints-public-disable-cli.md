---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465052"
---
次の CLI コマンドを使用すると、ストレージ アカウントのパブリック エンドポイントに対するすべてのトラフィックが拒否されます。 このコマンドでは、`-bypass` パラメーターが `AzureServices` に設定されていることに注意してください。 これにより、Azure File Sync などの信頼できるファーストパーティのサービスが、パブリック エンドポイント経由でストレージ アカウントにアクセスできるようになります。

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```