---
title: リソース ログの収集と分析
description: 認証、イメージのプッシュ、イメージのプルなど、Azure Container Registry のリソース ログ イベントを記録および分析します。
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409645"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>診断の評価と監査のための Azure Container Registry ログ

この記事では、[Azure Monitor](../azure-monitor/overview.md) の機能を使用して Azure コンテナー レジストリのログ データを収集する方法について説明します。 Azure Monitor を使用すると、レジストリ内のユーザー駆動型イベントの[リソース ログ](../azure-monitor/platform/platform-logs-overview.md) (以前は "*診断ログ*" と呼ばれていました) を収集できます。 このデータは、次のようなニーズに対応するために収集して使用します。

* レジストリ認証イベントを監査して、セキュリティとコンプライアンスを確保する 

* レジストリに関する操作上の問題を診断できるよう、プルやプル イベントなどのレジストリの成果物に関する完全な活動証跡を提供する 

Azure Monitor を使用してリソース ログ データを収集すると、追加のコストが発生する可能性があります。 「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。 

## <a name="repository-events"></a>リポジトリ イベント

現在、イメージとその他の成果物に対して次のリポジトリレベル イベントがログに記録されています。

* **プッシュ イベント**
* **プル イベント**
* **タグの解除イベント**
* **削除イベント** (リポジトリの削除イベントを含む)

現在ログに記録されていないリポジトリレベル イベント: 消去イベント。

## <a name="registry-resource-logs"></a>レジストリのリソース ログ

リソース ログには、Azure リソースによって出力された情報が含まれており、内部操作についての説明が得られます。 Azure コンテナー レジストリのログには、次のテーブルに格納されている認証およびリポジトリレベルのイベントが含まれます。 

* **ContainerRegistryLoginEvents** - 受信 ID および IP アドレスを含むレジストリ認証イベントと状態
* **ContainerRegistryRepositoryEvents** - レジストリ リポジトリ内のイメージとその他の成果物のプッシュやプルなどの操作
* **AzureMetrics** - 集計されたプッシュ数やプル数など、[コンテナー レジストリのメトリック](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries)。

操作のログ データには以下が含まれます。
  * 成功または失敗の状態
  * 開始および終了のタイム スタンプ

Azure には、リソース ログに加えて、[アクティビティ ログ](../azure-monitor/platform/platform-logs-overview.md)が用意されています。これは、コンテナー レジストリの作成や削除など、Azure 管理イベントの単一サブスクリプションレベルのレコードです。

## <a name="enable-collection-of-resource-logs"></a>リソース ログの収集を有効にする

コンテナー レジストリのリソース ログの収集は、既定では有効になっていません。 監視したい各レジストリの診断設定を明示的に有効にします。 診断設定を有効にするオプションについては、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/platform/diagnostic-settings.md)」を参照してください。

たとえば、Azure Monitor でコンテナー レジストリのログとメトリックをほぼリアルタイムで表示するには、Log Analytics ワークスペースでリソース ログを収集します。 Azure portal を使用してこの診断設定を有効にするには:

1. ワークスペースがまだない場合は、[Azure portal](../azure-monitor/learn/quick-create-workspace.md) を使用してワークスペースを作成します。 データ収集での待ち時間を最小限に抑えるために、ワークスペースをご自分のコンテナー レジストリと**同じリージョン**に配置するようにします。
1. ポータルでレジストリを選択し、 **[監視]、[診断設定]、[診断設定を追加する]** の順に選択します。
1. 設定の名前を入力し、 **[Log Analytics への送信]** を選択します。
1. レジストリ診断ログのワークスペースを選択します。
1. 収集したいログ データを選択し、 **[保存]** をクリックします。

次の画像は、ポータルを使用してレジストリの診断設定を作成する方法を示しています。

![診断設定の有効化](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> コストと監視ニーズのバランスを取りながら、必要なデータのみを収集するようにしてください。 たとえば、認証イベントのみを監査する必要がある場合は、**ContainerRegistryLoginEvents** ログのみを選択します。 

## <a name="view-data-in-azure-monitor"></a>Azure Monitor でデータを表示する

Log Analytics で診断ログの収集を有効にした後、データが Azure Monitor に表示されるまでに数分かかることがあります。 ポータルでデータを表示するには、レジストリを選択し、 **[監視]、[ログ]** の順に選択します。 レジストリのデータを含むテーブルの 1 つを選択します。 

クエリを実行してデータを表示します。 サンプル クエリがいくつか用意されています。自分で用意したものを実行してもかまいません。 たとえば、次のクエリを使用すると、**ContainerRegistryRepositoryEvents** テーブルから直近の 24 時間のデータを取得できます。

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

次の画像は、サンプル出力を示しています。

![ログ データのクエリ](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Azure portal での Log Analytics の使用に関するチュートリアルについては、[Azure Monitor Log Analytics の使用開始](../azure-monitor/log-query/get-started-portal.md)に関するページを参照するか、Log Analytics の[デモ環境](https://portal.loganalytics.io/demo)をお試しください。 

ログ クエリの詳細については、「[Azure Monitor のログ クエリの概要](../azure-monitor/log-query/log-query-overview.md)」を参照してください。

### <a name="additional-query-examples"></a>追加のクエリの例

#### <a name="100-most-recent-registry-events"></a>直近の 100 件のレジストリ イベント

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>追加のログの保存先

ログを Log Analytics に送信することに加えて、または別の方法として、Azure ストレージ アカウントをログの保存先として選択するのが一般的です。 ログを Azure Storage にアーカイブするには、診断設定を使用してアーカイブを有効にする前に、ストレージ アカウントを作成します。

また、診断ログ イベントを [Azure イベント ハブ](../event-hubs/event-hubs-what-is-event-hubs.md)にストリーム配信することもできます。 Event Hubs は、毎秒数百万のイベントを取り込み、任意のリアルタイム分析プロバイダーを使用して変換および格納できます。 

## <a name="next-steps"></a>次のステップ

* [Log Analytics](../azure-monitor/log-query/get-started-portal.md) の使用と[ログ クエリ](../azure-monitor/log-query/get-started-queries.md)の作成の詳細について学習します。
* 「[Azure プラットフォーム ログの概要](../azure-monitor/platform/platform-logs-overview.md)」を参照して、Azure のさまざまなレイヤーで使用できるプラットフォーム ログについて学習します。

