---
title: Azure Kubernetes Service (AKS) でのクラスター構成
description: Azure Kubernetes Service (AKS) でクラスターを構成する方法について説明します
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725148"
---
# <a name="configure-an-aks-cluster"></a>AKS クラスターの構成

AKS クラスターの作成の一環として、ニーズに合わせてクラスター構成をカスタマイズすることが必要になる場合があります。 この記事では、AKS クラスターをカスタマイズするためのいくつかのオプションについて説明します。

## <a name="os-configuration-preview"></a>OS 構成 (プレビュー)

AKS では、ノード オペレーティング システム (OS) として Ubuntu 18.04 をプレビュー段階でサポートするようになりました。 プレビュー期間中は、Ubuntu 16.04 と Ubuntu 18.04 の両方を使用できます。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.2.0 以降
- aks-preview 0.4.35 拡張機能

aks-preview 0.4.35 以降の拡張機能をインストールするには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
az extension list
```

`UseCustomizedUbuntuPreview` 機能を登録します。

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>新しいクラスター

クラスターが作成されるときに Ubuntu 18.04 を使用するようにクラスターを構成します。 `--aks-custom-headers` フラグを使用して、Ubuntu 18.04 を既定の OS として設定します。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

通常の Ubuntu 16.04 クラスターを作成するには、カスタムの `--aks-custom-headers` タグを省略します。

### <a name="existing-clusters"></a>既存のクラスター

Ubuntu 18.04 を使用するように新しいノード プールを構成します。 `--aks-custom-headers` フラグを使用して、Ubuntu 18.04 をそのノード プールの既定の OS として設定します。

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

通常の Ubuntu 16.04 ノード プールを作成するには、カスタムの `--aks-custom-headers` タグを省略します。


## <a name="custom-resource-group-name"></a>カスタム リソース グループ名

Azure Kubernetes Service クラスターを Azure にデプロイすると、ワーカー ノードに 2 つ目のリソース グループが作成されます。 既定では、AKS によってノード リソース グループに `MC_resourcegroupname_clustername_location` という名前が設定されますが、独自の名前を指定することもできます。

独自のリソース グループ名を指定するには、aks-preview Azure CLI 拡張機能バージョン 0.3.2 以降をインストールします。 Azure CLI で `az aks create` コマンドの `--node-resource-group` パラメーターを使用して、リソース グループのカスタム名を指定します。 Azure Resource Manager テンプレートを使用して AKS クラスターをデプロイする場合は、`nodeResourceGroup` プロパティを使用してリソース グループ名を定義できます。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

セカンダリ リソース グループは、自分のサブスクリプションの Azure リソース プロバイダーによって自動的に作成されます。 クラスターが作成されるときにのみ、カスタムのリソース グループ名を指定できます。 

ノード リソース グループを使うときは、次のことができないので注意してください。

- ノード リソース グループに対して既存のリソース グループを指定すること。
- ノード リソース グループに対して異なるサブスクリプションを指定すること。
- クラスターが作成された後で、ノード リソース グループ名を変更すること。
- ノード リソース グループ内の管理対象リソースに名前を指定すること。
- ノード リソース グループ内の管理対象リソースの Azure で作成されたタグを変更または削除すること。

## <a name="next-steps"></a>次のステップ

- `Kured` を使用して、クラスター内の [Linux ノードにセキュリティとカーネルの更新を適用する](node-updates-kured.md)方法について学習する。
- 「[Azure Kubernetes Service (AKS) クラスターのアップグレード](upgrade-cluster.md)」を参照し、クラスターを最新バージョンの Kubernetes にアップグレードする方法について学習する。
- [AKS についてよく寄せられる質問](faq.md)に関する記事の一覧を参照し、AKS についての一般的な質問への回答を確認する。