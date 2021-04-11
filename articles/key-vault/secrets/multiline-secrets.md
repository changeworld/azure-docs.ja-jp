---
title: Azure Key Vault に複数行のシークレットを格納する
description: Azure CLI と PowerShell を使用して Azure Key Vault から複数行のシークレットを設定する方法を示すチュートリアル
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608676"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Azure Key Vault に複数行のシークレットを格納する

[Azure CLI のクイックスタート](quick-create-cli.md)と [Azure PowerShell のクイックスタート](quick-create-powershell.md)では、単一行のシークレットを格納する方法を示しています。   Key Vault を使用して、JSON ファイルや RSA 秘密キーなどの複数行のシークレットを格納することもできます。

複数行のシークレットを Azure CLI の [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) コマンドや Azure PowerShell の [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) コマンドレットにコマンドラインを介して渡すことはできません。 代わりに、まず複数行のシークレットをテキスト ファイルとして格納する必要があります。 

たとえば、次の行を含む "secretfile.txt" という名前のテキスト ファイルを作成します。

```bash
This is my
multi-line
secret
```

その後、`--file` パラメーターを使用して、このファイルを Azure CLI の [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) コマンドに渡すことができます。

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Azure PowerShell では、最初に [Get-Content](/powershell/module/microsoft.powershell.management/get-content) コマンドレットを使用してファイルを読み取り、次に [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring) を使用してセキュリティで保護された文字列に変換する必要があります。 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

最後に、[Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) コマンドレットを使用してシークレットを格納します。

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

どちらの場合も、Azure CLI の [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) コマンド、または Azure PowerShell の [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) コマンドレットを使用して、格納されているシークレットを表示できます。

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

シークレットは、改行が埋め込まれた状態で返されます。

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [Azure CLI のクイックスタート](quick-create-cli.md)を参照する
- [Azure CLI の az keyvault コマンド](/cli/azure/keyvault)を参照する
- [Azure PowerShell のクイックスタート](quick-create-powershell.md)を参照する
- [Azure PowerShell の Az.KeyVault コマンドレット](/powershell/module/az.keyvault#key-vault)を参照する
