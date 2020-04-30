---
title: Azure Migrate Server Assessment での依存関係の分析
description: Azure Migrate Server Assessment を使用して、評価のために依存関係の分析を使用する方法について説明します。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024763"
---
# <a name="dependency-analysis"></a>依存関係の分析

この記事では、Azure Migrate:Server Assessment での依存関係の分析について説明します。

## <a name="overview"></a>概要

依存関係の分析を行うと、評価して Azure に移行しようとしているオンプレミスのマシン間の依存関係を特定する助けとなります。 

- Azure Migrate:Server Assessment では、複数のマシンを 1 つのグループにまとめ、そのグループを評価します。 依存関係の分析は、マシンをより正確にグループ化し、評価の信頼性を高めるために役立ちます。
- 依存関係の分析によって、一緒に移行する必要があるマシンを特定できます。 コンピューターが使用中かどうか、または移行せずに使用を停止できるかどうかを特定できます。
- 依存関係を分析すると、忘れたことがないことを確認し、移行時の突然の停止を回避する助けになります。
- 分析は特に、マシンが、Azure に移行するアプリのデプロイの一部になっているかどうか不明な場合に役立ちます。
- 依存関係の分析に関する一般的な質問を[確認](common-questions-discovery-assessment.md#what-is-dependency-visualization)してください。

依存関係の分析をデプロイする場合は、2 つのオプションがあります

- **エージェントベース**:エージェントベースの依存関係の分析では、分析するオンプレミスの各マシンにエージェントをインストールする必要があります。
- **エージェントレス**:エージェントレスの分析では、クロスチェックを行うコンピューターにエージェントをインストールする必要はありません。 このオプションは現在プレビュー段階であり、VMware VM でのみ使用できます。

> [!NOTE]
> エージェントベースの依存関係の分析は、Azure Government では使用できません。 エージェントレスの依存関係の分析を使用できます。

## <a name="agentless-analysis"></a>エージェントレスの分析

エージェントレスの依存関係の分析は、それが有効になっているマシンから TCP 接続データをキャプチャすることで機能します。 分析するマシンにエージェントがインストールされている必要はありません。

### <a name="collected-data"></a>収集されるデータ

依存関係の検出が開始されると、アプライアンスは 5 分ごとにマシンのデータをポーリングし、データを収集します。 このデータは、vSphere API を使用して、vCenter Server 経由でゲスト VM から収集されます。 収集されたデータは、Azure Migrate アプライアンスで処理されて ID 情報が推測され、6 時間おきに Azure Migrate に送信されます。

ポーリングでは、以下のデータがマシンから収集されます。 
- アクティブな接続が含まれるプロセスの名前。
- アクティブな接続が含まれるプロセスを実行するアプリケーションの名前。
- アクティブな接続に関する宛先のポート。

## <a name="agent-based-analysis"></a>エージェントベースの分析

エージェントベースの分析の場合、Server Assessment で Azure Monitor の [Service Map ソリューション](../azure-monitor/insights/service-map.md)を使用して、依存関係の視覚化と分析を可能にします。 分析する各マシンには、[Microsoft Monitoring Agent/Log Analytics エージェント](../azure-monitor/platform/agents-overview.md#log-analytics-agent)と [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent) をインストールする必要があります。

### <a name="collected-data"></a>収集されるデータ

エージェントベースの分析では、以下のデータが収集されます。

- ソース マシンのサーバー名、プロセス、アプリケーション名。
- ターゲット マシンのサーバー名、プロセス、アプリケーション名、ポート。
- 接続数、待機時間、データ転送に関する情報が収集され、Log Analytics クエリで使用できます。 


## <a name="compare-agentless-and-agent-based"></a>エージェントレスとエージェントベースを比較する

エージェントレスの視覚化とエージェントベースの視覚化の違いを次の表にまとめます。

**要件** | **エージェントレス** | **エージェント ベース**
--- | --- | ---
サポート | このオプションは現在プレビュー段階であり、VMware VM でのみ使用できます。 サポートされているオペレーティング システムについては[こちらを確認してください](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)。 | 一般提供 (GA) 中。
エージェント | クロスチェックを行うマシンにエージェントをインストールする必要はありません。 | 分析するオンプレミスの各マシンにエージェントをインストールします。[Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) と[依存関係エージェント](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
Log Analytics | 不要。 | Azure Migrate では、依存関係の分析のために [Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)の [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) ソリューションを使用します。 
しくみ | 依存関係の視覚化が有効になっているコンピューター上の TCP 接続データをキャプチャします。 検出後は、5 分間隔でデータが収集されます。 | マシンにインストールされている Service Map エージェントにより、TCP プロセスと、各プロセスの受信/送信接続に関するデータが収集されます。
Data | ソース マシンのサーバー名、プロセス、アプリケーション名。<br/><br/> ターゲット マシンのサーバー名、プロセス、アプリケーション名、ポート。 | ソース マシンのサーバー名、プロセス、アプリケーション名。<br/><br/> ターゲット マシンのサーバー名、プロセス、アプリケーション名、ポート。<br/><br/> 接続数、待機時間、データ転送に関する情報が収集され、Log Analytics クエリで使用できます。 
グラフ | 1 つのサーバーの依存関係マップを、1 時間から 30 日までの範囲で表示できます。 | 1 つのサーバーの依存関係マップ。<br/><br/> マップは 1 時間についてのみ表示できます。<br/><br/> サーバーのグループの依存関係マップ。<br/><br/> マップ ビューからグループのサーバーを追加および削除します。
データのエクスポート | 現在、表形式でダウンロードすることはできません。 | データは Log Analytics で照会できます。



## <a name="next-steps"></a>次のステップ
- [VMware VM](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)、[物理サーバー](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)、および [Hyper-V VM](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements) に向けてエージェントベースの分析を設定するための要件を確認する。
- VMware VM のエージェントレスの分析に関する要件を[確認](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)する。
- エージェントベースの依存関係の視覚化を[設定する](how-to-create-group-machine-dependencies.md)
- VMware VM のエージェントレスの依存関係の視覚化を[試す](how-to-create-group-machine-dependencies-agentless.md)。
- 依存関係の視覚化について[よく寄せられる質問](common-questions-discovery-assessment.md#what-is-dependency-visualization)を確認する。


