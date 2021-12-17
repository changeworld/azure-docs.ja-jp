---
title: Azure Database for PostgreSQL コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics での Azure Database for PostgreSQL コネクタに関する問題のトラブルシューティング方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8613c6b17007f51caf437fb2d66d2d8e17965007
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390389"
---
# <a name="troubleshoot-the-azure-database-for-postgresql-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse での Azure Database for PostgreSQL コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の Azure Database for PostgreSQL コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-code-azurepostgresqlnpgsqldatatypenotsupported"></a>エラー コード: AzurePostgreSqlNpgsqlDataTypeNotSupported

- **メッセージ**: `The data type of the chosen Partition Column, '%partitionColumn;', is '%dataType;' and this data type is not supported for partitioning.`

- **推奨事項**: int、bigint、smallint、serial、bigserial、smallserial、タイム ゾーンのある (またはない) タイムスタンプ、タイム ゾーンのない時刻、または日付のデータ型のパーティション列を選択します。

## <a name="error-code-azurepostgresqlnpgsqlpartitioncolumnnamenotprovided"></a>エラー コード: AzurePostgreSqlNpgsqlPartitionColumnNameNotProvided

- **メッセージ**: `Partition column name must be specified.`

- **原因**: パーティション列名が指定されておらず、自動的に決定できませんでした。
 
## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
