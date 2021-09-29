---
title: リンクされたサービスをパラメーター化する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory パイプラインおよび Azure Synapse Analytics パイプラインのリンクされたサービスをパラメーター化し、実行時に動的な値を渡す方法について説明します。
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
author: chez-charlie
ms.author: chez
ms.openlocfilehash: 6500e743644cc505a15e44be02358d943fa5baa4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750510"
---
# <a name="parameterize-linked-services-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory および Azure Synapse Analytics のリンクされたサービスをパラメーター化する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

リンクされたサービスをパラメーター化し、実行時に動的な値を渡せるようになりました。 たとえば、同じ論理 SQL サーバー上の異なるデータベースに接続する場合に、リンクされたサービスの定義内でデータベース名をパラメーター化することができるようになりました。 これにより、論理 SQL サーバー上のデータベースごとに、リンクされたサービスを作成する必要がなくなります。 リンクされたサービスの定義内で、他のプロパティをパラメーター化することもできます (たとえば、*ユーザー名* など)。

リンクされたサービスをパラメーター化するには、Azure portal の UI か、プログラミング インターフェイスを使用できます。

> [!TIP]
> パスワードやシークレットはパラメーター化しないようにすることをお勧めします。 シークレットはすべて Azure Key Vault 内に格納し、*シークレット名* をパラメーター化するようにしてください。

> [!Note]
> パラメーター名に "-" を使用する未解決のバグがあります。バグが解決されるまでは、"-" を含まない名前を使用することをお勧めします。

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>サポートされるリンクされたサービスの種類

すべてのリンクされたサービスの種類は、パラメーター化でサポートされています。

**UI でネイティブにサポートされる:** リンクされたサービスを UI で作成する場合、このサービスは、次の種類のリンクされたサービスに対して組み込みのパラメーター化エクスペリエンスを提供します。 リンクされたサービスの作成/編集ブレードで、新しいパラメーターのオプションを検索し、動的なコンテンツを追加できます。 「[UI エクスペリエンス](#ui-experience)」を参照してください。

- Amazon Redshift
- Amazon S3
- Amazon S3 互換ストレージ
- Azure Blob Storage
- Azure Cosmos DB (SQL API)
- Azure Data Lake Storage Gen2
- Azure Database for MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL Database 
- Azure SQL Managed Instance
- Azure Synapse Analytics 
- Azure Table Storage
- 汎用 HTTP
- 汎用 REST
- MySQL
- Oracle
- Oracle Cloud Storage
- SQL Server

**詳細作成:** 上記の一覧にないその他のリンクされたサービスの種類では、UI で JSON を編集して、リンクされたサービスをパラメーター化できます。

- リンクされたサービスの作成/編集ブレードで > 下部にある "詳細" を展開し > "JSON 形式で動的な内容を指定する" ためのチェックボックスをオンにして > リンクされたサービスの JSON ペイロードを指定します。 
- または、パラメーター化せずにリンクされたサービスを作成した後で、[管理ハブ](author-visually.md#management-hub) > リンクされたサービス > 特定のリンクされたサービスを検索 > "コード" (ボタン {}) をクリックして JSON を編集します。 

` parameters` セクションを追加してパラメーターを定義し、` @{linkedService().paramName} ` を使用してパラメーターを参照するには、[JSON サンプル](#json)を参照してください。

## <a name="ui-experience"></a>UI エクスペリエンス

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-1.png" alt-text="リンクされたサービスの定義に動的なコンテンツを追加する":::

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-2.png" alt-text="新しいパラメーターを作成する":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-1-synapse.png" alt-text="リンクされたサービスの定義に動的なコンテンツを追加する":::

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-2-synapse.png" alt-text="新しいパラメーターを作成する":::

---

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
