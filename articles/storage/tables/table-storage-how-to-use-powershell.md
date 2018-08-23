---
title: PowerShell を使用した Azure Table Storage 操作の実行 | Microsoft Docs
description: PowerShell を使用して Azure Table Storage 操作を実行します。
services: cosmos-db
author: robinsh
ms.service: cosmos-db
ms.topic: article
ms.date: 03/14/2018
ms.author: robinsh
ms.component: cosmosdb-table
ms.openlocfilehash: 577e6db0caeebb465025da033051dc2bbc9cb804
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143349"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Azure PowerShell を使用した Azure Table Storage 操作の実行 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage は NoSQL データストアであり、これを使用することで、構造化された非リレーショナル データの巨大なセットを格納および照会できます。 このサービスのメイン コンポーネントは、テーブル、エンティティ、プロパティです。 テーブルは、エンティティのコレクションです。 エンティティは、プロパティのセットです。 各エンティティには、最大 252 個のプロパティを含めることができます。これらはすべて名前と値のペアです。 この記事では、Azure Table Storage サービスの概念について理解しているユーザーを対象としています。 詳しくは、「[テーブル サービス データ モデルについて](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)」および「[.NET を使用して Azure Table Storage を使用する](../../cosmos-db/table-storage-how-to-use-dotnet.md)」をご覧ください。

このハウツー記事では、Azure Table Storage の一般的な操作について取り上げます。 学習内容は次のとおりです。 

> [!div class="checklist"]
> * テーブルを作成する
> * テーブルを取得する
> * テーブル エンティティを追加する
> * テーブルを照会する
> * テーブル エンティティを削除する
> * テーブルを削除する

このハウツー記事では、作業の完了後に簡単に削除できるように、新しいリソース グループに新しい Azure Storage アカウントを作成する方法について説明します。 既存のストレージ アカウントを使用したい場合はそうしてもかまいません。

例を実行するには、Azure PowerShell モジュール バージョン 4.4.0 以降が必要です。 PowerShell ウィンドウで、`Get-Module -ListAvailable AzureRM` を実行して、バージョンを確認します。 何も表示されない場合や、アップグレードが必要な場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 

Azure PowerShell をインストールまたは更新した後は、エンティティを管理するコマンドを含む **AzureRmStorageTable** モジュールをインストールする必要があります。 このモジュールをインストールするには、PowerShell を管理者として実行し、**Install-Module** コマンドを使用します。

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>場所の一覧を取得する

使用する場所がわからない場合、利用できる場所を一覧表示できます。 一覧が表示されたら、使用する場所を見つけます。 これらの例では、**eastus** を使用しています。 後で使用するために、この値を変数 **location** に保存します。

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) コマンドでリソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 将来使用するために、リソース グループ名を変数に保存します。 この例では、*pshtablesrg* という名前のリソース グループを *eastus* リージョンに作成しています。

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>[ストレージ アカウントの作成]

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) を利用し、ローカル冗長ストレージ (LRS) で標準の汎用ストレージ アカウントを作成します。 使用されるストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。 ストレージ アカウントで作業するとき、資格情報を繰り返し入力する代わりに、このコンテキストを参照します。

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>新しいテーブルを作成する

テーブルを作成するには、[New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable) コマンドレットを使用します。 この例では、テーブル名として `pshtesttable` を使用しています。

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>ストレージ アカウントのテーブルの一覧を取得する

[Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable) を使用して、ストレージ アカウントのテーブルの一覧を取得します。

```powershell
Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>特定のテーブルへの参照を取得する

テーブルに対して操作を実行するには、その特定のテーブルに対する参照が必要になります。 [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable) を使用して、参照を取得します。 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>テーブルを削除する

テーブルを削除するには、[Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable) を使用します。 このコマンドレットを実行すると、テーブルと、そのすべてのデータが削除されます。

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このハウツー記事に従って最初に新しいリソース グループとストレージ アカウントを作成している場合は、リソース グループを削除することで、この練習で作成したすべてのアセットを削除できます。 このコマンドを実行すると、リソース グループと、そのグループに含まれるすべてのリソースが削除されます。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次の手順

このハウツー記事では、次のような、PowerShell を使用した Azure Table Storage の一般的な操作について学習しました。 

> [!div class="checklist"]
> * テーブルを作成する
> * テーブルを取得する
> * テーブル エンティティを追加する
> * テーブルを照会する
> * テーブル エンティティを削除する
> * テーブルを削除する

詳細については、次の記事を参照してください。

* [Storage PowerShell コマンドレット](/powershell/module/azurerm.storage#storage)

* [PowerShell を使用した Azure Storage テーブルの操作](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
