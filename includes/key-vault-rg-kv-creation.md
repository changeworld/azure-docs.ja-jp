---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: ccb69b4f0dc6090cc96849b09f7830c05aa77b73
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027183"
---
このクイックスタートでは、あらかじめ作成しておいた Azure キー コンテナーを使用します。 キー コンテナーは、[Azure CLI のクイックスタート](../articles/key-vault/general/quick-create-cli.md)、[Azure PowerShell のクイックスタート](../articles/key-vault/general/quick-create-powershell.md)、または [Azure portal のクイックスタート](../articles/key-vault/general/quick-create-portal.md)の手順に従って作成できます。 

また、以下の Azure CLI または Azure PowerShell コマンドを実行するだけでもかまいません。

> [!Important]
> 各キー コンテナーには一意の名前が必要です。 次の例では、<your-unique-keyvault-name> をお使いのキー コンテナーの名前に置き換えてください。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```