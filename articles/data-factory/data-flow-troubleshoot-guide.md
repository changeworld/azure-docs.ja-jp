---
title: データ フローのトラブルシューティング
description: Azure Data Factory でのデータ フローに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.openlocfilehash: a5244086c185c111762496086f8044f12f52be14
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632589"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Azure Data Factory でのデータ フローのトラブルシューティング

この記事では、Azure Data Factory のデータ フローの一般的なトラブルシューティング方法について説明します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="error-code-df-executor-sourceinvalidpayload"></a>エラー コード:DF-Executor-SourceInvalidPayload
- **メッセージ**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗しました
- **原因**:ストレージに存在しないコンテナーがデータセットに含まれている場合
- **推奨事項**:データセットで参照されているコンテナーが存在するかアクセス可能であることを確認してください。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>エラー コード:DF-Executor-SystemImplicitCartesian

- **メッセージ**:INNER join では暗黙的なデカルト積はサポートされていません。代わりに CROSS JOIN を使用してください。 結合で使用される列は、行に対して一意のキーを作成する必要があります。
- **原因**:論理プラン間の INNER join では暗黙的なデカルト積はサポートされていません。 結合で使用される列が一意のキーを作成する場合は、リレーションシップの両側から少なくとも 1 つの列が必要です。
- **推奨事項**:非等値ベースの結合では、CUSTOM CROSS JOIN を選択する必要があります。

### <a name="error-code-df-executor-systeminvalidjson"></a>エラー コード:DF-Executor-SystemInvalidJson

- **メッセージ**:JSON 解析エラー、サポートされていないエンコードまたは複数行が存在します
- **原因**:JSON ファイルで、次のような問題が発生している可能性があります。サポートされていないエンコード、バイトの破損、または入れ子になった多数の行での単一ドキュメントとしての JSON ソースの使用
- **推奨事項**:JSON ファイルのエンコードがサポートされているかどうか確認します。 JSON データセットを使用しているソース変換で [JSON 設定] を展開し、[単一のドキュメント] をオンにします。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>エラー コード:DF-Executor-BroadcastTimeout

- **メッセージ**:ブロードキャスト結合のタイムアウトエラーが発生しました。ブロードキャスト ストリームが、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成することを確認してください
- **原因**:ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト用に選択されたストリームは、この制限内にデータを生成するには大きすぎると思われます。
- **推奨事項**:処理に 60 秒を超える時間を要する可能性がある大規模なデータ ストリームのブロードキャストは避けてください。 代わりに、ブロードキャストするための小さいストリームを選択してください。 通常、大規模な SQL/DW テーブルとソース ファイルは適切な候補ではありません。

### <a name="error-code-df-executor-conversion"></a>エラー コード:DF-Executor-Conversion

- **メッセージ**:無効な文字が原因で、日付または時刻への変換に失敗しました
- **原因**:データの形式が正しくありません
- **推奨事項**:正しいデータ型を使用してください

### <a name="error-code-df-executor-invalidcolumn"></a>エラー コード:DF-Executor-InvalidColumn

- **メッセージ**:クエリに列名を指定する必要があります。SQL 関数を使用している場合は別名を設定してください
- **原因**:列名が指定されていませんでした
- **推奨事項**:min()/max() などの SQL 関数を使用している場合は、別名を設定してください。

### <a name="error-code-getcommand-outputasync-failed"></a>エラー コード:GetCommand OutputAsync に失敗しました

- **メッセージ**:データ フロー デバッグおよびデータ プレビューの実行中:GetCommand OutputAsync が次の理由で失敗しました。
- **原因**:これはバックエンド サービス エラーです。 操作を再試行し、デバッグ セッションを再起動することもできます。
- **推奨事項**:再試行して再起動しても問題が解決しない場合は、カスタマー サポートにお問い合わせください。

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>エラー コード:予期しない例外が発生し、実行が失敗しました

- **メッセージ**:データ フロー アクティビティの実行中:予期しない例外が発生し、実行が失敗しました。
- **原因**:これはバックエンド サービス エラーです。 操作を再試行し、デバッグ セッションを再起動することもできます。
- **推奨事項**:再試行して再起動しても問題が解決しない場合は、カスタマー サポートにお問い合わせください。

## <a name="general-troubleshooting-guidance"></a>一般的なトラブルシューティング ガイダンス

1. データセット接続の状態を確認します。 ソース/シンクの変換ごとに、使用している各データベースのリンクされたサービスにアクセスし、接続をテストします。
1. データ フロー デザイナーからファイルとテーブルの接続の状態を確認します。 デバッグをオンに切り替え、ソース変換のデータ プレビューをクリックし、データにアクセスできることを確認します。
1. データ プレビューに問題がなければ、パイプライン デザイナーに進み、パイプライン アクティビティにデータ フローを配置します。 エンドツーエンド テストとしてパイプラインをデバッグします。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。
*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [ADF マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)
