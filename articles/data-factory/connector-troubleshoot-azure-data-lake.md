---
title: Azure Data Lake Storage コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics の Azure Data Lake Storage Gen1 および Gen2 コネクタの問題のトラブルシューティングの方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e5300e8c2008d99ec7757ed3850b8b31698ac8a9
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064345"
---
# <a name="troubleshoot-the-azure-data-lake-storage-connectors-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse の Azure Data Lake Storage コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の Azure Data Lake Storage Gen1 および Gen2 コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>エラー メッセージ:基になる接続が閉じられました。SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。

- **現象**:コピー アクティビティが次のエラーにより失敗します。 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **原因**:TLS ハンドシェイク中に証明書の検証に失敗しました。

- **解決方法**:回避策として、ステージング コピーを使用して、Azure Data Lake Storage Gen1 のトランスポート層セキュリティ (TLS) 検証をスキップします。 この問題を再現し、ネットワーク モニター (netmon) トレースを収集してから、ネットワーク チームに連絡し、ローカル ネットワーク構成を確認する必要があります。

    :::image type="content" source="./media/connector-troubleshoot-guide/adls-troubleshoot.png" alt-text="問題をトラブルシューティングするための Azure Data Lake Storage Gen1 接続の図。":::


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>エラー メッセージ:リモート サーバーがエラー(403) 禁止されています

- **現象**: コピー アクティビティが次のエラーにより失敗します。 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **原因**:原因の 1 つとして、使用するサービス プリンシパルまたはマネージド ID に、特定のフォルダーまたはファイルにアクセスするためのアクセス許可が付与されていないことが考えられます。

- **解決方法**:コピーする必要があるすべてのフォルダーとサブフォルダーに適切なアクセス許可を付与します。 詳しくは、「[Azure Data Lake Storage Gen1 との間でデータをコピーする](connector-azure-data-lake-store.md#linked-service-properties)」をご覧ください。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>エラー メッセージ: サービス プリンシパルを使用してアクセス トークンを取得できませんでした。 ADAL エラー: service_unavailable

- **現象**:コピー アクティビティが次のエラーにより失敗します。

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **原因**:Azure Active Directory によって所有されているサービス トークン サーバー (STS) が使用できない場合、要求の処理ビジー状態であるため、HTTP エラー 503 が返されます。 

- **解決方法**: 数分後にコピー アクティビティを再実行します。

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>エラー コード:ADLSGen2OperationFailed

- **メッセージ**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

  | 原因分析                                               | 推奨                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 何らかの操作に失敗したことを示すエラーが Azure Data Lake Storage Gen2 によってスローされた場合。| Azure Data Lake Storage Gen2 によってスローされたエラー メッセージの詳細を確認します。 エラーが一時的なエラーである場合は、操作を再試行してください。 さらに支援が必要な場合は、Azure Storage サポートにお問い合わせください。その際、エラー メッセージに含まれる要求 ID をお知らせください。 |
  | エラー メッセージに "許可されていません" という文字列が含まれている場合は、使用するサービス プリンシパルまたはマネージド ID に、Azure Data Lake Storage Gen2 にアクセスするための十分なアクセス許可がない可能性があります。 | このエラーのトラブルシューティングを行うには、「[Azure Data Lake Storage Gen2 でのデータのコピーと変換](./connector-azure-data-lake-storage.md#service-principal-authentication)」を参照してください。 |
  | エラー メッセージに "InternalServerError" という文字列が含まれている場合は、Azure Data Lake Storage Gen2 によってエラーが返されます。 | このエラーは、一時的なエラーが原因で発生している可能性があります。 その場合は、操作を再試行します。 問題が解決しない場合は、Azure Storage サポートにお問い合わせください。その際、エラー メッセージに含まれる要求 ID をお知らせください。 |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Azure Data Lake Storage Gen2 アカウントへの要求によりタイムアウト エラーが発生する

- **Message**: 
  * エラー コード = `UserErrorFailedBlobFSOperation`
  * エラー メッセージ = `BlobFS operation failed for: A task was canceled.`

- **原因**:この問題は、通常はセルフホステッド統合ランタイム (IR) コンピューターで発生する Azure Data Lake Storage Gen2 シンク タイムアウト エラーが原因で発生します。

- **推奨事項**: 

    - 可能であれば、セルフホステッド IR マシンとターゲット Azure Data Lake Storage Gen2 アカウントを同じリージョンに配置します。 これにより、ランダム タイムアウト エラーを回避し、パフォーマンスを向上させることができます。

    - ExpressRoute などの特別なネットワーク設定があるかどうかを調べ、ネットワークに十分な帯域幅があることを確認します。 全体的な帯域幅が少ない場合は、セルフホステッド IR 同時実行ジョブの設定を小さくすることをお勧めします。 これにより、複数の同時実行ジョブにわたるネットワーク リソースの競合を回避できます。

    - ファイル サイズが中くらいまたは小さい場合に、このようなタイムアウト エラーを軽減するには、バイナリ以外のコピーにはさらに小さいブロック サイズを使用します。 詳細については、「[Blob Storage Put Block](/rest/api/storageservices/put-block)」をご覧ください。

       カスタム ブロック サイズを指定するには、次に示すように、JSON ファイル エディターでプロパティを編集します。
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

### <a name="the-copy-activity-is-not-able-to-pick-files-from-azure-data-lake-storage-gen2"></a>コピー操作で Azure Data Lake Storage Gen2 からファイルを選択できません。

- **現象**: ファイル名が "Asset_Metadata" のとき、コピー操作で Azure Data Lake Storage Gen2 からファイルを選択できません。 この問題は Parquet タイプのデータセットでのみ発生します。 他のタイプのデータセットの場合、同じファイル名でも正しく動作します。

- **原因**: 下位互換性のために、`_metadata` はファイル名で予約されている substring として扱われます。 

- **推奨**: 以下の Parquet の予約一覧を避けるため、ファイル名を変更します。 
    1. ファイル名に `_metadata` が含まれています。
    2. ファイル名は `.` (ドット) で始まります。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
