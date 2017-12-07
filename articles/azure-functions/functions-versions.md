---
title: "Azure Functions ランタイム バージョンをターゲットにする方法"
description: "Azure Functions では、複数のバージョンのランタイムがサポートされます。 Azure でホストされる関数アプリのランタイム バージョンを指定する方法について説明します。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 588437af80ecf60b7c4b24dbf6bccc67fc33da7a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions ランタイム バージョンをターゲットにする方法

関数アプリは、Azure Functions ランタイムの特定のバージョンで実行されます。 メジャー バージョンには、1.x と 2.x の 2 つがあります。 この記事では、使用するメジャー バージョンの選択方法と、選択したバージョンで実行されるように Azure で関数アプリを構成する方法について説明します。 特定のバージョン用にローカル開発環境を構成する方法については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」を参照してください。

## <a name="differences-between-runtime-1x-and-2x"></a>ランタイム 1.x と 2.x の違い

> [!IMPORTANT] 
> ランタイム 1.x は、運用環境用に承認された唯一のバージョンです。

| ランタイム | 状態 |
|---------|---------|
|1.x|一般公開 (GA)|
|2.x|Preview|

以降のセクションでは、言語、バインド、およびクロスプラットフォーム開発のサポートの違いについて説明します。

### <a name="languages"></a>言語

次の表は、各ランタイム バージョンでどのプログラミング言語がサポートされているかを示しています。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

詳細については、[サポートされている言語](supported-languages.md)に関するページを参照してください。

### <a name="bindings"></a>バインド 

ランタイム 2.x では、カスタム [バインド拡張](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)を作成できます。 この拡張モデルを使用する組み込みバインドは、2.x だけで使用できます。最初の組み込みバインドとして、[Microsoft Graph バインド](functions-bindings-microsoft-graph.md)などがあります。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

2.x でのバインドのサポートおよびその他の機能的な差異については、[ランタイム 2.0 の既知の問題](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)に関するページをご覧ください。

### <a name="cross-platform-development"></a>クロスプラットフォーム開発

ランタイム 1.x では、ポータルまたは Windows だけで関数開発がサポートされています。2.x を使用すると、Linux または macOS で Azure Functions を開発して実行することができます。

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

## <a name="target-the-version-20-runtime"></a>バージョン 2.0 ランタイムをターゲットにする

>[!IMPORTANT]   
> Azure Functions ランタイム 2.0 はプレビュー段階であり、現在のところ、Azure Functions のすべての機能はサポートされていません。 詳細については、この記事の前の方の「[ランタイム 1.x と 2.x の違い](#differences-between-runtime-1x-and-2x)」を参照してください。

Azure Portal で、関数アプリをランタイム バージョン 2.0 のプレビューに移行できます。 **[Function App の設定]** タブで、**[ランタイム バージョン]** にある **[ベータ]** を選択します。  

![[Function App の設定] を選択する](./media/functions-versions/function-app-view-version.png)

この設定は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を `beta` に設定するのと同じです。 現在パブリックにサポートされているメジャー バージョンに戻すには、**[~ 1]** ボタンを選択します。 Azure CLI を使用して、このアプリケーション設定を更新することもできます。 

## <a name="target-a-version-using-the-portal"></a>ポータルを使用してバージョンをターゲットにする

現在のメジャー バージョンまたはバージョン 2.0 以外のバージョンをターゲットにする必要がある場合は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を設定する必要があります。

1. [Azure Portal ](https://portal.azure.com)で、お使いの関数アプリに移動して、**[構成済みの機能]** で **[アプリケーションの設定]** を選択します。

    ![[Function App の設定] を選択する](./media/functions-versions/add-update-app-setting1a.png)

2. **[アプリケーションの設定]** タブで `FUNCTIONS_EXTENSION_VERSION` 設定を探して、1.x ランタイムの有効なバージョンか、バージョン 2.0 の `beta` に値を変更します。 

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

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure#az_login)を実行してサインインした後に、このコマンドを実行することもできます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ローカル開発環境で 2.0 ランタイムをターゲットにする](functions-run-local.md)

> [!div class="nextstepaction"]
> [ランタイムのバージョンについては、リリース ノートを参照してください](https://github.com/Azure/azure-webjobs-sdk-script/releases)