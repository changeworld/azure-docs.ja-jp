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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API と Azure Table Storage を使用した開発

Azure Cosmos DB Table API と Azure Table Storage は同じテーブル データ モデルを共有しており、SDK を介して同じ create、delete、update、およびクエリ操作が公開されています。 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API を使用した開発

現時点で [Azure Cosmos DB Table API](table-introduction.md) には、.NET SDK で利用可能な唯一の [Microsoft Azure Storage Premium Table (プレビュー)](https://aka.ms/premiumtablenuget) があります。 このライブラリには、公開されている [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage) と同じクラスおよびメソッド シグネチャが含まれますが、Table API (プレビュー) を使用して Azure Cosmos DB アカウントに接続する機能もあります。 Azure Cosmos DB Table API の使用に関するクイックスタートとチュートリアルについては、次の記事をご覧ください。
- クイックスタート: [Azure Cosmos DB: Table API を使用した .NET アプリケーションの構築](create-table-dotnet.md)
- チュートリアル: [Azure Cosmos DB: .NET での Table API を使用した開発](tutorial-develop-table-dotnet.md)

Table API の使用に関するその他の情報は、[FAQ: Table API を使用した開発](faq.md#develop-with-the-table-api-preview)の記事で入手できます。

## <a name="developing-with-the-azure-table-storage"></a>Azure Table Storage を使用した開発

[Azure Table Storage](table-storage-overview.md) では多くの SDK とチュートリアルが利用可能です。それらはすべて [Azure Table Storage](table-storage-overview.md) のセクションですぐに利用できます。 これらの記事は、Azure Table Storage SDK と Azure Cosmos DB Table API の間の相互運用性が利用可能になりしだい更新されます。  





