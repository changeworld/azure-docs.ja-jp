---
title: Azure Cosmos DB の "見つかりません" 例外をトラブルシューティングする
description: "\"見つかりません\" 例外を診断して修正する方法"
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293902"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Azure Cosmos DB の "見つかりません" の問題を診断してトラブルシューティングする
HTTP 状態コード 404 は、リソースがもう存在しないことを表します。

## <a name="expected-behavior"></a>正しい動作
アプリケーションで 404 が予期されていて、そのシナリオが正しく処理される有効なシナリオが多数あります。

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>存在しているはず、または実際に存在している項目に対して "見つかりません" が返された
項目が存在しているはず、または実際に存在している場合に状態コード 404 が返される理由は次のとおりです。

### <a name="1-race-condition"></a>1.競合状態
複数の SDK クライアント インスタンスがあり、書き込みの前に読み取りが発生しました。

#### <a name="solution"></a>解決方法:
1. Cosmos DB の既定のアカウント整合性は、セッション整合性です。 項目が作成または更新されると、応答により、セッション トークンが返されます。これを SDK インスタンス間で渡すことで、読み取り要求がその変更を含むレプリカから読み取っていることが保証されます。
2. [整合性レベル](consistency-levels-choosing.md)を[より強力なレベル](consistency-levels-tradeoffs.md)に変更します。

### <a name="2-invalid-partition-key-and-id-combination"></a>2.パーティション キーと ID の組み合わせが無効
パーティション キーと ID の組み合わせが無効です。

#### <a name="solution"></a>解決方法:
不適切な組み合わせの原因となっているアプリケーション ロジックを修正します。 

### <a name="3-invalid-character-in-item-id"></a>3.項目 ID に無効な文字がある
項目 ID に[無効な文字](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks)を含む項目が Cosmos DB に挿入されています。

#### <a name="solution"></a>解決方法:
ID を、特殊文字を含まない別の値に変更することをお勧めします。 ID を変更できない場合は、ID を Base64 でエンコードして特殊文字をエスケープすることができます。

コンテナーに既に挿入されている項目 ID は、名前ベースの参照の代わりに RID 値を使用して置き換えることができます。
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4.Time To Live (TTL) の消去
項目に、[Time To Live (TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) プロパティが設定されていました。 有効期限が切れたため、項目は削除されました。

#### <a name="solution"></a>解決方法:
項目が消去されないようにするには、Time To Live を変更します。

### <a name="5-lazy-indexing"></a>5.Lazy インデックス作成
[Lazy インデックス作成](index-policy.md#indexing-mode)が追い付いていません。

#### <a name="solution"></a>解決方法:
インデックス作成が追い付くまで待つか、インデックス作成ポリシーを変更します。

### <a name="6-parent-resource-deleted"></a>6.親リソースが削除された
項目が存在するデータベースやコンテナーが削除されています。

#### <a name="solution"></a>解決方法:
1. 親リソースを[復元](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period)するか、リソースを再作成します。
2. 新しいリソースを作成して、削除されたリソースを置き換えます。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK の使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)
* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインについて学習する