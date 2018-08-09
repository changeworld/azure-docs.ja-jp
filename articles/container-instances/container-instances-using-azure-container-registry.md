---
title: Azure Container Registry から Azure Container Instances へのデプロイ
description: Azure Container Registry のコンテナー イメージを使用して、Azure Container Instances のコンテナーをデプロイする方法を紹介します。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 5c3cf162caf5cf9aa88b012257d4caab37b7893c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424213"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure Container Registry から Azure Container Instances へのデプロイ

Azure Container Registry は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 この記事では、Azure Container Registry に格納されているコンテナー イメージを Azure Container Instances にデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

**Azure Container Registry**: この記事の手順を完了するには、Azure コンテナー レジストリのほかにレジストリ内に 1 つ以上のコンテナー イメージが必要です。 レジストリが必要な場合は、「[Azure CLI を使用したコンテナー レジストリの作成](../container-registry/container-registry-get-started-azure-cli.md)」を参照してください。

**Azure CLI**: この記事のコマンドラインの例では、[Azure CLI](/cli/azure/) を使用して Bash シェルに合わせた形式に設定されます。 ローカルに [Azure CLI をインストール](/cli/azure/install-azure-cli)するほかに、[Azure Cloud Shell][cloud-shell-bash] を使用することもできます。

## <a name="configure-registry-authentication"></a>レジストリの認証を構成する

本番環境シナリオでは、Azure コンテナー レジストリへのアクセス権を[サービス プリンシパル](../container-registry/container-registry-auth-service-principal.md)を使用して提供する必要があります。 サービス プリンシパルを使用すると、コンテナー イメージへのロールベースのアクセス制御を提供できます。 たとえば、レジストリへのプルのみのアクセス権を持つサービス プリンシパルを設定できます。

このセクションでは、Azure Key Vault とサービス プリンシパルを作成し、サービス プリンシパルの資格情報を資格情報コンテナーに格納します。

### <a name="create-key-vault"></a>キー コンテナーの作成

[Azure Key Vault](/azure/key-vault/) に資格情報コンテナーがない場合、次のコマンドを使用して Azure CLI で 1 つ作成します。

`RES_GROUP` 変数を、キー コンテナーを作成するリソース グループの名前で、`ACR_NAME` をコンテナー レジストリの名前でそれぞれ更新します。 `AKV_NAME` で新しいキー コンテナーの名前を指定します。 資格情報コンテナー名は、3 ～ 24 文字の英数字で、Azure 内で一意である必要があります。名前の先頭には英字、末尾には英字または数字を使用する必要があります。また、ハイフンを連続させることはできません。

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>サービス プリンシパルを作成し、資格情報を格納する

これで、サービス プリンシパルを作成し、その資格情報をキー コンテナーに格納する必要があります。

次のコマンドは、[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] を使用してサービス プリンシパルを作成し、[az keyvault secret set][az-keyvault-secret-set] を使用して資格情報コンテナーにサービス プリンシパルの**パスワード**を格納します。

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

上記のコマンドの `--role` 引数により、*reader* ロールを持つサービス プリンシパルが構成されます。これにより、レジストリへのプルのみのアクセス権が付与されます。 プッシュ アクセス権とプル アクセス権の両方を付与するには、`--role` 引数を *contributor* に変更します。

次にサービス プリンシパルの *appId* を資格情報コンテナーに格納します。appId は、認証のために Azure Container Registry に渡す**ユーザー名**です。

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Azure Key Vault を作成してに 2 つのシークレットを格納します。

* `$ACR_NAME-pull-usr`: サービス プリンシパルID。コンテナー レジストリの**ユーザー名**として使用します。
* `$ACR_NAME-pull-pwd`: サービス プリンシパルのパスワード。コンテナー レジストリの**パスワード**として使用します。

これらのシークレットは、アプリケーションおよびサービスがレジストリからイメージをプルしたときの名前で参照できます。

## <a name="deploy-container-with-azure-cli"></a>Azure CLI でコンテナーをデプロイする

これでサービス プリンシパルの資格情報を Azure Key Vault シークレットに格納し、格納した資格情報をアプリケーションとサービスで使用してプライベート レジストリにアクセスできます。

次の [az container create][az-container-create] コマンドを実行して、コンテナー インスタンスをデプロイします。 このコマンドは、Azure Key Vault に格納されたサービス プリンシパルの資格情報を使用して、コンテナー レジストリに対する認証を行います。事前に [aci-helloworld](container-instances-quickstart.md) イメージをレジストリにプッシュしていることが前提です。 レジストリにある別のイメージを使用する場合、`--image` の値を更新します。

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_NAME.azurecr.io/aci-helloworld:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

前のコマンドが、コンテナーの DNS 名のラベルに乱数を追加するため、`--dns-name-label` の値は Azure 内で一意でなければなりません。 コマンドの出力には、次のようにコンテナーの完全修飾ドメイン名 (FQDN) が表示されます。

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

コンテナーが正常に開始されると、ブラウザーでその FQDN に移動して、アプリケーションが正常に実行されていることを確認できます。

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

Resource Manager テンプレートでの Azure Key Vault シークレットの参照については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/resource-manager-keyvault-parameter.md)」を参照してください。

## <a name="deploy-with-azure-portal"></a>Azure Portal でのデプロイ

Azure Container Registry にコンテナー イメージを保持している場合は、Azure ポータルを使用して Azure Container Instances 内にコンテナーを簡単に作成できます。

1. Azure ポータルで、自分のコンテナー レジストリに移動します。

1. **[リポジトリ]** を選択し、次にデプロイ元のリポジトリを選択して、デプロイするコンテナー イメージのタグを右クリックし、**[実行インスタンス]** を選択します。

    ![Azure Portal の Azure Container Registry の "実行インスタンス"][acr-runinstance-contextmenu]

1. コンテナーの名前とリソース グループの名前を入力します。 必要に応じて既定の値を変更することもできます。

    ![Azure Container Instances の [作成] メニュー][acr-create-deeplink]

1. デプロイが完了したら、通知ウィンドウからコンテナー グループに移動して、その IP アドレスとその他のプロパティを確認できます。

    ![Azure Container Instances のコンテナー グループの詳細ビュー][aci-detailsview]

## <a name="next-steps"></a>次の手順

Azure Container Registry の認証について詳しくは、「[Azure コンテナー レジストリによる認証](../container-registry/container-registry-authentication.md)」をご覧ください。

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set