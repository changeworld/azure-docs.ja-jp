---
title: Azure Functions ランタイム バージョンをターゲットにする方法
description: Azure Functions では、複数のバージョンのランタイムがサポートされます。 Azure でホストされる関数アプリのランタイム バージョンを指定する方法について説明します。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: glenga
ms.openlocfilehash: 6d89746c0a2d4642e5025789d352803195c0a3b9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886809"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions ランタイム バージョンをターゲットにする方法

関数アプリは、Azure Functions ランタイムの特定のバージョンで実行されます。 メジャー バージョンには、[1.x と 2.x](functions-versions.md) の 2 つがあります。 既定では、作成される関数アプリはバージョン 2.x のランタイムです。 この記事では、選択したバージョンで実行されるように Azure の関数アプリを構成する方法について説明します。 特定のバージョン用にローカル開発環境を構成する方法については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」を参照してください。

> [!NOTE]
> 1 つまたは複数の関数のある関数アプリのランタイム バージョンを変更することはできません。 ランタイム バージョンを変更するには、Azure portal を使用する必要があります。

## <a name="automatic-and-manual-version-updates"></a>自動および手動でのバージョンの更新

Functions を使用すると、関数アプリ内で `FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用して、ランタイムの特定のバージョンをターゲットにすることができます。 関数アプリは、新しいバージョンへの移行を明示的に選択しない限り、指定されたメジャー バージョンに保持されます。

メジャー バージョン (2.x の場合は "~2"、1.x の場合は "~1") のみを指定した場合、関数アプリは、ランタイムの新しいマイナー バージョンが利用可能になった時点で、自動的に新しいマイナー バージョンに更新されます。 新しいマイナー バージョンには、重大な変更は導入されません。 マイナー バージョン (たとえば "2.0.12345") を指定した場合、関数アプリは、明示的にバージョンを変更するまで、その特定のバージョンに固定されます。

新しいバージョンが公開されると、ポータルで確認のメッセージが表示され、そのバージョンに移行することができます。 新しいバージョンに移行した後は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用して、いつでも以前のバージョンに戻すことができます。

ランタイム バージョンを変更するたびに、関数アプリが再起動されます。

自動更新を有効にするために `FUNCTIONS_EXTENSION_VERSION` アプリ設定で設定できる値は、現在、1.x ランタイムでは "~1"、2.x では "~2" です。

## <a name="view-and-update-the-current-runtime-version"></a>現在のランタイム バージョンの表示と更新

関数アプリで現在使用されているランタイム バージョンを表示するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、関数アプリに移動します。

1. **[構成済みの機能]** で、**[Function App の設定]** を選択します。

    ![[Function App の設定] を選択する](./media/set-runtime-version/add-update-app-setting.png)

1. **[Function App の設定]** タブで、**[ランタイム バージョン]** を探します。 特定のランタイム バージョンと要求されたメジャー バージョンをメモします。 以下の例では、バージョンが `~2` に設定されています。

   ![[Function App の設定] を選択する](./media/set-runtime-version/function-app-view-version.png)

1. バージョン 1.x ランタイムに関数アプリを固定するには、**[ランタイム バージョン]** で **[~1]** を選択します。 アプリに関数がある場合、このスイッチは無効です。

1. ランタイム バージョンを変更した場合は、**[概要]** タブに戻り、**[再起動]** を選択してアプリを再起動します。  関数アプリがバージョン 1.x ランタイムでの実行を再開します。関数を作成するときは、バージョン 1.x のテンプレートを使用します。

## <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Azure CLI を使用してランタイム バージョンを表示および更新する

Azure CLI から `FUNCTIONS_EXTENSION_VERSION` を表示および設定することもできます。

>[!NOTE]
>その他の設定はランタイム バージョンの影響を受ける可能性があるため、ポータルでバージョンを変更する必要があります。 ランタイム バージョンを変更すると、ポータルで自動的に、Node.js のバージョンやランタイム スタックなどのその他の必要な更新が行われます。  

Azure CLI を使用して、[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) コマンドで現在のランタイム バージョンを表示します。

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

このコードでは、`<function_app>` をお使いの関数アプリの名前に置き換えます。 また、`<my_resource_group>` をお使いの関数アプリのリソース グループの名前に置き換えます。 

明確にするために切り捨てられていますが、次の出力に `FUNCTIONS_EXTENSION_VERSION` が表示されます。

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) コマンドで、関数アプリの `FUNCTIONS_EXTENSION_VERSION` の設定を更新できます。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

`<function_app>` をお使いの関数アプリの名前に置き換えます。 また、`<my_resource_group>` をお使いの関数アプリのリソース グループの名前に置き換えます。 また、`<version>` を 1.x ランタイムの有効なバージョンまたはバージョン 2.x の `~2` に置き換えます。

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure/reference-index#az-login)を実行してサインインした後に、このコマンドを実行することもできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ローカル開発環境で 2.0 ランタイムをターゲットにする](functions-run-local.md)

> [!div class="nextstepaction"]
> [ランタイムのバージョンについては、リリース ノートを参照してください](https://github.com/Azure/azure-webjobs-sdk-script/releases)
