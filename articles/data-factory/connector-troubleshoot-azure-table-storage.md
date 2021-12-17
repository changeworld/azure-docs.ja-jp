---
title: Azure Table Storage コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics での Azure Table Storage コネクタに関する問題のトラブルシューティング方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e99a31f4ca0380d8bbc12f941248d745e0c7f74c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390392"
---
# <a name="troubleshoot-the-azure-table-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse での Azure Table Storage コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse での Azure Table Storage コネクタに関する一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-code-azuretableduplicatecolumnsfromsource"></a>エラー コード:AzureTableDuplicateColumnsFromSource

- **メッセージ**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **原因**:重複するソース列は、次のいずれかの理由で発生する可能性があります。
   * データベースをソースとして使用し、テーブル結合を適用しています。
   * ヘッダー行に重複する列名を含む非構造化 CSV ファイルがあります。

- **推奨事項**:必要に応じて、ソース列を再確認して修正します。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
