---
title: "Azure Container Instances から Azure Container Registry の認証を受ける"
description: "Azure Active Directory サービス プリンシパルを使用して、Azure Container Instances からプライベート コンテナー レジストリ内のイメージへのアクセスを許可する方法を説明します。"
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 00d9632a5d0c42eceee1b412f8963bbadbea651f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Azure Container Instances から Azure Container Registry の認証を受ける

Azure Active Directory (Azure AD) サービス プリンシパルを使って、Azure Container Registry 内のプライベート コンテナー レジストリへのアクセスを提供できます。

この記事では、Azure AD サービス プリンシパルを作成し、レジストリへの*プル*権限を構成する方法を説明します。 その後で、認証用のサービス プリンシパルを使って、プライベート レジストリからそのイメージをプルする Azure Container Instances (ACI) のコンテナーを起動します。

## <a name="when-to-use-a-service-principal"></a>サービス プリンシパルを使う場合

サービス プリンシパルは、自動的にまたはそれ以外の無人の方法でコンテナー インスタンスを作成するアプリケーションやサービスなどの**ヘッドレス シナリオ**で ACI から認証を受けるために使う必要があります。

たとえば、夜間に実行され、何らかのデータを処理する[タスク ベースのコンテナー インスタンス](../container-instances/container-instances-restart-policy.md)を作成する自動スクリプトがある場合、このスクリプトでプルのみ (閲覧者) のアクセス許可を持つサービス プリンシパルを使ってレジストリへの認証を受けることができます。 その後、サービス プリンシパルの資格情報を交換したり、他のサービスやアプリケーションにまったく影響を与えずにそのアクセスを取り消したりできます。

サービス プリンシパルは、レジストリの [admin ユーザー](container-registry-authentication.md#admin-account)が無効のときにも使う必要があります。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>サービス プリンシパルを使って認証を受ける

サービス プリンシパルを使って Azure Container Instances 内のコンテナーを起動するには、`--registry-username` でその ID、`--registry-password` でそのパスワードを指定します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="next-steps"></a>次の手順

次の記事には、サービス プリンシパルと ACR の使用方法の詳細が含まれています。

* [サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md)
* [Azure Container Service (AKS) から Azure Container Registry の認証を受ける](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->
