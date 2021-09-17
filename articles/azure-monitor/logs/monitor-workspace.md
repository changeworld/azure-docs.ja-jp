---
title: Azure Monitor で Log Analytics ワークスペースの正常性を監視する
description: Operation テーブル内のデータを使用して、Log Analytics ワークスペースの正常性を監視する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: ca35dc76107ca63dec885d34ebac9c9798334edb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690647"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor で Log Analytics ワークスペースの正常性を監視する
Azure Monitor で Log Analytics ワークスペースのパフォーマンスと可用性を維持するには、発生する問題を事前に検出できるようにする必要があります。 この記事では、[Operation](/azure/azure-monitor/reference/tables/operation) テーブル内のデータを使用して、Log Analytics ワークスペースの正常性を監視する方法について説明します。 このテーブルはすべての Log Analytics ワークスペースに含まれているもので、その中にはお使いのワークスペースで発生したエラーと警告が入っています。 "警告" および "エラー" レベルの問題についてアラートを作成することをお勧めします。

## <a name="_logoperation-function"></a>_LogOperation 関数

Azure Monitor ログでは、問題が発生したワークスペース内の [Operation](/azure/azure-monitor/reference/tables/operation) テーブルに問題の詳細を送信します。 **_LogOperation** システム関数は **Operation** テーブルに基づいており、分析とアラート作成のための簡略化された情報セットを提供します。

## <a name="columns"></a>[列]

**_LogOperation** 関数では、次の表に示した列が返されます。

| 列 | 説明 |
|:---|:---|
| TimeGenerated | インシデントが発生した時刻 (UTC)。 |
| カテゴリ  | 操作カテゴリ グループ。 これを使用すると、操作の種類をフィルター処理し、より正確なシステム監査およびアラートを作成できます。 カテゴリの一覧については、以下のセクションをご覧ください。 |
| 操作  | 操作の種類の説明。 操作は、Log Analytics の制限のいずれかに達したこと、バックエンド プロセスに関連する問題、またはその他のサービス メッセージを示している可能性があります。 |
| Level | 問題の重大度レベル:<br>- 情報: 特別な注意は必要ありません。<br>- 警告: プロセスが想定どおりに完了しなかったため、注意が必要です。<br>- エラー: プロセスが失敗したため、注意が必要です。 
| 詳細 | 操作の詳細な説明。具体的なエラー メッセージが含まれます。 |
| _ResourceId | 操作に関連する Azure リソースのリソース ID。  |
| Computer | コンピューター名 (操作が Azure Monitor エージェントに関連している場合)。 |
| CorrelationId | 連続する関連操作をグループ化するために使用されます。 |


## <a name="categories"></a>Categories

次の表では、_LogOperation 関数から得られるカテゴリについて説明します。 

| カテゴリ | 説明 |
|:---|:---|
| データの取り込み           | データ インジェスト プロセスに含まれている操作。 |
| エージェント               | エージェントのインストールに関する問題を示します。 |
| データ コレクション     | データ コレクション プロセスに関連する操作。 |
| ソリューションのターゲット設定  | *ConfigurationScope* 型の操作が処理されました。 |
| 評価ソリューション | 評価プロセスが実行されました。 |


### <a name="ingestion"></a>データの取り込み
インジェスト操作は、Azure Log Analytics ワークスペースの制限に達したことに関する通知など、データ インジェストの処理中に発生した問題です。 このカテゴリのエラー状態はデータ損失を示唆している可能性があるため、それらを監視することが重要です。 これらの操作の詳細については、以下の表をご覧ください。 Log Analytics ワークスペースに関するサービスの制限については、「[Azure Monitor サービスの制限](../service-limits.md#log-analytics-workspaces)」をご覧ください。

 
#### <a name="operation-data-collection-stopped"></a>操作: データ収集の停止  
"1 日あたりの無料データの上限に達したため、データ コレクションが停止しました。 取り込みの状態 = クォータ超過"

過去 7 日間で、ログ収集が 1 日に設定された上限に達しました。 この上限は、ワークスペースが "Free レベル" に設定されているか、このワークスペースに 1 日の収集上限が構成されているときに設定されます。
設定された上限に達すると、その日のデータ収集が自動的に停止され、次の収集日にのみ再開されます。 
 
推奨アクション: 
*   _LogOperation テーブルで、収集停止イベントと収集再開イベントを確認します。</br>
`_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Data collection"`
*   "データ収集の停止" 操作イベントの[アラートを作成](./manage-cost-storage.md#alert-when-daily-cap-is-reached)します。このアラートにより、収集の上限に達したときに通知を受け取ることができます。
*   1 日の収集上限に達した後に収集されたデータは失われます。[Workspace Insights] ブレードを使用して、各ソースからの使用率を確認します。 または、[データの 1 日の最大ボリュームを管理する](./manage-cost-storage.md#manage-your-maximum-daily-data-volume) \ 収集率パターンに合わせて[価格レベルを変更する](./manage-cost-storage.md#changing-pricing-tier)こともできます。 
* データ収集率は 1 日ごとに計算され、翌日の開始時にリセットされます。"データ収集の再開" 操作イベントの[アラートを作成](./manage-cost-storage.md#alert-when-daily-cap-is-reached)することで、収集再開イベントを監視することもできます。

#### <a name="operation-ingestion-rate"></a>操作: インジェスト率
"データ インジェスト ボリューム率がワークスペースのしきい値 (1 分あたり {0:0.00} MB) を超え、データが削除されました。" 

推奨アクション:
*   _LogOperation テーブルでインジェスト率イベントを確認します (`_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Ingestion rate"`)。 
  注: しきい値を超え続けている間の、ワークスペースの 6 時間ごとの操作テーブル。 
*   "データ収集の停止" 操作イベントの[アラートを作成](./manage-cost-storage.md#alert-when-daily-cap-is-reached)します。このアラートにより、上限に達したときに通知を受け取ることができます。
*   インジェスト率が 100% に達している間に収集されたデータは破棄され、失われます。 

[Workspace Insights] ブレードで使用パターンを確認し、それらの削減を試みます。</br>
詳細情報: </br>
[Azure Monitor サービスの制限](../service-limits.md#data-ingestion-volume-rate) </br>
[Azure Monitor ログの使用量とコストを管理する](./manage-cost-storage.md#alert-when-daily-cap-is-reached)  

 
#### <a name="operation-maximum-table-column-count"></a>操作: テーブルの最大列数
"種類が \<**table name**\> のデータが削除されました。フィールド数 \<**new fields count**\> がデータ種別ごとのカスタム フィールド数の上限 \<**current field count limit**\> を超えたためです。" 

推奨アクション: カスタム テーブルの場合、クエリでの[データの解析](./parse-text.md)に移ることができます。

#### <a name="operation-field-content-validation"></a>操作: フィールドの内容の検証
" 種類が \<**table name**\> である次のフィールドの値 \<**field name**\> が、最大許容サイズ \<**field size limit**\> バイトに切り捨てられました。 必要に応じて入力を調整してください。" 

上限を超えたフィールドが Azure ログによって処理され、許容されるフィールド制限まで切り捨てられました。 データ損失が発生するため、許容される制限を超えるフィールドの送信はお勧めしません。 

推奨アクション: 影響を受けるデータの種類のソースを確認します。
*   データが HTTP データ コレクター API を介して送信されている場合は、データを分割してから取り込むようにコードまたはスクリプトを変更する必要があります。
*   Log Analytics エージェントによって収集されたカスタム ログの場合、アプリケーションおよびツールのログ設定を変更します。
*   その他のデータの種類については、サポート ケースを作成してください。 
</br>詳細: [Azure Monitor サービスの制限](../service-limits.md#data-ingestion-volume-rate) 

### <a name="data-collection"></a>データ コレクション
#### <a name="operation-azure-activity-log-collection"></a>操作: Azure アクティビティ ログの収集
"サブスクリプションへのアクセスが失われました。 \<**subscription id**\> サブスクリプションが \<**tenant id**\> Azure Active Directory テナントにあることを確認します。 サブスクリプションが他のテナントに移されてもサービスには影響しませんが、テナントの情報が反映されるまでに最大で 1 時間かかります。 '"

説明: 別のテナントへのサブスクリプションの移動など、状況によっては、Azure アクティビティ ログがワークスペースに送信されなくなることがあります。 このような状況では、この記事で説明するプロセスに従って、サブスクリプションを再接続する必要があります。

推奨アクション: 
* 警告メッセージに記載されているサブスクリプションが既に存在しない場合は、[Azure アクティビティ ログ] ブレードに移動し、[ワークスペースのデータ ソース] で該当するサブスクリプションを選択して、最後に [切断] ボタンを選択します。
* 警告メッセージに記載されているサブスクリプションにアクセスできなくなった場合:
  * 手順 1 に従ってサブスクリプションを切断します。 
  * このサブスクリプションからのログの収集を続行するには、サブスクリプションの所有者に連絡してアクセス許可を修正し、アクティビティ ログの収集を再度有効にします。
* アクティビティ ログを Log Analytics ワークスペースに送信するには、[診断設定を作成します](../essentials/activity-log.md#send-to-log-analytics-workspace)。 

### <a name="agent"></a>エージェント
#### <a name="operation-linux-agent"></a>操作: Linux エージェント
"OMS 設定からの連続した 2 つの構成適用が失敗しました"

ポータルの構成設定が変更されました。

推奨アクション: この問題は、エージェントが新しい構成設定を取得する際に問題が発生した場合に発生します。 この問題を軽減するには、エージェントを再インストールする必要があります。 _LogOperation テーブルでエージェント イベントを確認します。</br>

 `_LogOperation | where TimeGenerated >= ago(6h) | where Category == "Agent" | where Operation == "Linux Agent"  | distinct _ResourceId`

リストには、エージェントの構成が間違っているリソース ID が示されます。
この問題を軽減するには、リストに示されているエージェントを再インストールする必要があります。

   

## <a name="alert-rules"></a>アラート ルール
Azure Monitor で[ログ クエリ アラート](../alerts/alerts-log-query.md)を使用すると、Log Analytics ワークスペースで問題が検出されたときに事前に通知されるようにできます。 コストを最小限に抑えながら、適切なタイミングで問題に対処できる方法を使用します。 サブスクリプションには、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」に記載されているアラート ルールごとに課金されます。

推奨される方法は、問題のレベルに基づいて、2 つのアラート ルールから始めることです。 エラーには 5 分おきなどの短い頻度を使用し、警告には 24 時間などの長い頻度を使用します。 エラーはデータ損失の可能性を示しているため、損失を最小限に抑えるために迅速に対応する必要があります。 警告は通常、迅速な対応を必要としない問題を示しているため、毎日確認することができます。

ログ アラート ルールを作成するには、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](../alerts/alerts-log.md)」に記載されているプロセスを使用します。 次のセクションでは、各ルールについて詳しく説明します。


| クエリ | しきい値 | 期間 | 頻度 |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

これらのアラート ルールでは、エラーまたは警告が発生したすべての操作に対して同じように対応します。 アラートを生成する操作に慣れてきたら、特定の操作に対して別の対応を取ることをお勧めします。 たとえば、特定の操作に対して別のユーザーに通知を送信することができます。 

特定の操作用のアラート ルールを作成するには、 **[カテゴリ]** と **[操作]** 列が含まれるクエリを使用します。 

次の例では、インジェストのボリューム率が上限の 80% に達したときに警告アラートが作成されます。

- ターゲット:Log Analytics ワークスペースを選択する
- 条件:
  - シグナル名: カスタム ログ検索
  - 検索クエリ: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - ベース: 結果の数
  - 条件: より大きい
  - しきい値: 0
  - 期間: 5 (分)
  - 頻度: 5 (分)
- アラート ルール名: 1 日のデータ制限に達しました
- 重大度: 警告 (重大度 1)


次の例では、データ コレクションが 1 日の上限に達したときに警告アラートが作成されます。 

- ターゲット:Log Analytics ワークスペースを選択する
- 条件:
  - シグナル名: カスタム ログ検索
  - 検索クエリ: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - ベース: 結果の数
  - 条件: より大きい
  - しきい値: 0
  - 期間: 5 (分)
  - 頻度: 5 (分)
- アラート ルール名: 1 日のデータ制限に達しました
- 重大度: 警告 (重大度 1)
 
## <a name="next-steps"></a>次の手順

- [ログ アラート](../alerts/alerts-log.md)の詳細を確認します。
- お使いのワークスペースに関する[クエリの監査データを収集](./query-audit.md)します。
