---
title: 一般的なマッピング データ フローのエラーとメッセージ
description: Azure Data Factory でのマッピング データ フローに関する一般的なエラー コードとメッセージのトラブルシューティングの方法について説明します。
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: 58b608c7e848dba2ea0d0e56532e51dcc4fe5a7b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390406"
---
# <a name="common-error-codes-and-messages"></a>一般的なエラー コードとメッセージ 

この記事では、Azure Data Factory でのマッピング データ フローによって報告される一般的なエラー コードとメッセージ、およびそれに関連する原因と推奨事項を示します。

## <a name="error-code-df-executor-sourceinvalidpayload"></a>エラー コード:DF-Executor-SourceInvalidPayload
- **メッセージ**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗しました
- **原因**: ストレージに存在しないコンテナーがデータセットに含まれています。
- **推奨事項**: データセットで参照されているコンテナーが存在し、アクセス可能であることをご確認ください。

## <a name="error-code-df-executor-systeminvalidjson"></a>エラー コード:DF-Executor-SystemInvalidJson

- **メッセージ**:JSON 解析エラー、サポートされていないエンコードまたは複数行が存在します
- **原因**: JSON ファイルで、次のような問題が発生しているおそれがあります。サポートされていないエンコード、バイトの破損、または入れ子になった多数の行での単一ドキュメントとしての JSON ソースの使用。
- **推奨事項**: JSON ファイルのエンコードがサポートされていることをご確認ください。 JSON データセットを使用しているソース変換で **[JSON Settings]\(JSON 設定\)** を展開し、 **[Single Document]\(単一ドキュメント\)** をオンにします。
 
## <a name="error-code-df-executor-broadcasttimeout"></a>エラー コード:DF-Executor-BroadcastTimeout

- **メッセージ**:ブロードキャスト結合のタイムアウトエラーが発生しました。ブロードキャスト ストリームが、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成することを確認してください
- **原因**: ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト用に選択されたストリームは、この制限内にデータを生成するには大きすぎます。
- **推奨事項**: データ フロー変換の **[最適化]** タブで、結合、存在、参照をご確認ください。 ブロードキャストの既定のオプションは **[Auto]\(自動\)** です。 **[Auto]\(自動\)** が設定されている場合、または **[Fixed]\(固定\)** で左側または右側でブロードキャストするように手動で設定している場合は、より大きな Azure 統合ランタイム (IR) 構成を設定するか、ブロードキャストをオフにすることができます。 データ フローで最適なパフォーマンスを得るためには、 **[Auto]\(自動\)** を使用して Spark がブロードキャストできるよう許可し、メモリ最適化 Azure IR を使用できるようにすることをお勧めします。 
 
  デバッグ パイプライン実行からのデバッグ テスト実行でデータ フローを実行している場合、この条件がより頻繁に実行されることがあります。 これは、より高速のデバッグ エクスペリエンスを維持するために、Azure Data Factory によってブロードキャスト タイムアウトが 60 秒に調整されるためです。 タイムアウトは、トリガーされた実行の 300 秒のタイムアウトまで延長できます。 これを行うには、 **[Debug]\(デバッグ\)**  >  **[Use Activity Runtime]\(アクティビティ ランタイムを使用\)** オプションを使用して、データ フローの実行パイプライン アクティビティで定義されている Azure IR を使用できます。

- **メッセージ**: ブロードキャスト結合のタイムアウト エラー。この問題を回避するには、結合/存在/参照変換でブロードキャスト オプションの [オフ] を選択します。 パフォーマンスを向上させるために結合オプションをブロードキャストする場合は、ブロードキャスト ストリームで、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成できることを確認します。
- **原因**: ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト結合の場合、ブロードキャスト用に選択されたストリームは、この制限内にデータを生成するには大きすぎます。 ブロードキャスト結合が使用されていない場合、データフローによる既定のブロードキャストが同じ制限に達することがあります。
- **推奨事項**: ブロードキャスト オプションをオフにするか、処理に 60 秒を超える時間を要する可能性がある大規模なデータ ストリームのブロードキャストは避けてください。 小さいストリームを選択してブロードキャストします。 大きな Azure SQL データ ウェアハウス テーブルとソース ファイルは、通常は適切な選択ではありません。 ブロードキャスト結合が存在しない場合、エラーが発生したときはより大きなクラスターを使用します。

## <a name="error-code-df-executor-conversion"></a>エラー コード:DF-Executor-Conversion

- **メッセージ**:無効な文字が原因で、日付または時刻への変換に失敗しました
- **原因**: データの形式が正しくありません。
- **推奨事項**: 正しいデータ型を使用してください。

## <a name="error-code-df-executor-invalidcolumn"></a>エラー コード:DF-Executor-InvalidColumn
- **メッセージ**:クエリに列名を指定する必要があります。SQL 関数を使用している場合は別名を設定してください
- **原因**: 列名が指定されていません。
- **推奨事項**: min() や max() などの SQL 関数を使用している場合は、別名を設定します。

## <a name="error-code-df-executor-drivererror"></a>エラー コード:DF-Executor-DriverError
- **メッセージ**:INT96 は、ADF データフローではサポートされていない従来のタイムスタンプ型です。 列の型を最新の型にアップグレードすることを検討してください。
- **原因**: ドライバーエラーです。
- **推奨事項**: INT96 は、Azure Data Factory データ フローでサポートされていない従来のタイムスタンプ型です。 列の型を最新の型にアップグレードすることを検討してください。

## <a name="error-code-df-executor-blockcountexceedslimiterror"></a>エラー コード:DF-Executor-BlockCountExceedsLimitError
- **メッセージ**:コミット前のブロック数は、ブロックの上限である 100,000 個を超えることはできません。 BLOB 構成を確認してください。
- **原因**: BLOB でのコミットされていないブロックの最大数は 100,000 です。
- **推奨事項**: この問題の詳細については、Microsoft の製品チームにお問い合わせください。

## <a name="error-code-df-executor-partitiondirectoryerror"></a>エラー コード:DF-Executor-PartitionDirectoryError
- **メッセージ**: 指定されたソース パスに、複数のパーティション ディレクトリ (たとえば、&lt;ソース パス&gt;/<パーティション ルート ディレクトリ 1>/a=10/b=20、&lt;ソース パス&gt;/&lt;パーティション ルート ディレクトリ 2&gt;/c=10/d=30)、他のファイルを含むパーティション ディレクトリ、またはパーティション分割されていないディレクトリ (たとえば、&lt;ソース パス&gt;/&lt;パーティション ルート ディレクトリ 1&gt;/a=10/b=20、&lt;ソース パス&gt;/Directory 2/file1) のいずれかが含まれています。ソース パスからパーティション ルート ディレクトリを削除して、別個のソース変換を利用して読み取ってください。
- **原因**: ソース パスに複数のパーティション ディレクトリ、別のファイルを含むパーティション ディレクトリ、またはパーティション分割されていないディレクトリのいずれかが含まれています。
- **推奨事項**: パーティション ルート ディレクトリをソース パスから削除し、別個のソース変換を使用して読み取ってください。

## <a name="error-code-df-executor-invalidtype"></a>エラー コード:DF-Executor-InvalidType
- **メッセージ**:パラメーターの型が、渡された値の型と一致していることを確認してください。 パイプラインからの float パラメーターの引き渡しは現在、サポートされていません。
- **原因**: 宣言された型と実際のパラメーター値の間でデータ型に互換性がありません。
- **推奨事項**: データ フローに渡されたパラメーター値が、宣言された型と一致することをご確認ください。

## <a name="error-code-df-executor-parseerror"></a>エラー コード:DF-Executor-ParseError
- **メッセージ**: 式を解析できません。
- **原因**: 書式が正しくないため、式によって解析エラーが生成されました。
- **推奨事項**: 式の書式をご確認ください。

## <a name="error-code-df-executor-systemimplicitcartesian"></a>エラー コード:DF-Executor-SystemImplicitCartesian
- **メッセージ**:INNER join では暗黙的なデカルト積はサポートされていません。代わりに CROSS JOIN を使用してください。 結合で使用される列は、行に対して一意のキーを作成する必要があります。
- **原因**: 論理プラン間の INNER 結合では暗黙的なデカルト積はサポートされていません。 結合で列を使用している場合は、一意のキーを作成します。
- **推奨事項**:非等値ベースの結合では、CROSS JOIN を使用します。

## <a name="error-code-getcommand-outputasync-failed"></a>エラー コード:GetCommand OutputAsync に失敗しました
- **メッセージ**:データ フロー デバッグおよびデータ プレビューの実行中:GetCommand OutputAsync が次の理由で失敗しました。
- **原因**: このエラーはバックエンド サービス エラーです。 
- **推奨事項**: 操作を再試行し、デバッグ セッションを再起動してみてください。 再試行して再起動しても問題が解決しない場合は、カスタマー サポートにお問い合わせください。 

## <a name="error-code-df-executor-outofmemoryerror"></a>エラー コード:DF-Executor-OutOfMemoryError
 
- **メッセージ**:実行中に、クラスターにメモリ不足の問題が発生しました。コア数がより大きく、メモリが最適化されたコンピューティングの種類で統合ランタイムを使用して、もう一度お試しください
- **原因**:クラスターのメモリがメモリ不足しています。
- **推奨事項**: デバッグ クラスターは、開発のためのものではありません。 データ サンプリングおよび適切なコンピューティングの種類とサイズを使用して、ペイロードを実行します。 パフォーマンスに関するヒントについては、[マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)に関する記事を参照してください。

## <a name="error-code-df-executor-illegalargument"></a>エラー コード:DF-Executor-illegalArgument

- **メッセージ**:リンクされたサービスのアクセス キーが正しいことを確認してください
- **原因**: アカウント名またはアクセス キーが正しくありません。
- **推奨事項**:リンク サービスで指定されたアカウント名またはアクセス キーが正しいことを確認してください。 

## <a name="error-code-df-executor-columnunavailable"></a>エラー コード:DF-Executor-ColumnUnavailable
- **メッセージ**: 式に使用されている列名が利用できないか、または無効です。
- **原因**: 式に使用されている列名が無効か、または利用できません。
- **推奨事項**: 式に使用されている列名をご確認ください。

 ## <a name="error-code-df-executor-outofdiskspaceerror"></a>エラー コード:DF-Executor-OutOfDiskSpaceError
- **メッセージ**: 内部サーバー エラー
- **原因**: クラスターのディスク領域が不足しています。
- **推奨事項**: パイプラインをもう一度お試しください。 それを行っても問題が解決しない場合は、カスタマー サポートにお問い合わせください。


 ## <a name="error-code-df-executor-storeisnotdefined"></a>エラー コード:DF-Executor-StoreIsNotDefined
- **メッセージ**: ストアの構成が定義されていません。 このエラーは、パイプラインでの無効なパラメーター割り当てが原因である可能性があります。
- **原因**: 無効なストア構成が指定されています。
- **推奨事項**: パイプラインでのパラメーター値の割り当てを確認してください。 パラメーター式に無効な文字が含まれている可能性があります。


## <a name="error-code-4502"></a>エラー コード:4502
- **メッセージ**: Integration Runtime での調整のために障害を発生させている、多くの同時 MappingDataflow 実行があります。
- **原因**: 統合ランタイムで、大量のデータ フロー アクティビティの実行が同時に発生しています。 詳細については、[Azure Data Factory の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)に関する記事を参照してください。
- **推奨事項**: 多くのデータ フロー アクティビティを並列に実行する場合は、複数の統合ランタイムに分散させてください。

## <a name="error-code-4510"></a>エラー コード: 4510
- **メッセージ**: 実行中に予期しないエラーが発生しました。 
- **原因**: デバッグ クラスターはジョブ クラスターとは異なる方法で動作します。そのため、デバッグが過剰に実行された場合、時間の経過に伴いクラスターが消耗し、メモリの問題が発生したり、突然再起動されたりする可能性があります。
- **推奨事項**: デバッグ クラスターを再起動します。 デバッグ セッション中に複数のデータフローを実行している場合は、代わりにアクティビティの実行を使用します。これは、アクティビティ レベルの実行では、メインのデバッグ クラスターに負担をかけずに個別のセッションが作成されるためです。

## <a name="error-code-invalidtemplate"></a>エラー コード:InvalidTemplate
- **メッセージ**: パイプライン式を評価できません。
- **原因**: データ フロー アクティビティで渡されたパイプライン式が、構文エラーのため正しく処理されていません。
- **推奨事項**: アクティビティ監視でアクティビティを確認して、式を検証します。

## <a name="error-code-2011"></a>エラー コード:2011
- **メッセージ**: アクティビティは Azure Integration Runtime で実行されていましたが、セルフホステッド統合ランタイムを介して接続されたデータ ストアまたはコンピューティングの資格情報の暗号化を解除できませんでした。 このアクティビティに関連付けられているリンク サービスの構成を確認し、適切な統合ランタイムの種類を使用していることを確認してください。
- **原因**: セルフホステッド統合ランタイムでのリンク サービスは、データ フローではサポートされていません。
- **推奨事項**: マネージド仮想ネットワーク統合ランタイムで実行するように、データ フローを構成します。

## <a name="error-code-df-xml-invalidvalidationmode"></a>エラー コード: DF-Xml-InvalidValidationMode
- **メッセージ**: 無効な xml 検証モードが指定されています。
- **原因**: 無効な XML 検証モードが指定されています。
- **推奨事項**: パラメーター値を確認し、適切な検証モードを指定します。

## <a name="error-code-df-xml-invaliddatafield"></a>エラー コード: DF-Xml-InvalidDataField
- **メッセージ**: 破損レコードのフィールドは、文字列型および null 許容型である必要があります。
- **原因**: XML ソースに、列の無効なデータ型が指定されています `\"_corrupt_record\"`。
- **推奨事項**: XML ソースの列 `\"_corrupt_record\"` に文字列データ型と Null 許容が含まれていることを確認してください。

## <a name="error-code-df-xml-malformedfile"></a>エラー コード: DF-Xml-MalformedFile
- **メッセージ**: FAILFAST モードのパスの形式が正しくない XML です。
- **原因**: FAILFAST モードのパスの形式が正しくない形式に誤りがある XML です。
- **推奨事項**: XML ファイルの内容を適切な形式に更新します。

## <a name="error-code-df-xml-invalidreferenceresource"></a>エラー コード: DF-Xml-InvalidReferenceResource
- **メッセージ**: XML データ ファイル内の参照リソースを解決できません。
- **原因**: XML データ ファイル内の参照リソースを解決できません。
- **推奨事項**: XML データ ファイル内の参照リソースを確認する必要があります。

## <a name="error-code-df-xml-invalidschema"></a>エラー コード: DF-Xml-InvalidSchema
- **メッセージ**: スキーマの検証に失敗しました。
- **原因**: XML ソースに無効なスキーマが指定されています。
- **推奨事項**: XML ソースのスキーマ設定を調べて、ソース データのサブセット スキーマであることを確認します。

## <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>エラー コード: DF-Xml-UnsupportedExternalReferenceResource
- **メッセージ**: xml データ ファイル内の外部参照リソースはサポートされていません。
- **原因**: XML データ ファイル内の外部参照リソースはサポートされていません。
- **推奨事項**: 外部参照リソースが現在サポートされていない場合は、XML ファイルの内容を更新します。

## <a name="error-code-df-gen2-invalidaccountconfiguration"></a>エラー コード: DF-GEN2-InvalidAccountConfiguration
- **メッセージ**: アカウント キーまたは tenant/spnId/spnCredential/spnCredentialType または miServiceUri/miServiceToken のいずれかを指定する必要があります。
- **原因**: ADLS Gen2 のリンク サービスに無効な資格情報が指定されています。
- **推奨事項**: 適切な資格情報を構成するように、ADLS Gen2 のリンク サービスを更新します。

## <a name="error-code-df-gen2-invalidauthconfiguration"></a>エラー コード: DF-GEN2-InvalidAuthConfiguration
- **メッセージ**: 3 つの認証方法 (Key、ServicePrincipal、MI) のうち 1 つだけを指定できます。
- **原因**: ADLS gen2 のリンク サービスに無効な認証方法が指定されています。
- **推奨事項**: ADLS Gen2 のリンク サービスを更新して、キー、ServicePrincipal、および MI の 3 つの認証方法のいずれかを設定します。

## <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>エラー コード: DF-GEN2-InvalidServicePrincipalCredentialType
- **メッセージ**: サービス プリンシパルの資格情報の種類が無効です。
- **原因**: サービス プリンシパルの資格情報の種類が無効です。
- **推奨事項**: ADLS Gen2 のリンク サービスを更新して、適切にサービス プリンシパルの資格情報の種類を設定してください。

## <a name="error-code-df-blob-invalidaccountconfiguration"></a>エラー コード: DF-Blob-InvalidAccountConfiguration
- **メッセージ**: アカウント キーまたは sas_token のいずれかを指定する必要があります。
- **原因**: Azure Blob のリンク サービスに無効な資格情報が指定されています。
- **推奨事項**: Azure Blob のリンク サービスには、アカウント キーまたは SAS トークンを使用します。

## <a name="error-code-df-blob-invalidauthconfiguration"></a>エラー コード: DF-InvalidAuthConfiguration
- **メッセージ**: 2 つの認証方法 (Key、SAS) のうち 1 つだけを指定できます。
- **原因**: リンク サービスに無効な認証メソッドが指定されています。
- **推奨事項**: Azure Blob のリンク サービスには、キーまたは SAS 認証を使用します。

## <a name="error-code-df-cosmos-partitionkeymissed"></a>エラー コード: DF-Cosmos-PartitionKeyMissed
- **メッセージ**: 更新操作と削除操作には、パーティション キーのパスを指定する必要があります。
- **原因**: Azure Cosmos DB シンクで、パーティションキーのパスが見つかりません。
- **推奨事項**: Azure Cosmos DB シンクの設定で提供パーティション キーを使用します。

## <a name="error-code-df-cosmos-invalidpartitionkey"></a>エラー コード: DF-Cosmos-InvalidPartitionKey
- **メッセージ**: 更新操作と削除操作では、パーティション キーのパスを空にすることはできません。
- **原因**: 更新操作と削除操作では、パーティション キーのパスを空にすることはできません。
- **推奨事項**: Azure Cosmos DB シンクの設定で提供パーティション キーを使用します。

- **メッセージ**: 削除操作および更新操作のためのパーティション キーがシンクでマップされていません。
- **原因**: 無効なパーティションキーが指定されています。
- **推奨事項**: Cosmos DB シンクの設定で、コンテナーのパーティション キーと同じ適切なパーティション キーを使用します。

## <a name="error-code-df-cosmos-idpropertymissed"></a>エラー コード: DF-Cosmos-IdPropertyMissed
- **メッセージ**: 削除操作と更新操作には、'id' プロパティをマップする必要があります。
- **原因**: 更新操作と削除操作で `id` プロパティが不明です。
- **推奨事項**: Cosmos DB シンクの設定で、入力データに `id` 列が含まれていることを確認します。 含まれていない場合は、**変換の選択または派生** を使用して、シンクの前にこの列を生成します。

## <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>エラー コード: DF-Cosmos-InvalidPartitionKeyContent
- **メッセージ**: パーティション キーは/で始まっている必要があります。
- **原因**: 無効なパーティションキーが指定されています。
- **推奨事項**: Cosmos DB シンクの設定で、パーティション キーが `/` で始まっていることを確認します (例: `/movieId`)。

## <a name="error-code-df-cosmos-invalidconnectionmode"></a>エラー コード: DF-Cosmos-InvalidConnectionMode
- **メッセージ**: 接続モードが無効です。
- **原因**: 無効な接続モードが指定されています。
- **推奨事項**: Cosmos DB の設定で、サポートされているモードが **Gateway** と **DirectHttps** であることを確認します。

## <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>エラー コード: DF-Cosmos-InvalidAccountConfiguration
- **メッセージ**: accountName または accountEndpoint のいずれかを指定する必要があります。
- **原因**: 無効なアカウント情報が指定されています。
- **推奨事項**: Cosmos DB のリンク サービスで、アカウント名またはアカウントのエンドポイントを指定します。

## <a name="error-code-df-github-writenotsupported"></a>エラー コード: DF-Github-WriteNotSupported
- **メッセージ**: Github ストアで書き込みが許可されていません。
- **原因**: GitHub ストアは読み取り専用です。
- **推奨事項**: ストア エンティティ定義は他の場所にあります。
  
## <a name="error-code-df-pgsql-invalidcredential"></a>エラー コード: DF-PGSQL-InvalidCredential
- **メッセージ**: ユーザー/パスワードを指定する必要があります。
- **原因**: ユーザー/パスワードが見つかりません。
- **推奨事項**: 関連する PostgreSQL リンク サービスに適切な資格情報が設定されていることを確認します。

## <a name="error-code-df-snowflake-invalidstageconfiguration-only-blob-storage-can-be-used-as-stage"></a>エラー コード: DF-InvalidStageConfiguration (BLOB ストレージのみステージとして使用可能)
- **メッセージ**: Snowflake 読み取り/書き込み操作では、BLOB ストレージ タイプのみをステージとして使用できます。
- **原因**: Snowflake に無効なステージング構成が用意されています。
- **推奨事項**: Snowflake ステージング設定を更新して、Azure Blob のリンク サービスのみが使用されるようにします。

- **メッセージ**: Snowflake のステージ プロパティは、Azure BLOB + SAS 認証で指定する必要があります。
- **原因**: Snowflake に無効なステージング構成が用意されています。
- **推奨事項**: Azure BLOB + SAS 認証のみが Snowflake ステージング設定で指定されていることを確認します。

## <a name="error-code-df-snowflake-invaliddatatype"></a>エラー コード: DF-Snowflake-InvalidDataType
- **メッセージ**: spark 型は Snowflake ではサポートされていません。
- **原因**: Snowflake に無効なデータ型が提供されています。
- **推奨事項**: Snowflake シンクを適用する前に、派生変換を使用して、入力データの関連列を文字列型に更新してください。

## <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>エラー コード: DF-Hive-InvalidBlobStagingConfiguration
- **メッセージ**: BLOB ストレージのステージング プロパティを指定する必要があります。
- **原因**: Hive に無効なステージング構成が用意されています。
- **推奨事項**: ステージングとして使用される関連する Blob のリンク サービスで、アカウントキー、アカウント名、コンテナーが適切に設定されているかどうかを確認してください。

## <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>エラー コード: DF-Hive-InvalidGen2StagingConfiguration
- **メッセージ**: ADLS Gen2 ストレージのステージングは、サービス プリンシパルのキー資格情報のみをサポートしています。
- **原因**: Hive に無効なステージング構成が用意されています。
- **推奨事項**: ステージングとして使用される関連 ADLS Gen2 のリンク サービスを更新してください。 現時点では、サービス プリンシパル キー資格情報のみがサポートされています。

- **メッセージ**: ADLS Gen2 ストレージのステージング プロパティを指定する必要があります。 キーまたは tenant/spnId/spnKey または miServiceUri/miServiceToken のいずれかが必要です。
- **原因**: Hive に無効なステージング構成が用意されています。
- **推奨事項**: 関連する ADLS Gen2 のリンク サービスを、Hive でステージングとして使用される適切な資格情報で更新します。

## <a name="error-code-df-hive-invaliddatatype"></a>エラー コード: DF-Hive-InvalidDataType
- **メッセージ**: サポートされていない列です。
- **原因**: サポートされていない列が指定されています。
- **推奨事項**: 入力データの列を、Hive でサポートされているデータ型と一致するように更新します。

## <a name="error-code-df-hive-invalidstoragetype"></a>エラー コード: DF-Hive-InvalidStorageType
- **メッセージ**: ストレージの種類には、blob または gen2 を指定できます。
- **原因**: サポートされているのは、Azure Blob または ADLS Gen2 ストレージの種類のみです。
- **推奨事項**: Azure Blob または ADLS Gen2 から適切にストレージの種類を選択します。

## <a name="error-code-df-delimited-invalidconfiguration"></a>エラー コード: DF-Delimited-InvalidConfiguration
- **メッセージ**: 空の行またはカスタム ヘッダーのいずれかを指定する必要があります。
- **原因**: 無効な区切られた構成が指定されています。
- **推奨事項**: CSV 設定を更新して、空の行またはカスタム ヘッダーのいずれかを指定してください。

## <a name="error-code-df-delimited-columndelimitermissed"></a>エラー コード: DF-Delimited-ColumnDelimiterMissed
- **メッセージ**: 解析には列区切り記号が必要です。
- **原因**: 列区切り記号が見つかりません。
- **推奨事項**: CSV 設定で、解析に必要な列区切り記号があることを確認します。 

## <a name="error-code-df-mssql-invalidcredential"></a>エラー コード: DF-MSSQL-InvalidCredential
- **メッセージ**: user/pwd または tenant/spnId/spnKey または miServiceUri/miServiceToken のいずれかを指定する必要があります。
- **原因**: MSSQL のリンク サービスに無効な資格情報が指定されています。
- **推奨事項**: 関連する MSSQL リンク サービスを適切な資格情報で更新し、 **user/pwd** または **tenant/spnId/spnKey** または **miServiceUri/miServiceToken** のいずれかを指定する必要があります。

## <a name="error-code-df-mssql-invaliddatatype"></a>エラー コード: DF-MSSQL-InvalidDataType
- **メッセージ**: サポートされていないフィールドです。
- **原因**: サポートされていないフィールドが指定されています。
- **推奨事項**: 入力データの列を、MSSQL でサポートされているデータ型と一致するように変更します。

## <a name="error-code-df-mssql-invalidauthconfiguration"></a>エラー コード: DF-MSSQL-InvalidAuthConfiguration
- **メッセージ**: 3 つの認証方法 (Key、ServicePrincipal、MI) のうち 1 つだけを指定できます。
- **原因**: MSSQL リンク サービスに無効な認証メソッドが指定されています。
- **推奨事項**: 関連する MSSQL リンク サービスでは、3 つの認証方法 (Key、ServicePrincipal、MI) のうち 1 つだけを指定できます。

## <a name="error-code-df-mssql-invalidcloudtype"></a>エラー コード: DF-MSSQL-InvalidCloudType
- **メッセージ**: クラウドの種類が無効です。
- **原因**: 無効なクラウドの種類が指定されています。
- **推奨事項**: 関連する MSSQL リンク サービスで、クラウドの種類を確認します。

## <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>エラー コード: DF-SQLDW-InvalidBlobStagingConfiguration
- **メッセージ**: BLOB ストレージのステージング プロパティを指定する必要があります。
- **原因**: 無効な blob ストレージのステージング設定が指定されています
- **推奨事項**: ステージングに使用する Blob リンク サービスに正しいプロパティがあるかどうかを確認してください。

## <a name="error-code-df-sqldw-invalidstoragetype"></a>エラー コード: DF-SQLDW-InvalidStorageType
- **メッセージ**: ストレージの種類には、blob または gen2 を指定できます。
- **原因**: ステージング用に無効なストレージの種類が指定されています。
- **推奨事項**: ステージングに使用されるリンク サービスのストレージの種類を確認し、Blob または Gen2 であることを確認します。

## <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>エラー コード: DF-SQLDW-InvalidGen2StagingConfiguration
- **メッセージ**: ADLS Gen2 ストレージのステージングは、サービス プリンシパルのキー資格情報のみをサポートしています。
- **原因**: ADLS gen2 ストレージ ステージングに無効な資格情報が指定されています。
- **推奨事項**: ステージングに使用する Gen2 のリンク サービスのサービス プリンシパル キー資格情報を使用します。
 

## <a name="error-code-df-sqldw-invalidconfiguration"></a>エラー コード: DF-SQLDW-InvalidConfiguration
- **メッセージ**: ADLS Gen2 ストレージのステージング プロパティを指定する必要があります。 キーまたは tenant/spnId/spnCredential/spnCredentialType または miServiceUri/miServiceToken のいずれかが必要です。
- **原因**: 無効な ADLS Gen2 ステージング プロパティが指定されています。
- **推奨事項**: ADLS Gen2 ストレージ ステージング設定を更新して、 **キー** または **tenant/spnId/spnCredential/spnCredentialType** または **miServiceUri/miServiceToken** のいずれかを設定してください。

## <a name="error-code-df-delta-invalidconfiguration"></a>エラー コード: DF-DELTA-InvalidConfiguration
- **メッセージ**: タイムスタンプとバージョンを同時に設定することはできません。
- **原因**: タイムスタンプとバージョンを同時に設定することはできません。
- **推奨事項**: 差分設定でタイムスタンプまたはバージョンを設定します。

## <a name="error-code-df-delta-keycolumnmissed"></a>エラー コード: DF-DELTA-KeyColumnMissed
- **メッセージ**: 挿入不可能な操作には、キー列を指定する必要があります。
- **原因**: 挿入不可能な操作に対してキー列が見つかりません。
- **推奨**: デルタ シンクのキー列を指定して、挿入不可能な操作を行うようにします。

## <a name="error-code-df-delta-invalidtableoperationsettings"></a>エラー コード: DF-DELTA-InvalidTableOperationSettings
- **メッセージ**: 再作成オプションと切り詰めオプションの両方を指定することはできません。
- **原因**: 再作成オプションと切り詰めオプションの両方を同時に指定することはできません。
- **推奨事項**: 差分設定を更新して、再作成または切り捨て操作を行います。

## <a name="error-code-df-excel-worksheetconfigmissed"></a>エラー コード: DF-Excel-WorksheetConfigMissed
- **メッセージ**: Excel シートの名前またはインデックスが必要です。
- **原因**: 無効な Excel ワークシート構成が指定されています。
- **推奨事項**: パラメーター値を確認し、Excel データを読み取るシート名またはインデックスを指定してください。

## <a name="error-code-df-excel-invalidworksheetconfiguration"></a>エラー コード: DF-Excel-InvalidWorksheetConfiguration
- **メッセージ**: Excel シートの名前とインデックスが同時に存在することはできません。
- **原因**: Excel シートの名前とインデックスが同時に提供されます。
- **推奨事項**: パラメーター値を確認し、Excel データを読み取るシート名またはインデックスを指定してください。

## <a name="error-code-df-excel-invalidrange"></a>エラー コード: DF-Excel-InvalidRange
- **メッセージ**: 無効な範囲が指定されています。
- **原因**:無効な範囲が指定されています。
- **推奨事項**: パラメーター値を確認し、 [「Azure Data Factory での Excel 形式」の「データセットのプロパティ」](./format-excel.md#dataset-properties)に従って有効な範囲を指定してください。

## <a name="error-code-df-excel-worksheetnotexist"></a>エラー コード: DF-Excel-WorksheetNotExist
- **メッセージ**: Excel ワークシートが存在しません。
- **原因**: 無効なワークシート名またはインデックスが指定されています。
- **推奨事項**: パラメーター値を確認し、Excel データを読み取る有効なシート名またはインデックスを指定してください。

## <a name="error-code-df-excel-differentschemanotsupport"></a>エラー コード: DF-Excel-DifferentSchemaNotSupport
- **メッセージ**: スキーマが異なる Excel ファイルの読み取りは、現時点ではサポートされていません。
- **原因**: スキーマが異なる Excel ファイルの読み取りは、現時点ではサポートされていません。
- **推奨事項**: この問題を解決するには、次のいずれかのオプションを適用してください。
    - **ForEach** +  **データ フロー** アクティビティを使用して、Excel ワークシートを 1 つずつ読み取ります。 
    - データを読み取る前に、各ワークシート スキーマを更新して、同じ列を手動で作成します。

## <a name="error-code-df-excel-invaliddatatype"></a>エラー コード: DF-Excel-InvalidDataType
- **メッセージ**: データ型はサポートされていません。
- **原因**: このデータ型はサポートされていません。
- **推奨**: 関連する入力データ列のデータ型を **' string '** に変更してください。

## <a name="error-code-df-excel-invalidfile"></a>エラー コード: DF-Excel-InvalidFile
- **メッセージ**: 無効な Excel ファイルが指定されていますが、.xlsx と .xls のみがサポートされています。
- **原因**: 無効な Excel ファイルが指定されています。
- **推奨事項**: `.xls` データを読み取る前に、ワイルドカードを使用してフィルター処理し、`.xlsx` Excel ファイルを取得します。

## <a name="error-code-df-executor-outofmemorysparkbroadcasterror"></a>エラー コード: DF-Executor-OutOfMemorySparkBroadcastError
- **メッセージ**: 左/右オプションを使用して明示的にブロードキャストされたデータセットは、ノードのメモリに収まらないほど小さくする必要があります。 この問題を回避したり、より高いメモリを持つ統合ランタイムを使用したりする場合は、結合/存在/参照変換のブロードキャスト オプションを「オフ」に選択できます。
- **原因**: ブロードキャストされたテーブルのサイズがノード メモリの制限を超えています。
- **推奨事項**: ブロードキャストの左/右オプションは、ノードのメモリに収めることができる小さいデータセットのサイズに対してのみ使用してください。そのため、ノード サイズを適切に構成するか、[ブロードキャスト] オプションをオフにしてください。

## <a name="error-code-df-mssql-invalidfirewallsetting"></a>エラー コード: DF-MSSQL-InvalidFirewallSetting
- **メッセージ**: ホストに TCP/IP 接続できませんでした。 SQL Server のインスタンスがホストで実行されており、ポートで TCP/IP 接続を受け入れることを確認してください。 ポートへの TCP 接続がファイアウォールでブロックされていないことを確認してください。
- **原因**: SQL データベースのファイアウォール設定によって、データ フローへのアクセスがブロックされています。
- **推奨事項**: SQL データベースのファイアウォール設定を確認し、Azure のサービスとリソースにこのサーバーへのアクセスを許可してください。

## <a name="error-code-df-executor-acquirestoragememoryfailed"></a>エラー コード: DF-Executor-AcquireStorageMemoryFailed
- **メッセージ**: ストレージ メモリへのアンロール メモリの転送に失敗しました。 クラスターの実行中にメモリが不足しました。 コア数が多い統合ランタイム、またはメモリが最適化されたコンピューティングの種類を使用して、もう一度やり直してください。
- **原因**: クラスターのメモリが不足しています。
- **推奨**: コア数が多い統合ランタイム、またはメモリが最適化されたコンピューティングの種類を使用して、もう一度やり直してください。

## <a name="error-code-df-cosmos-deletedatafailed"></a>エラー コード: DF-Cosmos-DeleteDataFailed
- **メッセージ**: 3 回再試行した後に cosmos からデータを削除できませんでした。
- **原因**: Cosmos コレクションのスループットが小さいためにスロットリングが発生したか、行データが Cosmos に存在しません。
- **推奨事項**: この問題を解決するには、次の操作を行ってください。
    - エラーが 404 の場合は、関連する行データが Cosmos コレクションに存在することを確認します。 
    - エラーがスロットリングである場合は、Cosmos collection スループットを増やすか、自動スケールに設定してください。
    - エラーが要求のタイムアウトの場合は、Cosmos シンクの "バッチ サイズ" を 1000 などの小さい値に設定してください。

## <a name="error-code-df-sqldw-errorrowsfound"></a>エラー コード: DF-SQLDW-ErrorRowsFound
- **原因**: Azure Synapse Analytics シンクへの書き込み時に、エラーまたは無効な行が見つかりました。
- **推奨事項**: エラー行が構成されている場合は、拒否されたデータの保存場所にあることを確認してください。

## <a name="error-code-df-sqldw-exporterrorrowfailed"></a>エラー コード: DF-SQLDW-ExportErrorRowFailed
- **メッセージ**: エラー行をストレージに書き込んでいるときに例外が発生しました。
- **原因**: エラー行をストレージに書き込んでいるときに例外が発生しました。
- **推奨事項**: 拒否されたデータのリンク サービスの構成を確認してください。

## <a name="error-code-df-executor-fieldnotexist"></a>エラー コード: DF-Executor-FieldNotExist
- **メッセージ**: struct のフィールドが存在しません。
- **原因**: 式で使用されているフィールド名が無効か、使用できません。
- **推奨事項**: 式に使用されている列名をご確認ください。

## <a name="error-code-df-xml-invalidelement"></a>エラー コード: DF-Xml-InvalidElement
- **メッセージ**: XML 要素には、変換できないサブ要素または属性があります。
- **原因**: XML 要素には、変換できないサブ要素または属性があります。
- **推奨**: XML 要素に右のサブ要素または属性が含まれるように、XML ファイルを更新します。

## <a name="error-code-df-gen2-invalidcloudtype"></a>エラー コード: DF-GEN2-InvalidCloudType
- **メッセージ**: クラウドの種類が無効です。
- **原因**: 無効なクラウドの種類が指定されています。
- **推奨事項**: 関連する ADLS Gen2 リンク サービスで、クラウドの種類を確認します。

## <a name="error-code-df-blob-invalidcloudtype"></a>エラー コード: DF-Blob-InvalidCloudType
- **メッセージ**: クラウドの種類が無効です。
- **原因**: 無効なクラウドの種類が指定されています。
- **推奨事項**: 関連する Azure Blob リンク サービスで、クラウドの種類を確認します。

## <a name="error-code-df-cosmos-failtoresetthroughput"></a>エラー コード: DF-Cosmos-FailToResetThroughput
- **メッセージ**: 別のスケール操作が進行中のため、Cosmos DB スループットのスケール操作を実行できません。しばらくしてから、もう一度やり直してください。
- **原因**: 別のスケール操作が進行中のため、Cosmos DB のスループットスケール操作を実行できません。
- **推奨事項**: Cosmos アカウントにログインし、コンテナーのスループットを自動スケールに手動で変更するか、データ フローの後にカスタムアクティビティを追加してスループットをリセットしてください。

## <a name="error-code-df-executor-invalidpath"></a>エラー コード: DF-Executor-InvalidPath
- **メッセージ**: パスはどのファイルにも解決されません。 ファイル/フォルダーが存在し、非表示になっていないことを確認してください。
- **原因**: 無効なファイルまたはフォルダーのパスが指定されています。このパスが見つからないか、アクセスできません。
- **推奨事項**: ファイルまたはフォルダーのパスを確認し、それが存在し、ストレージ内でアクセスできることを確認してください。

## <a name="error-code-df-executor-invalidpartitionfilenames"></a>エラー コード: DF-Executor-InvalidPartitionFileNames
- **メッセージ**: ファイル名オプションがパーティションごとに設定されている間は、ファイル名に空の値を指定することはできません。
- **原因**: 無効なパーティション ファイル名が指定されています。
- **推奨事項**: シンク設定でファイル名の正しい値を確認してください。

## <a name="error-code-df-executor-invalidoutputcolumns"></a>エラー コード: DF-Executor-InvalidOutputColumns
- **Message**: 結果には 0 個の出力列があります。 少なくとも 1 つの列がマップされていることを確認してください。
- **下人**: マップされている列がありません。
- **推奨事項**: シンク スキーマを確認して、少なくとも 1 つの列がマップされていることを確認してください。

## <a name="error-code-df-executor-invalidinputcolumns"></a>エラー コード: DF-Executor-InvalidInputColumns
- **メッセージ**: ソース データのスキーマにソース構成の列が見つかりません。
- **原因**: ソースに無効な列が指定されています。
- **推奨事項**: ソース構成の列を確認し、ソース データのスキーマのサブセットであることを確認します。

## <a name="error-code-df-adobeintegration-invalidmaptofilter"></a>エラー コード: DF-AdobeIntegration-InvalidMapToFilter
- **メッセージ**: カスタム リソースでは、キー/Id を 1 つだけフィルターにマップできます。
- **原因**: 無効な構成が指定されています。
- **推奨事項**: AdobeIntegration の設定で、カスタム リソースがフィルターにマップされたキー/ID を 1 つだけ持つことを確認します。

## <a name="error-code-df-adobeintegration-invalidpartitionconfiguration"></a>エラー コード: DF-AdobeIntegration-InvalidPartitionConfiguration
- **メッセージ**: 単一パーティションのみサポートされています。 パーティション スキーマには、RoundRobin または Hash を指定できます。
- **原因**: 無効なパーティション構成が指定されています。
- **推奨事項**: AdobeIntegration の設定で、1 つのパーティションのみが設定されていて、パーティション スキーマが、RoundRobin またはハッシュを丸めている可能性があることを確認します。

## <a name="error-code-df-adobeintegration-keycolumnmissed"></a>エラー コード: DF-AdobeIntegration-KeyColumnMissed
- **メッセージ**: 挿入不可能な操作には、キーを指定する必要があります。
- **原因**: キー列が見つかりません。
- **推奨事項**: AdobeIntegration 設定を更新して、挿入不可能な操作に対してキー列が指定されていることを確認します。

## <a name="error-code-df-adobeintegration-invalidpartitiontype"></a>エラー コード: DF-AdobeIntegration-InvalidPartitionType
- **メッセージ**: パーティションの種類は roundRobin である必要があります。
- **原因**: 無効なパーティションの種類が指定されています。
- **推奨事項**: AdobeIntegration の設定を更新して、パーティションの種類が RoundRobin になるようにしてください。

## <a name="error-code-df-adobeintegration-invalidprivacyregulation"></a>エラー コード: DF-AdobeIntegration-InvalidPrivacyRegulation
- **メッセージ**: 現在サポートされているプライバシー規則は "GDPR" のみです。
- **原因**: 無効なプライバシー構成が指定されています。
- **推奨事項**: AdobeIntegration 設定を更新してください。プライバシー ' GDPR ' のみがサポートされています。

## <a name="error-code-df-executor-remoterpcclientdisassociated"></a>エラー コード: DF-Executor-RemoteRPCClientDisassociated
- **メッセージ**: リモート RPC クライアントの関連付けが解除されました。 Likely due to containers exceeding thresholds, or network issues. (リモート RPC クライアントの関連付けが解除されました。コンテナーがしきい値を超えたか、ネットワークの問題が、原因である可能性があります)
- **原因**: 一時的なネットワークの問題が発生したか、Spark クラスターの 1 つのノードでメモリが不足しているため、データ フロー アクティビティの実行に失敗しました。
- **推奨事項**: この問題を解決するには、次のオプションを使用してください。
  - オプション-1: 強力なクラスター (ドライブとエグゼキューターの両方のノードに、ビッグ データを処理するために十分なメモリがある) を使用し、[計算の種類] を [メモリ最適化] に設定してデータ フロー パイプラインを実行します。 次の図にその設定を示します。
        
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-compute-type.png" alt-text="[計算の種類] の構成を示すスクリーンショット。":::   

  - オプション-2: より大きなクラスター サイズ (48 コアなど) を使用して、データ フロー パイプラインを実行します。 クラスターのサイズの詳細については、[クラスターのサイズ](./concepts-integration-runtime-performance.md#cluster-size)に関するページを参照してください。
  
  - オプション-3: 入力データのパーティションを再分割します。 データ フローの Spark クラスターで実行されているタスクの場合、1 つのパーティションが 1 つのタスクであり、1 つのノードで実行されます。 1 つのパーティション内のデータが大きすぎる場合、ノードで実行されている関連タスクで、ノード自体よりも多くのメモリを消費することが必要になります。これにより、障害が発生します。 そのため、パーティションの再分割を使用してデータ スキューを回避することで、各パーティションのデータ サイズを平均化して、メモリ消費量が過剰にならないようにすることができます。
    
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-partition.png" alt-text="パーティションの構成を示すスクリーンショット。":::

    > [!NOTE]
    >  入力データのデータのサイズまたはパーティション数を評価し、[最適化] で適切なパーティション数を設定する必要があります。 たとえば、データ フロー パイプラインの実行で使用するクラスターは 8 コアで、各コアのメモリは 20 GB ですが、入力データは 10 個のパーティションで 1000 GB です。 データ フローを直接実行する場合は、1000 GB/10 > 20 GB であるため、OOM の問題が発生します。そのため、再パーティション数を 100 (1000 GB/100 < 20 GB) に設定することが適切です。
    
  - オプション-4: ソース/シンク/変換の設定を調整および最適化します。 たとえば、1 つのコンテナーにすべてのファイルをコピーし、ワイルドカード パターンを使用しないようにします。 詳細については、「[Mapping Data Flow のパフォーマンスとチューニング ガイド](./concepts-data-flow-performance.md)」を参照してください。

## <a name="error-code-df-mssql-errorrowsfound"></a>エラー コード: DF-MSSQL-ErrorRowsFound
- **原因**: Azure SQL Database シンクへの書き込み中に、エラー/無効な行が見つかりました。
- **推奨事項**: 拒否されたデータの保存場所 (構成されている場合) でエラー行を見つけてください。

## <a name="error-code-df-mssql-exporterrorrowfailed"></a>エラー コード: DF-MSSQL-ExportErrorRowFailed
- **メッセージ**: エラー行をストレージに書き込んでいるときに例外が発生しました。
- **原因**: エラー行をストレージに書き込んでいるときに例外が発生しました。
- **推奨事項**: 拒否されたデータのリンク サービスの構成を確認してください。

## <a name="error-code-df-synapse-invaliddatabasetype"></a>エラー コード: DF-Synapse-InvalidDatabaseType
- **メッセージ**: データベースの種類がサポートされていません。
- **原因**: データベースの種類がサポートされていません。
- **推奨事項**: データベースの種類を確認し、適切な種類に変更します。

## <a name="error-code-df-synapse-invalidformat"></a>エラー コード: DF-Synapse-InvalidFormat
- **メッセージ**: 形式がサポートされていません。
- **原因**: 形式がサポートされていません。 
- **推奨事項**: 形式を確認して、適切な形式に変更します。

## <a name="error-code-df-synapse-invalidtabledbname"></a>エラー コード: DF-Synapse-InvalidTableDBName
- **原因**: テーブル/データベース名が無効です。
- **推奨**: テーブルまたはデータベースを有効な名前に変更します。 有効な名前には、アルファベット文字、数字、`_` のみが含まれています。

## <a name="error-code-df-synapse-invalidoperation"></a>エラー コード: DF-Synapse-InvalidOperation
- **原因**: この操作はサポートされていません。
- **推奨**: 無効な操作を変更します。

## <a name="error-code-df-synapse-dbnotexist"></a>エラー コード: DF-Synapse-DBNotExist
- **原因**: データベースが存在しません。
- **推奨**: データベースが存在するかどうかを確認します。

## <a name="error-code-df-synapse-storedprocedurenotsupported"></a>エラー コード: DF-Synapse-StoredProcedureNotSupported
- **メッセージ**: 'ストアド プロシージャ' をソースとして使用することは、サーバーレス (オンデマンド) プールではサポートされていません。
- **原因**: サーバーレス プールには制限があります。
- **推奨事項**: 'クエリ' をソースとして使用するか、ストアド プロシージャをビューとして保存してから、'テーブル' をソースとして使用して直接ビューから読み取ります。

## <a name="error-code-df-executor-broadcastfailure"></a>エラー コード: DF-Executor-BroadcastFailure
- **メッセージ**: ブロードキャスト交換中にデータ フローの実行に失敗しました。 原因としては、ソースでの接続の構成が正しくないか、ブロードキャスト結合のタイムアウト エラーが考えられます。 ソースが正しく構成されていることを確認するには、接続をテストするか、データ フロー デバッグ セッションでソース データのプレビューを実行してください。 ブロードキャスト結合のタイムアウトを回避するには、結合/存在/参照の変換で [オフ] ブロードキャスト オプションを選択できます。 パフォーマンスを向上させるためにブロードキャスト オプションを使用する場合は、ブロードキャスト ストリームで、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成できることを確認します。 問題が解決しない場合は、カスタマー サポートにお問い合わせください。

- **原因**:  
    1. ソース接続/構成エラーにより、結合/存在/参照の変換でブロードキャスト エラーが発生することがあります。
    2. ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト結合で、ブロードキャスト用に選択されたストリームが、この制限内にデータを生成するには大きすぎることが考えられます。 ブロードキャスト結合が使用されていない場合、データ フローによって実行される既定のブロードキャストが同じ制限に達することがあります。

- **推奨事項**:
    - ソースでデータのプレビューを実行して、ソースが適切に構成されていることを確認します。 
    - ブロードキャスト オプションをオフにするか、処理に 60 秒を超える時間を要する可能性がある大規模なデータ ストリームのブロードキャストは避けてください。 代わりに、小さいストリームを選択してブロードキャストしてください。 
    - 通常、大規模な SQL/データ ウェアハウスのテーブルとソース ファイルは、適切な候補ではありません。 
    - ブロードキャスト結合が存在しない場合、エラーが発生したときはより大きなクラスターを使用します。 
    - 問題が解決しない場合は、カスタマー サポートにお問い合わせください。

## <a name="error-code-df-cosmos-shorttypenotsupport"></a>エラー コード: DF-Cosmos-ShortTypeNotSupport
- **メッセージ**: Short データ型は Cosmos DB ではサポートされていません。
- **原因**: Short データ型は Azure Cosmos DB ではサポートされていません。
- **推奨事項**: Cosmos シンクで使用する前に、関連する列を short から integer に変換する派生変換を追加します。

## <a name="error-code-df-blob-functionnotsupport"></a>エラー コード: DF-Blob-FunctionNotSupport
- **メッセージ**: このエンドポイントでは、BlobStorageEvents、SoftDelete、または AutomaticSnapshot がサポートされていません。 このエンドポイントを使用する場合は、これらのアカウント機能を無効にしてください。
- **原因**: Azure Blob Storage のリンクされたサービスがサービス プリンシパルまたはマネージド ID 認証で作成されている場合、データ フローで Azure Blob Storage イベント、論理的な削除、または自動スナップショットはサポートされません。
- **推奨事項**: Azure Blob アカウントで Azure Blob Storage イベント、論理的な削除、または自動スナップショット機能を無効にするか、キー認証を使用してリンクされたサービスを作成します。

## <a name="error-code-df-cosmos-invalidaccountkey"></a>エラー コード: DF-Cosmos-InvalidAccountKey
- **メッセージ**: この入力認証トークンでは、要求を処理できません。 Please check that the expected payload is built as per the protocol, and check the key being used (必要なペイロードがプロトコルにしたがって作成されていることを確認し、使用されているキーを確認してください)。
- **原因**: Azure Cosmos DB のデータの読み取り/書き込みを行うための十分なアクセス許可がありません。
- **推奨事項**: 読み取り/書き込みキーを使用して、Azure Cosmos DB にアクセスしてください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

- [マッピング データ フローのトラブルシューティング ガイド](data-flow-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)