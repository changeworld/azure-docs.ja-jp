---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: 985c50f39476c3b1c52670f06d51fde5b3156978
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387664"
---
Azure CLI [az login](/cli/azure/reference-index#az_login) コマンドまたは Azure PowerShell [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure にサインインします。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzAccount
```
---

CLI または PowerShell で既定のブラウザーを開くことができる場合、ブラウザが開き、Azure サインイン ページが読み込まれます。 そうでない場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) を開き、ターミナルに表示されている認証コードを入力します。

メッセージが表示されたら、ブラウザーでアカウントの資格情報を使用してサインインします。
