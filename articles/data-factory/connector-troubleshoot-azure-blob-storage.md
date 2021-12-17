---
title: Azure Blob Storage コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics で、Azure Blob Storage コネクタの問題を解決する方法を説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e9a1c085e3d1d88d0897d46b924c5a9a21c747a2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390396"
---
# <a name="troubleshoot-the-azure-blob-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapseで Azure Blob Storage コネクタのトラブルシューティングを行います

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse で、Azure Blob Storage コネクタのよくある問題を解決するための方法を説明します。

## <a name="error-code-azurebloboperationfailed"></a>エラー コード:AzureBlobOperationFailed

- **メッセージ**: 「Blob 操作に失敗しました。 ContainerName: %containerName;、パス: %path;。」

- **原因**:Azure Blob Storage 操作に問題があります。

- **推奨事項**:エラーの詳細を確認するには、[Azure Blob Storage エラー コード](/rest/api/storageservices/blob-service-error-codes)に関する記事を参照してください。 詳細については、Azure Blob Storage チームにお問い合わせください。


## <a name="invalid-property-during-copy-activity"></a>コピー アクティビティ中プロパティが無効です

- **メッセージ**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **原因**:データセットで定義されている型が、Copy アクティビティで定義されているソースまたはシンクの型と一致しません。

- **解決方法**:データセットまたはパイプライン JSON 定義を編集して、型の一貫性を保ち、デプロイを再実行してください。

## <a name="error-code-fipsmodeisnotsupport"></a>エラー コード: FIPSModeIsNotSupport

- **メッセージ**: `Fail to read data form Azure Blob Storage for Azure Blob connector needs MD5 algorithm which can't co-work with FIPS mode. Please change diawp.exe.config in self-hosted integration runtime install directory to disable FIPS policy following https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/runtime/enforcefipspolicy-element.`

- **原因**:セルフホステッド統合ランタイムがインストールされた VM で FIPS ポリシーが有効になっています。

- **推奨事項**:セルフホステッド統合ランタイムがインストールされている VM で FIPS モードを無効にします。 Windows では FIPS モードは推奨されません。

## <a name="error-code-azureblobinvalidblocksize"></a>エラー コード: AzureBlobInvalidBlockSize

- **メッセージ**: `Block size should between %minSize; MB and 100 MB.`

- **原因**:ブロック サイズが BLOB の制限を超えています。

## <a name="error-code-azurestorageoperationfailedconcurrentwrite"></a>エラー コード: AzureStorageOperationFailedConcurrentWrite

- **メッセージ**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`

- **原因**: 複数の同時コピー アクティビティの実行またはアプリケーションが同じファイルに書き込んでいます。

## <a name="error-code-azureappendblobconcurrentwriteconflict"></a>エラー コード: AzureAppendBlobConcurrentWriteConflict

- **メッセージ**: `Detected concurrent write to the same append blob file, it's possible because you have multiple concurrent copy activities runs or applications writing to the same file '%name;'. Please check your ADF configuration and retry.`

- **原因**: 複数の書き込み要求が同時に発生したため、ファイルの内容が競合しています。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
