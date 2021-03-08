---
title: Azure Kubernetes Service でマネージド ID を使用する
description: Azure Kubernetes Service (AKS) でマネージド ID を使用する方法について説明します。
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 3ace7f1c93ab3918f460d245a863db43d98f1db5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176095"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service でマネージド ID を使用する

現在、Azure Kubernetes Service (AKS) クラスター (具体的には Kubernetes クラウド プロバイダー) で Azure 内にロード バランサーやマネージド ディスクなどの追加リソースを作成するには、ID が必要です。 この ID には、*マネージド ID* または *サービス プリンシパル* を指定できます。 [サービス プリンシパル](kubernetes-service-principal.md)を使用する場合、それを 1 つ指定する必要があります。指定しない場合、AKS によって代理で作成されます。 マネージド ID を使用する場合は、AKS によってこれが自動作成されます。 サービス プリンシパルを使用するクラスターでは、やがてサービス プリンシパルを更新しないと、そのクラスターを動作させ続けることができない状態になります。 サービス プリンシパルを管理しなければならない場合、複雑さが増すので、代わりにマネージ ID を使用した方が簡単です。 アクセス許可の要件は、サービス プリンシパルにおいてもマネージド ID においても同じです。

*マネージド ID* は、本質的にサービス プリンシパルのラッパーであり、管理がより簡単になります。 MI の資格情報のローテーションは、Azure Active Directory の既定に従って 46 日ごとに自動的に行われます。 AKS では、システム割り当てとユーザー割り当ての両方の種類のマネージド ID が使用されます。 これらの ID は、現在変更できません。 詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) について確認してください。

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- Azure CLI、バージョン 2.15.1 以降

## <a name="limitations"></a>制限事項

* マネージド ID が有効になっているクラスターのテナントの移動/移行はサポートされていません。
* クラスターで `aad-pod-identity` が有効になっている場合、Azure Instance Metadata エンドポイントの呼び出しをインターセプトするよう、Node Managed Identity (NMI) ポッドによりノードの iptables が変更されます。 この構成の場合、Metadata エンドポイントに要求が行われると、ポッドで `aad-pod-identity` が使用されていない場合でも NMI により要求がインターセプトされます。 CRD に定義されているラベルに一致するポッドから Metadata エンドポイントに要求が行われた場合、NMI で何も処理することなく、その要求をプロキシ処理することを `aad-pod-identity` に通知するよう、AzurePodIdentityException CRD を構成できます。 _kube-system_ 名前空間の `kubernetes.azure.com/managedby: aks` ラベルを持つシステム ポッドは、AzurePodIdentityException CRD を構成することで、`aad-pod-identity` で除外してください。 詳細については、「[特定のポッドまたはアプリケーションの aad-pod-identity を無効にする](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)」を参照してください。
  例外を構成するには、[mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml) をインストールします。

## <a name="summary-of-managed-identities"></a>マネージド ID の概要

AKS では、組み込みのサービスとアドオンに対して複数のマネージド ID が使用されます。

| ID                       | 名前    | 使用事例 | 既定のアクセス許可 | 独自の ID を使用する
|----------------------------|-----------|----------|
| コントロール プレーン | 非表示 | イングレス ロード バランサーと AKS マネージド パブリック IP、Cluster Autoscaler 操作など、クラスター リソースを管理する目的で AKS コントロール プレーン コンポーネントによって使用されます | ノード リソース グループの共同作成者ロール | サポート対象
| kubelet | AKS クラスター名 - agentpool | Azure Container Registry (ACR) を使用した認証 | NA (kubernetes v1.15+ 用) | 現在、サポートされていません
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

マネージド ID を使用して正常にクラスターが作成されると、次のサービス プリンシパル プロファイル情報が含まれます。

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

次のコマンドを使用して、コントロール プレーン マネージド ID の objectid を照会します。

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

結果は次のようになります。

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

クラスターが作成されたら、新しいクラスターにアプリケーションのワークロードをデプロイし、サービス プリンシパル ベースの AKS クラスターの場合と同様に対話できます。

> [!NOTE]
> 独自の VNet、静的 IP アドレス、またはアタッチされた Azure ディスク (リソースはワーカー ノード リソース グループの外部にある) を作成および使用するには、クラスター System Assigned Managed Identity の PrincipalID を使用してロールの割り当てを実行します。 ロールの割り当ての詳細については、[他の Azure リソースへのアクセスの委任](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)に関する記事を参照してください。
>
> Azure クラウド プロバイダーによって使用されるクラスター マネージド ID へのアクセス許可が付与されるまでに最大 60 分かかる場合があります。

最後に、クラスターにアクセスする資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>AKS クラスターをマネージド ID に更新する (プレビュー)

次の CLI コマンドを使用し、マネージド ID と連動するよう、サービス プリンシパルで現在動作している AKS クラスターを更新できるようになりました。

まず、システムによって割り当てられた ID の機能フラグを登録します。

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

システム割り当て ID を更新します。

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

ユーザーによって割り当てられた ID の機能フラグを登録します。

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

ユーザー割り当て ID を更新します。

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> システム割り当てまたはユーザー割り当ての ID がマネージド ID に更新されたら、ノードで `az aks nodepool upgrade --node-image-only` を実行し、マネージド ID への更新を完了します。

## <a name="bring-your-own-control-plane-mi"></a>独自のコントロール プレーン MI を使用する
カスタムのコントロール プレーン ID を使用すると、クラスターの作成前に、既存の ID にアクセス権を付与できます。 この機能により、カスタム VNET や outboundType UDR を、事前作成されたマネージド ID と一緒に使用するなどのシナリオが可能になります。

Azure CLI バージョン 2.15.1 以降がインストールされている必要があります。

### <a name="limitations"></a>制限事項
* Azure Government は現在サポートされていません。
* Azure China 21Vianet は現在サポートされていません。

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
  "tenantId": "<tenant-id>>",
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
    --assign-identity <identity-id> \
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

## <a name="next-steps"></a>次のステップ
* マネージド ID が有効になっているクラスターを作成するには、[Azure Resource Manager (ARM) テンプレート][aks-arm-template]を使用します。

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-list]: /cli/azure/identity#az-identity-list
