---
title: Azure CLI のサンプル スクリプト - Azure App Configuration ストアからのエクスポート | Microsoft Docs
description: Azure App Configuration ストアからのエクスポートを行うサンプル スクリプトについて取り上げます。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3310dc5d72284e8d94b95b855fee90d560205fa4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884389"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Azure App Configuration ストアからのエクスポート

このサンプル スクリプトでは、Azure App Configuration ストアからキー/値をエクスポートします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

最初に次のコマンドを実行して Azure App Configuration CLI の拡張機能をインストールする必要があります。

        az extension add -n appconfig

## <a name="sample-script"></a>サンプル スクリプト

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用してアプリ構成ストアをエクスポートします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az appconfig export](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-export) | アプリ構成ストア リソースからのエクスポートを行います。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Configuration の CLI サンプル スクリプトは、[Azure App Configuration のドキュメント](../cli-samples.md)のページにあります。
