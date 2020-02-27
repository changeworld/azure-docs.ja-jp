---
title: Azure Kubernetes Service でマネージド ID を使用する
description: Azure Kubernetes Service (AKS) でマネージド ID を使用する方法について説明します。
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 6d00fd72c338fc101420bf78b5608516715d44ad
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592970"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>プレビュー - Azure Kubernetes Service でマネージド ID を使用する

現在、Azure Kubernetes Service (AKS) クラスター (具体的には Kubernetes クラウド プロバイダー) で Azure 内にロード バランサーやマネージド ディスクなどの追加リソースを作成するには、*サービス プリンシパル*が必要です。 ユーザーがサービス プリンシパルを指定するか、AKS が代理で作成する必要があります。 通常、サービス プリンシパルには有効期限があります。 最終的にはクラスターは、クラスターを引き続き機能させるためにサービス プリンシパルを更新する必要がある状態になります。 サービス プリンシパルを管理すると、複雑さが増します。

*マネージド ID* は、本質的にサービス プリンシパルのラッパーであり、管理がより簡単になります。 詳細については、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) について確認してください。

AKS は 2 つのマネージド ID を作成します。

- **システム割り当てマネージド ID**:Kubernetes クラウド プロバイダーがユーザーに代わって Azure リソースを作成するために使用する ID。 このシステム割り当ての ID のライフ サイクルは、クラスターのそれに関連付けられます。 この ID は、クラスターが削除されると削除されます。
- **ユーザー割り当てマネージド ID**:クラスターでの承認に使用される ID。 たとえば、ユーザーが割り当てた ID を使用して、アクセス制御レコード (ACR) を使用するように AKS を承認したり、kubelet が Azure からメタデータを取得することを承認したりします。

このプレビュー期間中は、まだサービス プリンシパルが必要です。 これは監視、仮想ノード、Azure Policy、HTTP アプリケーション ルーティングなどのアドオンの承認に使用されます。 サービス プリンシパル名 (SPN) のアドオンの依存関係を削除する作業が進行中です。 最終的には、AKS の SPN の要件が完全に削除されます。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトイン単位で利用できます。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、カスタマー サポートによってベスト エフォートで部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポート記事を参照してください。
>
> - [AKS のサポート ポリシー](support-policies.md)
> - [Azure サポートに関する FAQ](faq.md)

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- Azure CLI、バージョン 2.0.70 以降
- aks-preview 0.4.14 拡張機能

aks-preview 0.4.14 以降の拡張機能をインストールするには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> サブスクリプションで機能を登録した後、現時点ではその機能を登録解除することはできません。 一部のプレビュー機能を有効にすると、サブスクリプションで後で作成されたすべての AKS クラスターに対して既定値が使用される場合があります。 運用サブスクリプションではプレビュー機能を有効にしないでください。 代わりに、プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>マネージド ID を指定して AKS クラスターを作成する

次の CLI コマンドを使用し、マネージド ID を指定して AKS クラスターを作成できるようになりました。

最初に、Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

次に、AKS クラスターを作成します。

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

最後に、クラスターにアクセスする資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

クラスターは数分で作成されます。 その後、新しいクラスターにアプリケーションのワークロードをデプロイし、サービス プリンシパル ベースの AKS クラスターの場合と同様に対話することができます。

> [!IMPORTANT]
>
> - マネージド ID を指定した AKS クラスターは、クラスターの作成時にのみ有効にすることができます。
> - 既存の AKS クラスターを更新またはアップグレードしてマネージド ID を有効にすることはできません。
