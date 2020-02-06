---
title: Azure CLI のサンプル スクリプト - Azure App Configuration ストアにインポートする
titleSuffix: Azure App Configuration
description: Azure App Configuration ストアへのインポートを行うサンプル スクリプトについて取り上げます。
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: bb661e6d4497a85cf2ef445fc39774e93a04cc99
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899420"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Azure App Configuration ストアへのインポート

このサンプル スクリプトでは、Azure App Configuration ストアにキーと値の設定をインポートします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールとアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

最初に次のコマンドを実行して Azure App Configuration CLI の拡張機能をインストールする必要があります。

        az extension add -n appconfig

## <a name="sample-script"></a>サンプル スクリプト

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して App Configuration ストアへのインポートを実行します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [az appconfig kv import](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | App Configuration ストア リソースへのインポートを行います。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページを参照してください。

その他の App Configuration の CLI サンプル スクリプトは、[Azure App Configuration の CLI サンプル](../cli-samples.md)のページにあります。
