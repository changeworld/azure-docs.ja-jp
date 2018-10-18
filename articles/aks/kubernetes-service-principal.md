---
title: Azure Kubernetes Service (AKS) 用のサービス プリンシパル
description: Azure Kubernetes Service (AKS) のクラスター用の Azure Active Directory サービス プリンシパルを作成して管理する
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: ef3139c4b3f06644b219e177fad0c094ed600fb6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394592"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのサービス プリンシパル

Azure API で操作するために、AKS クラスターには [Azure Active Directory (AD) サービス プリンシパル][aad-service-principal]が必要です。 サービス プリンシパルは、Azure のロード バランサーやコンテナー レジストリ (ACR) などの Azure リソースを動的に作成し、管理するために必要です。

この記事では、AKS クラスター用のサービス プリンシパルを作成して使用する方法を示します。

## <a name="before-you-begin"></a>開始する前に

Azure AD サービス プリンシパルを作成するには、アプリケーションを Azure AD テナントに登録し、そのアプリケーションをサブスクリプション内のロールに割り当てるためのアクセス許可が必要です。 必要なアクセス許可がない場合は、必要なアクセス許可を割り当てるよう Azure AD またはサブスクリプションの管理者に依頼するか、AKS クラスターで使用するサービス プリンシパルを事前に作成する必要が生じることがあります。

また、Azure CLI バージョン 2.0.46 以降がインストールされ、構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="automatically-create-and-use-a-service-principal"></a>サービス プリンシパルを自動的に作成して使用する

Azure portal で、または [az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成するときには、Azure によるサービス プリンシパルの自動生成が可能です。

次の Azure CLI の例では、サービス プリンシパルは指定されていません。 このシナリオでは、Azure CLI によって、AKS クラスター用のサービス プリンシパルが作成されます。 この操作を正しく完了するためには、Azure アカウントが、サービス プリンシパルを作成するための適切な権限を持っている必要があります。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="manually-create-a-service-principal"></a>手動でサービス プリンシパルを作成する

Azure CLI を使用して手動でサービス プリンシパルを作成するには、[az ad sp create-for-rbac][az-ad-sp-create] コマンドを使用します。 次の例では、`--skip-assignment` パラメーターによって、追加の既定の割り当てが行われないようにしています。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

出力は次の例のようになります。 使っている `appId` と `password` をメモします。 これらの値は、次のセクションで AKS クラスターを作成するときに使用します。

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2018-09-25-21-10-19",
  "name": "http://azure-cli-2018-09-25-21-10-19",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>AKS クラスター用のサービス プリンシパルを指定する

[az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成するときに既存のサービス プリンシパルを使用するには、`--service-principal` パラメーターと `--client-secret` パラメーターを使用して、[az ad sp create-for-rbac][az-ad-sp-create] コマンドの出力からの `appId` と `password` を指定します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

Azure portal を使用して AKS クラスターをデプロイする場合は、**[Create Kubernetes cluster]\(Kubernetes クラスターの作成)** ダイアログ ボックスの *[Authentication]\(認証)* ページで、**[Configure service principal]\(サービス プリンシパルの構成)** を選択します。 **[既存のものを使用]** を選択し、以下の値を指定します。

- **[Service principal client ID]\(サービス プリンシパルのクライアント ID)** は、実際の *appId* です
- **[Service principal client secret]\(サービス プリンシパルのクライアント シークレット)** は、*password* の値です

![Azure Vote にブラウザーでアクセスしたところ](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="additional-considerations"></a>追加の考慮事項

AKS と Azure AD サービス プリンシパルを使用する場合は、以下の考慮事項を念頭に置いてください。

- Kubernetes のサービス プリンシパルは、クラスター構成の一部です。 ただし、クラスターのデプロイに ID を使用しないでください。
- すべてのサービス プリンシパルは、Azure AD アプリケーションに関連付けられています。 Kubernetes クラスターのサービス プリンシパルは、有効な任意の Azure AD アプリケーション名 (たとえば *https://www.contoso.org/example*) に関連付けることができます。 アプリケーションの URL は、実際のエンドポイントである必要はありません。
- サービス プリンシパルの**クライアント ID** を指定するときには、`appId` の値を使用します。
- Kubernetes クラスター内のマスター VM とノード VM では、サービス プリンシパルの資格情報が `/etc/kubernetes/azure.json` ファイルに格納されます
- [az aks create][az-aks-create] コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルの資格情報は、コマンドの実行に使用されたコンピューター上の `~/.azure/aksServicePrincipal.json` ファイルに書き込まれます。
- [az aks create][az-aks-create] によって作成された AKS クラスターを削除しても、自動的に作成されたサービス プリンシパルは削除されません。
    - サービス プリンシパルを削除するには、まず、[az ad app list][az-ad-app-list] でサービス プリンシパルの ID を取得します。 次の例では、*myAKSCluster* という名前のクラスターを検索するクエリを実行し、該当するアプリ ID を [az ad app delete][az-ad-app-delete] で削除しています。 これらの名前は、実際の値に置き換えてください。

        ```azurecli
        az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
        az ad app delete --id <appId>
        ```

## <a name="next-steps"></a>次の手順

Azure Active Directory サービス プリンシパルの詳細については、「[アプリケーション オブジェクトとサービス プリンシパル オブジェクト][service-principal]」を参照してください

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
[az-aks-create]: /cli/azure/aks#az-aks-create
