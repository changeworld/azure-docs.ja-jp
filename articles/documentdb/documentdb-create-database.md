---
redirect_url: https://azure.microsoft.com/services/cosmos-db/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a6b0cd10c13622bf0d5af5d5871457204de37eb1
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017



---
# <a name="how-to-create-a-database-for-azure-cosmos-db-using-the-azure-portal"></a>Azure Portal を使用して Azure Cosmos DB のデータベースを作成する方法
Microsoft Azure Cosmos DB を使用するには、[Azure Cosmos DB アカウント](documentdb-create-account.md)、データベース、コレクション、ドキュメントを用意する必要があります。 Azure Portal では、コレクションの作成と同時に Azure Cosmos DB データベースも作成されるようになりました。 

ポータルで Azure Cosmos DB のデータベースとコレクションを作成する方法については、[Azure Portal を使用して Azure Cosmos DB コレクションを作成する方法](documentdb-create-collection.md)に関する記事をご覧ください。

## <a name="other-ways-to-create-an-azure-cosmos-db-database"></a>Azure Cosmos DB データベースのその他の作成方法
データベースは必ずしもポータルを使用して作成する必要はなく、[DocumentDB SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/mt489072.aspx) を使用して作成することもできます。 .NET SDK を使用してデータベースを操作する方法については、「 [データベースのサンプル](documentdb-dotnet-samples.md#database-examples)」を参照してください。 Node.js SDK を使用してデータベースを操作する方法については、「 [データベースのサンプル](documentdb-nodejs-samples.md#database-examples)」を参照してください。 

## <a name="next-steps"></a>次のステップ
データベースとコレクションを作成できたら、ポータルのドキュメント エクスプローラーを使用して [JSON ドキュメントを追加](documentdb-view-json-document-explorer.md)したり、DocumentDB データ移行ツールを使用してコレクションに[ドキュメントをインポート](documentdb-import-data.md)したり、いずれかの [DocumentDB SDK](documentdb-sdk-dotnet.md) を使用して CRUD 操作を実行できるようになります。 DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。 ドキュメントを作成、削除、更新、削除する方法が紹介されている .NET コード サンプルについては、「 [ドキュメントのサンプル](documentdb-dotnet-samples.md#document-examples)」を参照してください。 Node.js SDK を使用してドキュメントを操作する方法については、「 [ドキュメントのサンプル](documentdb-nodejs-samples.md#document-examples)」を参照してください。 

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](documentdb-sdk-dotnet.md) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#ExecutingSqlQueries)できます。 


