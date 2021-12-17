---
title: Azure Cosmos DB コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Azure Synapse Analytics の Azure Cosmos DB および Azure Cosmos DB (SQL API) コネクタの問題のトラブルシューティングの方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: cd5f10c05ab1ca2524f384a4d085a913193e7293
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390444"
---
# <a name="troubleshoot-the-azure-cosmos-db-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse の Azure Cosmos DB コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の Azure Cosmos DB および Azure Cosmos DB (SQL API) コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-message-request-size-is-too-large"></a>エラー メッセージ: 要求のサイズが大きすぎます。

- **現象**:既定の書き込みバッチサイズを使用して Azure Cosmos DB にデータをコピーすると、次のエラーが表示されます。`Request size is too large.`

- **原因**:Azure Cosmos DB は、1 つの要求のサイズを 2 MB に制限します。 式は、*要求サイズ = 1 つのドキュメント サイズ <\*書き込みバッチ サイズ*> となります。 ドキュメントのサイズが大きい場合、既定の動作の結果として要求サイズが過度に大きくなります。 書き込みバッチ サイズを調整することができます。

- **解決方法**:Copy アクティビティのシンクで、<*書き込みバッチ サイズ*> の値を小さくします (既定値は 10000)。

## <a name="error-message-unique-index-constraint-violation"></a>エラー メッセージ: 一意なインデックスの制約違反

- **現象**:Azure Cosmos DB にデータをコピーすると、次のエラーが表示されます。

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **原因**:原因として、次の 2 つが考えられます。

    - 原因 1:書き込み動作として **Insert** を使用する場合、このエラーは、ソース データに同じ ID を持つ行またはオブジェクトがあることを意味します。
    - 原因 2:書き込み動作として **Upsert** を使用し、コンテナーに別の一意のキーを設定した場合、このエラーは、ソース データの行またはオブジェクトの ID はそれぞれ異なりますが、定義された一意のキーとして同じ値が存在することを意味します。

- **解決方法**: 

    - 原因 1 の場合は、書き込み動作として **Upsert** を設定します。
    - 原因 2 の場合は、定義された一意のキーに対して各ドキュメントの値が異なることを確認してください。

## <a name="error-message-request-rate-is-large"></a>エラー メッセージ:要求率が大きいです

- **現象**:Azure Cosmos DB にデータをコピーすると、次のエラーが表示されます。

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **原因**:使用されている要求ユニット (RU) の数が、Azure Cosmos DB で構成されている使用可能な RU を超えています。 Azure Cosmos DB が RU を計算する方法については、「[Azure Cosmos DB の要求ユニット](../cosmos-db/request-units.md#request-unit-considerations)」を参照してください。

- **解決方法**:次の 2 つの解決策のいずれかを試してください。

    - <*コンテナー RU*> 数を Azure Cosmos DB でより大きな値に増やします。 このソリューションにより、Copy アクティビティのパフォーマンスが向上しますが、Azure Cosmos DB にかかるコストが増加します。 
    - *writeBatchSize* を 1000 などの小さい値に減らし、*parallelCopies* を 1 などの小さい値に減らします。 このソリューションでは、コピーの実行のパフォーマンスが低下しますが、Azure Cosmos DB にかかるコストは増加しません。

## <a name="columns-missing-in-column-mapping"></a>列マッピングでの列の欠落

- **現象**:列マッピングのために Azure Cosmos DB のスキーマをインポートするときに、一部の列が欠落しています。 

- **原因**: Azure Data Factory と Synapse のパイプラインは、最初の 10 個の Azure Cosmos DB ドキュメントからスキーマを推論します。 一部のドキュメント列またはプロパティに値が含まれていない場合、スキーマが検出されず、その結果表示されません。

- **解決方法**:次のコードに示すように、クエリを調整して、列の値を結果セットに空の値で表示するように強制できます。 最初の 10 個のドキュメントで、*impossible* 列が欠落していると見なします。 あるいは、マッピング用の列を手動で追加することもできます。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

## <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>エラー メッセージ:リーダーの GuidRepresentation が CSharpLegacy です

- **現象**:汎用一意識別子 (UUID) フィールドを使用して Azure Cosmos DB MongoAPI または MongoDB からデータをコピーすると、次のエラーが表示されます。

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **原因**:バイナリ JSON (BSON) で UUID を表すには、次の 2 つの方法があります。UuidStardard と UuidLegacy。 既定では、UuidLegacy はデータの読み取りに使用されます。 MongoDB 内の UUID データが UuidStandard の場合は、エラーが発生します。

- **解決方法**:MongoDB の接続文字列に、*uuidRepresentation=standard* オプションを追加します。 詳細については、[MongoDB 接続文字列](connector-mongodb.md#linked-service-properties)に関する記事を参照してください。

## <a name="error-code-cosmosdbsqlapioperationfailed"></a>エラー コード:CosmosDbSqlApiOperationFailed

- **メッセージ**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **原因**:CosmosDbSqlApi 操作に問題があります。  これは、具体的には Cosmos DB (SQL API) コネクタに適用されます。

- **推奨事項**:エラーの詳細を確認するには、[Azure Cosmos DB のヘルプ ドキュメント](../cosmos-db/troubleshoot-dot-net-sdk.md)を参照してください。 詳細については、Azure Cosmos DB チームにお問い合わせください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
