---
title: PowerShell を使用して、Azure リソースに MSI アクセスを割り当てる方法
description: PowerShell を使用して、1 つのリソースに MSI を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 6d503526ed97dd1f61269acd83810cd44598d72f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "33929131"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>PowerShell を使用して、リソースに管理対象サービス ID (MSI) アクセスを割り当てる

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

MSI で Azure リソースを構成したら、他のセキュリティ プリンシパルと同じように、別のリソースに MSI アクセスを付与できます。 この例では、PowerShell を使用して、Azure 仮想マシンの MSI アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

まだ [Azure PowerShell バージョン 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) がインストールされていなければ、それもインストールします。

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC を使用して、別のリソースに MSI アクセスを割り当てる

[Azure VM などの](qs-configure-powershell-windows-vm.md) Azure リソースで MSI を有効にした後、次のようにします。

1. `Connect-AzureRmAccount` コマンドレットを使用して Azure にサインインします。 次のように、MSI を構成した Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```powershell
   Connect-AzureRmAccount
   ```
2. この例では、ストレージ アカウントに Azure VM アクセスを許可しています。 まず、[Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) を使用して "myVM" という VM のサービス プリンシパルを取得します。これは、MSI が有効になっているときに作成されたものです。 次に、[New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) を使用して、VM の「閲覧者」アクセスを "myStorageAcct" というストレージ アカウントに付与します。

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>トラブルシューティング

リソースの MSI が使用可能な ID の一覧に表示されない場合は、MSI が正しく有効になっていることを確認します。 この場合、 [Azure ポータル](https://portal.azure.com)の Azure VM に戻り、次の操作を行うことができます。

- [構成] ページを参照し、MSI の有効が [はい] になっていることを確認します。
- [拡張] ページを参照して、MSI 拡張機能が正常に展開されていることを確認します。

いずれかが正しくない場合は、リソース上で MSI を再デプロイするか、デプロイ エラーのトラブルシューティングを行う必要があります。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](overview.md)に関する記事をご覧ください。
- Azure VM で MSI を有効にするには、「[PowerShell を使用して Azure VM 管理対象サービスID (MSI) を構成する](qs-configure-powershell-windows-vm.md)」を参照してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

