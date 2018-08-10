---
title: Azure Kubernetes クラスターのサービス プリンシパル
description: AKS で Kubernetes クラスターの Azure Active Directory サービス プリンシパルを作成および管理します
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4ad0fc3fdb7d5b7c14f13fd6c279915974558dc9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578798"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのサービス プリンシパル

AKS クラスターには、Azure API と対話するための [Azure Active Directory サービス プリンシパル][aad-service-principal]が必要です。 サービス プリンシパルは、[Azure Load Balancer][azure-load-balancer-overview] などのリソースを動的に作成および管理するために必要です。

この記事では、AKS で Kubernetes クラスターのサービス プリンシパルを設定するためのさまざまなオプションを紹介します。

## <a name="before-you-begin"></a>開始する前に


Azure AD サービス プリンシパルを作成するには、アプリケーションを Azure AD テナントに登録し、そのアプリケーションをサブスクリプション内のロールに割り当てるためのアクセス許可が必要です。 必要なアクセス許可がない場合は、必要なアクセス許可の割り当てを Azure AD またはサブスクリプションの管理者に依頼するか、Kubernetes クラスターのサービス プリンシパルを事前に作成することが必要になる可能性があります。

また、Azure CLI バージョン 2.0.27 以降がインストールされ、構成されていることも必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="create-sp-with-aks-cluster"></a>AKS クラスターで SP を作成する

`az aks create` コマンドを使用して AKS クラスターをデプロイするときに、サービス プリンシパルを自動的に生成することもできます。

次の例では、AKS クラスターが作成されます。また、既存のサービス プリンシパルが指定されていないため、そのクラスター用にサービス プリンシパルが作成されます。 この操作を完了するために、アカウントには、サービス プリンシパルを作成するための適切な権限が必要です。

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>既存の SP を使用する

既存の Azure AD サービス プリンシパルは、AKS クラスターで使用することも、AKS クラスターで使用するために事前に作成しておくことも可能です。 これは、サービス プリンシパルの情報を指定する必要がある Azure Portal からクラスターをデプロイする際に役立ちます。 既存のサービス プリンシパルを使用するとき、クライアント シークレットはパスワードとして構成されている必要があります。

## <a name="pre-create-a-new-sp"></a>新しい SP を事前に作成する

Azure CLI を使用してサービス プリンシパルを作成するには、[az ad sp create-for-rbac][az-ad-sp-create] コマンドを使用します。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

出力は次のようになります。 `appId` と `password` を書き留めておきます。 これらの値は、AKS クラスターの作成時に使用します。

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>既存の SP を使用する

事前に作成したサービス プリンシパルを使用するときに、`appId` と `password` を引数の値として `az aks create` コマンドに指定します。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Azure Portal を使用して AKS クラスターをデプロイする場合は、AKS クラスター構成フォームの **[サービス プリンシパル クライアント ID]** フィールドに `appId` 値を、**[Service principal client secret]\(サービス プリンシパル クライアント シークレット\)** フィールドに `password` 値を入力します。

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>追加の考慮事項

AKS と Azure AD サービス プリンシパルを使用する場合は、次の点に注意してください。

* Kubernetes のサービス プリンシパルは、クラスター構成の一部です。 ただし、クラスターのデプロイに ID を使用しないでください。
* すべてのサービス プリンシパルは、Azure AD アプリケーションに関連付けられています。 Kubernetes クラスターのサービス プリンシパルは、有効な任意の Azure AD アプリケーション名 (たとえば `https://www.contoso.org/example`) に関連付けることができます。 アプリケーションの URL は、実際のエンドポイントである必要はありません。
* サービス プリンシパル **クライアント ID** を指定するときは、`appId` の値を使用します。
* Kubernetes クラスター内のマスター VM とノード VM では、サービス プリンシパルの資格情報が `/etc/kubernetes/azure.json` ファイルに格納されます。
* `az aks create` コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルの資格情報は、コマンドの実行に使用されたコンピューター上の `~/.azure/aksServicePrincipal.json` ファイルに書き込まれます。
* `az aks create` によって作成された AKS クラスターを削除しても、自動的に作成されたサービス プリンシパルは削除されません。 サービス プリンシパルを削除するには、まず、[az ad app list][az-ad-app-list] でサービス プリンシパルの ID を取得します。 次の例では、*myAKSCluster* という名前のクラスターを検索するクエリを実行し、該当するアプリ ID を [az ad app delete][az-ad-app-delete] で削除しています。 これらの名前は、実際の値に置き換えてください。

    ```azurecli-interactive
    az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
    az ad app delete --id <appId>
    ```

## <a name="next-steps"></a>次の手順

Azure Active Directory サービス プリンシパルの詳細については、Azure AD アプリケーションのドキュメントを参照してください。

> [!div class="nextstepaction"]
> [アプリケーション オブジェクトとサービス プリンシパル オブジェクト][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
