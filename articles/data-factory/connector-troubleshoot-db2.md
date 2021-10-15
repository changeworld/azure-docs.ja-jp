---
title: DB2 コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics の DB2 コネクタの問題のトラブルシューティングの方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f2a9e79fb3c01938eae52b6c34687943a9472024
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390391"
---
# <a name="troubleshoot-the-db2-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse の DB2 コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の Azure Database for PostgreSQL コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-code-db2driverrunfailed"></a>エラー コード:DB2DriverRunFailed

- **メッセージ**: `Error thrown from driver. Sql code: '%code;'`

- **原因**: エラー メッセージに "SQLSTATE=51002 SQLCODE=-805" という文字列が含まれている場合は、[DB2 からのデータのコピー](./connector-db2.md#linked-service-properties)に関する記事の「ヒント」に従います。

- **推奨事項**:`packageCollection` プロパティで "NULLID" の設定を試みます。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
