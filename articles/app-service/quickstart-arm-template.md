---
title: Azure Resource Manager テンプレートを使用して App Service アプリを作成する
description: App Service にデプロイする多くの方法の 1 つである Azure Resource Manager テンプレートを使用して、Azure App Service への最初のアプリを数秒で作成します。
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653298"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して App Service アプリを作成する

Azure Resource Manager テンプレートと [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) をCloud Shell で使用して、アプリをクラウドにデプロイすることによって、[Azure App Service](overview.md) の使用を開始します。 App Service の Free レベルを使用するため、このクイックスタートを完了するのにコストはかかりません。

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>Azure App Service アプリを作成する

### <a name="review-the-template"></a>テンプレートを確認する

::: zone pivot="platform-windows"
このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/)からのものです。 これは App Service プランおよび App Service アプリを Windows にデプロイします。 .NET Core、.NET Framework、PHP、Node.js、静的 HTML アプリと互換性があります。 Java の場合は、[Java アプリの作成](app-service-web-get-started-java.md)に関する記事を参照してください。 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

テンプレートでは、次の 2 つの Azure リソースが定義されています。

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service プランを作成します。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service アプリを作成します。

このテンプレートには、利便性のために事前に定義されているいくつかのパラメーターが含まれています。 パラメーターの既定値とその説明については、次の表を参照してください。

| パラメーター | Type    | 既定値                | 説明 |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | アプリの名前 |
| location   | string  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | アプリのリージョン |
| sku        | string  | "F1"                         | インスタンス サイズ (F1 = Free レベル) |
| language   | string  | ".net"                       | プログラミング言語スタック (.net、php、node、html) |
| helloWorld | boolean | False                        | True = "Hello World" アプリをデプロイする |
| repoUrl    | string  |  。                          | 外部 Git リポジトリ (オプション) |
::: zone-end
::: zone pivot="platform-linux"
このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/)からのものです。 これは App Service プランおよび App Service アプリを Linux にデプロイします。 これは App Service でサポートされているすべてのプログラミング言語と互換性があります。

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

テンプレートでは、次の 2 つの Azure リソースが定義されています。

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service プランを作成します。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service アプリを作成します。

このテンプレートには、利便性のために事前に定義されているいくつかのパラメーターが含まれています。 パラメーターの既定値とその説明については、次の表を参照してください。

| パラメーター | Type    | 既定値                | 説明 |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | アプリの名前 |
| location   | string  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | アプリのリージョン |
| sku        | string  | "F1"                         | インスタンス サイズ (F1 = Free レベル) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "プログラミング言語スタック &#124; バージョン" |
| repoUrl    | string  |  。                          | 外部 Git リポジトリ (オプション) |

---
::: zone-end


### <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイするために、ここでは Azure CLI を使用します。 Azure portal、Azure PowerShell、REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。 

以下のコードでは、リソース グループ、App Service プラン、Web アプリを作成します。 既定のリソース グループ、App Service プラン、場所が設定されています。 `<app-name>` を、グローバルに一意であるアプリ名に置き換えてください (有効な文字は、`a-z`、`0-9`、`-` です)。

::: zone pivot="platform-windows"
Windows に .NET framework アプリをデプロイするには、次のコードを実行します。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
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
>ローカル Git からのデプロイ

> [!div class="nextstepaction"]
>ASP.NET Core と SQL Database

> [!div class="nextstepaction"]
>Python と Postgres

> [!div class="nextstepaction"]
>PHP と MySQL

> [!div class="nextstepaction"]
> [Java を使用して Azure SQL Database に接続する](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
>カスタム ドメインのマップ