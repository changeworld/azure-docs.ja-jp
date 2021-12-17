---
title: XML フォーマット コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics で XML フォーマット コネクタの問題を解決する方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: d4a6d8c26b9441663cf67a1ea9a837091a51a624
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390476"
---
# <a name="troubleshoot-the-xml-format-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse で XML フォーマット コネクタの問題を解決する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse で XML フォーマット コネクタに関する一般的な問題を解決する方法を提案します。

## <a name="error-code-xmlsinknotsupported"></a>エラー コード:XmlSinkNotSupported

- **メッセージ**: `Write data in XML format is not supported yet, choose a different format!`

- **原因**:XML データセットは、Copy アクティビティでシンク データセットとして使用されていました。

- **推奨事項**:シンク データセットとは異なる形式のデータセットを使用します。


## <a name="error-code-xmlattributecolumnnameconflict"></a>エラー コード:XmlAttributeColumnNameConflict

- **メッセージ**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **原因**:属性プレフィックスを使用しました。これが競合の原因になりました。

- **推奨事項**:"attributePrefix" プロパティに別の値を設定します。


## <a name="error-code-xmlvaluecolumnnameconflict"></a>エラー コード:XmlValueColumnNameConflict

- **メッセージ**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **原因**:子要素名の 1 つを、要素値の列名として使用しました。

- **推奨事項**:"valueColumn" プロパティに別の値を設定します。


## <a name="error-code-xmlinvalid"></a>エラー コード:XmlInvalid

- **メッセージ**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **原因**:入力 XML ファイルの形式が正しくありません。

- **推奨事項**:正しい形式になるように XML ファイルを修正します。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
