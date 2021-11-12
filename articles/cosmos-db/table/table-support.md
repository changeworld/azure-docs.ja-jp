---
title: Azure Cosmos DB における Azure Table Storage サポート
description: 同じテーブル データ モデルと操作を共有して Azure Cosmos DB Table API と Azure Storage Tables を連携させる方法について説明します
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 11/03/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: d0039773cc1ddf50d3c34466d3480ecbe753cf0e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555592"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API と Azure Table Storage を使用した開発
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Azure Cosmos DB Table API と Azure Table Storage は同じテーブル データ モデルを共有しており、SDK を介して同じ create、delete、update、およびクエリ操作が公開されています。

> [!NOTE]
> Azure Cosmos DB の Table API で[サーバーレス容量モード](../serverless.md)が利用できるようになりました。

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="azure-sdks"></a>Azure SDK

### <a name="current-release"></a>現在のリリース

次の SDK パッケージが、Azure Cosmos Table API と Azure Table Storage の両方で機能します。

* **.NET** - NuGet で入手できる [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) を使用します。

* **Python** - PyPi から入手できる [azure-data-tables](https://pypi.org/project/azure-data-tables/) を使用します。

* **JavaScript/TypeScript** - npm.js で入手できる [@azure/data-tables](https://www.npmjs.com/package/@azure/data-tables) パッケージを使用します。  

* **Java** - Maven で入手できる [azure-data-tables](https://mvnrepository.com/artifact/com.azure/azure-data-tables/12.0.0) パッケージを使用します。

### <a name="prior-releases"></a>以前のリリース

次の SDK パッケージは、Azure Cosmos DB Table API でのみ動作します。

* **.NET** -  NuGet で入手できる [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/)。  Azure Tables クライアント ライブラリは、コードを変更しない Azure Table Storage または Azure Cosmos DB table service エンドポイントをシームレスにターゲットにできます。

* **Python** - PyPi から入手できる [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/)。 この SDK は、Azure Table Storage と Azure Cosmos DB Table API の両方に接続します。

* **JavaScript/TypeScript** - npm.js で入手できる [azure-storage](https://www.npmjs.com/package/azure-storage) パッケージ。 この Azure Storage SDK には、Table API を使用して Azure Cosmos DB アカウントに接続する機能があります。

* **Java** - Maven の [Java 用 Microsoft Azure Storage クライアント SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)。 この Azure Storage SDK には、Table API を使用して Azure Cosmos DB アカウントに接続する機能があります。

* **C++**   - [C++ 用 Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp/)。 このライブラリでは、Azure Storage 用のアプリケーションを作成できます。

* **Ruby** - [Ruby 用 Azure Storage Table クライアント ライブラリ](https://github.com/azure/azure-storage-ruby/tree/master/table)。 このプロジェクトは、Azure Table Storage service に間単にアクセスできる Ruby パッケージを提供します。

* **PHP** - [Azure Storage Table PHP クライアント ライブラリ](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)。 このプロジェクトは、Azure Table Storage service に間単にアクセスできる PHP クライアント ライブラリを提供します。

* **PowerShell** - [AzureRmStorageTable PowerShell モジュール](https://www.powershellgallery.com/packages/AzureRmStorageTable)。 この PowerShell モジュールには、ストレージ テーブルを使用するためのコマンドレットがあります。
