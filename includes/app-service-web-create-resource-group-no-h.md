---
title: "インクルード ファイル"
description: "インクルード ファイル"
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8d0367b4e087da5954e9b16828655625978634fb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
[!INCLUDE [resource group intro text](resource-group.md)]

Cloud Shell で [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) コマンドを使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを "*西ヨーロッパ*" の場所に作成します。 App Service がサポートされているすべての場所を表示するには、[`az appservice list-locations`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations) コマンドを実行します。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

通常は、現在地付近の地域にリソース グループおよびリソースを作成します。 

コマンドが完了すると、リソース グループのプロパティが JSON 出力に表示されます。