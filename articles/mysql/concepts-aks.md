---
title: Azure Kubernetes Service への接続 - Azure Database for MySQL
description: Azure Kubernetes Service と Azure Database for MySQL を接続する方法について説明します
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 0906c255c3fd77be4de670a980bdc0a2f108c121
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347106"
---
# <a name="best-practices-for-azure-kubernetes-service-and-azure-database-for-mysql"></a>Azure Kubernetes Service と Azure Database for MySQL のベスト プラクティス

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Kubernetes Service (AKS) からは、Azure で使用できるマネージド Kubernetes クラスターが提供されます。 AKS と Azure Database for MySQL を併用してアプリケーションを作成するときに考慮する選択肢を下にいくつか示します。

## <a name="create-database-before-creating-the-aks-cluster"></a>AKS クラスターを作成する前にデータベースを作成する

Azure Database for MySQL には、次の 2 つのデプロイ オプションがあります。

- 単一サーバー
- フレキシブル サーバー

単一サーバーでは単一の可用性ゾーンがサポートされ、フレキシブル サーバーでは複数の可用性ゾーンがサポートされています。 一方、AKS でも、単一または複数の可用性ゾーンの有効化がサポートされています。  最初にデータベース サーバーを作成し、サーバーが配置されている可用性ゾーンを確認してから、同じ可用性ゾーンに AKS クラスターを作成します。 これにより、ネットワークの待機時間が短縮され、アプリケーションのパフォーマンスが向上します。

## <a name="use-accelerated-networking"></a>高速ネットワークを使用する

AKS クラスターで高速ネットワーク対応の基礎 VM を使用します。 VM で高速ネットワークが有効になると、待ち時間が少なくなり、ジッターが減り、VM の CPU 使用率が下がります。 高速ネットワークのしくみ、サポートされている OS バージョン、[Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 向けにサポートされている VM インスタンスについて説明します。

2018 年 11 月から、AKS は、これらのサポートされている VM インスタンスでの高速ネットワークに対応します。 これらの VM を使用する新しい AKS クラスターでは、高速ネットワークが既定で有効になっています。

AKS クラスターで高速ネットワークが有効になっているかどうかは次の方法で確認できます。

1. Azure portal に進み、AKS クラスターを選択します。
2. [プロパティ] タブを選択します。
3. **[インフラストラクチャ リソース グループ]** の名前をコピーします。
4. ポータル検索バーを使用し、インフラストラクチャ リソース グループを見つけて開きます。
5. そのリソース グループ内の VM を選択します。
6. VM の **[ネットワーク]** タブに移動します。
7. **[高速ネットワーク]** が "有効" になっていることを確認します。

または、Azure CLI で、次の 2 つのコマンドを使用します。

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```

出力は、AKS が作成する、ネットワーク インターフェイスを含む生成されたリソース グループになります。 名前 "nodeResourceGroup" を利用し、次のコマンドで使用します。 **EnableAcceleratedNetworking** は、true または false のいずれかになります。

```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="use-azure-premium-fileshare"></a>Azure Premium ファイル共有を使用する

 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできる永続ストレージとして、[Azure Premium ファイル共有](../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal)を使用します。 ファイル ストレージで大量の I/O 操作が予想される場合、Azure Premium ファイル共有を使用すると、アプリケーションのパフォーマンスを最大限に高めることができます。 詳細については、[Azure Files を有効にする方法](../aks/azure-files-dynamic-pv.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Kubernetes Service クラスターを作成する](../aks/kubernetes-walkthrough.md)