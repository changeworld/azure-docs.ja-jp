---
title: チュートリアル - Azure Red Hat OpenShift 4 クラスターを作成する
description: Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターを作成する方法を学習します
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d9b02c11c055b4b072c5f8a1ff47e44001ec4580
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509722"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>チュートリアル:Azure Red Hat OpenShift 4 クラスターを作成する

このチュートリアルは、3 部構成のパート 1 です。OpenShift 4 が実行される Azure Red Hat OpenShift クラスターを作成する環境を準備し、クラスターを作成します。 学習内容:
> [!div class="checklist"]
> * 前提条件を設定し、必要な仮想ネットワークとサブネットを作成する
> * クラスターのデプロイ

## <a name="before-you-begin"></a>開始する前に

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.75 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

### <a name="install-the-az-aro-extension"></a>`az aro` 拡張機能をインストールする
`az aro` 拡張機能を使用すると、Azure CLI を使用してコマンド ラインから直接 Azure Red Hat OpenShift クラスターを作成、アクセス、削除できます。

次のコマンドを実行して、`az aro` 拡張機能をインストールします。

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

拡張機能が既にインストールされている場合は、次のコマンドを実行して更新できます。

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

次に、お使いのサブスクリプションに `Microsoft.RedHatOpenShift` リソースプロバイダーを登録する必要があります。

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

拡張機能が登録されていることを確認します。

```azurecli-interactive
az -v
```

  次のような出力が表示されます。

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat プル シークレットを取得する (省略可能)

Red Hat プル シークレットを使用すると、クラスターは追加のコンテンツと共に Red Hat コンテナー レジストリにアクセスできます。 この手順は省略可能ですが、実施することをお勧めします。

https://cloud.redhat.com/openshift/install/azure/aro-provisioned に移動し、 *[Download pull secret]\(プル シークレットのダウンロード\)* をクリックして、プル シークレットを取得します。

Red Hat アカウントにログインするか、お使いのビジネス メール アドレスを使用して新しい Red Hat アカウントを作成し、使用条件に同意する必要があります。

保存されている `pull-secret.txt` ファイルは安全な場所に保管してください。このファイルは、クラスターを作成するたびに使用します。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>2 つの空のサブネットを含む仮想ネットワークを作成する

次に、2 つの空のサブネットを含む仮想ネットワークを作成します。

1. **次の変数を設定します。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **リソース グループの作成**

    Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    次の出力例では、正常に作成されたリソース グループが示されています。

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **仮想ネットワークを作成します。**

    OpenShift 4 を実行する Azure Red Hat OpenShift クラスターでは、マスター ノードとワーカー ノード用の 2 つの空のサブネットを持つ仮想ネットワークが必要です。

    前に作成したのと同じリソース グループ内に新しい仮想ネットワークを作成します。

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    次の出力例では、正常に作成された仮想ネットワークが示されています。

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **マスター ノード用の空のサブネットを追加します。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **ワーカー ノード用の空のサブネットを追加します。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **マスター サブネットの[サブネット プライベート エンドポイント ポリシーを無効にします](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。** これは、クラスターに接続して管理できるようにするために必要です。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>クラスターを作成する

次のコマンドを実行して、クラスターを作成します。 必要に応じて、プル シークレットを渡して、クラスターが追加のコンテンツと共に Red Hat コンテナー レジストリにアクセスできるようにすることができます。 [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) に移動し、 **[Copy Pull Secret]\(プル シークレットのコピー\)** をクリックして、プル シークレットにアクセスします。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> 通常、クラスターの作成には約 35 分かかります。

>[!IMPORTANT]
> **foo.example.com** などのカスタム ドメインを指定すると、組み込みドメイン `https://console-openshift-console.apps.<random>.<location>.aroapp.io` の代わりに `https://console-openshift-console.apps.foo.example.com` などの URL で OpenShift コンソールを使用できるようになります。
>
> OpenShift では、既定で、`*.apps.<random>.<location>.aroapp.io` に作成されたすべてのルートに自己署名証明書が使用されます。  クラスターに接続した後でカスタム DNS を使用する場合は、OpenShift のドキュメントに従って、[イングレス コントローラー用のカスタム CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) と、[API サーバー用のカスタム CA を構成する](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)必要があります。
>

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * 前提条件を設定し、必要な仮想ネットワークとサブネットを作成する
> * クラスターのデプロイ

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターに接続する](tutorial-connect-cluster.md)
