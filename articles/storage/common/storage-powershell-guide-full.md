---
title: "Azure Storage での Azure PowerShell の使用 | Microsoft Docs"
description: "Azure Storage で Azure PowerShell コマンドレットを使用する方法について説明します。"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: robinsh
ms.openlocfilehash: a116b4c15046e704e374ca67c5695ff3f01ba7fb
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2017
---
# <a name="using-azure-powershell-with-azure-storage"></a>Azure Storage での Azure PowerShell の使用

Azure PowerShell は、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 Azure Storage では、これらのコマンドレットは 2 つのカテゴリ (コントロール プレーンとデータ プレーン) に分類されます。 コントロールプレーンのコマンドレットは、ストレージ アカウントを管理する (ストレージ アカウントの作成、プロパティの設定、ストレージ アカウントの削除、アクセス キーの交換など) ために使用します。 データ プレーンのコマンドレットは、ストレージ アカウント*に*格納されたデータを管理するために使用します。 たとえば、BLOB のアップロード、ファイル共有の作成、キューへのメッセージの追加などです。

このハウツー記事では、管理プレーンのコマンドレットを使用してストレージ アカウントを管理する際の一般的な操作について説明します。 学習内容は次のとおりです。 

> [!div class="checklist"]
> * ストレージ アカウントの列挙
> * 既存のストレージ アカウントへの参照の取得
> * ストレージ アカウントの作成 
> * ストレージ アカウント プロパティの設定
> * アクセス キーの取得および再生成
> * ストレージ アカウントへのアクセスの保護 
> * Storage Analytics の有効化

また、Storage Analyics を有効にしてアクセスする方法やデータ プレーンのコマンドレットを使用する方法など、ストレージに関するその他の PowerShell 記事のリンクも紹介します。
<!-- also how to access the china and government clouds  -->

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

この演習には、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 

この演習では、通常の PowerShell ウィンドウにコマンドを入力するか、または [Windows PowerShell Integrated Scripting Environment (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) を使用してエディターにコマンドを入力してから、それぞれの例で 1 つ以上のコマンドを同時にテストできます。 実行する行を強調表示して [選択項目の実行] をクリックし、それらのコマンドだけを実行できます。

ストレージ アカウントの詳細については、「[Microsoft Azure Storage の概要](storage-introduction.md)」および「[Azure ストレージ アカウントについて](storage-create-storage-account.md)」を参照してください。

## <a name="log-in-to-azure"></a>Azure へのログイン

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>サブスクリプションのストレージ アカウントの列挙

[Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) コマンドレットを実行して、現在のサブスクリプションのストレージ アカウントのリストを取得します。 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>ストレージ アカウントへの参照の取得

続いて、ストレージ アカウントへの参照が必要になります。 新しいストレージ アカウントを作成することも、既存のストレージ アカウントへの参照を取得することもできます。 以降のセクションでは、その両方の方法を示します。 

### <a name="use-an-existing-storage-account"></a>既存のストレージ アカウントの使用 

既存のストレージ アカウントを取得するには、リソース グループの名前とストレージ アカウントの名前が必要です。 これらの 2 つのフィールドに変数を設定して、[Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) コマンドレットを使用します。 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

これで、既存のストレージ アカウントを参照する $storageAccount が取得されました。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

次のスクリプトは、[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) を使用して汎用のストレージ アカウントを作成する方法を示しています。 アカウントを作成したら、そのコンテキストを取得して、後続のコマンドで使用できます。各呼び出しで認証を指定する必要はありません。

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard\_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

このスクリプトでは、次の PowerShell コマンドレットを使用します。 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) -- 有効な場所のリストを取得します。 この例では、`eastus` を場所として使用します。

*   [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) -- 新しいリソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この例では、`teststoragerg` を使用します。 

*   [New-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) -- 実際のストレージ アカウントを作成します。 この例では、`testpshstorage` を使用します。

SKU 名は、LRS (ローカル冗長ストレージ) など、ストレージ アカウントのレプリケーションの種類を示します。 レプリケーションの詳細については、「[Azure Storage のレプリケーション](storage-redundancy.md)」を参照してください。

> [!IMPORTANT]
> ストレージ アカウントの名前は Azure 内で一意であり、小文字でなければなりません。 名前付け規則および制限については、「[コンテナー、BLOB、およびメタデータの名前付け](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)」を参照してください。
> 

これで、新しいストレージ アカウントとそのアカウントへの参照が作成されました。 

## <a name="managing-the-storage-account"></a>ストレージ アカウントの管理

新しいストレージ アカウントまたは既存のストレージ アカウントへの参照を取得したので、次のセクションではストレージ アカウントを管理するためのコマンドを示します。

### <a name="storage-account-properties"></a>ストレージ アカウント プロパティ

ストレージ アカウントの設定を変更するには、[Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount) を使用します。 ストレージ アカウントの場所や格納されているリソース グループを変更することはできませんが、その他の多くのプロパティを変更できます。 PowerShell を使用して変更できるいくつかのプロパティを次に示します。

* ストレージ アカウントに割り当てられている**カスタム ドメイン**。

* ストレージ アカウントに割り当てられている**タグ**。 多くの場合、タグは課金のためのリソースの分類に使用されます。

* **SKU** は、LRS (ローカル冗長ストレージ) など、ストレージ アカウントのレプリケーション設定です。 たとえば、Standard\_LRS を Standard\_GRS または Standard\_RAGRS に変更できます。 Standard ZRS や Premium LRS をその他の SKU に変更したり、その他の SKU を Standard ZRS や Premium LRS に変更したりすることはできません。 

* Blob Storage アカウントの**アクセス層**。 アクセス層の値は**ホット**または**クール**に設定されます。この値を使用すると、ストレージ アカウントの使用方法に合致したアクセス層を選択してコストを最小限に抑えることができます。 詳細については、「[Azure Blob Storage: ホット、クール、およびアーカイブ (プレビュー) ストレージ層](../blobs/storage-blob-storage-tiers.md)」を参照してください。

* Blob Storage や File Storage の Storage Service Encryption の設定。 SSE の詳細については、「[Azure Storage Service Encryption for Data at Rest](storage-service-encryption.md)」を参照してください。

* HTTPS トラフィックのみを許可。 

### <a name="managing-the-access-keys"></a>アクセス キーの管理

Azure Storage アカウントには 2 つのアカウント キーがあります。 キーを取得するには、[Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey) を使用します。 この例では、最初のキーを取得します。 もう 1 つのキーを取得するには、`Value[0]` の代わりに `Value[1]` を使用します。

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

キーを再生成するには、[New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey) を使用します。 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

もう 1 つのキーを再生成するには、キー名として `key1` の代わりに `key2` を使用します。

いずれかのキーを再生成してもう一度取得し、新しい値を確認します。

> [!NOTE] 
> 運用環境のストレージ アカウントのキーを再生成する前には、入念な計画が必要です。 一方または両方のキーを再生成すると、再生成されたキーを使用するアプリケーションのアクセスが無効になります。 詳細については、「[ストレージ アクセス キーの再生成](storage-create-storage-account.md#regenerate-storage-access-keys)」を参照してください。


### <a name="delete-a-storage-account"></a>ストレージ アカウントの削除 

ストレージ アカウントを削除するには、[Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount) を使用します。 

> [!IMPORTANT]
> ストレージ アカウントを削除すると、そのアカウントに格納されている資産もすべて削除されます。 アカウントを誤って削除した場合は、すぐにサポートに問い合わせてストレージ アカウントを復元するチケットをオープンしてください。 データの復旧は保証されませんが、この対処が有効な場合もあります。 サポート チケットが解決されるまでは、古いストレージ アカウントと同じ名前の新しいストレージ アカウントを作成しないでください。 
>

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

### <a name="protecting-your-storage-account-using-vnets-and-firewalls"></a>VNet とファイアウォールを使用したストレージ アカウントの保護

既定では、インターネットにアクセス可能なネットワークからすべてのストレージ アカウントにアクセスできます。 ただし、ストレージ アカウントへのアクセスを特定の仮想ネットワークのアプリケーションにのみ許可するようにネットワーク ルールを構成できます。 詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークの構成 (プレビュー)](storage-network-security.md)」を参照してください。 

この記事では、次の PowerShell コマンドレットを使用してこれらの設定を管理する方法を示しています。
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="using-storage-analytics"></a>Storage Analytics の使用  

[Azure Storage Analytics](storage-analytics.md) は、[Storage Analytics Metrics](/rest/api/storageservices/about-storage-analytics-metrics) と [Storage Analytics Logging](/rest/api/storageservices/about-storage-analytics-logging) で構成されます。 

**Storage Analytics Metrics** は、Azure ストレージ アカウントのメトリックを収集するために使用します。このメトリックを使用して、ストレージ アカウントのヘルスを監視できます。 BLOB、ファイル、テーブル、およびキューに対してメトリックを有効にすることができます。

**Storage Analytics Logging** はサーバー側で発生し、ストレージ アカウントに対する成功した要求と失敗した要求の両方について詳細を記録することが可能になります。 これらのログを使用すると、読み取り、書き込み、削除の各操作の詳細、失敗した要求の原因を確認できます。 Logging は、Azure Files では使用できません。

[Azure Portal](https://portal.azure.com) または PowerShell を使用して監視を構成できます。また、ストレージ クライアント ライブラリを使用したプログラムで監視を構成することもできます。 

> [!NOTE]
> PowerShell を使用して詳細な分析を有効にすることができます。 この機能は Portal で使用できません。
>

* PowerShell を使用し、ストレージ メトリックのデータを有効にして表示する方法については、「[Azure のストレージ メトリックの有効化とメトリック データの表示](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell)」を参照してください。

* PowerShell を使用してストレージ ログを有効にする方法とログ データを取得する方法については、「[PowerShell を使用してストレージ ログを有効にする方法](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell)」および「[ストレージ ログのログ データの検索](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data)」をご覧ください。

* ストレージ メトリックとストレージ ログを使用してストレージの問題のトラブルシューティングを行う方法の詳細については、 [Microsoft Azure Storage の監視、診断、トラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)に関するページを参照してください。

## <a name="managing-the-data-in-the-storage-account"></a>ストレージ アカウントのデータの管理

PowerShell によるストレージ アカウントの管理方法について確認したので、以下の記事では、PowerShell を使用してストレージ アカウントのデータ オブジェクトにアクセスする方法を示します。

* [Azure PowerShell を使用して Azure Blob Storage の操作を実行する](../blobs/storage-how-to-use-blobs-powershell.md)
* [PowerShell を使用して Azure Files を管理する方法](../files/storage-how-to-use-files-powershell.md)
* [Azure PowerShell を使用し、Azure Queue Storage を操作する](../queues/storage-powershell-how-to-use-queues.md)

<!--## Government Cloud and China Cloud

ROBINROBINROBIN 

To access the Government cloud of the China datacenters, you have to use some special steps. The following article shows how to access these special cloud accounts using PowerShell.

* [How to manage storage accounts in Government Cloud and China](storage-powershell-govt-china.md)
-->

## <a name="next-steps"></a>次のステップ

このハウツー記事では、管理プレーンのコマンドレットを使用してストレージ アカウントを管理する際の一般的な操作について説明します。 学習内容は次のとおりです。 

> [!div class="checklist"]
> * ストレージ アカウントの列挙
> * 既存のストレージ アカウントへの参照の取得
> * ストレージ アカウントの作成 
> * ストレージ アカウント プロパティの設定
> * アクセス キーの取得および再生成
> * ストレージ アカウントへのアクセスの保護 
> * Storage Analytics の有効化

データ オブジェクトを管理する方法や Storage Analytics を有効にする方法など、その他の記事のリンクも紹介します。 参照用のその他の関連記事とリソースがあります。 
<!--, and how to access storage with PowerShell using the Government Cloud and the China Cloud.
-->

* [Azure Storage のコントロール プレーンの PowerShell コマンドレット](/powershell/module/AzureRM.Storage/)
* [Azure Storage のデータ プレーンの PowerShell コマンドレット](/powershell/module/azure.storage/)
* [Windows PowerShell リファレンス](https://msdn.microsoft.com/library/ms714469.aspx)