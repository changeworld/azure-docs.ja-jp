---
title: Azure Cosmos DB でのマルチマスターの競合解決
description: この記事では、Azure Comsos DB マルチマスターでの Last-Writer-Wins (LWW)、カスタム - ユーザー定義プロシージャ、カスタム - 非同期などの競合カテゴリ モードと競合解決モードについて説明します。
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 39fd393e78a2b66749c6aa34a758b185b38effdf
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041189"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Azure Cosmos DB でのマルチマスターの競合解決 

Azure Cosmos DB マルチマスターには、レプリケートされているすべてのリージョンでデータの一貫性を確保するために、グローバルな競合解決モードが用意されています。

## <a name="conflict-categories"></a>競合カテゴリ

Azure Cosmos DB データを使用する際に発生する可能性がある競合には、次の 3 つのカテゴリがあります。

* **挿入の競合:** この種類の競合は、アプリケーションが同じ一意のインデックス (ID など) を持つ複数のドキュメントを複数のリージョンから同時に挿入した場合に発生します。 この場合、最初はすべての書き込みが成功する可能性がありますが、選択した競合解決ポリシーに基づいて、元の ID を持つ 1 つのドキュメントのみがコミットされます。

* **置換の競合:** この種類の競合は、アプリケーションが複数のリージョンから 1 つのドキュメントを同時に更新する場合に発生します。

* **削除の競合:** この種類の競合は、アプリケーションが 1 つのリージョンからドキュメントを削除し、1 つ以上のリージョンからドキュメントを更新する場合に発生します。 

## <a name="conflict-resolution-modes"></a>競合解決モード

Azure Cosmos DB には、3 つの競合解決モードがあります。 競合解決モードは、各コレクションに対して定義されています。

> [!NOTE]
> SQL API ユーザーは、3 種類の競合解決モードから選択できます。 他のすべての API モデル (MongoDB、Cassandra、Graph、および Table) では、Last-Writer Wins を使用して競合が解決されます。

### <a name="last-writer-wins-lww"></a>Last-Writer-Wins (LWW)

Last-Writer-Wins (最終書き込み者優先) は既定の競合解決モードです。 このモードでは、ドキュメントのプロパティで渡された数値に基づいて競合が解決されます。

次のコード スニペットは、.Net SDK を使用する場合に Last-Writer-Wins の競合解決ポリシーを構成する方法の例です。 "ConflictResolutionPath" は、競合の解決に使用されるプロパティへのパスを定義します。 この例で、`/userDefinedId` は競合解決パスであり、最大の `userDefinedId` 値を持つドキュメントが常に競合に勝ちます。 Last-Writer-Wins 解決モードを登録するには、次のように ConflictResolutionPolicy を使用してコレクションをプロビジョニングします。

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 Last-Writer-Wins 解決モードは、次のようにさまざまなデータ競合カテゴリに適用されます。

* **挿入と更新の競合:** 挿入操作または置換操作で複数のドキュメントが競合した場合、競合解決パスの最大値を含むドキュメントが勝者 (つまり userDefinedId) になります。 複数のドキュメントが競合解決パスに同じ数値を持つ場合、選択される勝者は非決定論的です。 ただし、すべてのリージョンが単一の勝者に収束されます。

* **削除の競合:** 削除の競合がある場合、競合解決パスの値に関係なく、常に他の置換の競合よりも削除が優先されます。

### <a name="custom--user-defined-procedure"></a>カスタム - ユーザー定義プロシージャ

このモードでは、ユーザー定義プロシージャ (UDP) をコレクションに登録することで競合解決を制御します。 この UDP には特定の署名があります。 このオプションを選択して UDP の登録に失敗した場合、または UDP が実行時に例外をスローした場合、競合は競合フィードに書き込まれ、そこで個別に競合が解決されます。

カスタム - ユーザー定義プロシージャ解決モードを登録するには、次のように ConflictResolutionPolicy を使用してコレクションをプロビジョニングします。

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

次に、以下のようにユーザー定義プロシージャをコレクションに追加します。

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

コレクションに登録されたストアド プロシージャには、特殊なシグネチャがあります。 以下の例では、UDP は競合を解決するためにプロパティ `UserDefinedId` を使用しています。 最大値を持つドキュメントが競合に勝ちます。

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

この手順には 4 つのパラメーターがあります。

* **incomingDocument:** ドキュメントの競合するバージョンを指定します。 競合には、挿入、置換、または削除の競合があります。 削除の競合の場合、これはコンテンツのない削除イメージ (廃棄標識) になります。

* **existingDocument:** incomingDocument と同じ "rid" 値を持つドキュメントのコミット済みバージョンを指定します。 このパラメーターは、挿入と削除の競合の場合には null 値に設定されます。

* **isDeleteConflict:** 受信ドキュメントが以前に削除されたドキュメントと競合しているかどうかを示すブール値のフラグ。 このパラメーターが true に設定されている場合、existingDocument も null になります。

* **conflictingDocuments:** データベース内のすべてのドキュメントのコミット済みバージョンのコレクションを指定します。これは、ID 列またはその他の一意のインデックス フィールドの incomingDocument と競合します。 incomingDocument と比較すると、これらのドキュメントは異なる "rid" 値を持ちます。

ユーザー定義プロシージャーは、Cosmos DB パーティション キーへのフル アクセス権を持ち、競合を解決するすべてのストア操作を実行できます。 ユーザー定義プロシージャが競合バージョンをコミットしない場合、システムは競合を破棄し、existingDocument がコミットされたままになります。 ユーザー定義プロシージャが失敗するか存在しない場合、[非同期競合解決モード](#custom--asynchronous)のように、Azure Cosmos DB は非同期で処理できる読み取り専用競合フィードに競合をすべて追加します。 

### <a name="custom--asynchronous"></a>カスタム - 非同期  

このモードでは、Azure Cosmos DB はすべての競合 (挿入、置換、および削除) をコミット対象から除外し、ユーザーのアプリケーションによる遅延解決のために読み取り専用競合フィードに登録します。 アプリケーションは、競合解決を非同期で実行し、任意のロジックを使用するか、外部ソース、アプリケーション、またはサービスを参照して競合を解決することができます。

カスタム - 非同期解決モードを登録するには、次のように ConflictResolutionPolicy を使用してコレクションをプロビジョニングします。

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

競合フィードの競合を読み取って処理するには、次のようにコードを実装します。 競合フィードに格納されたデータには、ある程度のストレージ コストがかかります。 そのため、処理後に競合フィードに格納されているデータを削除することをお勧めします。

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> 競合フィードには、Cosmos DB の変更フィードなどのダウンストリーム処理に関する通知を送信するリスナーは含まれません。 競合フィードをポーリングし、競合が存在するかどうかを判断するロジックを実装する必要があります。

## <a name="code-samples"></a>コード サンプル

以下は、表示されている API の競合解決を示すサンプル アプリケーションです。 各サンプルは、コンテナー内に競合を生成し、サポートされている競合解決モードごとに競合がどのように解決されるかを示しています。

|API モデル  | SDK |サンプル |
|---------|---------|---------|
|SQL API    | .NET    |[azure-cosmos-db-sql-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|SQL API    | ノード    |[azure-cosmos-js/samples/MultiRegionWrite/](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|SQL API    | Java    |[azure-cosmos-db-sql-java-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |
|MongoDB  | .NET    |[azure-cosmos-db-mongodb-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|Table API  | .NET    |[azure-cosmos-db-table-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|Gremlin API | .NET | [azure-cosmos-db-gremlin-dontnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dontnet-multi-master)|

## <a name="next-steps"></a>次の手順

この記事では、Azure Cosmos DB の競合カテゴリと競合解決モードについて説明しました。 次に、以下のリソースをご覧ください。

* [マルチマスターでの MongoDB クライアントの使用](multi-master-oss-nosql.md)
