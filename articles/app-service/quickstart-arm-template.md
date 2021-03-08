---
title: Azure Resource Manager テンプレートを使用して App Service アプリを作成する
description: App Service にデプロイする多くの方法の 1 つである Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure App Service への最初のアプリを数秒で作成します。
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-arm-template-uiex
ms.openlocfilehash: 8b048127c8a5581a34a62cfb67c19ba7aa50f2d7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701588"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して App Service アプリを作成する

Azure Resource Manager テンプレート (ARM テンプレート) と [Azure CLI](/cli/azure/get-started-with-azure-cli) を Cloud Shell で使用して、アプリをクラウドにデプロイすることによって、[Azure App Service](overview.md) の使用を開始します。 App Service の Free レベルを使用するため、このクイックスタートを完了するのにコストはかかりません。

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

次のボタンを使用して **Linux** にデプロイします。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)

次のボタンを使用して **Windows** にデプロイします。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>テンプレートを確認する

::: zone pivot="platform-windows"
このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows)からのものです。 これは App Service プランおよび App Service アプリを Windows にデプロイします。 .NET Core、.NET Framework、PHP、Node.js、静的 HTML アプリと互換性があります。 Java の場合は、[Java アプリの作成](./quickstart-java.md)に関する記事を参照してください。

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

テンプレートでは、次の 2 つの Azure リソースが定義されています。

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service プランを作成します。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service アプリを作成します。

このテンプレートには、利便性のために事前に定義されているいくつかのパラメーターが含まれています。 パラメーターの既定値とその説明については、次の表を参照してください。

| パラメーター | Type    | 既定値                | 説明 |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | アプリの名前 |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | アプリのリージョン |
| sku        | string  | "F1"                         | インスタンス サイズ (F1 = Free レベル) |
| language   | string  | ".net"                       | プログラミング言語スタック (.net、php、node、html) |
| helloWorld | boolean | False                        | True = "Hello World" アプリをデプロイする |
| repoUrl    | string  | " "                          | 外部 Git リポジトリ (オプション) |
::: zone-end
::: zone pivot="platform-linux"
このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux)からのものです。 これは App Service プランおよび App Service アプリを Linux にデプロイします。 これは App Service でサポートされているすべてのプログラミング言語と互換性があります。

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

テンプレートでは、次の 2 つの Azure リソースが定義されています。

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service プランを作成します。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service アプリを作成します。

このテンプレートには、利便性のために事前に定義されているいくつかのパラメーターが含まれています。 パラメーターの既定値とその説明については、次の表を参照してください。

| パラメーター | Type    | 既定値                | 説明 |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | アプリの名前 |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | アプリのリージョン |
| sku        | string  | "F1"                         | インスタンス サイズ (F1 = Free レベル) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "プログラミング言語スタック &#124; バージョン" |
| repoUrl    | string  | " "                          | 外部 Git リポジトリ (オプション) |

---
::: zone-end

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイするために、ここでは Azure CLI を使用します。 Azure portal、Azure PowerShell、REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

以下のコードでは、リソース グループ、App Service プラン、Web アプリを作成します。 既定のリソース グループ、App Service プラン、場所が設定されています。 `<app-name>` を、グローバルに一意であるアプリ名に置き換えてください (有効な文字は、`a-z`、`0-9`、`-` です)。

::: zone pivot="platform-windows"
Windows に .NET framework アプリをデプロイするには、次のコードを実行します。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

別の言語スタックをデプロイするには、`linuxFxVersion` を適切な値で更新します。 次に示すのは例です。 現在のバージョンを表示するには、Cloud Shell で次のコマンドを実行します: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Language    | 例:                                               |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> [その他の Azure App Service テンプレートのサンプルについてはこちら](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

`http://<app_name>.azurewebsites.net/` を参照して、作成されていることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[リソース グループを削除します](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> ローカル Git からのデプロイ

> [!div class="nextstepaction"]
> [ASP.NET Core と SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> Python と Postgres

> [!div class="nextstepaction"]
> [PHP と MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Java を使用して Azure SQL データベースに接続する](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [カスタム ドメインをマップする](app-service-web-tutorial-custom-domain.md)
