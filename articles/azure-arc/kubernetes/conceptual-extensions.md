---
title: クラスター拡張機能 - Azure Arc 対応 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事では、Azure Arc 対応 Kubernetes のクラスター拡張機能の概要について説明します
ms.openlocfilehash: 6ae9270822ae16608a2c470ed818bcb8e6c2aa38
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279274"
---
# <a name="cluster-extensions"></a>クラスター拡張機能

Azure Monitor、Microsoft Defender for Kubernetes、Azure アプリ サービスや Azure Data Service などのサービスのような管理サービスは、クラスター拡張機能を通じて Kubernetes クラスターでインスタンス化できます。 [Helm チャート](https://helm.sh/)により、最も複雑な Kubernetes アプリケーションでも、そのアプリケーションを定義、インストール、アップグレードするために必要な構成要素を提供することで管理できます。 クラスター拡張機能は、Helm のパッケージング コンポーネントの上に構築され、kubernetes クラスターで有効にする予定のサービスのインスタンスのインストールとライフサイクル管理のための Azure Resource Manager 主導のエクスペリエンスが提供されます。 クラスターのオペレーターまたは管理者は、クラスター拡張機能を使用して、次のことを行うことができます。 

- 必要な機能のさまざまな拡張機能をインストールし、すべてのクラスターとそれらのクラスターにインストールされている拡張機能のインベントリを Azure portal、CLI、SDK などの Azure インターフェイスから取得します。 
- 他の Azure リソースと同様に、Azure のロールベースのアクセス制御 (RBAC) を使用して、クラスター拡張機能リソースへのアクセスを制御できます
- Azure Policy を使用して、環境内のすべてのクラスターへのクラスター拡張機能の大規模なデプロイを自動化します。 
- 各拡張機能のリリース トレーニング (プレビュー、安定版など) にサブスクライブします。
- 拡張機能の自動アップグレードを設定するか、特定のバージョンに固定して、更新プログラムを管理します。
- 拡張機能のプロパティの更新や 1 つ以上の拡張機能インスタンスの削除など、拡張機能のライフサイクルを管理します。

拡張機能では、クラスタースコープにするか、名前空間にスコープを設定することができます。 各拡張機能の種類 (Azure Monitor、Microsoft Defender for Cloud、Azure アプリ サービスなど) によって、クラスター上で動作するスコープが定義されます。 

## <a name="architecture-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc 対応 Kubernetes クラスターのアーキテクチャ

[ ![クラスター拡張機能のアーキテクチャ](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

クラスター拡張機能インスタンスは、Azure Resource Manager で、(`Microsoft.Kubernetes/connectedClusters` によって表される) Azure Arc 対応 Kubernetes リソース上に拡張機能 Azure Resource Manager リソース (`Microsoft.KubernetesConfiguration/extensions`) として作成されます。 Azure Resource Manager での表現によって、特定のクラスター拡張機能が含まれる、または含まれないすべての Azure Arc 対応 Kubernetes リソースをチェックするポリシーを作成できます。 目的のプロパティ値を持つクラスター拡張機能がないクラスターを特定した後、Azure Policy を使用して、これらの非準拠リソースを修復できます。

クラスターで実行される `config-agent` により、Azure Arc 対応 Kubernetes リソースで新しく作成された拡張リソースと、既存の拡張機能リソースに対する更新が監視および追跡されます。 その後、クラスターで実行される `extensions-manager` コンポーネントにより、クラスター拡張機能に関連付けられている Helm チャートが Azure Container Registry または Microsoft Container Registry からプルされ、クラスターにインストールされます。 

クラスターで実行される `config-agent` および `extensions-manager` コンポーネントの両方により、新しいバージョンの更新プログラムと、拡張機能インスタンスのプロパティの更新や削除などの他の操作が処理されます。 これらのエージェントでは、システム割り当てマネージド ID を使用して、Azure のバックエンド サービスと安全に通信します。 

> [!NOTE]
> * `config-agent` により、Azure Arc 対応 Kubernetes リソースに新しく作成された拡張機能リソース、または既存の拡張機能リソースへの更新が監視されます。 そのため、拡張機能の目的の状態をクラスターにプルできるようにするために、エージェントには接続が必要です。 エージェントが Azure に接続できない場合、クラスターへの目的の状態の伝達が遅延します。
> * クラスター拡張機能に設定できるプロパティの 1 つに、ProtectedConfiguration 設定があります。 拡張機能の保護された構成設定は、Azure Arc 対応 Kubernetes サービスで最大 48 時間保存されます。 そのため、Azure で拡張機能リソースが作成された後、48 時間にわたってクラスターが切断されたままになっていると、拡張機能は `Pending` 状態から `Failed` 状態に遷移します。 クラスターをできるだけ定期的にオンラインにすることをお勧めします。

## <a name="next-steps"></a>次の手順

* クイックスタートを使用して、[Kubernetes クラスターを Azure Arc に接続します](./quickstart-connect-cluster.md)。
* Azure Arc 対応 Kubernetes クラスターに[クラスター拡張機能をデプロイ](./extensions.md)します。
