---
title: "Azure Cosmos DB における Azure Table Storage サポート | Microsoft Docs"
description: "Azure Cosmos DB Table API と Azure Storage Tables を連携させる方法について説明します。"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: c0c8f1aee75c4ee5cc35758b71ef573637fd3edd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API と Azure Table Storage を使用した開発

Azure Cosmos DB Table API と Azure Table Storage は同じテーブル データ モデルを共有しており、SDK を介して同じ create、delete、update、およびクエリ操作が公開されています。 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API を使用した開発

現時点では、[Azure Cosmos DB Table API](table-introduction.md) には開発に使用できる 4 つの SDK があります。 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK。 このライブラリには公開されている [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage) と同じクラスおよびメソッド シグネチャがありますが、Table API を使用して Azure Cosmos DB アカウントに接続する機能もあります。 
- [Python SDK](table-sdk-python.md)。 新しい Azure Cosmos DB Python SDK は、Python で Azure Table Storage をサポートする唯一の SDK です。 この SDK は、Azure Table Storage と Azure Cosmos DB Table API の両方に接続します。
- [Java SDK](table-sdk-java.md)。 この Azure Storage SDK には、Table API を使用して Azure Cosmos DB アカウントに接続する機能があります。
- [Node.js SDK](table-sdk-nodejs.md)。 この Azure Storage SDK には、Table API を使用して Azure Cosmos DB アカウントに接続する機能があります。

Table API の使用に関するその他の情報は、[FAQ: Table API を使用した開発](faq.md#develop-with-the-table-api)の記事で入手できます。

## <a name="developing-with-the-azure-table-storage"></a>Azure Table Storage を使用した開発

[Azure Table Storage](table-storage-overview.md) では多くの SDK とチュートリアルが利用可能です。それらはすべて [Azure Table Storage](table-storage-overview.md) のセクションですぐに利用できます。 これらの記事は、Azure Table Storage SDK と Azure Cosmos DB Table API の間の相互運用性が利用可能になりしだい更新されます。  





