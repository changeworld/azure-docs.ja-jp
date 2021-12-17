---
title: Azure Batch の監視
description: Azure Batch の Azure 監視サービス、メトリック、診断ログ、その他の監視機能について説明します。
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: b2b73261b07a7e5eb269c1f58a45f97d99133ac4
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768888"
---
# <a name="monitor-batch-solutions"></a>Batch ソリューションの監視

[Azure Monitor](../azure-monitor/overview.md) と Batch サービスには、Batch ソリューションを監視するためのさまざまなサービス、ツール、API が用意されています。 この概要記事は、ニーズに合った監視アプローチの選択に役立ちます。

## <a name="subscription-level-monitoring"></a>サブスクリプション レベルの監視

Batch アカウントを含むサブスクリプション レベルでは、[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)によっていくつかのカテゴリの運用イベント データが収集されます。

具体的に Batch アカウントでは、アクティビティ ログによってアカウントの作成と削除やキー管理に関連するイベントが収集されます。

アクティビティ ログを Azure portal で表示でき、Azure CLI、PowerShell コマンドレット、または Azure Monitor REST API を使用してイベントのクエリを実行できます。 アクティビティ ログをエクスポートしたり、[アクティビティ ログ アラート](../azure-monitor/alerts/alerts-activity-log.md)を構成したりすることもできます。

## <a name="batch-account-level-monitoring"></a>Batch アカウント レベルの監視

[Azure Monitor](../azure-monitor/overview.md) の機能を使用して各 Batch アカウントを監視します。 Azure Monitor では、Batch アカウントのレベルでリソース (プール、ジョブ、タスクなど) の[メトリック](../azure-monitor/essentials/data-platform-metrics.md)と、オプションで[リソース ログ](../azure-monitor/essentials/resource-logs.md)が収集されます。 このデータを手動またはプログラムによって収集および利用して、Batch アカウント内のアクティビティを監視し、問題を診断します。 詳しくは、「[Batch の診断の評価と監視用のメトリック、アラート、およびログ](batch-diagnostics.md)」をご覧ください。

> [!NOTE]
> メトリックは、既定では追加の構成なしで Batch アカウントで利用でき、30 日間のローリング履歴があります。 Batch アカウントのリソース ログを Log Analytics ワークスペースに送信するには、その診断設定を作成する必要があります。また、リソース ログ データを格納か処理するためには、追加のコストが発生する可能性があります。

## <a name="batch-resource-monitoring"></a>Batch リソースの監視

Batch アプリケーションでは、Batch API を使用して、ジョブ、タスク、ノード、プールなどのリソースの状態の監視またはクエリを実行します。 次に例を示します。

- [状態ごとにタスクとコンピューティング ノードをカウントする](batch-get-resource-counts.md)
- [効率的に Batch リソースを一覧表示するクエリを作成する](batch-efficient-list-queries.md)
- [タスクの依存関係を作成する](batch-task-dependencies.md)
- [ジョブ マネージャー タスク](/rest/api/batchservice/job/add#jobmanagertask)を使用する
- [タスクの状態](/rest/api/batchservice/task/list#taskstate)を監視する
- [ノードの状態](/rest/api/batchservice/computenode/list#computenodestate)を監視する
- [プールの状態](/rest/api/batchservice/pool/get#poolstate)を監視する
- [アカウントでのプールの使用状況](/rest/api/batchservice/pool/listusagemetrics)を監視する
- [状態別にプール ノード数をカウントする](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="additional-monitoring-solutions"></a>その他の監視ソリューション

[Application Insights](../azure-monitor/app/app-insights-overview.md) は、Batch のジョブとタスクの可用性、パフォーマンス、使用状況をプログラムで監視するために使用します。 Application Insights では、コンピューティング ノード (VM) からパフォーマンス カウンターを監視し、そのノードに対して実行されるタスクのカスタム情報を取得できます。

例については、[Application Insights による Batch .NET アプリケーションの監視とデバッグ](monitor-application-insights.md)に関する記事と付随する[コード サンプル](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)をご覧ください。

> [!NOTE]
> Application Insights の使用には追加コストが発生する可能性があります。 [価格情報](https://azure.microsoft.com/pricing/details/application-insights/)を参照してください。

[Batch Explorer](https://github.com/Azure/BatchExplorer) は、Azure Batch アプリケーションの作成、デバッグ、および監視を支援する、豊富な機能を備えた無料のスタンドアロン クライアント ツールです。 Mac、Linux、または Windows 用の[インストール パッケージ](https://azure.github.io/BatchExplorer/)をダウンロードしてください。 必要に応じて、[Azure Batch インサイト](https://github.com/Azure/batch-insights)を使用して、VM パフォーマンス カウンターなどの Batch ノードのシステム統計情報を Batch Explorer で取得します。

## <a name="next-steps"></a>次のステップ

- Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。
- Batch での[診断ログ](batch-diagnostics.md)の詳細について学習します。
