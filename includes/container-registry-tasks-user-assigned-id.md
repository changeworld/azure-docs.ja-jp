---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781146"
---
### <a name="create-a-user-assigned-identity"></a>ユーザー割り当て ID を作成する

[az identity create][az-identity-create] コマンドを使って、サブスクリプションに *myACRTasksId* という名前の ID を作成します。 前にコンテナー レジストリを作成するために使ったものと同じリソース グループ、または別のリソース グループを使用できます。

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

後の手順でユーザー割り当て ID を構成するために、[az identity show][az-identity-show] コマンドを使用して、ID のリソース ID、プリンシパル ID、およびクライアント ID を変数に保存します。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
