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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bce6bfb61eb59d1fa66c550a133ac8b6f8d7f2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769007"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して App Service アプリを作成する

アプリをクラウドにデプロイすることによって、[Azure App Service](overview.md) の使用を開始します。 <abbr title="1 つ以上の Azure リソースを宣言により定義すると共に、デプロイされるリソース間の依存関係を定義する JSON ファイル。 このテンプレートを使えば、リソースを一貫性のある形で繰り返しデプロイできます。">ARM テンプレート</abbr> と [Azure CLI](/cli/azure/get-started-with-azure-cli) を Cloud Shell で使用して、 App Service の Free レベルを使用するため、このクイックスタートを完了するのにコストはかかりません。 <abbr title="宣言型の構文では、デプロイしようとしているものを、デプロイを作成する一連のプログラミング コマンドを記述しなくても記述できます。">このテンプレートでは、宣言型の構文が使用されています。</abbr>

 環境が前提条件を満たしていて、[ARM テンプレート](../azure-resource-manager/templates/overview.md)の使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

::: zone pivot="platform-windows"
[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1.環境を準備する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. テンプレートを確認する

::: zone pivot="platform-windows"
このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows)からのものです。 これは App Service プランおよび App Service アプリを Windows にデプロイします。

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>テンプレートには何のリソースとパラメーターが定義されていますか?</summary>

テンプレートでは、次の 2 つの Azure リソースが定義されています。

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service プランを作成します。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service アプリを作成します。

次の表は、既定のパラメーターとその説明です。

| パラメーター | Type    | 既定値                | 説明 |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | アプリの名前 |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | アプリのリージョン |
| sku        | string  | "F1"                         | インスタンス サイズ (F1 = Free レベル) |
| language   | string  | ".net"                       | プログラミング言語スタック (.net、php、node、html) |
| helloWorld | boolean | False                        | True = "Hello World" アプリをデプロイする |
| repoUrl    | string  | " "                          | 外部 Git リポジトリ (オプション) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux)からのものです。 これは App Service プランおよび App Service アプリを Windows にデプロイします。

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

このテンプレートには、便宜上定義されている Azure リソースとパラメーターが含まれています。

<details>
<summary>テンプレートには何のリソースとパラメーターが定義されていますか?</summary>

テンプレートでは、次の 2 つの Azure リソースが定義されています。

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service プランを作成します。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service アプリを作成します。

次の表は、既定のパラメーターとその説明です。

| パラメーター | Type    | 既定値                | 説明 |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | アプリの名前 |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | アプリのリージョン |
| sku        | string  | "F1"                         | インスタンス サイズ (F1 = Free レベル) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "プログラミング言語スタック &#124; バージョン" |
| repoUrl    | string  | " "                          | 外部 Git リポジトリ (オプション) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. テンプレートのデプロイ

::: zone pivot="platform-windows"
Windows に .NET Framework アプリをデプロイするには、Azure CLI を使用して次のコードを実行します。 

Replace <abbr title="有効な文字は、`a-z`、`0-9`、`-` です。">`<app-name>`</abbr> グローバルに一意のアプリ名 その他の <abbr title="Azure portal、Azure PowerShell、REST API を使用することもできます。">展開方法</abbr>については、[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)に関するページを参照してください。 [その他の Azure App Service テンプレートのサンプルについてはこちら](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)を参照してください。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Linux で Python アプリを作成するには、次のコードを実行します。 

Replace <abbr title="有効な文字は、`a-z`、`0-9`、`-` です。">`<app-name>`</abbr> グローバルに一意のアプリ名

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>このコードは何をしているのですか?</summary>
<p>これらのコマンドによって次のアクションが実行されます。</p>
<ul>
<li>既定の <abbr title="1 つの単位として管理できる関連する Azure リソースの論理コンテナー。">resource group</abbr>.</li>
<li>既定の <abbr title="アプリをホストする Web サーバー ファームの場所、サイズ、機能を指定するプラン。">App Service プラン</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create"> <abbr title="Web アプリの表現。アプリのコード、DNS ホスト名、証明書、関連リソースが含まれています。">App Service アプリ</abbr>指定された名前で </a></li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>異なる言語スタックはどのようにしてデプロイすればよいですか?</summary>
別の言語スタックをデプロイするには、 <abbr title="このテンプレートは、.NET Core、.NET Framework、PHP、Node.js、静的 HTML アプリと互換性があります。">言語パラメーター</abbr> 適切な値 Java の場合は、<a href="/azure/app-service/quickstart-java-uiex">Java アプリの作成</a>に関する記事を参照してください。

| パラメーター | Type    | 既定値                | 説明 |
|------------|---------|------------------------------|-------------|
| language   | string  | ".net"                       | プログラミング言語スタック (.net、php、node、html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>異なる言語スタックはどのようにしてデプロイすればよいですか?</summary>
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

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4.デプロイの検証

`http://<app_name>.azurewebsites.net/` を参照して、作成されていることを確認します。

<hr/>

## <a name="5-clean-up-resources"></a>5.リソースをクリーンアップする

不要になったら、[リソース グループを削除します](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)。

<hr/>

## <a name="next-steps"></a>次のステップ

- ローカル Git からのデプロイ
- [ASP.NET Core と SQL Database](tutorial-dotnetcore-sqldb-app.md)
- Python と Postgres
- [PHP と MySQL](tutorial-php-mysql-app.md)
- [Java を使用して Azure SQL データベースに接続する](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [カスタム ドメインをマップする](app-service-web-tutorial-custom-domain-uiex.md)
