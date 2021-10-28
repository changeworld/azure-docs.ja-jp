---
title: Azure Bicep を使用して App Service アプリを作成する
description: App Service にデプロイする多くの方法の 1 つである Azure Bicep を使用して、Azure App Service への最初のアプリを数秒で作成します。
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.openlocfilehash: 764a44a79ce6f892f0715dde6d657755a3048acf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257197"
---
# <a name="create-app-service-app-using-bicep"></a>Bicep を使用して App Service アプリを作成する

[Bicep](../azure-resource-manager/bicep/index.yml) ファイルと [Azure CLI](/cli/azure/get-started-with-azure-cli) をCloud Shell 内で使用して、アプリをクラウドにデプロイすることによって、[Azure App Service](overview.md) の使用を開始します。 App Service の Free レベルを使用するため、このクイックスタートを完了するのにコストはかかりません。

Bicep は、宣言型の構文を使用して Azure リソースをデプロイするドメイン固有言語 (DSL) です。 簡潔な構文、信頼性の高いタイプ セーフ、およびコードの再利用のサポートが提供されます。 JSON の代わりに Bicep を使用して、Azure Resource Manager テンプレート ([ARM テンプレート](../azure-resource-manager/templates/overview.md)) を開発できます。 ARM テンプレートを作成するための JSON 構文は冗長になることがあり、複雑な式を必要とします。 Bicep 構文では、その複雑さが軽減され、開発エクスペリエンスが向上します。 Bicep は ARM テンプレート JSON に対する透過的な抽象化であり、JSON テンプレートの機能は一切失われません。 デプロイ中、Bicep CLI により、Bicep ファイルが ARM テンプレート JSON にトランスパイルされます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Bicep を使用してリソースを効率的に作成するには、Bicep [開発環境](../azure-resource-manager/bicep/install.md)を設定する必要があります。 [Visual Studio Code](https://code.visualstudio.com/) の Bicep 拡張機能によって、言語サポートとリソース オートコンプリートが提供されます。 拡張機能は、Bicep ファイルの作成と検証に役立ちます。これは、このクイックスタートを完了した後、Bicep を使ってリソースを作成する開発者にお勧めします。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートを以下に示します。 これにより App Service プランと App Service アプリが Linux 上にデプロイされます。また、サンプル Node.js "Hello World" アプリが [Azure サンプル](https://github.com/Azure-Samples) リポジトリからデプロイされます。

```bicep
param webAppName string = uniqueString(resourceGroup().id) // Generate unique String for web app name
param sku string = 'F1' // The SKU of App Service Plan
param linuxFxVersion string = 'node|14-lts' // The runtime stack of web app
param location string = resourceGroup().location // Location for all resources
param repositoryUrl string = 'https://github.com/Azure-Samples/nodejs-docs-hello-world'
param branch string = 'master'
var appServicePlanName = toLower('AppServicePlan-${webAppName}')
var webSiteName = toLower('wapp-${webAppName}')
resource appServicePlan 'Microsoft.Web/serverfarms@2020-06-01' = {
  name: appServicePlanName
  location: location
  properties: {
    reserved: true
  }
  sku: {
    name: sku
  }
  kind: 'linux'
}
resource appService 'Microsoft.Web/sites@2020-06-01' = {
  name: webSiteName
  location: location
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      linuxFxVersion: linuxFxVersion
    }
  }
}
resource srcControls 'Microsoft.Web/sites/sourcecontrols@2021-01-01' = {
  name: '${appService.name}/web'
  properties: {
    repoUrl: repositoryUrl
    branch: branch
    isManualIntegration: true
  }
}
```

テンプレートには、次の 3 つの Azure リソースが定義されています。

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service プランを作成します。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service アプリを作成します。
* [**Microsoft.Web/sites/sourcecontrols**](/azure/templates/microsoft.web/sites/sourcecontrols): 外部 Git デプロイ構成を作成します。

このテンプレートには、利便性のために事前に定義されているいくつかのパラメーターが含まれています。 パラメーターの既定値とその説明については、次の表を参照してください。

| パラメーター | Type    | 既定値                | 説明 |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | アプリの名前 |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | アプリのリージョン |
| sku        | string  | "F1"                         | インスタンスのサイズ  |
| linuxFxVersion   | string  | "NODE&#124;14-LTS"       | "プログラミング言語スタック &#124; バージョン" |
| repositoryUrl    | string  | "https://github.com/Azure-Samples/nodejs-docs-hello-world"    | 外部 Git リポジトリ (オプション) |
| 分岐    | string  | "master"    | コード サンプルの既定のブランチ |

---

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをコピーして好みのエディター/IDE に貼り付け、ファイルをご自身のローカル作業ディレクトリに保存します。

テンプレートをデプロイするために、ここでは Azure CLI を使用します。 Azure portal、Azure PowerShell、REST API を使用することもできます。 他のデプロイ方法については、[Bicep デプロイ コマンド](../azure-resource-manager/bicep/deploy-cli.md)に関するページをご覧ください。

以下のコードでは、リソース グループ、App Service プラン、Web アプリを作成します。 既定のリソース グループ、App Service プラン、場所が設定されています。 `<app-name>` を、グローバルに一意であるアプリ名に置き換えてください (有効な文字は、`a-z`、`0-9`、`-` です)。

Azure CLI がインストールされているターミナルを開き、以下のコードを実行して、Linux 上で Node.js アプリを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --template-file <path-to-template>
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

## <a name="validate-the-deployment"></a>デプロイの検証

`http://<app_name>.azurewebsites.net/` を参照して、作成されていることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[リソース グループを削除します](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bicep のドキュメント](../azure-resource-manager/bicep/index.yml)
> [!div class="nextstepaction"]
> [Azure App Service の Bicep サンプル](./samples-bicep.md)