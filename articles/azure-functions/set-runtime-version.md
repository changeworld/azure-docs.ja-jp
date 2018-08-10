---
title: Azure Functions ランタイム バージョンをターゲットにする方法
description: Azure Functions では、複数のバージョンのランタイムがサポートされます。 Azure でホストされる関数アプリのランタイム バージョンを指定する方法について説明します。
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 3efc09f93cf8b3e65d595c87d0cf25691386d6e2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434766"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions ランタイム バージョンをターゲットにする方法

関数アプリは、Azure Functions ランタイムの特定のバージョンで実行されます。 メジャー バージョンには、[1.x と 2.x](functions-versions.md) の 2 つがあります。 この記事では、選択したバージョンで実行されるように Azure の関数アプリを構成する方法について説明します。 特定のバージョン用にローカル開発環境を構成する方法については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」を参照してください。

>[!IMPORTANT]   
> Azure Functions ランタイム 2.0 はプレビュー段階であり、現在のところ、Azure Functions のすべての機能はサポートされていません。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](functions-versions.md)」をご覧ください。

## <a name="automatic-and-manual-version-updates"></a>自動および手動でのバージョンの更新

Functions を使用すると、関数アプリ内で `FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用して、ランタイムの特定のバージョンをターゲットにすることができます。 関数アプリは、新しいバージョンへの移行を明示的に選択しない限り、指定されたメジャー バージョンに保持されます。

メジャー バージョン (1.x の場合は "~1"、2.x の場合は "beta") のみを指定した場合、関数アプリは、ランタイムの新しいマイナー バージョンが利用可能になった時点で、自動的に新しいマイナー バージョンに更新されます。 新しいマイナー バージョンには、重大な変更は導入されません。 マイナー バージョン (たとえば "1.0.11360") を指定した場合、関数アプリは、明示的にバージョンを変更するまで、そのバージョンに保持されます。 

新しいバージョンが公開されると、ポータルで確認のメッセージが表示され、そのバージョンに移行することができます。 新しいバージョンに移行した後は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用して、いつでも以前のバージョンに戻すことができます。

ランタイム バージョンを変更するたびに、関数アプリが再起動されます。

自動更新を有効にするために `FUNCTIONS_EXTENSION_VERSION` アプリ設定で設定できる値は、現在、1.x ランタイムでは "~1"、2.x では "beta" です。

## <a name="view-the-current-runtime-version"></a>現在のランタイム バージョンの表示

関数アプリで現在使用されているランタイム バージョンを表示するには、次の手順に従います。 

1. [Azure Portal](https://portal.azure.com) で関数アプリに移動して、**[構成済みの機能]** で **[Function App の設定]** を選択します。 

    ![[Function App の設定] を選択する](./media/functions-versions/add-update-app-setting.png)

2. **[Function App の設定]** タブで、**[ランタイム バージョン]** を探します。 特定のランタイム バージョンと要求されたメジャー バージョンをメモします。 次の例では、FUNCTIONS\_EXTENSION\_VERSION アプリ設定は `~1` に設定されています。
 
   ![[Function App の設定] を選択する](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>ポータルを使用してバージョンをターゲットにする

現在のメジャー バージョンまたはバージョン 2.0 以外のバージョンをターゲットにする必要がある場合は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を設定します。

1. [Azure Portal ](https://portal.azure.com)で、お使いの関数アプリに移動して、**[構成済みの機能]** で **[アプリケーションの設定]** を選択します。

    ![[Function App の設定] を選択する](./media/functions-versions/add-update-app-setting1a.png)

2. **[アプリケーションの設定]** タブで `FUNCTIONS_EXTENSION_VERSION` 設定を探して、1.x ランタイムの有効なバージョンか、バージョン 2.0 の `beta` に値を変更します。 メジャー バージョンのチルダ (例: "~1") は、そのメジャー バージョンの最新バージョンを使用することを意味します。 

    ![関数アプリ設定を行う](./media/functions-versions/add-update-app-setting2.png)

3. **[保存]** をクリックして、アプリケーション設定の更新を保存します。 

## <a name="target-a-version-using-azure-cli"></a>Azure CLI を使用してバージョンをターゲットにする

 Azure CLI から `FUNCTIONS_EXTENSION_VERSION` を設定することもできます。 Azure CLI を使用して、[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) コマンドを使って関数アプリのアプリケーション設定を更新します。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
このコードでは、`<function_app>` をお使いの関数アプリの名前に置き換えます。 また、`<my_resource_group>` をお使いの関数アプリのリソース グループの名前に置き換えます。 `<version>` を 1.x ランタイムの有効なバージョンまたはバージョン 2.0 の `beta` に置き換えます。 

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure/reference-index#az-login)を実行してサインインした後に、このコマンドを実行することもできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ローカル開発環境で 2.0 ランタイムをターゲットにする](functions-run-local.md)

> [!div class="nextstepaction"]
> [ランタイムのバージョンについては、リリース ノートを参照してください](https://github.com/Azure/azure-webjobs-sdk-script/releases)