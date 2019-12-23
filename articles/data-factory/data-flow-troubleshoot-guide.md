---
title: データ フローのトラブルシューティング
description: Azure Data Factory でのデータ フローに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.custom: seo-lt-2019
ms.date: 12/06/2019
ms.openlocfilehash: b972bbeac419d88afdd257a7fd19587dbaedf0d9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930168"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Azure Data Factory データ フローのトラブルシューティング

この記事では、Azure Data Factory のデータ フローの一般的なトラブルシューティング方法について説明します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>エラー メッセージ:DF-SYS-01: shaded.databricks.org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException:指定されたコンテナーが存在しません。

- **現象**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗する

- **原因**:ストレージに存在しないコンテナーがデータセットに含まれている場合

- **解決方法**:データセットで参照しているコンテナーが存在することを確認する

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>エラー メッセージ:DF-SYS-01: java.lang.AssertionError: アサーションに失敗しました:ディレクトリ構造の競合が検出されました。 疑わしいパス

- **現象**:Parquet ファイルでソースを変換するときにワイルドカードを使用した場合

- **原因**:ワイルドカード構文が正しくないか、無効になっている

- **解決方法**:ソース変換オプションで使用しているワイルドカード構文を確認する

### <a name="error-message-df-src-002-container-container-name-is-required"></a>エラー メッセージ:DF-SRC-002: 'コンテナー' (コンテナー名) が必須です

- **現象**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗する

- **原因**:ストレージに存在しないコンテナーがデータセットに含まれている場合

- **解決方法**:データセットで参照しているコンテナーが存在することを確認する

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>エラー メッセージ:DF-UNI-001:PrimaryKeyValue の IntegerType 型と StringType 型に互換性がありません

- **現象**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗する

- **原因**:データベース シンクに間違った主キー型を挿入すると発生する

- **解決方法**:データ フローの主キーに使用している列を派生列でキャストし、ターゲット データベースのデータ型に合わせる

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>エラー メッセージ:DF-SYS-01: com.microsoft.sqlserver.jdbc.SQLServerException:ホスト xxxxx.database.windows.net ポート 1433 に TCP/IP 接続できませんでした。 エラー: "xxxx.database.windows.net。 接続プロパティを確認してください。 SQL Server のインスタンスがホストで実行されており、ポートで TCP/IP 接続を受け入れることを確認してください。 ポートへの TCP 接続がファイアウォールでブロックされていないことを確認してください。"

- **現象**:データベース ソースまたはシンクでデータをプレビューできず、パイプラインを実行できない

- **原因**:データベースがファイアウォールで保護されている

- **解決方法**:データベースに対してファイアウォール アクセスを開く

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>エラー メッセージ:DF-SYS-01: com.microsoft.sqlserver.jdbc.SQLServerException:データベースに 'xxxxxx' という名前のオブジェクトが既に存在します。

- **現象**:シンクでテーブルを作成できない

- **原因**:ソースまたはデータセットで定義されているものと同じ名前を持つテーブル名がターゲット データベースに既に存在する

- **解決方法**:これから作成するテーブルの名前を変更する

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>エラー メッセージ:DF-SYS-01: com.microsoft.sqlserver.jdbc.SQLServerException:文字列データまたはバイナリ データが切り捨てられます。 

- **現象**:SQL シンクにデータを書き込む場合、パイプラインの実行時にデータ フローが失敗し、切り捨てエラーが発生する可能性がある

- **原因**:データ フローのフィールドのマップ先となっている SQL データベースの列に、値を格納できるだけの十分な幅がなく、SQL ドライバーがこのエラーをスローした

- **解決方法**:派生列で ```left()``` を使用して文字列型の列のデータの長さを減らすか、["エラー行" のパターン](how-to-data-flow-error-rows.md)を実装する

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>エラー メッセージ:Spark 2.3 以降では、参照先の列が内部破損レコード列のみの場合に未加工の JSON ファイルや CSV ファイルからのクエリが許可されません。 

- **現象**:JSON ソースからの読み取りが失敗する

- **原因**:JSON ソースで 1 つのドキュメントが入れ子になった多数の行に分かれている場合に、その JSON ソースを対象として読み取りを実行すると、新しいドキュメントがどこから始まっており、前のドキュメントがどこで終わっているかを ADF (Spark 経由) が特定できません。

- **解決方法**:JSON データセットを使用しているソース変換で [JSON 設定] を展開し、[単一のドキュメント] をオンにします。

### <a name="error-message-duplicate-columns-found-in-join"></a>エラー メッセージ:結合の中に重複する列が見つかりました

- **現象**:結合変換により、左辺と右辺の両方から、重複する列名を含む列が生成されました

- **原因**:結合される予定のストリームに、共通の列名があります

- **解決方法**:結合後に Select 変換を追加し、入力と出力の両方に対して [Remove duplicate columns]\(重複する列の削除\) を選択します。


## <a name="general-troubleshooting-guidance"></a>一般的なトラブルシューティング ガイダンス

1. データセット接続の状態を確認します。 ソース/シンクの変換ごとに、使用している各データベースのリンクされたサービスにアクセスし、接続をテストします。
2. データ フロー デザイナーからファイルとテーブルの接続の状態を確認します。 デバッグをオンに切り替え、ソース変換のデータ プレビューをクリックし、データにアクセスできることを確認します。
3. データ プレビューに問題がなければ、パイプライン デザイナーに進み、パイプライン アクティビティにデータ フローを配置します。 エンドツーエンド テストとしてパイプラインをデバッグします。

## <a name="next-steps"></a>次の手順

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [ADF マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)
