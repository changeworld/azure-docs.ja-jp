---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560851"
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