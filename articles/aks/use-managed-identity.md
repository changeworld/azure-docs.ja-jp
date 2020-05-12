---
title: Azure Kubernetes Service でマネージド ID を使用する
description: Azure Kubernetes Service (AKS) でマネージド ID を使用する方法について説明します。
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112968"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service でマネージド ID を使用する

現在、Azure Kubernetes Service (AKS) クラスター (具体的には Kubernetes クラウド プロバイダー) で Azure 内にロード バランサーやマネージド ディスクなどの追加リソースを作成するには、"*マネージド ID*" または "*サービス プリンシパル*" のいずれかの ID が必要です。 [サービス プリンシパル](kubernetes-service-principal.md)を使用する場合、それを 1 つ指定する必要があります。指定しない場合、AKS によって代理で作成されます。 マネージド ID を使用する場合は、AKS によってこれが自動作成されます。 サービス プリンシパルを使用するクラスターでは、やがてサービス プリンシパルを更新しないと、そのクラスターを動作させ続けることができない状態になります。 サービス プリンシパルを管理しなければならない場合、複雑さが増すので、代わりにマネージ ID を使用した方が簡単です。 アクセス許可の要件は、サービス プリンシパルにおいてもマネージド ID においても同じです。

*マネージド ID* は、本質的にサービス プリンシパルのラッパーであり、管理がより簡単になります。 詳細については、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) について確認してください。

AKS は 2 つのマネージド ID を作成します。

- **システム割り当てマネージド ID**:Kubernetes クラウド プロバイダーがユーザーに代わって Azure リソースを作成するために使用する ID。 このシステム割り当ての ID のライフ サイクルは、クラスターのそれに関連付けられます。 この ID は、クラスターが削除されると削除されます。
- **ユーザー割り当てマネージド ID**:クラスターでの承認に使用される ID。 たとえば、ユーザーが割り当てた ID を使用して、Azure Container Registry (ACR) を使用するように AKS を承認したり、kubelet が Azure からメタデータを取得することを承認したりします。

マネージド ID を使用してアドオンを認証することもできます。 アドオンごとに、AKS によってマネージド ID が作成され、アドオンの期限が切れるまで存続します。 

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

> [!NOTE]
> 独自の VNet、静的 IP アドレス、またはアタッチされた Azure ディスク (リソースは MC_* リソース グループの外部にある) を作成および使用するには、クラスター System Assigned Managed Identity の PrincipalID を使用してロールの割り当てを実行します。 ロールの割り当ての詳細については、[他の Azure リソースへのアクセスの委任](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)に関する記事を参照してください。
>
> Azure クラウド プロバイダーによって使用されるクラスター マネージド ID へのアクセス許可が付与されるまでに最大 60 分かかる場合があります。

最後に、クラスターにアクセスする資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

クラスターは数分で作成されます。 その後、新しいクラスターにアプリケーションのワークロードをデプロイし、サービス プリンシパル ベースの AKS クラスターの場合と同様に対話することができます。

> [!IMPORTANT]
>
> - マネージド ID を指定した AKS クラスターは、クラスターの作成時にのみ有効にすることができます。
> - 既存の AKS クラスターを更新またはアップグレードしてマネージド ID を有効にすることはできません。
