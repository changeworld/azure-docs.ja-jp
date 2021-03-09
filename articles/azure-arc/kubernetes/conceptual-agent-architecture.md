---
title: Azure Arc 対応 Kubernetes エージェントのアーキテクチャ
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事では、Azure Arc 対応 Kubernetes エージェントのアーキテクチャの概要について説明します
keywords: Kubernetes, Arc, Azure, コンテナー
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561225"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure Arc 対応 Kubernetes エージェントのアーキテクチャ

[Kubernetes](https://kubernetes.io/) を使用すると、ハイブリッドおよびマルチクラウド環境にコンテナー化されたワークロードを一貫した方法でデプロイできます。 これらの異種環境全体でポリシー、ガバナンス、セキュリティを一貫して管理するための一元的なコントロール プレーンとして、Azure Arc 対応 Kubernetes を使用できます。 この記事では、次について説明します。

* Azure Arc へのクラスターの接続に関するアーキテクチャの概要。
* エージェントが従う接続パターン。
* クラスター環境と Azure の間で交換されるデータの説明。

## <a name="deploy-agents-to-your-cluster"></a>クラスターにエージェントをデプロイする

ほとんどのオンプレミス データセンターにおいては、ネットワーク境界で使用されるファイアウォールで、受信通信を禁止する厳格なネットワーク規則が適用されます。 Azure Arc 対応 Kubernetes は、選択されたエグレス エンドポイントのみを送信通信用に有効にし、ファイアウォールで受信ポートを要求しないことで、これらの制限と連携します。 送信接続は、Azure Arc 対応 Kubernetes エージェントによって開始されます。

![アーキテクチャの概要](./media/architectural-overview.png)

クラスターを Azure Arc に接続するには、次の手順のようにします。

1. 選択したインフラストラクチャ (VMware vSphere、アマゾン ウェブ サービス、Google Cloud Platform など) に、Kubernetes クラスターを作成します。 

    > [!NOTE]
    > 現在、Azure Arc 対応 Kubernetes によってサポートされるのは既存の Kubernetes クラスターを Azure Arc にアタッチすることのみなので、Kubernetes クラスターのライフサイクルは、お客様が作成して管理する必要があります。  

1. Azure CLI を使用して、クラスターの Azure Arc 登録を開始します。
    * Azure CLI により、Helm を使用して、クラスターにエージェントの Helm Chart がデプロイされます。
    * クラスター ノードにより、[Microsoft Container Registry](https://github.com/microsoft/containerregistry) への送信通信が開始され、`azure-arc` 名前空間に次のエージェントを作成するために必要なイメージがプルされます。

        | エージェント | 説明 |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | 現在、Azure Arc 対応 Kubernetes によってサポートされているのは、[システム割り当て ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) のみです。 clusteridentityoperator により、他のエージェントが Azure との通信に使用する管理サービス ID (MSI) 証明書をフェッチするために必要な最初の送信通信が行われます。 |
        | `deployment.apps/config-agent` | クラスターに適用されるソース管理構成リソースの接続されたクラスターを監視し、コンプライアンス状態を更新します |
        | `deployment.apps/controller-manager` | Azure Arc コンポーネント間の相互作用を調整する、オペレーターのオペレーターです |    
        | `deployment.apps/metrics-agent` | 他の Arc エージェントのメトリックを収集し、これらのエージェントのパフォーマンスが最適であることを確認します |
        | `deployment.apps/cluster-metadata-operator` | クラスターのメタデータを収集します - クラスターのバージョン、ノード数、Azure Arc エージェントのバージョン |
        | `deployment.apps/resource-sync-agent` | 前述のクラスター メタデータを Azure に同期します |
        | `deployment.apps/flux-logs-agent` | ソース管理構成の一部としてデプロイされる Flux オペレーターからログを収集します |
    
1. すべての Azure Arc 対応 Kubernetes エージェントのポッドが `Running` 状態になったら、クラスターが Azure Arc に接続されていることを確認します。次のような結果が表示されます。
    * [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 内の Azure Arc 対応 Kubernetes リソース。 このリソースは、実際の Kubernetes クラスター自体ではなく、カスタマー マネージド Kubernetes クラスターのプロジェクションとして Azure で追跡されます。
    * クラスターのメタデータ (Kubernetes のバージョン、エージェントのバージョン、ノードの数など) は、Azure Arc 対応 Kubernetes リソースのメタデータとして表示されます。

## <a name="data-exchange-between-cluster-environment-and-azure"></a>クラスター環境と Azure の間のデータ交換

| データ型 | シナリオ | 通信モード |
| --------- | -------- | ------------------ |
| Kubernetes クラスターのバージョン | クラスター メタデータ | エージェントが Azure にプッシュする |
| クラスターのノードの数 | クラスター メタデータ | エージェントが Azure にプッシュする |
| エージェントのバージョン | クラスター メタデータ | エージェントが Azure にプッシュする |
| Kubernetes の分散の種類 | クラスター メタデータ | Azure CLI が Azure にプッシュする |
| インフラストラクチャの種類 (AWS/GCP/vSphere/...) | クラスター メタデータ | Azure CLI が Azure にプッシュする |
| クラスター内のノードの vCPU 数 | 課金 | Azure CLI が Azure にプッシュする |
| エージェント ハートビート | リソース正常性 | エージェントが Azure にプッシュする |
| エージェントによるリソース消費量 (メモリ/CPU) | 診断とサポート性 | エージェントが Azure にプッシュする |
| すべてのエージェント コンテナーのログ | 診断とサポート性 | エージェントが Azure にプッシュする |
| エージェントのアップグレードの利用可能性 | エージェントのアップグレード | エージェントが Azure からプルする |
| 構成の望ましい状態 -Git リポジトリの URL、Flux オペレーターのパラメーター、秘密キー、既知のホストのコンテンツ、HTTPS ユーザー名、トークンとパスワード | 構成 | エージェントが Azure からプルする |
| Flux オペレーターのインストールの状態 | 構成 | エージェントが Azure にプッシュする |
| クラスター内で Gatekeeper の適用が必要な Azure Policy の割り当て | Azure Policy | エージェントが Azure からプルする |
| クラスター内のポリシー適用の監査とコンプライアンスの状態 | Azure Policy | エージェントが Azure にプッシュする |
| 顧客のワークロードのメトリックとログ | Azure Monitor | エージェントが、顧客のテナントとサブスクリプション内の Log Analytics ワークスペース リソースにプッシュする |

## <a name="connectivity-status"></a>接続性の状態

| Status | 説明 |
| ------ | ----------- |
| 接続 | Azure Arc 対応 Kubernetes リソースは Azure Resource Manager に作成されましたが、サービスはエージェントのハートビートをまだ受信していません。 |
| 接続中 | Azure Arc 対応 Kubernetes サービスは、過去 15 分以内にエージェントのハートビートを受信しました。 |
| オフライン | Azure Arc 対応 Kubernetes リソースは以前は接続されていましたが、サービスは 15 分間エージェントのハートビートを受信していません。 |
| 有効期限切れ | マネージド サービス ID (MSI) 証明書の有効期間は、発行後 90 日です。 この証明書の有効期限が切れると、リソースは `Expired` であると見なされ、構成、監視、ポリシーなどのすべての機能はこのクラスターで動作を停止します。 有効期限が切れた Azure Arc 対応 Kubernetes リソースに対処する方法の詳細については、[こちら](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources)を参照してください |

## <a name="understand-connectivity-modes"></a>接続モードを理解する

| 接続モード | [説明] |
| ----------------- | ----------- |
| 完全に接続済み | エージェントは常に Azure に接続できます。 この場合、構成の伝達 (GitOps)、ポリシーの適用 (Azure Policy と Gatekeeper)、ワークロードのメトリックとログの収集 (Azure Monitor) には遅延がほとんどないため、エクスペリエンスは理想的です |
| 半接続済み | `clusteridentityoperator` によってプルダウンされた MSI 証明書は、証明書の有効期限が切れるまで最大 90 日有効です。 証明書の有効期限が切れると、Azure Arc 対応 Kubernetes リソースの動作は停止します。 クラスターで Arc のすべての機能を動作させるには、Azure Arc 対応 Kubernetes リソースとエージェントを削除して作成し直します。 ユーザーには、90 日の期間中、少なくとも 30 日ごとに 1 回はクラスターに接続することをお勧めします。 |
| [Disconnected]\(切断済み\) | 現在、Azure にアクセスできない切断された環境の Kubernetes クラスターは、Azure Arc 対応 Kubernetes ではサポートされていません。 この機能が重要な場合は、[Azure Arc の UserVoice フォーラム](https://feedback.azure.com/forums/925690-azure-arc)でアイデアを提出するか賛成投票してください。

## <a name="next-steps"></a>次のステップ

* [Azure Arc にクラスターを接続する](./connect-cluster.md)
* [構成の概念の概要](./conceptual-configurations.md)