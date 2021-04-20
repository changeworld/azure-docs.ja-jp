---
title: Azure Data Factory でのパイプライン オーケストレーションおよびトリガーのトラブルシューティング
description: さまざまな方法を使用して、Azure Data Factory でのパイプライン トリガーの問題のトラブルシューティングを行います
author: ssabat
ms.service: data-factory
ms.date: 04/01/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 49205025e26f7c0eb609638e70a58c9c0c14748e
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385413"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Azure Data Factory でのパイプライン オーケストレーションおよびトリガーのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory の "パイプライン実行" により、パイプラインの実行のインスタンスが定義されます。 たとえば、午前 8 時、午前 9 時、午前 10 時に実行するパイプラインがあるとします。 この場合、3 つの独立したパイプライン実行があることになります。 各パイプライン実行には、一意のパイプライン実行 ID があります。 実行 ID は、特定のパイプライン実行を定義するグローバル一意識別子 (GUID) です。

パイプライン実行は、通常、パイプラインで定義したパラメーターに引数を渡してインスタンス化されます。 パイプラインを実行するには、手動で行う方法とトリガーを使用する方法があります。 詳細については、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)」を参照してください。

## <a name="common-issues-causes-and-solutions"></a>一般的な問題、原因、解決策

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions アプリ パイプラインからプライベート エンドポイント接続のエラーがスローされる
 
Data Factory と Azure 関数アプリがプライベート エンドポイントで実行されているとします。 関数アプリと連携するパイプラインを実行しようとしています。 3 つの異なるメソッドを試行しましたが、1 つのメソッドでエラー "Bad Request" が返され、他の 2 つのメソッドで "103 Error Forbidden" が返されました。

**原因**

現在、Data Factory では、関数アプリ用のプライベート エンドポイント コネクタはサポートされていません。 Azure Functions は、プライベート リンクからの接続のみを許可するように構成されているため、呼び出しは拒否されます。

**解像度**

**PrivateLinkService** エンドポイントを作成し、関数アプリの DNS を指定します。

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>パイプライン実行をキャンセルしたにもかかわらず、モニターに進行状況が表示される

**原因**

パイプラインの実行をキャンセルしても、多くの場合、パイプラインの監視で進行状況が表示されます。 これは、ブラウザーのキャッシュの問題が原因で発生します。 また、正しい監視フィルターがない可能性もあります。

**解像度**

ブラウザーを最新の状態に更新し、正しい監視フィルターを適用します。
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>パイプラインをコピーするときに "DelimitedTextMoreColumnsThanDefined" エラーが表示される
 
 **原因**
 
コピーしているフォルダーに、不定の列数、異なる区切り文字、引用符文字の設定、何らかのデータ問題などスキーマが異なるファイルが含まれている場合、Data Factory パイプラインによって次のエラーがスローされる場合があります。

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**解像度**

Copy アクティビティの作成時に **[バイナリ コピー]** オプションを選択します。 これにより、データの一括コピーやデータ レイク間でのデータの移行の際に、Data Factory によってファイルが開かれてスキーマが読み取られることがなくなります。 代わりに、Data Factory によって各ファイルがバイナリとして扱われて、他の場所にコピーされます。

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>データ フローの統合ランタイムの容量の上限に達したときに、パイプライン実行が失敗する

**問題点**

エラー メッセージ

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**原因**

統合ランタイムの容量の上限に達しました。 同じ統合ランタイムを同時に使用して、大量のデータ フローを実行している可能性があります。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2)」を参照してください。

**解像度**
 
- 異なるトリガーのタイミングでパイプラインを実行します。
- 新しい統合ランタイムを作成し、パイプラインを複数の統合ランタイムで分割します。

### <a name="a-pipeline-run-error-while-invoking-rest-api-in-a-web-activity"></a>Web アクティビティで REST API を呼び出すと、パイプライン実行エラーが発生する

**問題点**

エラー メッセージ

`
Operation on target Cancel failed: {“error”:{“code”:”AuthorizationFailed”,”message”:”The client ‘<client>’ with object id ‘<object>’ does not have authorization to perform action ‘Microsoft.DataFactory/factories/pipelineruns/cancel/action’ over scope ‘/subscriptions/<subscription>/resourceGroups/<resource group>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelineruns/<pipeline run id>’ or the scope is invalid. If access was recently granted, please refresh your credentials.”}}
`

**原因**

Azure Data Factory メンバーに共同作成者ロールが割り当てられている場合にのみ、パイプラインで Web アクティビティを使用して ADF REST API メソッドを呼び出すことができます。 最初に構成を行い、共同作成者セキュリティ ロールに Azure Data Factory マネージド ID を追加する必要があります。 

**解像度**

Web アクティビティの [設定] タブで Azure Data Factory の REST API を使用する前に、セキュリティを構成する必要があります。 Azure Data Factory マネージド ID に "*共同作成者*" ロールが割り当てられている場合にのみ、Azure Data Factory パイプラインで Web アクティビティを使用して ADF REST API メソッドを呼び出すことができます。 最初に、Azure portal を開き、左側のメニューの **[すべてのリソース]** リンクをクリックします。 *[ロールの割り当てを追加する]* ボックスの **[追加]** ボタンをクリックして、共同作成者ロールを持つ ADF マネージド ID を追加する **Azure Data Factory** を選択します。


### <a name="how-to-check-and-branch-on-activity-level-success-and-failure-in-pipelines"></a>パイプラインでアクティビティ レベルの成功と失敗を確認および分岐する方法

**原因**

Azure Data Factory オーケストレーションでは、条件付きロジックを使用することができ、ユーザーは前のアクティビティの結果に基づいて異なるパスを選択できます。 次の 4 つの条件付きパスを使用できます。**成功時** (既定のパス)、**失敗時**、**完了時**、**スキップ時**。 

Azure Data Factory では、すべてのリーフレベルのアクティビティの結果が評価されます。 すべてのリーフが成功した場合にのみ、パイプラインの結果は成功します。 リーフ アクティビティがスキップされた場合は、代わりにその親アクティビティを評価します。 

**解像度**

* [パイプラインの失敗とエラーの処理方法](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)に関するページに従って、アクティビティレベルのチェックを実装します。
* [Factory によるクエリ](/rest/api/datafactory/pipelineruns/querybyfactory)に関するページに従って、Azure Logic Apps を使用して定期的な間隔でパイプラインを監視します。
* [視覚的にパイプラインを監視する](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>定期的な間隔でパイプライン エラーを監視する方法

**原因**

障害が発生した Data Factory パイプラインを、特定の間隔 (5 分など) で監視する必要がある場合があります。 エンドポイントを使用して、データ ファクトリからパイプライン実行にクエリを実行し、フィルター処理することができます。 

**解像度**
* [Factory によるクエリ](/rest/api/datafactory/pipelineruns/querybyfactory)に関するページで説明されているように、失敗したすべてのパイプラインのクエリを 5 分ごとに実行するように Azure ロジック アプリを設定できます。 そうすると、インシデントをチケット システムに報告できるようになります。
* [視覚的にパイプラインを監視する](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>並列処理の次数を増やしてもスループットが向上しない

**原因** 

*ForEach* の並列処理の次数は、実際には並列処理の最大限度です。 このパラメーターは、特定の同時実行数を保証するものではなく、設定された値を超えないことを保証するものです。 これは、ソースとシンクへの同時アクセスを制御するときに利用できる制限値として考えてください。

*ForEach* に関する既知の事実
 * Foreach には、batch count(n) というプロパティがあります。既定値は 20 で、最大値は 50 です。
 * バッチ カウント (n) は、作成するキューの個数を指定するために使用されます。 
 * 各キューは順番に実行されますが、複数のキューを並列に実行することもできます。
 * キューは事前に作成されます。 つまり、実行時にはキューの再調整は行われません。
 * 処理されるアイテムは、キューごとに常に 1 つだけです。 つまり、一度に処理される最大項目数は n 個となります。
 * foreach の合計処理時間は、最も長いキューの処理時間と同じです。 つまり、foreach アクティビティのあり方は、キューの作成方法によって決まります。
 
**解像度**

 * *SetVariable* アクティビティは、並列で実行される *For Each* 内では使用しないでください。
 * お客様は、キューの作成のしくみを考慮しながら、複数の *foreach* を設定し、各 *foreach* の項目の処理時間が同程度になるようにすることで、foreach のパフォーマンスを向上させることができます。 
 * これにより、長時間の実行が順次ではなく、並列で処理されるようになります。

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>パイプラインが長時間キューに入っているか停止している状態である
 
 **原因**
 
 これは、同時実行の上限への到達、サービスの停止、ネットワーク エラーなど、さまざまな理由で発生する可能性があります。
 
 **解像度**
 
* 同時実行の上限: パイプラインに同時実行ポリシーがある場合は、実行中の古いパイプラインの実行がないことを確認します。 Azure Data Factory で許可されるパイプラインの最大同時実行数は 10 パイプラインです。 
* 監視の制限: ADF 作成キャンバスにアクセスし、パイプラインを選択し、同時実行プロパティが割り当てられているかどうかを確認します。 割り当てられている場合は、監視ビューに移動し、過去 45 日間に進行中のものがないことを確認します。 進行中の何かがある合は、それを取り消し、新しいパイプラインの実行を開始する必要があります。
* 一時的な問題: 一時的なネットワークの問題、資格情報の失敗、サービスの停止などが、実行に影響した可能性があります。このような場合、Azure Data Factory には、すべての実行を監視し、何らかの問題が検出されるとそれらを開始する内部回復プロセスがあります。 このプロセスは 1 時間ごとに発生するため、実行が 1 時間以上停止する場合は、サポート ケースを作成します。
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>ADF コピーとデータ フローのアクティビティの開始時間が長い

**原因**

これは、データフローまたは最適化され SHIR に対して Time to Live を実装していない場合に発生する可能性があります。

**解像度**

* 各コピー アクティビティの開始に最大 2 分かかり、問題が主に VNet 結合でが発生する場合は (Azure IR ではなく)、コピーのパフォーマンスの問題である可能性があります。 トラブルシューティングの手順を確認するには、[コピー パフォーマンスの向上](./copy-activity-performance-troubleshooting.md)に関するページを参照してください。
* Time to Live 機能を使用すると、データ フロー アクティビティのクラスターの起動時間を短縮できます。 [データ フロー統合ランタイム](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)を確認してください。

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>SHIR (セルフホステッド統合ランタイム) での容量の問題の発生
 
 **原因**
 
これは、ワークロードに従って SHIR をスケールアップしていない場合に発生する可能性があります。

**解像度**

* SHIR で容量の問題が発生する場合は、VM をアップグレードしてノードを増やし、アクティビティのバランスを取ります。 長いキューが生成される可能性のある、セルフホステッド IR の一般的な障害またはエラー、セルフホステッド IR のアップグレード、またはセルフホステッド IR の接続の問題についてのエラー メッセージが表示される場合は、「[セルフホステッド統合ランタイムのトラブルシューティング](./self-hosted-integration-runtime-troubleshoot-guide.md)」を参照してください。

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>ADF コピーとデータ フローの長時間キューが原因のエラーメッセージ

**原因**

長時間キューに関連するエラー メッセージは、さまざまな理由で表示されることがあります。 

**解像度**
* 長いキューが生成される可能性のある、コネクタを介した送信元または送信先からのエラー メッセージが表示される場合は、[コネクタのトラブルシューティング ガイド](./connector-troubleshoot-guide.md)に関するページを参照してください。
* 長いキューが生成される可能性のある、マッピング データ フローに関するエラー メッセージが表示される場合は、[データ フローのトラブルシューティング ガイド](./data-flow-troubleshoot-guide.md)に関するページを参照してください。
* 長いキューが生成される可能性のある、Databricks、カスタム アクティビティ、HDI などの他のアクティビティに関するエラー メッセージが表示される場合は、[アクティビティのトラブルシューティング ガイド](./data-factory-troubleshoot-guide.md)に関するページを参照してください。
* 長いキューが生成される可能性のある、SSIS パッケージの実行に関するエラー メッセージが表示される場合は、Azure-SSIS の[パッケージ実行トラブルシューティング ガイド](./ssis-integration-runtime-ssis-activity-faq.md)および [Integration Runtime 管理トラブルシューティング ガイド](./ssis-integration-runtime-management-troubleshoot.md)に関するページで詳細を確認してください。


## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
