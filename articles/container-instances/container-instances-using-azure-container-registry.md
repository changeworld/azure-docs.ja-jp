---
title: "Azure Container Registry から Azure Container Instances へのデプロイ"
description: "Azure Container Registry のコンテナー イメージを使用して、Azure Container Instances のコンテナーをデプロイする方法をご紹介します。"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/24/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c69b95f66bf2eaf4975961da5b25f5ac6172798c
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure Container Registry から Azure Container Instances へのデプロイ

Azure Container Registry は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 この記事では、Azure Container Registry に格納されているコンテナー イメージを Azure Container Instances にデプロイする方法について説明します。

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

Azure CLI には、Azure Container Instances でコンテナーを作成および管理するためのコマンドが含まれています。 [az container create][az-container-create] コマンドでプライベート イメージを指定する場合は、コンテナー レジストリでの認証に必要なイメージ レジストリ パスワードも指定できます。

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

[az container create][az-container-create] コマンドで、`--registry-login-server` と `--registry-username` を指定することもできます。 ただし、Azure Container Registry のログイン サーバーは常に *registryname*.azurecr.io であり、既定のユーザー名は *registryname* であるため、これらの値が明示的に指定されていない場合はイメージ名から推測されます。

## <a name="deploy-with-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用したデプロイ

`imageRegistryCredentials` プロパティをコンテナー グループに含めることで、Azure Resource Manager テンプレート内に Azure Container Registry のプロパティを指定できます。

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

コンテナー レジストリのパスワードをテンプレートに直接保存することを避けるため、パスワードを [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) にシークレットとして保存し、[Azure Resource Manager と Key Vault のネイティブ統合](../azure-resource-manager/resource-manager-keyvault-parameter.md)を使用してテンプレート内で参照することをお勧めします。

## <a name="deploy-with-azure-portal"></a>Azure Portal でのデプロイ

Azure Container Registry にコンテナー イメージを保持している場合は、Azure ポータルを使用して Azure Container Instances 内にコンテナーを簡単に作成できます。

1. Azure ポータルで、自分のコンテナー レジストリに移動します。

1. **[リポジトリ]** を選択し、次にデプロイ元のリポジトリを選択して、デプロイするコンテナー イメージのタグを右クリックし、**[実行インスタンス]** を選択します。

    ![Azure Portal の Azure Container Registry の "実行インスタンス"][acr-runinstance-contextmenu]

1. コンテナーの名前とリソース グループの名前を入力します。 必要に応じて既定の値を変更することもできます。

    ![Azure Container Instances の [作成] メニュー][acr-create-deeplink]

1. デプロイが完了したら、通知ウィンドウからコンテナー グループに移動して、その IP アドレスとその他のプロパティを確認できます。

    ![Azure Container Instances のコンテナー グループの詳細ビュー][aci-detailsview]

## <a name="service-principal-authentication"></a>サービス プリンシパルの認証

Azure コンテナー レジストリの管理者ユーザーが無効になっている場合は、コンテナー インスタンス作成時に、Azure Active Directory の[サービス プリンシパル](../container-registry/container-registry-auth-service-principal.md)を使用して、レジストリへの認証を行うことができます。 サービス プリンシパルを用いた認証は、無人でコンテナー インスタンスを作成するスクリプトやアプリケーションといった、ヘッドレス シナリオでも推奨されています。

詳細については、「[Authenticate with Azure Container Registry from Azure Container Instances (Azure Container Registry による Azure Container Instances からの認証)](../container-registry/container-registry-auth-aci.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

[チュートリアルを完了](container-instances-tutorial-prepare-app.md)することで、コンテナーを作成し、そのコンテナーをプライベート コンテナー レジストリにプッシュして、Azure Container Instances にデプロイします。

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create