---
title: Azure Kubernetes Service でマネージド ID を使用する
description: Azure Kubernetes Service (AKS) でマネージド ID を使用する方法について説明します。
services: container-service
author: mlearned
ms.topic: article
ms.date: 07/10/2020
ms.author: mlearned
ms.openlocfilehash: 95a303a4b6a83901560b26679bca920b9de4d3f4
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250907"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service でマネージド ID を使用する

現在、Azure Kubernetes Service (AKS) クラスター (具体的には Kubernetes クラウド プロバイダー) で Azure 内にロード バランサーやマネージド ディスクなどの追加リソースを作成するには、ID が必要です。 この ID には、*マネージド ID* または*サービス プリンシパル*を指定できます。 [サービス プリンシパル](kubernetes-service-principal.md)を使用する場合、それを 1 つ指定する必要があります。指定しない場合、AKS によって代理で作成されます。 マネージド ID を使用する場合は、AKS によってこれが自動作成されます。 サービス プリンシパルを使用するクラスターでは、やがてサービス プリンシパルを更新しないと、そのクラスターを動作させ続けることができない状態になります。 サービス プリンシパルを管理しなければならない場合、複雑さが増すので、代わりにマネージ ID を使用した方が簡単です。 アクセス許可の要件は、サービス プリンシパルにおいてもマネージド ID においても同じです。

*マネージド ID* は、本質的にサービス プリンシパルのラッパーであり、管理がより簡単になります。 MI の資格情報のローテーションは、Azure Active Directory の既定に従って 46 日ごとに自動的に行われます。 AKS では、システム割り当てとユーザー割り当ての両方の種類のマネージド ID が使用されます。 これらの ID は、現在変更できません。 詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) について確認してください。

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.8.0 以降

## <a name="limitations"></a>制限事項

* 独自のマネージド ID を使用することは現在サポートされていません。
* マネージド ID を指定した AKS クラスターは、クラスターの作成時にのみ有効にすることができます。
* 既存の AKS クラスターを更新またはアップグレードしてマネージド ID を有効にすることはできません。
* クラスターの**アップグレード**操作中は、マネージド ID が一時的に使用できなくなります。

## <a name="summary-of-managed-identities"></a>マネージド ID の概要

AKS では、組み込みのサービスとアドオンに対して複数のマネージド ID が使用されます。

| ID                       | 名前    | 使用事例 | 既定のアクセス許可 | 独自の ID を使用する
|----------------------------|-----------|----------|
| コントロール プレーン | 非表示 | AKS がネットワーク リソースを管理するために使用します。たとえば、イングレスやパブリック IP などのロード バランサーを作成します| ノード リソース グループの共同作成者ロール | 現在、サポートされていません
| kubelet | AKS クラスター名 - agentpool | Azure Container Registry (ACR) を使用した認証 | ノード リソース グループの閲覧者ロール | 現在、サポートされていません
| アドオン | AzureNPM | ID は必要ありません | NA | いいえ
| アドオン | AzureCNI ネットワーク監視 | ID は必要ありません | NA | いいえ
| アドオン | azurepolicy (ゲートキーパー) | ID は必要ありません | NA | いいえ
| アドオン | azurepolicy | ID は必要ありません | NA | いいえ
| アドオン | Calico | ID は必要ありません | NA | いいえ
| アドオン | ダッシュボード | ID は必要ありません | NA | いいえ
| アドオン | HTTPApplicationRouting | 必要なネットワーク リソースを管理します | ノード リソース グループの閲覧者ロール、DNS ゾーンの共同作成者ロール | いいえ
| アドオン | イングレス アプリケーション ゲートウェイ | 必要なネットワーク リソースを管理します| ノード リソース グループの共同作成者ロール | いいえ
| アドオン | omsagent | AKS メトリックを Azure Monitor に送信するために使用されます | 監視メトリック パブリッシャー ロール | いいえ
| アドオン | 仮想ノード (ACIConnector) | Azure Container Instances (ACI) のために必要なネットワーク リソースを管理します | ノード リソース グループの共同作成者ロール | いいえ


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

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

次のコマンドを使用して、コントロール プレーン マネージド ID の objectid を照会します。

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

結果は次のようになります。

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> 独自の VNet、静的 IP アドレス、またはアタッチされた Azure ディスク (リソースはワーカー ノード リソース グループの外部にある) を作成および使用するには、クラスター System Assigned Managed Identity の PrincipalID を使用してロールの割り当てを実行します。 ロールの割り当ての詳細については、[他の Azure リソースへのアクセスの委任](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)に関する記事を参照してください。
>
> Azure クラウド プロバイダーによって使用されるクラスター マネージド ID へのアクセス許可が付与されるまでに最大 60 分かかる場合があります。

最後に、クラスターにアクセスする資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

クラスターは数分で作成されます。 その後、新しいクラスターにアプリケーションのワークロードをデプロイし、サービス プリンシパル ベースの AKS クラスターの場合と同様に対話することができます。

## <a name="next-steps"></a>次のステップ
* マネージド ID が有効になっているクラスターを作成するには、[Azure Resource Manager (ARM) テンプレート][aks-arm-template]を使用します。

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
