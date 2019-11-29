---
title: ブロック BLOB ストレージ アカウントを作成する - Azure Storage |Microsoft Docs
description: Premium のパフォーマンス特性を持つ Azure BlockBlobStorage アカウントを作成する方法について説明します。
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 98a9295363461864d3abbb11bbc22b8bd8d6fdfa
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933182"
---
# <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage アカウントを作成する

BlockBlobStorage アカウントの種類を使用すると、Premium のパフォーマンス特性を持つブロック BLOB を作成できます。 この種類のストレージ アカウントは、トランザクション レートが高い、または非常に高速なアクセス時間を必要とするワークロードに最適化されています。 この記事では、Azure portal、Azure CLI、または Azure PowerShell を使用して、BlockBlobStorage カウントを作成する方法について説明します。

BlockBlobStorage アカウントの詳細については、「[Azure ストレージ アカウントの概要](https://docs.microsoft.com/azure/storage/common/storage-account-overview)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

なし。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

このハウツー記事には、Azure PowerShell モジュール Az バージョン 1.2.0 以降が必要です。 現在のバージョンを調べるには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

次の 2 つの方法のいずれかで Azure にサインインし、Azure CLI コマンドを実行できます。

- Azure portal 内から、Azure Cloud Shell で CLI コマンドを実行できます。
- CLI をインストールして、CLI コマンドをローカルで実行できます。

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell を使用する

Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI は、アカウントで使用できるように事前にインストールおよび構成されています。 Azure portal の右上のセクションのメニューで **[Cloud Shell]** ボタンをクリックします。

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

このボタンによって対話型のシェルが起動されます。このハウツー記事で説明する手順は、これを使って実行できます。

[![ポータルに Cloud Shell のウィンドウが表示されるスクリーンショット](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>CLI をローカルにインストールする

Azure CLI はローカルにインストールして使用することもできます。 このハウツー記事では、Azure CLI バージョン 2.0.46 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

---

## <a name="sign-in-to-azure"></a>Azure へのサインイン

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

[Azure Portal](https://portal.azure.com) にサインインします。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

`Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従って認証を行います。

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell を起動するには、[Azure portal](https://portal.azure.com) にサインインします。

CLI のローカル インストールにログインするには、[az login](/cli/azure/reference-index#az-login) コマンドを実行します。

```cli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage アカウントを作成する

## <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
Azure portal で BlockBlobStorage アカウントを作成するには、次の手順に従います。

1. Azure portal で、 **[すべてのサービス]** > **[ストレージ]** カテゴリ > **[ストレージ アカウント]** の順に選択します。

1. **[ストレージ アカウント]** の下で **[追加]** を選択します。

1. **[サブスクリプション]** フィールドで、ストレージ アカウントを作成するサブスクリプションを選択します。

1. **[リソース グループ]** フィールドで、既存のリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。

1. **[ストレージ アカウント名]** で、アカウントの名前を入力します。 次のガイドラインに注意してください。

   - この名前は Azure 全体で一意である必要があります。
   - 名前は 3 から 24 文字の範囲でなければなりません。
   - 名前には、数字と小文字のみを含めることができます。

1. **[場所]** フィールドで、ストレージ アカウントの場所を選択するか、または既定の場所を使います。

1. 残りの設定については、次のように構成します。

   |フィールド     |値  |
   |---------|---------|
   |**パフォーマンス**    |  **[Premium]** を選択します。   |
   |**アカウントの種類**    | **[BlockBlobStorage]** を選択します。      |
   |**レプリケーション**    |  **[ローカル冗長ストレージ (LRS)]** の既定の設定のままにします。      |

   ![ブロック BLOB ストレージ アカウントを作成するポータル UI を表示する](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認します。

1. **作成** を選択します。

## <a name="azure-powershelltabazure-powershell"></a>[Azure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 管理者特権の Windows PowerShell セッション (管理者として実行) を開きます。

1. 次のコマンドを実行して、最新バージョンの `Az` PowerShell モジュールがインストールされていることを確認します。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 新しい PowerShell コンソールを開き、ご使用の Azure アカウントでサインインします。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 必要な場合は、新しいリソース グループを作成します。 引用符内の値を置き換えて、次のコマンドを実行します。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. BlockBlobStorage アカウントを作成します。 引用符内の値を置き換えて、次のコマンドを実行します。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、ブロック BLOB アカウントを作成するには、最初に Azure CLI v. 2.0.46 以降のバージョンをインストールする必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. Azure サブスクリプションにサインインします。

   ```azurecli
   az login
   ```

1. 必要な場合は、新しいリソース グループを作成します。 角かっこ内の値 (角かっこを含む) を置き換えて、次のコマンドを実行します。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. BlockBlobStorage アカウントを作成します。 角かっこ内の値 (角かっこを含む) を置き換えて、次のコマンドを実行します。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>次の手順

- ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](https://docs.microsoft.com/azure/storage/common/storage-account-overview)」をご覧ください。

- リソース グループの詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」を参照してください。
