---
title: Azure Cosmos DB における Azure Table Storage サポート | Microsoft Docs
description: Azure Cosmos DB Table API と Azure Storage Tables を連携させる方法について説明します。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 114286b45df5f47e81bd2b990c8b50c8b7b7a482
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43185368"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API と Azure Table Storage を使用した開発

Azure Cosmos DB Table API と Azure Table Storage は同じテーブル データ モデルを共有しており、SDK を介して同じ create、delete、update、およびクエリ操作が公開されています。 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API を使用した開発

現時点では、[Azure Cosmos DB Table API](table-introduction.md) には開発に使用できる 4 つの SDK があります。 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK。 このライブラリには公開されている [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage) と同じクラスおよびメソッド シグネチャがありますが、Table API を使用して Azure Cosmos DB アカウントに接続する機能もあります。 `Microsoft.Azure.CosmosDB.Table` ライブラリは現在 .NET Standard のみで利用でき、まだ .NET Core では利用できないことに注意してください。
- [Python SDK](table-sdk-python.md)。 新しい Azure Cosmos DB Python SDK は、Python で Azure Table Storage をサポートする唯一の SDK です。 この SDK は、Azure Table Storage と Azure Cosmos DB Table API の両方に接続します。
- [Java SDK](table-sdk-java.md)。 この Azure Storage SDK には、Table API を使用して Azure Cosmos DB アカウントに接続する機能があります。
- [Node.js SDK](table-sdk-nodejs.md)。 この Azure Storage SDK には、Table API を使用して Azure Cosmos DB アカウントに接続する機能があります。

Table API の使用に関するその他の情報は、[FAQ: Table API を使用した開発](faq.md#table)の記事で入手できます。

## <a name="developing-with-azure-table-storage"></a>Azure Table Storage を使用した開発

Azure Table Storage では以下の SDK を開発に使用できます。

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)。 このライブラリでは、Storage Table service を使うことができます。
- [Python SDK](table-sdk-python.md)。 Azure Cosmos DB Table SDK for Python も Storage Table service をサポートしています。
- [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java)。 この Azure Storage SDK は、Azure Table Storage を使用するためのクライアント ライブラリを Java で提供します。
- [Node.js SDK](table-sdk-nodejs.md)。 この SDK は、Storage Table service を使用するための Node.js パッケージとブラウザー互換の JavaScript クライアント ライブラリを提供します。
- [AzureRmStorageTable PowerShell モジュール](https://www.powershellgallery.com/packages/AzureRmStorageTable/1.0.0.7)。 この PowerShell モジュールには、ストレージ テーブルを使用するためのコマンドレットがあります。
- [C++ 用 Azure Storage クライアント ライブラリリ](https://github.com/Azure/azure-storage-cpp/)。 このライブラリでは、Azure Storage 用のアプリケーションを作成できます。
- [Ruby 用 Azure Storage Table クライアント ライブラリ](https://github.com/azure/azure-storage-ruby/tree/master/table)。 このプロジェクトは、Azure Table Storage service に間単にアクセスできる Ruby パッケージを提供します。
- [Azure Storage Table PHP クライアント ライブラリ](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)。 このプロジェクトは、Azure Table Storage service に間単にアクセスできる PHP クライアント ライブラリを提供します。


   





