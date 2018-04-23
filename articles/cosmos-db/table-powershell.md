---
title: PowerShell を使用した Azure Cosmos DB Table API 操作の実行 | Microsoft Docs
description: PowerShell を使用して Azure Cosmos DB Table API 操作を実行する方法
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d40a56ca3c07bfadcf6e24d407b059a39522ca2b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Azure PowerShell を使用した Azure Cosmos DB Table API 操作の実行 

>[!NOTE]
>Azure Cosmos DB Table API は、ターンキー グローバル配布、低待ち時間の読み取りと書き込み、自動セカンダリ インデックス付け、専用スループットなど、テーブル ストレージに対する高度な機能を提供します。 この記事の PowerShell コマンドは、ほとんどの場合、Azure Cosmos DB Table API と Azure Table Storage の両方で機能しますが、この記事は Azure Cosmos DB Table API に固有のものです。 Azure Table Storage を使用している場合は、「[Azure PowerShell を使用した Azure Table Storage 操作の実行](table-storage-how-to-use-powershell.md)」を参照してください。
>

Azure Cosmos DB Table API を使用すると、構造化された非リレーショナル データの巨大なセットを格納および照会できます。 このサービスのメイン コンポーネントは、テーブル、エンティティ、プロパティです。 テーブルは、エンティティのコレクションです。 エンティティは、プロパティのセットです。 各エンティティには、最大 252 個のプロパティを含めることができます。これらはすべて名前と値のペアです。 この記事では、Azure Cosmos DB Table API の概念について既に理解しているユーザーを対象としています。 詳細については、「[Azure Cosmos DB の Table API の概要](table-introduction.md)」および [Table API を使用した .NET アプリケーションの構築](create-table-dotnet.md)に関するページを参照してください。

このハウツー記事では、Table API の一般的な操作について取り上げます。 学習内容は次のとおりです。 

> [!div class="checklist"]
> * テーブルを作成する
> * テーブルを取得する
> * テーブル エンティティを追加する
> * テーブルを照会する
> * テーブル エンティティを削除する

## <a name="prerequisites"></a>前提条件

例を実行するには、Azure PowerShell モジュール バージョン 4.4.0 以降が必要です。 PowerShell ウィンドウで、`Get-Module -ListAvailable AzureRM` を実行して、バージョンを確認します。 何も表示されない場合や、アップグレードが必要な場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 

Azure PowerShell をインストールまたは更新した後は、エンティティを管理するコマンドを含む **AzureRmStorageTable** モジュールをインストールする必要があります。 このモジュールをインストールするには、PowerShell を管理者として実行し、**Install-Module** コマンドを使用します。

```powershell
Install-Module AzureRmStorageTable
```

これらの PowerShell コマンドレットを使用するためには、さらに Azure Cosmos DB のアセンブリをローカルにインストールします。 その方法については、[Cosmos DB テーブルの Azure RM Storage テーブル PowerShell モジュール](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/)に関するページを参照してください。

次の演習を試すには、Azure Cosmos DB データベース アカウントが必要です。 まだ持っていない場合は、[Azure Portal](https://portal.azure.com) を使用して新しい Azure Cosmos DB アカウントを作成します。 新しいデータベース アカウントの作成方法については、[Azure Cosmos DB のデータベース アカウントの作成](create-table-dotnet.md#create-a-database-account)に関するセクションを参照してください。

ポータルからデータベース アカウント名とリソース グループを取得します。テーブルにアクセスするには、これらの値をスクリプトに入力する必要があります。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>テーブルを作成する、またはテーブルを参照する

テーブルを作成する場合やテーブルへの参照を取得する場合は、**Get-AzureStorageTableTable** を使用します。 存在しないテーブルの名前を使用してこのコマンドレットを呼び出した場合、その名前のテーブルが新しく作成され、その新しいテーブルへの参照が返されます。 テーブルが存在する場合は、既存のテーブルへの参照が返されます。

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

PowerShell を使用して Azure Cosmos DB アカウント内のテーブルの一覧を表示することはできませんが、ポータルにサインインしてテーブルを確認することはできます。 次に、テーブル内のエンティティを管理する方法を見てみましょう。

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>テーブルを削除する 

PowerShell では、Azure Cosmos DB からのテーブルの削除はサポートされていません。 テーブルを削除するには、[Azure Portal](https://azure.portal.com) に移動し、使用している Azure Cosmos DB アカウントを見つけた後、テーブルを検索して削除します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

新しいリソース グループを作成し、そのグループに新しい Azure Cosmos DB アカウントを作成している場合は、リソース グループを削除することで、この練習で作成したすべてのアセットを削除できます。 このコマンドを実行すると、リソース グループと、そのグループに含まれるすべてのリソースが削除されます。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次の手順

このハウツー記事では、次のような、PowerShell を使用した Table API の一般的な操作について学習しました。 

> [!div class="checklist"]
> * テーブルを作成する
> * テーブルを取得する
> * テーブル エンティティを追加する
> * テーブルを照会する
> * テーブル エンティティを削除する

詳細については、次の記事を参照してください。

* [PowerShell を使用した Azure Storage テーブルの操作](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
