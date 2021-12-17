---
title: Azure portal から Kubernetes リソースにアクセスする
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure portal から Azure Arc 対応 Kubernetes クラスターを管理するために Kubernetes リソースを操作する方法について説明します。
ms.openlocfilehash: 5af26c853df67b213b1951224ae9603d15a9dd25
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000576"
---
# <a name="access-kubernetes-resources-from-azure-portal"></a>Azure portal から Kubernetes リソースにアクセスする

Azure portal には、Azure Arc 対応 Kubernetes クラスター内の Kubernetes リソースに簡単にアクセスするための Kubernetes リソース ビューが用意されています。 Azure portal から Kubernetes リソースを表示すると、Azure portal と `kubectl` コマンド ライン ツールの間のコンテキストの切り替えが削減されるため、Kubernetes リソースを表示および編集するためのエクスペリエンスが効率化されます。 リソース ビューアーには現在、デプロイ、ポッド、レプリカ セットなどの複数のリソースの種類が含まれています。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

- Azure Arc 対応 Kubernetes リソースとして Azure に[接続された](quickstart-connect-cluster.md)既存の Kubernetes クラスター。

- Azure Arc 対応 Kubernetes クラスターで[クラスター接続機能が有効になっている必要があります](cluster-connect.md#enable-cluster-connect-feature)。

- クラスターに対する認証のための[サービス アカウント トークン](cluster-connect.md#service-account-token-authentication-option)。

## <a name="view-kubernetes-resources"></a>Kubernetes リソースを表示する

Kubernetes リソースを表示するには、Azure portal で AKS クラスターに移動します。 左側のナビゲーション ウィンドウは、リソースにアクセスするために使用されます。 リソースには次のものがあります。

- **[名前空間]** には、クラスターの名前空間が表示されます。 名前空間の一覧の上部にあるフィルターを使用すると、名前空間リソースをすばやくフィルター処理して表示できます。
- **[ワークロード]** には、クラスターにデプロイされているデプロイ、ポッド、レプリカ セット、ステートフル セット、デーモン セット、ジョブ、cron ジョブに関する情報が表示されます。
- **[サービスとイングレス]** には、クラスターのすべてのサービスとイングレス リソースが表示されます。
- **[ストレージ]** には、Azure ストレージ クラスと永続ボリュームの情報が表示されます。
- **[構成]** には、クラスターの構成マップとシークレットが表示されます。

[ ![Azure portal に表示された Kubernetes ワークロードの情報](media/kubernetes-resource-view/workloads.png) ](media/kubernetes-resource-view/workloads.png#lightbox)

## <a name="edit-yaml"></a>YAML を編集する

Kubernetes リソース ビューには YAML エディターも含まれています。 組み込みの YAML エディターを使用すると、ポータル内から Kubernetes オブジェクトを更新し、すぐに変更を適用できます。

YAML を編集した後、 **[確認と保存]** を選択し、変更を確認して、再び保存することによって変更を適用します。

[ ![Azure portal に表示された Kubernetes オブジェクトの YAML エディター](media/kubernetes-resource-view/yaml-editor.png) ](media/kubernetes-resource-view/yaml-editor.png#lightbox)

>[!WARNING]
> UI または CLI から運用上の直接的変更を行うことはお勧めできません。運用環境に対しては、[構成 (GitOps)](tutorial-use-gitops-connected-cluster.md) を使用することを検討する必要があります。 Azure portal の Kubernetes 管理機能や YAML エディターは、開発用やテスト用の設定で新しいデプロイの学習やフライト化を行うために構築されています。

## <a name="next-steps"></a>次のステップ

Azure Monitor for containers では、この記事で説明されている Kubernetes リソース ビューで使用可能な Kubernetes リソースの論理ビューと比較すると、クラスターのノードとコンテナーに関して、より詳細な情報が提供されます。 クラスターに [Azure Monitor for containers をデプロイする](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)方法について学習します。