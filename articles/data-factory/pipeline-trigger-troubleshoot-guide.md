---
title: Azure Data Factory でのパイプライン オーケストレーションおよびトリガーのトラブルシューティング
description: さまざまな方法を使用して、Azure Data Factory でのパイプライン トリガーの問題のトラブルシューティングを行います
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589170"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Azure Data Factory でのパイプライン オーケストレーションおよびトリガーのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory の "パイプライン実行" により、パイプラインの実行のインスタンスが定義されます。 たとえば、午前 8 時、午前 9 時、午前 10 時に実行するパイプラインがあるとします。 この場合、パイプラインの 3 つの独立した実行 (パイプライン実行) があることになります。 各パイプライン実行には、一意のパイプライン実行 ID があります。 実行 ID は、特定のパイプライン実行を定義する GUID (グローバル一意識別子) です。

パイプライン実行は、通常、パイプラインで定義したパラメーターに引数を渡してインスタンス化されます。 パイプラインを実行するには、手動で行う方法と "トリガー" を使用する方法があります。 詳細については、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)」をご覧ください。

## <a name="common-issues-causes-and-solutions"></a>一般的な問題、原因、解決策

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Azure 関数を使用したパイプラインで、プライベート エンドポイント接続でエラーがスローされる
 
#### <a name="issue"></a>問題
一部のコンテキストでは、Data Factory と Azure 関数アプリがプライベート エンドポイントで実行されています。 Azure 関数アプリと連携して動作するパイプラインを取得しようとしています。 3 つの異なるメソッドを試行しましたが、1 つのメソッドでエラー `Bad Request` が返され、他の 2 つのメソッドで `103 Error Forbidden` が返されました。

#### <a name="cause"></a>原因 
現在、Data Factory は Azure 関数アプリ用のプライベート エンドポイント コネクタをサポートしていません。 これが、Azure 関数アプリがプライベート リンクからの接続だけを許可するように構成されているために呼び出しを拒否している理由です。

#### <a name="resolution"></a>解決方法
**PrivateLinkService** 型のプライベート エンドポイントを作成し、関数アプリの DNS を指定して、接続を機能させることができます。

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>パイプラインの実行は強制終了されるが、モニターには進行状況が表示される

#### <a name="issue"></a>問題
パイプラインの実行を強制終了しても、パイプラインの監視で進行状況が表示されることがよくあります。 これは、ブラウザーでのキャッシュの問題が原因で発生し、監視のための適切なフィルターがありません。

#### <a name="resolution"></a>解決方法
ブラウザーを最新の情報に更新し、監視用に適切なフィルターを適用します。
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>コピー パイプラインの失敗 - 予想された列数よりも多くの列が見つかった (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>問題  
コピーしている特定のフォルダーにあるファイルに、不定の列数、異なる区切り文字、引用符文字の設定、何らかのデータ問題などスキーマが異なるファイルが含まれている場合、Data Factory パイプラインの実行は次のエラーで終了します。

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>解決方法
[データのコピー] アクティビティの作成中に [バイナリ コピー] オプションを選択します。 このように、**バイナリ** オプションを使用してデータを一括コピーまたは Data Lake 間で移行する場合、Data Factory はファイルを開いてスキーマを読み取ることはせず、すべてのファイルをバイナリとして扱い、他の場所にコピーするだけです。

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>統合ランタイムの容量限度に達した場合、パイプラインの実行が失敗する

#### <a name="issue"></a>問題
エラー メッセージ

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

このエラーは、統合ランタイムごとの限度 (現在は 50) を示しています。 詳細については、[限度](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2)に関するページをご覧ください。

同じ統合ランタイムを同時に使用して大量のデータフローを実行した場合、このようなエラーが発生する可能性があります。

#### <a name="resolution"></a>解決方法 
- これらのパイプラインを別々のトリガー時間に分割して実行します。
- 新しい統合ランタイムを作成し、複数の統合ランタイムにこれらのパイプラインを分割します。

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>定期的な間隔でパイプライン エラーを監視する方法

#### <a name="issue"></a>問題
多くの場合、Data Factory パイプラインはたとえば 5 分間隔で定期的に監視する必要があります。 エンドポイントを使用して、データ ファクトリからパイプラインの実行を照会し、フィルター処理することができます。 

#### <a name="recommendation"></a>推奨
1. 失敗したすべてのパイプラインを 5 分ごとに照会するように Azure Logic App を設定します。
2. 次に、[QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory) に従って、インシデントをチケット システムに報告できます。

#### <a name="reference"></a>関連項目
- [Data Factory から通知を外部送信する](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>パイプラインのアクティビティ レベルのエラーと障害を処理する方法

#### <a name="issue"></a>問題
Azure Data Factory オーケストレーションでは、条件付きロジックが許可され、ユーザーは、前のアクティビティの結果に基づいて異なるパスを選択できます。 次の 4 つの条件付きパスを使用できます。"成功時 (既定のパス)"、"失敗時"、"完了時"、"スキップ時"。 異なるパスを使用できます。

Azure Data Factory では、パイプライン実行の成功と失敗が次のように定義されています。

- すべてのリーフ レベル アクティビティの結果を評価します。 リーフ アクティビティがスキップされた場合は、代わりにその親アクティビティを評価します。
- すべてのリーフが成功した場合にのみ、パイプラインの結果は成功します。

#### <a name="recommendation"></a>推奨
- [パイプラインの失敗とエラーの処理方法](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)に従って、アクティビティ レベルのチェックを実装します。
- Azure Logic App を使用して、[DataFactory によるクエリ]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)に関するページに従って、定期的な間隔でパイプラインを監視します。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)