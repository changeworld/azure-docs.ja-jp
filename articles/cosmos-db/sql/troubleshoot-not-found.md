---
title: Azure Cosmos DB の "見つかりません" 例外をトラブルシューティングする
description: "\"見つかりません\" 例外を診断して修正する方法について説明します。"
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 05/26/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: ignite-fall-2021
ms.openlocfilehash: c1d95b399718a7d4b7f88daaef31bb11f763fa2d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045160"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Azure Cosmos DB の "見つかりません" 例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 状態コード 404 は、リソースがもう存在しないことを表します。

## <a name="expected-behavior"></a>正しい動作
アプリケーションでコード 404 が予期されていて、そのシナリオが正しく処理される有効なシナリオが多数あります。

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>存在しているはず、または実際に存在している項目に対して "見つかりません" 例外が返された
項目が存在しているはず、または実際に存在している場合に状態コード 404 が返される理由は、次のとおりです。

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>入力セッション トークンでは読み取りセッションを使用できません

#### <a name="solution"></a>解決方法:
1. 現在の SDK を、入手できる最新版に更新します。 この特定のエラーの最も一般的な原因は最新版の SDK で解決されています。

### <a name="race-condition"></a>競合状態
複数の SDK クライアント インスタンスがあり、書き込みの前に読み取りが発生しました。

#### <a name="solution"></a>解決方法:
1. Azure Cosmos DB の既定のアカウント整合性は、セッション整合性です。 項目が作成または更新されると、応答により、セッション トークンが返されます。これを SDK インスタンス間で渡すことで、読み取り要求がその変更を含むレプリカから読み取っていることが保証されます。
1. [整合性レベル](../consistency-levels.md)を[より強力なレベル](../consistency-levels.md)に変更します。

### <a name="reading-throughput-for-a-container-or-database-resource"></a>コンテナーまたはデータベースのスループットを見る
PowerShell または Azure CLI で *not found* (見つかりません) というエラー メッセージが出る。

#### <a name="solution"></a>解決方法 :
スループットの情報は、データベース単位、コンテナー単位またはその両方で用意できます。 *not found* (見つかりません) というエラーが出る場合は、親データベース リソースまたは子コンテナー リソースでスループットの確認を試みてください。

### <a name="invalid-partition-key-and-id-combination"></a>パーティション キーと ID の組み合わせが無効
パーティション キーと ID の組み合わせが無効です。

#### <a name="solution"></a>解決方法:
不適切な組み合わせの原因となっているアプリケーション ロジックを修正します。 

### <a name="invalid-character-in-an-item-id"></a>項目 ID に無効な文字がある
項目 ID に[無効な文字](/dotnet/api/microsoft.azure.documents.resource.id#remarks)を含む項目が Azure Cosmos DB に挿入されています。

#### <a name="solution"></a>解決方法:
ID を、特殊文字を含まない別の値に変更します。 ID を変更できない場合は、ID を Base64 でエンコードして特殊文字をエスケープすることができます。 Base64 からはそれでも、置換する必要がある無効な文字 "/" を含む名前が生成されることがあります。

その ID のコンテナーに既に挿入されている項目は、名前ベースの参照の代わりに RID 値を使用して置き換えることができます。
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
// https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Time to Live 消去
項目に、[Time to Live (TTL)](./time-to-live.md) プロパティが設定されていました。 TTL プロパティの期限が切れたため、項目は消去されました。

#### <a name="solution"></a>解決方法:
項目が消去されないようにするには、TTL プロパティを変更します。

### <a name="lazy-indexing"></a>Lazy インデックス作成
[Lazy インデックス作成](../index-policy.md#indexing-mode)が追い付いていません。

#### <a name="solution"></a>解決方法:
インデックス作成が追い付くまで待つか、インデックス作成ポリシーを変更します。

### <a name="parent-resource-deleted"></a>親リソースが削除された
項目が存在するデータベースやコンテナーが削除されました。

#### <a name="solution"></a>解決方法:
1. 親リソースを[復元](../configure-periodic-backup-restore.md#request-restore)するか、リソースを再作成します。
1. 新しいリソースを作成して、削除されたリソースを置き換えます。

### <a name="7-containercollection-names-are-case-sensitive"></a>7.コンテナーおよびコレクション名は、大文字と小文字が区別されます。
Cosmos DB では、コンテナーおよびコレクション名の大文字と小文字が区別されます。

#### <a name="solution"></a>解決方法:
Cosmos DB に接続するときは、必ず正確な名前を使用してください。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。
* Azure Cosmos DB Java v4 SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-java-sdk-v4-sql.md)。
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) のパフォーマンス ガイドラインを確認する。
