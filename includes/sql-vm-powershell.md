---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 998f771efe9bf1dac34a083f71f78c1dba36c385
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721572"
---
## <a name="start-your-powershell-session"></a>PowerShell セッションの開始
 

[**Connect-Az Account**](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを実行すると、資格情報を入力するサインイン画面が表示されます。 Azure ポータルにサインインするときと同じ資格情報を使用してください。

```powershell
Connect-AzAccount
```

複数のサブスクリプションがある場合は、[**Set-AzContext**](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、PowerShell セッションで使用するサブスクリプションを選択します。 PowerShell セッションが現在使用しているサブスクリプションを確認するには、[**Get-AzContext**](/powershell/module/az.accounts/get-azcontext) を実行します。 すべてのサブスクリプションを表示するには、[**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription) を実行します。

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```
