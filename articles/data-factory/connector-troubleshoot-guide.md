---
title: コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Azure Synapse Analytics でのコネクタに関する問題のトラブルシューティングを行う方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: synapse
ms.openlocfilehash: 3271c2d37c9193fd9fb682359d0e0c57a6585ae4
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504338"
---
# <a name="troubleshoot-azure-data-factory-and-azure-synapse-analytics-connectors"></a>Azure Data Factory と Azure Synapse Analytics のコネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse Analytics でのコネクタに関するトラブルシューティングを行う方法について説明します。  

## <a name="connector-specific-problems"></a>コネクタ固有の問題

各コネクタのトラブルシューティングのページでは、そのコネクタに特有の問題点と、その原因の説明、および解決するための推奨事項が紹介されています。

- [Azure Blob Storage](connector-troubleshoot-azure-blob-storage.md)
- [Azure Cosmos DB (SQL API コネクタを含む)](connector-troubleshoot-azure-cosmos-db.md)
- [Azure Data Lake (Gen1 と Gen2)](connector-troubleshoot-azure-data-lake.md)
- [Azure Database for PostgreSQL](connector-troubleshoot-postgresql.md)
- [Azure Files Storage](connector-troubleshoot-azure-files.md)
- [Azure Synapse Analytics、Azure SQL Database、および SQL Server](connector-troubleshoot-synapse-sql.md)
- [DB2](connector-troubleshoot-db2.md)
- [区切りテキスト形式](connector-troubleshoot-delimited-text.md)
- [Dynamics 365、Dataverse (Common Data Service)、および Dynamics CRM](connector-troubleshoot-dynamics-dataverse.md)
- [FTP、SFTP、および HTTP](connector-troubleshoot-ftp-sftp-http.md)
- [Hive](connector-troubleshoot-hive.md)
- [Oracle](connector-troubleshoot-oracle.md)
- [ORC 形式](connector-troubleshoot-orc.md)
- [Parquet 形式](connector-troubleshoot-parquet.md)
- [REST](connector-troubleshoot-rest.md)
- [SharePoint Online リスト](connector-troubleshoot-sharepoint-online-list.md)
- [XML 形式](connector-troubleshoot-xml.md)

## <a name="general-copy-activity-errors"></a>一般的な Copy アクティビティのエラー

以下は、Copy アクティビティに関する一般的なエラーで、どのコネクタでも発生する可能性があります。

### <a name="error-code-jrenotfound"></a>エラー コード:JreNotFound

- **メッセージ**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**:セルフホステッド IR は、Java ランタイムを見つけることができません。 Java ランタイムは、特定のソースを読み取るために必要です。

- **推奨事項**:統合ランタイム環境を確認するには、[セルフホステッド統合ランタイムの使用](./format-parquet.md#using-self-hosted-integration-runtime)に関する記事を参照してください。


### <a name="error-code-wildcardpathsinknotsupported"></a>エラー コード:WildcardPathSinkNotSupported

- **メッセージ**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**:シンク データセットでは、ワイルドカード値はサポートされません。

- **推奨事項**:シンク データセットを確認し、ワイルドカード値を使用せずにパスを書き直します。


### <a name="fips-issue"></a>FIPS の問題

- **現象**:次のエラー メッセージが表示された FIPS 対応のセルフホステッド IR コンピューターでは、Copy アクティビティが失敗します: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **原因**:このエラーは、Azure BLOB、SFTP などのコネクタを使用してデータをコピーするときに発生する可能性があります。 Federal Information Processing Standards (FIPS) では、使用が許可される暗号化アルゴリズムの特定のセットを定義します。 マシンで FIPS モードが有効になっている場合、一部のシナリオでは、コピー アクティビティが依存する一部の暗号化クラスがブロックされます。

- **解決方法**:[現在は "FIPS モード" の使用を推奨していない理由](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)と、セルフホステッド IR コンピューターで FIPS を無効にできるかどうかを評価する方法について参照してください。

    または、FIPS をバイパスしてアクティビティを正常に実行できるようにするだけの場合は、次の手順を実行します。

    1. セルフホステッド IR がインストールされているフォルダーを開きます。 パスは通常、*C:\Program Files\Microsoft Integration Runtime \<IR version>\Shared* です。

    2. *diawp.exe.config* ファイルを開き、`<runtime>` セクションの最後で、次のように `<enforceFIPSPolicy enabled="false"/>` を追加します。

        :::image type="content" source="./media/connector-troubleshoot-guide/disable-fips-policy.png" alt-text="FIPS が無効になっていることを示す、diawp.exe.config ファイルのセクションのスクリーンショット。":::

    3. ファイルを保存し、セルフホステッド IR コンピューターを再起動します。

### <a name="error-code-jniexception"></a>エラー コード: JniException

- **メッセージ**: `An error occurred when invoking Java Native Interface.`

- **原因**: エラー メッセージに "Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]"\(JVM を作成できません: JNI リターン コード [-6] [JNI 呼び出しが失敗しました: 無効な引数です。]\) が含まれている場合、一部の無効な (グローバル) 引数が設定されているため、JVM を作成できない可能性があります。

- **推奨事項**: セルフホステッド統合ランタイムの *各ノード* をホストするマシンにログインします。 次のように、システム変数が正しく設定されていることを確認します`_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8G`。 すべての統合ランタイム ノードを再起動してから、パイプラインを再実行します。

### <a name="error-code-getoauth2accesstokenerrorresponse"></a>エラー コード: GetOAuth2AccessTokenErrorResponse

- **メッセージ**: `Failed to get access token from your token endpoint. Error returned from your authorization server: %errorResponse;.`

- **原因**: クライアント ID またはクライアント シークレットが無効なため、承認サーバーで認証に失敗しました。

- **推奨事項**: 承認サーバーのすべての OAuth2 クライアント資格情報フロー設定を修正します。

### <a name="error-code-failedtogetoauth2accesstoken"></a>エラー コード: FailedToGetOAuth2AccessToken

- **メッセージ**: `Failed to get access token from your token endpoint. Error message: %errorMessage;.`

- **原因**: OAuth2 クライアント資格情報フロー設定が無効です。

- **推奨事項**: 承認サーバーのすべての OAuth2 クライアント資格情報フロー設定を修正します。

### <a name="error-code-oauth2accesstokentypenotsupported"></a>エラー コード: OAuth2AccessTokenTypeNotSupported

- **メッセージ**: `The toke type '%tokenType;' from your authorization server is not supported, supported types: '%tokenTypes;'.`

- **原因**: お使いの承認サーバーはサポートされていません。

- **推奨事項**: サポートされているトークンの種類のトークンを返すことができる承認サーバーを使用します。

### <a name="error-code-oauth2clientidcolonnotallowed"></a>エラー コード: OAuth2ClientIdColonNotAllowed

- **メッセージ**: `The character colon(:) is not allowed in clientId for OAuth2ClientCredential authentication.`

- **原因**: クライアント ID に無効な文字であるコロン (`:`) が含まれています。

- **推奨事項**: 有効なクライアント ID を使用してください。

### <a name="error-code-managedidentitycredentialobjectnotsupported"></a>エラー コード: ManagedIdentityCredentialObjectNotSupported

- **メッセージ**: `Managed identity credential is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **推奨事項**: サポートされているバージョンを確認し、統合ランタイムを新しいバージョンにアップグレードします。

### <a name="error-code-querymissingformatsettingsindataset"></a>エラー コード: QueryMissingFormatSettingsInDataset

- **メッセージ**: `The format settings are missing in dataset %dataSetName;.`

- **原因**:データセットの型が Binary で、これはサポートされていません。

- **推奨事項**: DelimitedText、Json、Avro、Orc、または Parquet のデータセットを代わりに使用してください。

- **原因**: ファイル ストレージについて、データセットにフォーマット設定がありません。

- **推奨事項**: データセットの "バイナリ コピー" の選択を解除し、正しいフォーマット設定を設定します。

### <a name="error-code-queryunsupportedcommandbehavior"></a>エラー コード: QueryUnsupportedCommandBehavior

- **メッセージ**: `The command behavior "%behavior;" is not supported.`

- **推奨事項**: コマンド動作をプレビューまたは GetSchema API 要求 URL のパラメーターとして追加しないでください。

### <a name="error-code-dataconsistencyfailedtogetsourcefilemetadata"></a>エラー コード: DataConsistencyFailedToGetSourceFileMetadata

- **メッセージ**: `Failed to retrieve source file ('%name;') metadata to validate data consistency.`

- **原因**: シンク データ ストアに一時的な問題があるか、シンク データ ストアからのメタデータの取得が許可されていません。

### <a name="error-code-dataconsistencyfailedtogetsinkfilemetadata"></a>エラー コード: DataConsistencyFailedToGetSinkFileMetadata

- **メッセージ**: `Failed to retrieve sink file ('%name;') metadata to validate data consistency.`

- **原因**: シンク データ ストアに一時的な問題があるか、シンク データ ストアからのメタデータの取得が許可されていません。

### <a name="error-code-dataconsistencyvalidationnotsupportedfornondirectbinarycopy"></a>エラー コード: DataConsistencyValidationNotSupportedForNonDirectBinaryCopy

- **メッセージ**: `Data consistency validation is not supported in current copy activity settings.`

- **原因**: データ整合性の検証は、ダイレクト バイナリ コピー シナリオでのみサポートされています。

- **推奨事項**: コピー アクティビティ ペイロードの 'validateDataConsistency' プロパティを削除します。

### <a name="error-code-dataconsistencyvalidationnotsupportedforlowversionselfhostedintegrationruntime"></a>エラー コード: DataConsistencyValidationNotSupportedForLowVersionSelfHostedIntegrationRuntime

- **メッセージ**: `'validateDataConsistency' is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **推奨事項**: サポートされている統合ランタイム バージョンを確認し、それを上位バージョンにアップグレードするか、コピー アクティビティから 'validateDataConsistency' プロパティを削除します。

### <a name="error-code-skipmissingfilenotsupportedfornondirectbinarycopy"></a>エラー コード: SkipMissingFileNotSupportedForNonDirectBinaryCopy

- **メッセージ**: `Skip missing file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **推奨事項**: コピー アクティビティ ペイロードの skipErrorFile 設定の 'fileMissing' を削除します。

### <a name="error-code-skipinconsistencydatanotsupportedfornondirectbinarycopy"></a>エラー コード: SkipInconsistencyDataNotSupportedForNonDirectBinaryCopy

- **メッセージ**: `Skip inconsistency is not supported in current copy activity settings, it's only supported with direct binary copy when validateDataConsistency is true.`

- **推奨事項**: コピー アクティビティ ペイロードの skipErrorFile 設定の 'dataInconsistency' を削除します。

### <a name="error-code-skipforbiddenfilenotsupportedfornondirectbinarycopy"></a>エラー コード: SkipForbiddenFileNotSupportedForNonDirectBinaryCopy

- **メッセージ**: `Skip forbidden file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **推奨事項**:コピー アクティビティ ペイロードの skipErrorFile 設定の 'fileForbidden' を削除します。

### <a name="error-code-skipforbiddenfilenotsupportedforthisconnector"></a>エラー コード: SkipForbiddenFileNotSupportedForThisConnector

- **メッセージ**: `Skip forbidden file is not supported for this connector: ('%connectorName;').`

- **推奨事項**: コピー アクティビティ ペイロードの skipErrorFile 設定の 'fileForbidden' を削除します。

### <a name="error-code-skipinvalidfilenamenotsupportedfornondirectbinarycopy"></a>エラー コード: SkipInvalidFileNameNotSupportedForNonDirectBinaryCopy

- **メッセージ**: `Skip invalid file name is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **推奨事項**: コピー アクティビティ ペイロードの skipErrorFile 設定の 'invalidFileName' を削除します。

### <a name="error-code-skipinvalidfilenamenotsupportedforsource"></a>エラー コード: SkipInvalidFileNameNotSupportedForSource

- **メッセージ**: `Skip invalid file name is not supported for '%connectorName;' source.`

- **推奨事項**: コピー アクティビティ ペイロードの skipErrorFile 設定の 'invalidFileName' を削除します。

### <a name="error-code-skipinvalidfilenamenotsupportedforsink"></a>エラー コード: SkipInvalidFileNameNotSupportedForSink

- **メッセージ**: `Skip invalid file name is not supported for '%connectorName;' sink.`

- **推奨事項**: コピー アクティビティ ペイロードの skipErrorFile 設定の 'invalidFileName' を削除します。

### <a name="error-code-skipallerrorfilenotsupportedfornonbinarycopy"></a>エラー コード: SkipAllErrorFileNotSupportedForNonBinaryCopy

- **メッセージ**: `Skip all error file is not supported in current copy activity settings, it's only supported with binary copy with folder.`

- **推奨事項**: コピー アクティビティ ペイロードの skipErrorFile 設定の 'allErrorFile' を削除します。

### <a name="error-code-deletefilesaftercompletionnotsupportedfornondirectbinarycopy"></a>エラー コード: DeleteFilesAfterCompletionNotSupportedForNonDirectBinaryCopy

- **メッセージ**: `'deleteFilesAfterCompletion' is not support in current copy activity settings, it's only supported with direct binary copy.`

- **推奨事項**: 'deleteFilesAfterCompletion' 設定を削除するか、ダイレクト バイナリ コピーを使用してください。

### <a name="error-code-deletefilesaftercompletionnotsupportedforthisconnector"></a>エラー コード: DeleteFilesAfterCompletionNotSupportedForThisConnector

- **メッセージ**: `'deleteFilesAfterCompletion' is not supported for this connector: ('%connectorName;').`

- **推奨事項**: コピー アクティビティ ペイロードの 'deleteFilesAfterCompletion' 設定を削除してください。

### <a name="error-code-failedtodownloadcustomplugins"></a>エラー コード: FailedToDownloadCustomPlugins

- **メッセージ**: `Failed to download custom plugins.`

- **原因**: ダウンロード リンクが無効であるか、一時的な接続の問題が発生しています。

- **推奨事項**: 一時的な問題であることをメッセージが示す場合は、再試行します。 問題が解決しない場合は、サポート チームにお問い合わせください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
