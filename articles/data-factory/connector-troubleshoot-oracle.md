---
title: Oracle コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics での Oracle コネクタに関する問題のトラブルシューティング方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 0450faa94af3c01fccebc618c5f91094447116f5
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390399"
---
# <a name="troubleshoot-the-oracle-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse での Oracle コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse での Oracle コネクタに関する一般的な問題のトラブルシューティングのための提案を示します。

## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>エラー コード:ArgumentOutOfRangeException

- **メッセージ**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **原因**: Azure Data Factory および Synapse のパイプラインでは、DateTime 値は、0001-01-01 00:00:00 から 9999-12-31 23:59:59 の範囲でサポートされています。 ただし、Oracle では、紀元前や 59 より大きい分または秒など、より広い範囲の DateTime 値がサポートされているため、エラーが発生します。

- **推奨事項**: 

    Oracle の値がサポートされる範囲内にあるかどうかを確認するには、`select dump(<column name>)` を実行します。 

    結果のバイト シーケンスの詳細については、[Oracle に格納されている日付](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
