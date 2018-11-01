---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: d44865dc3189a7f9dc05106baf9f4d120e5e8bf6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133290"
---
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az_group_create) でリソース グループを作成します。 Azure リソース グループとは、Function App、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、`myResourceGroup` という名前のリソース グループを作成します。  
Cloud Shell を使用していない場合は、まず `az login` でサインインします。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
通常は、現在地付近の地域にリソース グループおよびリソースを作成します。 App Service プランがサポートされているすべての場所を表示するには、[az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations) コマンドを実行します。