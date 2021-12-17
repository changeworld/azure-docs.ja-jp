---
title: Terraform テンプレートを使用して App Service アプリを作成する
description: App Service にデプロイする多くの方法の 1 つである Terraform テンプレートを使用して、Azure App Service への最初のアプリを数秒で作成します。
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.custom: subject-terraform
ms.openlocfilehash: d41b61252abd3e914239a28e78564128a4fbf32d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227152"
---
# <a name="create-app-service-app-using-a-terraform-template"></a>Terraform テンプレートを使用して App Service アプリを作成する

[Terraform](/azure/developer/terraform/) を使用して、アプリをクラウドにデプロイすることによって、[Azure App Service](overview.md) の使用を開始します。 App Service の Free レベルを使用するため、このクイックスタートを完了するのにコストはかかりません。

Terraform を利用すれば、Azure で完全なインフラストラクチャ デプロイを定義し、作成できます。 整合性があり、再現可能な方法で Azure リソースを作成し、構成する Terraform テンプレートを人間が読める形式でビルドします。 この記事では、Terraform で Windows アプリを作成する方法を説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

Terraform の構成: まだ行っていない場合は、次のいずれかのオプションを使用して Terraform を構成します。

* [Bash を使用して Azure Cloud Shell で Terraform を構成する](/azure/developer/terraform/get-started-cloud-shell-bash?tabs=bash)
* [PowerShell を使用して Azure Cloud Shell で Terraform を構成する](/azure/developer/terraform/get-started-cloud-shell-powershell?tabs=bash)
* [Bash を使用して Windows で Terraform を構成する](/azure/developer/terraform/get-started-windows-bash?tabs=bash)
* [PowerShell を使用して Windows で Terraform を構成する](/azure/developer/terraform/get-started-windows-powershell?tabs=bash)

Azure Terraform Visual Studio Code 拡張機能を使用すると、エディターから Terraform を操作できます。 この拡張機能を使用して、Terraform 構成の作成、テスト、実行を行うことができます。 また、この拡張機能は、グラフを使ったリソースの視覚化もサポートしています。 Azure Terraform Visual Studio Code 拡張機能の構成については、[こちらのガイド](/azure/developer/terraform/configure-vs-code-extension-for-terraform)をご覧ください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートを以下に示します。 これにより App Service プランと App Service アプリが Windows 上にデプロイされます。また、サンプル Node.js "Hello World" アプリが [Azure サンプル](https://github.com/Azure-Samples) リポジトリからデプロイされます。

```hcl
# Configure the Azure provider
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 2.65"
    }
  }
  required_version = ">= 0.14.9"
}
provider "azurerm" {
  features {}
}
# Generate a random integer to create a globally unique name
resource "random_integer" "ri" {
  min = 10000
  max = 99999
}
# Create the resource group
resource "azurerm_resource_group" "rg" {
  name     = "myResourceGroup-${random_integer.ri.result}"
  location = "eastus"
}
# Create the Linux App Service Plan
resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "webapp-asp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku {
    tier = "Free"
    size = "F1"
  }
}
# Create the web app, pass in the App Service Plan ID, and deploy code from a public GitHub repo
resource "azurerm_app_service" "webapp" {
  name                = "webapp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
  source_control {
    repo_url           = "https://github.com/Azure-Samples/nodejs-docs-hello-world"
    branch             = "master"
    manual_integration = true
    use_mercurial      = false
  }
}
```

テンプレートには、次の 3 つの Azure リソースと 1 つのサブリソースが定義されています。 詳細および使用方法に関する情報については、次の [Azure プロバイダーの Terraform レジストリ](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)のリンクからご確認ください。

* [**Microsoft.Resources/resourcegroups**](/azure/templates/microsoft.resources/resourcegroups?tabs=json): リソース グループを作成します (まだ存在していない場合)。
  * [azurerm_resource_group](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group) 
* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service プランを作成します。
  * [azurerm_app_service_plan](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service_plan)
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service アプリを作成します。
  * [azurerm_app_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service)
* [**Microsoft.Web/sites/sourcecontrols**](/azure/templates/microsoft.web/sites/sourcecontrols): 外部 Git デプロイ構成を作成します。
  * [source_control](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service#source_control)

Terraform テンプレートを作成する方法の詳細については、[Terraform に関するドキュメント](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)をご覧ください。

## <a name="implement-the-terraform-code"></a>Terraform コードを実装する

Terraform には、インフラストラクチャを管理、構築、デプロイ、更新するための機能が多数用意されています。 以下の手順は、ご自身のリソースをデプロイおよび破棄する方法を示しています。 詳細については、こちらの [Terraform に関するドキュメント](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)と [Azure 上の Terraform のドキュメント](/azure/developer/terraform/)をご覧ください。これらのドキュメントは、Azureインフラストラクチャ戦略の一環として Terraform を使用する場合に確認する必要があります。

1. サンプル Terraform コードをテストして実行するディレクトリを作成し、それを現在のディレクトリにします。

    ```bash
    mkdir appservice_tf_quickstart
    cd appservice_tf_quickstart
    ```

1. `main.tf` という名前のファイルを作成し、上記のコードを挿入します。

    ```bash
    code main.tf
    ```

1. Terraform を初期化します。

    ```bash
    terraform init
    ```

1. Terraform プランを作成します。

    ```bash
    terraform plan
    ```

1. `main.tf` 構成ファイルで定義されているリソースをプロビジョニングします (操作を確認するには、プロンプトで「`yes`」と入力します)。

    ```bash
    terraform apply
    ```

## <a name="validate-the-deployment"></a>デプロイの検証

1. Azure portal のメイン メニューで、 **[リソース グループ]** を選択し、上記のテンプレートを使用して作成したリソース グループに移動します。 この名前は "myResourceGroup-" で始まり、その後にランダムな整数の文字列が続きます。

1. これで Terraform によって作成されたすべてのリソース (App Service と App Service プラン) が表示されるようになりました。

1. **[App Service]** を選択し、URL に移動して、ご自身にサイトが適切に作成されたことを確認します。 代わりに、`http://<app_name>.azurewebsites.net/` に移動して確認することもできます。このアプリ名は "webapp-" で始まり、その後にリソース グループの同じランダムな整数文字列が続きます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[リソース グループを削除](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)するか、ご自身のターミナル/コマンド ラインに戻って `terraform destroy` を実行し、このクイックスタートに関連付けられているすべてのリソースを削除します。

> [!NOTE]
> Azure App Service のその他の Terraform のサンプルについては、[こちら](./samples-terraform.md)を参照してください。 [こちら](https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples)からは、すべての Azure サービスのその他の Terraform サンプルをご確認いただくことができます。
## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"] 
> [Azure での Terraform の使用について詳細を参照](/azure/terraform)
> [!div class="nextstepaction"] 
> [Azure App Service の Terraform サンプル](./samples-terraform.md)