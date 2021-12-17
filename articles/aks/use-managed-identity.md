---
title: Azure Kubernetes Service でマネージド ID を使用する
description: Azure Kubernetes Service (AKS) でマネージド ID を使用する方法について説明します。
ms.topic: article
ms.date: 05/12/2021
ms.openlocfilehash: e9a7a0a46e36d544a5b7d785da2b64ecde4f3faa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585304"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service でマネージド ID を使用する

現在、Azure Kubernetes Service (AKS) クラスター (具体的には Kubernetes クラウド プロバイダー) で Azure 内にロード バランサーやマネージド ディスクなどの追加リソースを作成するには、ID が必要です。 この ID には、*マネージド ID* または *サービス プリンシパル* を指定できます。 [サービス プリンシパル](kubernetes-service-principal.md)を使用する場合、それを 1 つ指定する必要があります。指定しない場合、AKS によって代理で作成されます。 マネージド ID を使用する場合は、AKS によってこれが自動作成されます。 サービス プリンシパルを使用するクラスターでは、やがてサービス プリンシパルを更新しないと、そのクラスターを動作させ続けることができない状態になります。 サービス プリンシパルを管理しなければならない場合、複雑さが増すので、代わりにマネージ ID を使用した方が簡単です。 アクセス許可の要件は、サービス プリンシパルにおいてもマネージド ID においても同じです。

*マネージド ID* は、本質的にサービス プリンシパルのラッパーであり、管理がより簡単になります。 MI の資格情報のローテーションは、Azure Active Directory の既定に従って 46 日ごとに自動的に行われます。 AKS では、システム割り当てとユーザー割り当ての両方の種類のマネージド ID が使用されます。 これらの ID は、現在変更できません。 詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) について確認してください。

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- Azure CLI、バージョン 2.23.0 以降

## <a name="limitations"></a>制限事項

* マネージド ID が有効になっているクラスターのテナントの移動/移行はサポートされていません。
* クラスターで `aad-pod-identity` が有効になっている場合、Azure Instance Metadata エンドポイントの呼び出しをインターセプトするよう、Node Managed Identity (NMI) ポッドによりノードの iptables が変更されます。 この構成の場合、Metadata エンドポイントに要求が行われると、ポッドで `aad-pod-identity` が使用されていない場合でも NMI により要求がインターセプトされます。 CRD に定義されているラベルに一致するポッドから Metadata エンドポイントに要求が行われた場合、NMI で何も処理することなく、その要求をプロキシ処理することを `aad-pod-identity` に通知するよう、AzurePodIdentityException CRD を構成できます。 _kube-system_ 名前空間の `kubernetes.azure.com/managedby: aks` ラベルを持つシステム ポッドは、AzurePodIdentityException CRD を構成することで、`aad-pod-identity` で除外してください。 詳細については、「[特定のポッドまたはアプリケーションの aad-pod-identity を無効にする](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)」を参照してください。
  例外を構成するには、[mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml) をインストールします。

## <a name="summary-of-managed-identities"></a>マネージド ID の概要

AKS では、組み込みのサービスとアドオンに対して複数のマネージド ID が使用されます。

| ID                       | 名前    | 使用事例 | 既定のアクセス許可 | 独自の ID を使用する
|----------------------------|-----------|----------|
| コントロール プレーン | AKS クラスター名 | イングレス ロード バランサーと AKS マネージド パブリック IP、クラスター オートスケーラー、Azure ディスクおよびファイル CSI ドライバーなど、クラスター リソースを管理する目的で AKS コントロール プレーン コンポーネントによって使用されます | ノード リソース グループの共同作成者ロール | サポートされています
| kubelet | AKS クラスター名 - agentpool | Azure Container Registry (ACR) を使用した認証 | NA (kubernetes v1.15+ 用) | サポートされています
| アドオン | AzureNPM | ID は必要ありません | NA | いいえ
| アドオン | AzureCNI ネットワーク監視 | ID は必要ありません | NA | いいえ
| アドオン | azure-policy (ゲートキーパー) | ID は必要ありません | NA | いいえ
| アドオン | azure-policy | ID は必要ありません | NA | いいえ
| アドオン | Calico | ID は必要ありません | NA | いいえ
| アドオン | ダッシュボード | ID は必要ありません | NA | いいえ
| アドオン | HTTPApplicationRouting | 必要なネットワーク リソースを管理します | ノード リソース グループの閲覧者ロール、DNS ゾーンの共同作成者ロール | いいえ
| アドオン | イングレス アプリケーション ゲートウェイ | 必要なネットワーク リソースを管理します| ノード リソース グループの共同作成者ロール | いいえ
| アドオン | omsagent | AKS メトリックを Azure Monitor に送信するために使用されます | 監視メトリック パブリッシャー ロール | いいえ
| アドオン | 仮想ノード (ACIConnector) | Azure Container Instances (ACI) のために必要なネットワーク リソースを管理します | ノード リソース グループの共同作成者ロール | いいえ
| OSS プロジェクト | aad-pod-identity | Azure Active Directory (AAD) を使用し、アプリケーションがクラウド リソースに安全にアクセスできるようにします | NA | https://github.com/Azure/aad-pod-identity#role-assignment でアクセス許可を付与するための手順。

## <a name="create-an-aks-cluster-with-managed-identities"></a>マネージド ID を指定して AKS クラスターを作成する

次の CLI コマンドを使用し、マネージド ID を指定して AKS クラスターを作成できるようになりました。

最初に、Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

次に、AKS クラスターを作成します。

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

クラスターが作成されたら、新しいクラスターにアプリケーションのワークロードをデプロイし、サービス プリンシパル ベースの AKS クラスターの場合と同様に対話できます。

最後に、クラスターにアクセスする資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities"></a>AKS クラスターをマネージド ID に更新する

次の CLI コマンドを使用し、マネージド ID と連動するよう、サービス プリンシパルで現在動作している AKS クラスターを更新できるようになりました。

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```
> [!NOTE]
> 更新後、クラスターのコントロール プレーンとアドオン ポッドはマネージド ID を使用するように切り替わりますが、kubelet は agentpool がアップグレードされるまでサービス プリンシパルを使用し続けます。 ノードで `az aks nodepool upgrade --node-image-only` を実行して、マネージド ID への更新を完了します。 
>
> クラスターで --attach-acr を使用して Azure Container Registry からイメージをプルしていた場合は、そのクラスターをマネージド ID に更新した後、`az aks update --attach-acr <ACR Resource ID>` を再実行して、マネージド ID に使用される新しく作成された kubelet で ACR からプルするためのアクセス許可を取得できるようにする必要があります。 そうしないと、アップグレードの後に ACR からプルできなくなります。
>
> Azure CLI により、移行後にアドオンのアクセス許可が正しく設定されていることが確認されます。移行操作の実行に Azure CLI を使用しない場合は、アドオン ID のアクセス許可を自分で処理する必要があります。 こちらに、[ARM](../role-based-access-control/role-assignments-template.md) を使用する一例があります。 

## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>AKS クラスターに対してシステムで割り当てられたマネージド ID を取得して使用する

次の CLI コマンドを使用して、AKS クラスターでマネージド ID が使用されていることを確認します。

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

クラスターでマネージド ID が使用されている場合は、"msi" という `clientId` 値が表示されます。 代わりに、サービス プリンシパルが使用されているクラスターでは、オブジェクト ID が表示されます。 次に例を示します。 

```output
{
  "clientId": "msi"
}
```

クラスターでマネージド ID が使用されていることを確認したら、次のコマンドを使用して、コントロール プレーン システムで割り当てられた ID のオブジェクト ID を見つけることができます。

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> 独自の VNet、静的 IP アドレス、またはアタッチされた Azure ディスク (リソースはワーカー ノード リソース グループの外部にある) を作成および使用するには、クラスター System Assigned Managed Identity の PrincipalID を使用してロールの割り当てを実行します。 ロールの割り当ての詳細については、[他の Azure リソースへのアクセスの委任](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)に関する記事を参照してください。
>
> Azure クラウド プロバイダーによって使用されるクラスター マネージド ID へのアクセス許可が付与されるまでに最大 60 分かかる場合があります。


## <a name="bring-your-own-control-plane-mi"></a>独自のコントロール プレーン MI を使用する
カスタムのコントロール プレーン ID を使用すると、クラスターの作成前に、既存の ID にアクセス権を付与できます。 この機能により、カスタム VNET や outboundType UDR を、事前作成されたマネージド ID と一緒に使用するなどのシナリオが可能になります。

Azure CLI バージョン 2.15.1 以降がインストールされている必要があります。

### <a name="limitations"></a>制限事項
* Azure Government の US DoD 中部、US DoD 東部、US Gov アイオワは現在サポートされていません。

マネージド ID をまだ持っていない場合は、[az identity CLI][az-identity-create] などを使用して作成してください。

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
結果は次のようになります。

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

マネージド ID がサブスクリプションの一部である場合は、[az identity CLI コマンド][az-identity-list]を使用してクエリを実行できます。  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

これで、次のコマンドを使用して、既存の ID でクラスターを作成できるようになりました。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id>
```

独自のマネージド ID を使用して正常にクラスターが作成されると、次の userAssignedIdentities プロファイル情報が含まれています。

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="bring-your-own-kubelet-mi"></a>ユーザー自身の kubelet MI を使う

kubelet ID を使用すると、クラスターの作成前に、既存の ID にアクセス権を付与できます。 この機能により、事前に作成されたマネージド ID を使用した ACR への接続などのシナリオが可能になります。

### <a name="prerequisites"></a>前提条件

- Azure CLI バージョン 2.26.0 以降がインストールされている必要があります。

### <a name="limitations"></a>制限事項

- ユーザー割り当てのマネージド クラスターでのみ機能します。
- Azure China 21Vianet の中国東部および中国北部は現在サポートされていません。

### <a name="create-or-obtain-managed-identities"></a>マネージド ID を作成または取得する

コントロール プレーンのマネージド ID をまだ持っていない場合は、作成してください。 次の例では、 [az identity create][az-identity-create] コマンドを使用します。

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```

結果は次のようになります。

```output
{                                  
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

kubelet マネージド ID をまだ持っていない場合は、作成してください。 次の例では、 [az identity create][az-identity-create] コマンドを使用します。

```azurecli-interactive
az identity create --name myKubeletIdentity --resource-group myResourceGroup
```

結果は次のようになります。

```output
{
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity", 
  "location": "westus2",
  "name": "myKubeletIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

既存のマネージド ID がサブスクリプションの一部である場合は、[az identity list][az-identity-list] コマンドを使用してクエリを実行できます。

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

### <a name="create-a-cluster-using-kubelet-identity"></a>Kubelet ID を使用してクラスターを作成する

これで、次のコマンドを使用して、既存の ID でクラスターを作成できるようになりました。 `assign-identity` を使用して、コントロールプレーン ID を提供し、`assign-kublet-identity` を使用して kubelet マネージ ID を提供し ます。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
    --assign-kubelet-identity <kubelet-identity-id>
```

独自の kubelet マネージ ID を使用してクラスターを正常に作成すると、次の出力が含まれます。

```output
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
        "clientId": "<client-id>",
        "principalId": "<principal-id>"
      }
    }
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "<client-id>",
      "objectId": "<object-id>",
      "resourceId": "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity"
    }
  },
```

## <a name="next-steps"></a>次の手順
* マネージド ID が有効になっているクラスターを作成するには、[Azure Resource Manager テンプレート][aks-arm-template]を使用します。

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-list]: /cli/azure/identity#az_identity_list
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
