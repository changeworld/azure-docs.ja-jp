---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeefb63a283c473a98639ead1aa6640d88409125
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318132"
---
## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzureRmAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzureRmAccount
```

使用する場所がわからない場合、利用できる場所を一覧表示できます。 次のコード例を使用して場所の一覧を表示し、使用する場所を見つけます。 この例では、**eastus** を使います。 場所を変数に格納し、この変数を使用することで、1 か所で場所を変更できます。

```powershell
Get-AzureRmLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) を使用して、LRS レプリケーション付きの標準の汎用ストレージ アカウントを作成します。 次に、使用するストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。 ストレージ アカウントを操作するときは、資格情報を繰り返し渡す代わりに、このコンテキストを参照します。 次の例を使用して、ローカル冗長ストレージ (LRS) と BLOB 暗号化機能 (既定で有効になります) を備える *mystorageaccount* というストレージ アカウントを作成します。

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
