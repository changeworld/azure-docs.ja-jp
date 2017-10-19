---
title: "Azure Functions ランタイム バージョンをターゲットにする方法 |Microsoft ドキュメント"
description: "Functions では、複数のバージョンのランタイムがサポートされます。 Azure でホストされる関数アプリのランタイム バージョンを指定する方法について説明します。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions ランタイム バージョンをターゲットにする方法

Azure Functions ランタイムは、Azure でのサーバーなしのコード実行を実装しています。 このランタイムは、Azure でホストされている以外にも、さまざまな環境で見つけられます。 [Azure Functions Core Tools](functions-run-local.md) は、開発コンピューターでこのランタイムを実装します。 [Azure Functions ランタイム](functions-runtime-overview.md)は、オンプレミス環境での関数の使用を可能にします。 

Functions では、ランタイムの複数のメジャー バージョンがサポートされています。 メジャー バージョンの更新プログラムでは、重大な変更を導入できます。 このトピックでは、Azure でホストされている場合に、関数アプリのターゲットを特定のランタイム バージョンにする方法について説明します。 

Functions を使用すると、お使いの関数アプリにある `FUNCTIONS_EXTENSION_VERSION` アプリケーションの設定を使用して、ランタイムの特定のメジャー バージョンをターゲットにすることができます。 これは、パブリックおよびプレビューのどちらのバージョンにも適用されます。 新しいバージョンに移行することが明示的に選択されるまで、お使いの関数アプリは、指定されたメジャーのランタイム バージョンに保管されます。 新しいマイナー バージョンのランタイムが使用可能になると、お使いの関数アプリはそのバージョンに更新されます。 マイナー バージョンの更新プログラムでは、重大な変更を導入していません。  

新しいメジャー バージョンがパブリックに使用可能になると、ポータルで関数アプリを表示したときに、そのバージョンに移行する機会が与えられます。 新しいバージョンに移行した後は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用して、いつでも以前のランタイム バージョンに移行することができます。

ランタイム バージョンを変更するごとに、関数アプリが再起動されます。 すべてのランタイム リリース (メジャーおよびマイナーの両方) に関するリリース ノートは、[GitHub リポジトリ](https://github.com/Azure/azure-webjobs-sdk-script/releases)に公開されています。   
## <a name="view-the-current-runtime-version"></a>現在のランタイム バージョンの表示

関数アプリで現在使用されている特定のランタイム バージョンを表示するには、次の手順に従います。 

1. [Azure Portal ](https://portal.azure.com)で、お使いの関数アプリに移動して、**[構成済みの機能]** で **[Function App の設定]** を選択します。 

    ![[Function App の設定] を選択する](./media/functions-versions/add-update-app-setting.png)

2. **[Function App の設定]** タブで、**[ランタイム バージョン]** を探します。 特定のランタイム バージョンと要求されたメジャー バージョンをメモします。 以下の例では、メジャー バージョンが `~1.0` に設定されています。
 
   ![[Function App の設定] を選択する](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>Functions のバージョン 2.0 のランタイムをターゲットにします。

>[!IMPORTANT]   
> Azure Functions ランタイム 2.0 はプレビュー段階であり、現在のところ、Azure Functions のすべての機能はサポートされていません。 詳細については、「[Azure Functions runtime 2.0 known issues](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)」(Azure Functions ランタイム 2.0 の既知の問題) を参照してください  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Azure ポータルで、お使いの関数アプリをランタイム バージョン 2.0 のプレビューに移行できます。 **[Function App の設定]** タブで、**[ランタイム バージョン]** にある **[ベータ]** を選択します。  

   ![[Function App の設定] を選択する](./media/functions-versions/function-app-view-version.png)

この設定は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を `beta` に設定するのと同じです。 現在パブリックにサポートされているメジャー バージョンに戻すには、**[~ 1]** ボタンを選択します。 Azure CLI を使用して、このアプリケーション設定を更新することもできます。 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>ポータルから特定のランタイム バージョンをターゲットにする

現在のメジャー バージョンまたはバージョン 2.0 以外のメジャー バージョンをターゲットにする必要がある場合、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を行う必要があります。

1. [Azure Portal ](https://portal.azure.com)で、お使いの関数アプリに移動して、**[構成済みの機能]** で **[アプリケーションの設定]** を選択します。

    ![[Function App の設定] を選択する](./media/functions-versions/add-update-app-setting1a.png)

2. **[アプリケーション設定]** タブで、`FUNCTIONS_EXTENSION_VERSION` 設定を探して、有効なメジャー バージョンである 1.x ランタイムまたはバージョン 2.0 の `beta` に値を変更します。 

    ![関数アプリ設定を行う](./media/functions-versions/add-update-app-setting2.png)

3. **[保存]** をクリックして、アプリケーション設定の更新を保存します。 

## <a name="target-a-specific-version-using-azure-cli"></a>Azure CLI を使用して特定のバージョンをターゲットにする

 Azure CLI から `FUNCTIONS_EXTENSION_VERSION` を設定することもできます。 Azure CLI を使用して、[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) コマンドを使って関数アプリのアプリケーション設定を更新します。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
このコードでは、`<function_app>` をお使いの関数アプリの名前に置き換えます。 また、`<my_resource_group>` をお使いの関数アプリのリソース グループの名前に置き換えます。 `<version>` を有効なメジャー バージョンである 1.x ランタイムまたはバージョン 2.0 の `beta` に置き換えます。 

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure#az_login)を実行してサインインした後に、このコマンドを実行することもできます。
