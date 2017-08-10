---
title: "Azure Container Registry から Azure Container Instances へのデプロイ | Azure Docs"
description: "Azure Container Registry から Azure Container Instances へのデプロイ"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: fcb21785584c3c5fb41f5ceb70346e3e05a30d93
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Azure Container Registry から Azure Container Instances へのデプロイ

Azure Container Registry は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 この記事では、Azure Container Registry に格納されているコンテナー イメージを Azure Container Instances にデプロイする方法について説明します。

## <a name="using-the-azure-cli"></a>Azure CLI の使用

Azure CLI には、Azure Container Instances でコンテナーを作成および管理するためのコマンドが含まれています。 `create` コマンドでプライベート イメージを指定する場合は、コンテナー レジストリでの認証に必要なイメージ レジストリ パスワードも指定できます。

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --image-registry-password myRegistryPassword --resource-group myresourcegroup
```

`create` コマンドでは、`image-registry-login-server` と `image-registry-username` の指定もサポートされます。 ただし、既定では、Azure Container Registry のログイン サーバーは単に *registryname*.azurecr.io で、ユーザー名は *registryname* であるため、こうした値が明示的に指定されていない場合、値はイメージ名から推測されます。

## <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートでは、Azure Container Registry のプロパティを指定できます。 コンテナー グループの定義に `imageRegistryCredentials` プロパティを含めるだけです。

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

テンプレートでコンテナー レジストリにパスワードを直接保存することを避けるため、[Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) にパスワードをシークレットとして保存し、[Azure Resource Manager と Key Vault の間でのネイティブ統合](../azure-resource-manager/resource-manager-keyvault-parameter.md)を使用して、それをテンプレートで参照することをお勧めします。


## <a name="next-steps"></a>次のステップ

[チュートリアルを完了](container-instances-tutorial-prepare-app.md)することで、コンテナーを作成し、そのコンテナーをプライベート コンテナー レジストリにプッシュして、Azure Container Instances にデプロイします。
