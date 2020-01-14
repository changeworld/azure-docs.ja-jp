---
title: Azure CLI のサンプル スクリプト - Azure App Configuration ストアに格納されているキー/値を操作する
titleSuffix: Azure App Configuration
description: Azure App Configuration ストアに格納されているキー/値の操作について取り上げます。
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 11/08/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 366485c8c735af5689e1b2c2e63eb192218f5ce0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433576"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Azure App Configuration ストアに格納されているキー/値を操作する

このサンプル スクリプトでは、Azure App Configuration ストアに新しいキー/値を作成します。さらに、既にあるすべてのキー/値をリストし、新しく作成したキーの値を更新して、最後にそれを削除します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

最初に次のコマンドを実行して Azure App Configuration CLI の拡張機能をインストールする必要があります。

        az extension add -n appconfig

## <a name="sample-script"></a>サンプル スクリプト

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、App Configuration ストアに格納されているキーと値を操作します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-set) | キー/値を作成または更新します。 |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-list) | App Configuration ストアに格納されているキーと値をリストします。 |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-delete) | キー/値を削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Configuration の CLI サンプル スクリプトは、[Azure App Configuration の CLI サンプル](../cli-samples.md)のページにあります。
