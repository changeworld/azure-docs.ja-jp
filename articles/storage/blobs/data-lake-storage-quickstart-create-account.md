---
title: Azure Data Lake Storage Gen2 ストレージ アカウントを作成する | Microsoft Docs
description: Azure portal、Azure PowerShell、または Azure CLI を使用して、Data Lake Storage Gen2 にアクセスできる新しいストレージ アカウントを作成する方法をすばやく学習します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e8deb8ed16186862349cecf70c9d617a4ad30399
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806900"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Azure Data Lake Storage Gen2 ストレージ アカウントを作成する

Azure Data Lake Storage Gen2 では、Hadoop 分散ファイル システム (HDFS) と連携するように調整された、ネイティブ ディレクトリベースのコンテナーを提供する[階層型名前空間がサポート](data-lake-storage-introduction.md)されています。 HDFS から Data Lake Storage Gen2 データにアクセスするには、[ABFS ドライバー](data-lake-storage-abfs-driver.md)を使用します。

この記事では、Azure portal、Azure PowerShell、または Azure CLI を使用して、アカウントを作成する方法を説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 

|           | 前提条件 |
|-----------|--------------|
|ポータル     | なし         |
|PowerShell | この記事には、PowerShell モジュールの Az.Storage バージョン **0.7** 以降が必要です。 現在のバージョンを調べるには、`Get-Module -ListAvailable Az.Storage` コマンドを実行します。 このコマンドを実行した後に結果が表示されない場合、または **0.7** より前のバージョンが表示された場合は、次の powershell モジュールをアップグレードする必要があります。 このガイドの「[PowerShell モジュールのアップグレード](#upgrade-your-powershell-module)」セクションを参照してください。
|CLI        | 次の 2 つの方法のいずれかで Azure にサインインし、Azure CLI コマンドを実行できます。 <ul><li>Azure Portal 内から、Azure Cloud Shell で CLI コマンドを実行できます </li><li>CLI をインストールして、CLI コマンドをローカルで実行できます</li></ul>|

コマンド ラインを使用する場合は、Azure Cloud Shell を実行するか、CLI をローカルにインストールするオプションがあります。

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell を使用する

Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Azure Portal の右上のメニューの **[Cloud Shell]** ボタンをクリックします。

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

このボタンによって対話型のシェルが起動されます。この記事の手順はこれを使って実行できます。

[![ポータルに Cloud Shell のウィンドウが表示されるスクリーンショット](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>CLI をローカルにインストールする

Azure CLI はローカルにインストールして使用することもできます。 この記事では、Azure CLI バージョン 2.0.38 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Azure Data Lake Storage Gen2 対応のストレージ アカウントを作成する

Azure ストレージ アカウントには、すべての Azure Storage データ オブジェクト (BLOB、ファイル、キュー、テーブル、およびディスク) が含まれます。 ストレージ アカウントでは、世界中のどこからでも HTTP または HTTPS 経由でアクセスできる Azure Storage データ用の一意の名前空間が提供されます。 Azure ストレージ アカウント内のデータは、持続性があり、高可用性で、セキュリティ保護されており、非常にスケーラブルです。

> [!NOTE]
> Data Lake Storage Gen2 機能を利用するには、作成する新しいストレージ アカウントの種類を **StorageV2 (汎用 V2)** にする必要があります。  

ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](../common/storage-account-overview.md)」をご覧ください。

## <a name="create-an-account-using-the-azure-portal"></a>Azure portal を使用してアカウントを作成する

[Azure Portal](https://portal.azure.com) にサインインします。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

すべてのストレージ アカウントは、Azure リソース グループに属している必要があります。 リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。 この記事では、新しいリソース グループを作成する方法を示します。

Azure Portal で汎用 v2 ストレージ アカウントを作成するには、次の手順に従います。

> [!NOTE]
> 階層型名前空間は現在、すべてのパブリック リージョンで利用できます。

1. ストレージ アカウントを作成するサブスクリプションを選択します。
2. Azure portal で、 **[リソースの作成]** を選択し、 **[ストレージ アカウント]** を選択します。
3. **[リソース グループ]** フィールドの下の **[新規作成]** を選択します。 新しいリソース グループの名前を入力します。
   
   リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。

4. 次に、ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。
5. 場所を選択します。
6. **[アカウントの種類]** ドロップダウン リストで **[StorageV2 (general purpose v2)]\(StorageV2 (汎用 v2\))** が選択されていることを確認します。
7. 必要に応じて、次の各フィールドの値を変更します。**パフォーマンス**、**レプリケーション**、**アクセス層**。 これらのオプションの詳細については、「[Azure Storage の概要](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services)」を参照してください。
8. **[詳細]** タブを選択します。
10. **[Data Lake Storage Gen2]** セクションで、 **[階層構造の名前空間]** を **[有効]** に設定します。
11. **[確認および作成]** をクリックして、ストレージ アカウントを作成します。

これで、ご自身のストレージ アカウントがポータルで作成されました。

### <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure Portal を使用してリソース グループを削除するには:

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 **[リソース グループ]** を選択して、リソース グループの一覧を表示します。
2. 削除するリソース グループを見つけて、一覧の右側にある **[詳細]** ボタン ( **...** ) を右クリックします。
3. **[リソース グループの削除]** を選択し、確認します。

## <a name="create-an-account-using-powershell"></a>PowerShell を使用してアカウントを作成する

最初に、[PowerShellGet](/powershell/scripting/gallery/installing-psget) モジュールの最新バージョンをインストールします。

次に、PowerShell モジュールをアップグレードして、お使いの Azure サブスクリプションにサインインし、リソース グループを作成してから、ストレージ アカウントを作成します。

### <a name="upgrade-your-powershell-module"></a>PowerShell モジュールのアップグレード

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell を使用して Data Lake Storage Gen2 を操作するには、モジュールの Az.Storage バージョン **0.7** 以降をインストールする必要があります。

最初に、引き上げられたアクセス許可を使って PowerShell セッションを開きます。

Az.Storage モジュールのインストール

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインする

`Login-AzAccount` コマンドを使用して、画面上の指示に従って認証を行います。

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>リソース グループの作成

PowerShell で新しいリソース グループを作成するには、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用します。 

> [!NOTE]
> 階層型名前空間は現在、すべてのパブリック リージョンで利用できます。

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>汎用 v2 ストレージ アカウントを作成する

ローカル冗長ストレージ (LRS) を使用して汎用 v2 ストレージ アカウントを PowerShell から作成するには、[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) コマンドを使用します。

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループと関連付けられているリソース (新しいストレージ アカウントを含む) を削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用します。 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Azure CLI を使用してアカウントを作成する

Azure Cloud Shell を開始するには、[Azure portal](https://portal.azure.com) にサインインします。

CLI のローカル インストールにサインインする場合は、login コマンドを実行します。

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Azure Data Lake Gen 2 対応の CLI 拡張機能を追加する

CLI を使用して Data Lake Storage Gen2 と対話するには、お使いのシェルに拡張機能を追加する必要があります。

そのためには、Cloud Shell またはローカル シェルを使用して、`az extension add --name storage-preview` コマンドを入力します。

### <a name="create-a-resource-group"></a>リソース グループの作成

Azure CLI で新しいリソース グループを作成するには、[az group create](/cli/azure/group) コマンドを使用します。

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > 階層型名前空間は現在、すべてのパブリック リージョンで利用できます。

### <a name="create-a-general-purpose-v2-storage-account"></a>汎用 v2 ストレージ アカウントを作成する

ローカル冗長ストレージを使用して汎用 v2 ストレージ アカウントを Azure CLI から作成するには、[az storage account create](/cli/azure/storage/account) コマンドを使用します。

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --enable-hierarchical-namespace true
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループと関連付けられているリソース (新しいストレージ アカウントを含む) を削除するには、[az group delete](/cli/azure/group) コマンドを使用します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

この記事では、Data Lake Storage Gen2 の機能が利用できるストレージ アカウントを作成しました。 ストレージ アカウントとの間で BLOB をアップロードおよびダウンロードする方法を学習するには、次のトピックを参照してください。

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
