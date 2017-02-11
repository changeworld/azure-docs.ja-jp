---
title: "DocumentDB でデータベースを作成する方法 | Microsoft Docs"
description: "きわめて高速なグローバル スケールの NoSQL データベースである Azure DocumentDB のオンライン サービス ポータルを使用してデータベースを作成する方法について説明します。"
keywords: "データベースの作成方法"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 2503db56-744f-44b5-bf6a-0be821d023ea
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: mimig
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5d93b218a61b325b6c7bd02a160bd2cae844341



---
# <a name="how-to-create-a-database-for-documentdb-using-the-azure-portal"></a>Azure ポータルを使用して DocumentDB のデータベースを作成する方法
Microsoft Azure DocumentDB を使用するには、 [DocumentDB アカウント](documentdb-create-account.md)、データベース、コレクション、およびドキュメントを用意する必要があります。 Azure Portal では、コレクションの作成と同時に DocumentDB データベースも作成されるようになりました。 

ポータルで DocumentDB データベースとコレクションを作成する方法については、「[Azure ポータルを使用して DocumentDB コレクションを作成する方法](documentdb-create-collection.md)」を参照してください。

## <a name="other-ways-to-create-a-documentdb-database"></a>DocumentDB データベースを作成するその他の方法
データベースは必ずしもポータルを使用して作成する必要はなく、[DocumentDB SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/mt489072.aspx) を使用して作成することもできます。 .NET SDK を使用してデータベースを操作する方法については、「 [データベースのサンプル](documentdb-dotnet-samples.md#database-examples)」を参照してください。 Node.js SDK を使用してデータベースを操作する方法については、「 [データベースのサンプル](documentdb-nodejs-samples.md#database-examples)」を参照してください。 

## <a name="next-steps"></a>次のステップ
データベースとコレクションを作成できたら、ポータルのドキュメント エクスプローラーを使用して [JSON ドキュメントを追加](documentdb-view-json-document-explorer.md)したり、DocumentDB データ移行ツールを使用してコレクションに[ドキュメントをインポート](documentdb-import-data.md)したり、いずれかの [DocumentDB SDK](documentdb-sdk-dotnet.md) を使用して CRUD 操作を実行できるようになります。 DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。 ドキュメントを作成、削除、更新、削除する方法が紹介されている .NET コード サンプルについては、「 [ドキュメントのサンプル](documentdb-dotnet-samples.md#document-examples)」を参照してください。 Node.js SDK を使用してドキュメントを操作する方法については、「 [ドキュメントのサンプル](documentdb-nodejs-samples.md#document-examples)」を参照してください。 

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](documentdb-sdk-dotnet.md) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#executing-sql-queries)できます。 




<!--HONumber=Nov16_HO3-->


