---
title: クラスター拡張機能 - Azure Arc 対応 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事では、Azure Arc 対応 Kubernetes のクラスター拡張機能について概要を示します。
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450880"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes のクラスター拡張機能

[Helm チャート](https://helm.sh/)により、最も複雑な Kubernetes アプリケーションでも、そのアプリケーションを定義、インストール、アップグレードするために必要な構成要素を提供することで管理できます。 クラスター拡張機能では、Helm のパッケージング コンポーネント上でのビルドが試行されます。 これは、Azure Monitor や Azure Defender for Kubernetes などのクラスター拡張機能のインストールとライフサイクル管理のために、Azure Resource Manager 主導のエクスペリエンスを提供することで行われます。 クラスター拡張機能により、Helm チャートで既にネイティブに得られているものに加えて、次の追加の利点が得られます。

- すべてのクラスターとそれらのクラスターにインストールされている拡張機能のインベントリを取得する。
- Azure Policy を使用して、クラスター拡張機能の大規模なデプロイを自動化する。
- すべての拡張機能の Release Train にサブスクライブする。
- 拡張機能の自動アップグレードを設定する。
- 拡張機能インスタンスの作成、および更新と削除のライフサイクル管理イベントのサポート。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architecture

[ ![クラスター拡張機能のアーキテクチャ](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

クラスター拡張機能インスタンスは、Azure Resource Manager で、(`Microsoft.Kubernetes/connectedClusters` によって表される) Azure Arc 対応 Kubernetes リソース上に拡張機能 Azure Resource Manager リソース (`Microsoft.KubernetesConfiguration/extensions`) として作成されます。 Azure Resource Manager での表現によって、特定のクラスター拡張機能が含まれる、または含まれないすべての Azure Arc 対応 Kubernetes リソースをチェックするポリシーを作成できます。 目的のプロパティ値を持つクラスター拡張機能がないクラスターを特定した後、Azure Policy を使用して、これらの非準拠リソースを修復できます。

クラスターで実行される `config-agent` により、Azure Arc 対応 Kubernetes リソースの新規または更新済み拡張機能リソースが追跡されます。 クラスターで実行される `extensions-manager` により、Azure Container Registry または Microsoft Container Registry から Helm チャートがプルされ、クラスターにインストールれます。 

クラスターで実行される `config-agent` と `extensions-manager` の両方のコンポーネントにより、バージョンの更新と拡張機能インスタンスの削除が処理されます。

> [!NOTE]
> * `config-agent` により、Arc 対応 Kubernetes リソースで使用できる新規または更新済み拡張機能リソースがないかモニターされます。 そのため、目的の状態をクラスターにプルできるようにするために、エージェントには接続が必要です。 エージェントが Azure に接続できない場合、クラスターへの目的の状態の伝達が遅延します。
> * 拡張機能の保護された構成設定は、Azure Arc 対応 Kubernetes サービスで最大 48 時間保存されます。 そのため、Azure で拡張機能リソースが作成された後、48 時間にわたってクラスターが切断されたままになっていると、拡張機能は `Pending` 状態から `Failed` 状態に遷移します。 クラスターをできるだけ定期的にオンラインにすることをお勧めします。

## <a name="next-steps"></a>次の手順

* クイックスタートを利用して、[Kubernetes クラスターを Azure Arc に接続](./quickstart-connect-cluster.md)する。
* Azure Arc 対応 Kubernetes クラスターに[クラスター拡張機能をデプロイ](./extensions.md)する。