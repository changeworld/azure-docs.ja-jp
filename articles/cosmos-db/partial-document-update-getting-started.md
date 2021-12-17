---
title: Azure Cosmos DB の部分的なドキュメント更新の概要
description: この記事では、.NET、Java、Node の各 SDK で部分的なドキュメント更新を使用する方法の例を示します。
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/25/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ceebe9f537dcf2f496594d6a6c63972fcb7af92
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434106"
---
# <a name="azure-cosmos-db-partial-document-update-getting-started"></a>Azure Cosmos DB の部分的なドキュメント更新: 概要
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、.NET、Java、Node の各 SDK で部分的なドキュメント更新を使用する方法の例と、発生する可能性のある一般的なエラーについて説明します。 次のシナリオのコード サンプルが用意されています。

- 1 つのパッチ操作の実行
- 複数のパッチ操作の組み合わせ
- フィルター述語に基づく条件付きパッチ構文
- トランザクションの一部としてのパッチ操作の実行

## <a name="net"></a>.NET

[NuGet ギャラリー](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.22.1-preview)から *3.22.1-preview* バージョンの Azure Cosmos DB .NET SDK v3 をダウンロードできます

> [!NOTE]
> 部分的なドキュメント更新の完全なサンプルは、GitHub の [.NET v3 サンプル リポジトリ](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/3fa885fdd84e2f8852d2a1d5c75c56b642b5bba3/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs)にあります。

**1 つのパッチ操作の実行**

```csharp
ItemResponse<SalesOrder> response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/TotalDue", 0) });

SalesOrder updated = response.Resource;
```

**複数のパッチ操作の組み合わせ**

```csharp
List<PatchOperation> patchOperations = new List<PatchOperation>();
patchOperations.Add(PatchOperation.Add("/nonExistentParent/Child", "bar"));
patchOperations.Add(PatchOperation.Remove("/cost"));
patchOperations.Add(PatchOperation.Increment("/taskNum", 6));
patchOperations.Add(patchOperation.Set("/existingPath/newproperty",value));

container.PatchItemAsync<item>(
                id: 5,
                partitionKey: new PartitionKey("task6"),
                patchOperations: patchOperations );
```

**フィルター述語に基づく条件付きパッチ構文**

```csharp
PatchItemRequestOptions patchItemRequestOptions = new PatchItemRequestOptions
{
    FilterPredicate = "from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))"
};
response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/ShippedDate", DateTime.UtcNow) },
    patchItemRequestOptions);

SalesOrder updated = response.Resource;
```

**トランザクションの一部としてのパッチ操作の実行**


```csharp
List<PatchOperation> patchOperationsUpdateTask = new List<PatchOperation>()
            {
                PatchOperation.Add("/children/1/pk", "patched"),
                PatchOperation.Remove("/description"),
                PatchOperation.Add("/taskNum", 8),
                PatchOperation.Replace("/taskNum", 12)
            };

TransactionalBatchPatchItemRequestOptions requestOptionsFalse = new TransactionalBatchPatchItemRequestOptions()
            {
                FilterPredicate = "from c where c.taskNum = 3"
            };

TransactionalBatchInternal transactionalBatchInternalFalse = (TransactionalBatchInternal)containerInternal.CreateTransactionalBatch(new Cosmos.PartitionKey(testItem.pk));
transactionalBatchInternalFalse.PatchItem(id: testItem1.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.PatchItem(id: testItem2.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.ExecuteAsync());
```

## <a name="java"></a>Java

Patch API は、現在 Java SDK の 4.20.0 バージョンではプレビュー段階です。 一般提供版は、4.21.0 バージョンで提供される予定です。 パッチ操作を使用する場合は、[Azure Cosmos DB Java v4 SDK](sql-api-sdk-java-v4.md) の *4.20.0* バージョンを `pom.xml` の依存関係のリストに追加するか、[Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) から直接ダウンロードしてください。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>4.20.0</version>
</dependency>
```

> [!NOTE]
> 完全なサンプルは、GitHub の [Java SDK v4 サンプル リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/)にあります

**1 つのパッチ操作の実行**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true);

CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**複数のパッチ操作の組み合わせ**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true)
                     .replace("/parents/0/familyName", "Doe");
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**フィルター述語に基づく条件付きパッチ構文**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
                                                                   .add("/vaccinated", true);
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();
options.setFilterPredicate("from f where f.registered = true");

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**トランザクションの一部としてのパッチ操作の実行**

```java
CosmosBatch batch = CosmosBatch.createCosmosBatch(new PartitionKey(family.getLastName()));
batch.createItemOperation(family);

CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create().add("/registered", false);
batch.patchItemOperation(family.getId(), cosmosPatchOperations);

CosmosBatchResponse response = container.executeCosmosBatch(batch);
if (response.isSuccessStatusCode()) {
    // if transactional batch succeeds   
}
```

## <a name="node"></a>Node

**1 つのパッチ操作の実行**

```javascript
const patchSource = itemDefList[1];

const replaceOperation: PatchOperation[] = 
    [{ 
      op: "replace", 
      path: "/lastName", 
      value: "Martin" 
    }];

const { resource: patchSource1 } = await container.item(patchSource.lastName).patch(replaceOperation); 
console.log(`Patched ${patchSource1.lastName} to new ${patchSource1.lastName}.`); 
```

**複数のパッチ操作の組み合わせ**

```javascript
const multipleOperations: PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/aka", 
      value: "MeFamily" 
    }, 
    { 
      op: "add", 
      path: "/years", 
      value: 12 
    }, 
    { 
      op: "replace", 
      path: "/lastName", 
      value: "Jose" 
    }, 
    { 
      op: "remove", 
      path: "/parents" 
    }, 
    { 
      op: "set", 
      path: "/children/firstName", 
      value: "Anderson" 
    }, 
    { 
      op: "incr", 
      path: "/years", 
      value: 5 
    } 
  ]; 

const { resource: patchSource2  } = await container.item(patchSource.id).patch(multipleOperations); 
 ```

**フィルター述語に基づく条件付きパッチ構文**

```javascript
const operations : PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/newImproved", 
      value: "it works" 
    } 
  ]; 

const condition = "from c where NOT IS_DEFINED(c.newImproved)"; 

const { resource: patchSource3 } = await container 
    .item(patchSource.id) 
    .patch({ condition, operations }); 

console.log(`Patched ${patchSource3} to new ${patchSource3}.`); 
```

## <a name="support-for-server-side-programming"></a>サーバー側プログラミングのサポート

部分的なドキュメント更新操作は、ストアド プロシージャ、トリガー、ユーザー定義関数を使用して、[サーバー側で実行](stored-procedures-triggers-udfs.md)することもできます。


```javascript
        this.patchDocument = function (documentLink, patchSpec, options, callback) { 
                if (arguments.length < 2) { 
                    throw new Error(ErrorCodes.BadRequest, sprintf(errorMessages.invalidFunctionCall, 'patchDocument', 2, arguments.length)); 
                }
                if (patchSpec === null || !(typeof patchSpec === "object" || Array.isArray(patchSpec))) { 
                    throw new Error(ErrorCodes.BadRequest, errorMessages.patchSpecMustBeObjectOrArray); 
                } 

                var documentIdTuple = validateDocumentLink(documentLink, false); 
                var collectionRid = documentIdTuple.collId; 
                var documentResourceIdentifier = documentIdTuple.docId; 
                var isNameRouted = documentIdTuple.isNameRouted; 

                patchSpec = JSON.stringify(patchSpec); 
                var optionsCallbackTuple = validateOptionsAndCallback(options, callback); 

                options = optionsCallbackTuple.options; 
                callback = optionsCallbackTuple.callback; 

                var etag = options.etag || ''; 
                var indexAction = options.indexAction || ''; 

                return collectionObjRaw.patch( 
                    collectionRid, 
                    documentResourceIdentifier, 
                    isNameRouted, 
                    patchSpec, 
                    etag, 
                    indexAction, 
                    function (err, response) { 
                        if (callback) { 
                            if (err) { 
                                callback(err); 
                            } else { 
                                callback(undefined, JSON.parse(response.body), response.options); 
                            } 
                        } else { 
                            if (err) { 
                                throw err; 
                            } 
                        } 
                    } 
                ); 
            }; 
```

## <a name="troubleshooting"></a>トラブルシューティング

この機能の使用中に発生する可能性のある一般的なエラーの一覧を次に示します。

| **エラー メッセージ** | **説明** |
| ------------ | -------- |
| Invalid patch request: check syntax of patch specification (無効なパッチ要求: パッチの仕様の構文を確認してください)| パッチ操作の構文が無効です。 [仕様](partial-document-update.md#partial-document-update-specification)を確認してください。
| Invalid patch request: Cannot patch system property `SYSTEM_PROPERTY`. (無効なパッチ要求: システム プロパティ `SYSTEM_PROPERTY` を修正できません) |  `_id`、 `_ts`、 `_etag`、 `_rid` などの、システムによって生成されたプロパティの修正はサポートされていません。 詳細情報: [部分的なドキュメント更新に関する FAQ](partial-document-update-faq.yml#is-partial-document-update-supported-for-system-generated-properties-) 
| The number of patch operations cannot exceed 10 (パッチ操作の数が 10 を超えることはできません) | 1 つのパッチの仕様に追加できるパッチ操作は 10 個までに制限されています。 詳細情報: [部分的なドキュメント更新に関する FAQ](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-)
| For Operation(`PATCH_OPERATION_INDEX`): Index(`ARRAY_INDEX`) to operate on is out of array bounds (Operation(`PATCH_OPERATION_INDEX`) の場合: 操作する Index(`ARRAY_INDEX`) が配列の範囲外です) | 修正する配列要素のインデックスが範囲外です 
| For Operation(`PATCH_OPERATION_INDEX`): Node(`PATH`) to be replaced has been removed earlier in the transaction. (Operation(`PATCH_OPERATION_INDEX`) の場合: 置き換える Node(`PATH`) がトランザクションで既に削除されています)| 修正しようとしているパスが存在しません。
| For Operation(`PATCH_OPERATION_INDEX`): Node(`PATH`) to be removed is absent. (Operation(`PATCH_OPERATION_INDEX`) の場合: 削除する Node(`PATH`) が存在しません) 注: トランザクションで既に削除された可能性もあります。  | 修正しようとしているパスが存在しません。
| For Operation(`PATCH_OPERATION_INDEX`): Node(`PATH`) to be replaced is absent. (Operation(`PATCH_OPERATION_INDEX`) の場合: 置き換える Node(`PATH`) が存在しません) | 修正しようとしているパスが存在しません。
| For Operation(`PATCH_OPERATION_INDEX`): Node(`PATH`) is not a number. (Operation(`PATCH_OPERATION_INDEX`) の場合: Node(`PATH`) が数字ではありません)| インクリメント操作は、整数と浮動でのみ機能します。 詳細情報: [サポートされている操作](partial-document-update.md#supported-operations)
| For Operation(`PATCH_OPERATION_INDEX`): Add Operation can only create a child object of an existing node(array or object) and cannot create path recursively, no path found beyond: `PATH`. (Operation(`PATCH_OPERATION_INDEX`) の場合: 追加操作では、既存のノード (配列またはオブジェクト) の子オブジェクトのみを作成できます。また、パスを再帰的に作成することはできません。`PATH` 以降にパスがありません) | 子パスは、オブジェクトまたは配列ノード型に追加できます。 また、`n` 番目の子を作成するには、`n-1` 番目の子が存在している必要があります 
| For Operation(`PATCH_OPERATION_INDEX`): Given Operation can only create a child object of an existing node(array or object) and cannot create path recursively, no path found beyond: `PATH`. (Operation(`PATCH_OPERATION_INDEX`) の場合: 指定の操作では、既存のノード (配列またはオブジェクト) の子オブジェクトのみを作成できます。また、パスを再帰的に作成することはできません。`PATH` 以降にパスがありません) | 子パスは、オブジェクトまたは配列ノード型に追加できます。 また、`n` 番目の子を作成するには、`n-1` 番目の子が存在している必要があります 

## <a name="next-steps"></a>次のステップ

- [部分的なドキュメント更新](partial-document-update.md)の概念的概要についての詳細情報
