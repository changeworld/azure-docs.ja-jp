---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "87512808"
---
このクイックスタートでは、あらかじめ作成しておいた Azure キー コンテナーを使用します。 キー コンテナーは、[Azure CLI のクイックスタート](/azure/key-vault/general/quick-create-cli)、[Azure PowerShell のクイックスタート](/azure/key-vault/general/quick-create-powershell)、または [Azure portal のクイックスタート](/azure/key-vault/general/quick-create-portal)の手順に従って作成できます。 

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