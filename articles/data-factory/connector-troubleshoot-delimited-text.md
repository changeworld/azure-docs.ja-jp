---
title: 区切りテキスト形式コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics の区切りテキスト形式コネクタの問題のトラブルシューティングの方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 42346a5922b0f607e467e495455166057248db5e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390456"
---
# <a name="troubleshoot-the-delimited-text-format-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse の区切りテキスト形式コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の区切りテキスト形式コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-code-delimitedtextcolumnnamenotallownull"></a>エラー コード:DelimitedTextColumnNameNotAllowNull

- **メッセージ**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**:アクティビティに 'firstRowAsHeader' が設定されている場合、最初の行が列名として使用されます。 このエラーは、最初の行に空の値が含まれていることを意味します (たとえば、'ColumnA, ColumnB')。

- **推奨事項**:最初の行を確認し、値が空の場合は修正します。


## <a name="error-code-delimitedtextmorecolumnsthandefined"></a>エラー コード:DelimitedTextMoreColumnsThanDefined

- **メッセージ**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

  | 原因分析                                               | 推奨                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 問題のある行の列数が、最初の行の列数を超えています。 データの問題、または列区切り記号や引用符文字の設定が正しくないことが原因である可能性があります。 | エラー メッセージから行数を取得し、行の列を確認して、データを修正してください。 |
  | エラー メッセージで予想される列数が "1" の場合は、正しくない圧縮やフォーマット設定が指定されている可能性があります。これにより、ファイルが正しく解析されませんでした。 | フォーマット設定を確認して、ソース ファイルと一致していることを確認してください。 |
  | ソースがフォルダーの場合、指定されたフォルダーの下にあるファイルのスキーマが異なる可能性があります。 | 指定されたフォルダー内のファイルのスキーマが同じであることを確認します。 |

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
