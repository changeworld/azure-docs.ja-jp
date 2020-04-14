---
title: Azure Kubernetes Service でマネージド ID を使用する
description: Azure Kubernetes Service (AKS) でマネージド ID を使用する方法について説明します。
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369968"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service でマネージド ID を使用する

現在、Azure Kubernetes Service (AKS) クラスター (具体的には Kubernetes クラウド プロバイダー) で Azure 内にロード バランサーやマネージド ディスクなどの追加リソースを作成するには、*サービス プリンシパル*が必要です。 ユーザーがサービス プリンシパルを指定するか、AKS が代理で作成する必要があります。 通常、サービス プリンシパルには有効期限があります。 最終的にはクラスターは、クラスターを引き続き機能させるためにサービス プリンシパルを更新する必要がある状態になります。 サービス プリンシパルを管理すると、複雑さが増します。

*マネージド ID* は、本質的にサービス プリンシパルのラッパーであり、管理がより簡単になります。 詳細については、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) について確認してください。

AKS は 2 つのマネージド ID を作成します。

- **システム割り当てマネージド ID**:Kubernetes クラウド プロバイダーがユーザーに代わって Azure リソースを作成するために使用する ID。 このシステム割り当ての ID のライフ サイクルは、クラスターのそれに関連付けられます。 この ID は、クラスターが削除されると削除されます。
- **ユーザー割り当てマネージド ID**:クラスターでの承認に使用される ID。 たとえば、ユーザーが割り当てた ID を使用して、Azure Container Registry (ACR) を使用するように AKS を承認したり、kubelet が Azure からメタデータを取得することを承認したりします。

マネージド ID を使用してアドオンを認証することもできます。 アドオンごとに、AKS によってマネージド ID が作成され、アドオンの期限が切れるまで存続します。 独自の VNet、静的 IP アドレス、またはアタッチされた Azure ディスク (リソースは MC_* リソース グループの外部にある) を作成および使用するには、クラスターの PrincipalID を使用してロールの割り当てを実行します。 ロールの割り当ての詳細については、[他の Azure リソースへのアクセスの委任](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)に関する記事を参照してください。

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.2.0 以降

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

マネージド ID を使用して正常にクラスターが作成されると、次のサービス プリンシパル プロファイル情報が含まれます。

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
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
