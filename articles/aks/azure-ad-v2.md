---
title: Azure Kubernetes Service で Azure AD を使用する
description: Azure Kubernetes Service (AKS) における Azure AD の使用方法
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 1ca4b70139ed5e0a136f6f5f2b0382b8c1688983
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389411"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Azure AD を Azure Kubernetes Service (プレビュー) に統合する

> [!Note]
> AD と統合された既存の AKS v1 クラスターは、新しい AKS v2 エクスペリエンスの影響を受けません。

Azure AD と AKS v2 の統合は、Azure AD と AKS v1 の統合エクスペリエンスを簡素化するように設計されています。これまでユーザーは、クライアント アプリとサーバー アプリを作成し、Azure AD テナントでディレクトリの読み取りアクセス許可を付与する必要がありました。 新しいバージョンでは、クライアント アプリとサーバー アプリは AKS リソース プロバイダーで管理されます。

## <a name="limitations"></a>制限事項

* 現在、既存の Azure AD が有効になっている AKS v1 クラスターを v2 エクスペリエンスにアップグレードすることはできません。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトイン単位で利用できます。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、ベストエフォート ベースでカスタマー サポートによって部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポート記事を参照してください。
>
> - [AKS のサポート ポリシー](support-policies.md)
> - [Azure サポートに関する FAQ](faq.md)

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.2.0 以降
- aks-preview 0.4.38 拡張機能
- 最小バージョンが [1.18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries) の kubectl

aks-preview 以降の拡張機能をインストールまたは更新するには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

kubectl をインストールするには、次を使用します
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

他のオペレーティング システムでは、[これらの手順](https://kubernetes.io/docs/tasks/tools/install-kubectl/)を使用します。

> [!CAUTION]
> サブスクリプションで機能を登録した後、現時点ではその機能を登録解除することはできません。 一部のプレビュー機能を有効にすると、サブスクリプションで後で作成されたすべての AKS クラスターに対して既定値が使用される場合があります。 運用サブスクリプションではプレビュー機能を有効にしないでください。 代わりに、プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD が有効になっている AKS クラスターを作成する

次の CLI コマンドを使用し、AKS クラスターを作成できるようになりました。

最初に、Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

次に、AKS クラスターを作成します。

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
上記のコマンドでは、3 ノードの AKS クラスターが作成されますが、クラスターを作成したユーザーは、既定では、このクラスターへのアクセス権を持つグループのメンバーではありません。 このユーザーは、Azure AD グループを作成し、そのグループのメンバーとして自身を追加した後、次のようにクラスターを更新する必要があります。 [こちら](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)で説明されている手順に従います

グループを作成して自身 (およびその他の人を) メンバーとして追加したら、次のコマンドを使用して、Azure AD グループでクラスターを更新できます。

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
また、最初にグループを作成してメンバーを追加した場合は、次のコマンドを使用して作成時に Azure AD グループを有効にすることもできます。

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

正常に作成された Azure AD v2 クラスターの応答本文には、次のセクションが含まれます
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

クラスターは数分以内に作成されます。

## <a name="access-an-azure-ad-enabled-cluster"></a>Azure AD が有効なクラスターへのアクセス
クラスターにアクセスするための管理者資格情報を取得するには、次のようにします。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
次に、kubectl get nodes コマンドを使用して、クラスターのノードを表示します。

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

クラスターにアクセスするためのユーザー資格情報を取得するには、次のようにします。
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
手順に従ってサインインします。

次のようなメッセージが表示された場合:**You must be logged in to the server (Unauthorized)**

上記のユーザーはクラスターへのアクセス権を持つグループに属していないため、エラーが発生しています。

## <a name="next-steps"></a>次のステップ

[Azure AD のロールベースのアクセス制御][azure-ad-rbac]について学習する。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
