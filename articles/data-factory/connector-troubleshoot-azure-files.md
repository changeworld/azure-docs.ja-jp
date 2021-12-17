---
title: Azure Files コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics の Azure Files コネクタの問題のトラブルシューティングの方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 809bf30f1e11eaa7741ac0581881479d854ed4cb
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390407"
---
# <a name="troubleshoot-the-azure-files-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse の Azure Files コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の Azure Files コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-code-azurefileoperationfailed"></a>エラー コード:AzureFileOperationFailed

- **メッセージ**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **原因**:Azure Files ストレージ操作に問題があります。

- **推奨事項**:エラーの詳細を確認するには、[Azure Files のヘルプ](/rest/api/storageservices/file-service-error-codes)を参照してください。 詳細については、Azure Files チームにお問い合わせください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
