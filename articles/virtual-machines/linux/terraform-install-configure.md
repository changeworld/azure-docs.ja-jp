---
title: VM などのインフラストラクチャを Azure にプロビジョニングするための Terraform のインストールと構成 | Microsoft Docs
description: Azure リソースを作成するための Terraform のインストールと構成
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: dada9c70eef2adb2704e276a5401509581e37538
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399170"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>VM などのインフラストラクチャを Azure にプロビジョニングするための Terraform のインストールと構成
 
Terraform は、[シンプルなテンプレート言語](https://www.terraform.io/docs/configuration/syntax.html)を使ってクラウド インフラストラクチャを簡単に定義、プレビュー、およびデプロイできるツールです。 この記事では、Terraform を使用して Azure にリソースをプロビジョニングするために必要な手順について説明します。 

> [!TIP]
Azure で Terraform を使用する方法の詳細については、[Terraform ハブ](/azure/terraform)に関する記事を参照してください。 [Cloud Shell](/azure/terraform/terraform-cloud-shell) には、Terraform が既定でインストールされています。 Cloud Shell を使用する場合は、このドキュメントのインストール/セットアップの部分は省略してかまいません。

## <a name="install-terraform"></a>Terraform のインストール

Terraform をインストールするには、ご利用のオペレーティング システムに適したパッケージを別個のインストール ディレクトリに[ダウンロード](https://www.terraform.io/downloads.html)します。 ダウンロードには実行可能ファイルが 1 つ含まれており、そのファイルのグローバル パスを定義する必要があります。 Linux と Mac 上でパスを設定する方法の詳細については、[こちらの Web ページ](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)をご覧ください。 Windows 上でパスを設定する方法の詳細については、[こちらの Web ページ](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)をご覧ください。 

`terraform` コマンドでパス構成を確認します。 使用可能な Terraform オプションの一覧が次のように出力として表示されます。

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Terraform から Azure にアクセスするための設定

[Azure AD サービス プリンシパル](/cli/azure/create-an-azure-service-principal-azure-cli)を構成して、Terraform で Azure にリソースをプロビジョニングできるようにします。 サービス プリンシパルは、Azure サブスクリプションにリソースをプロビジョニングするために、資格情報を使用して Terraform スクリプトを許可します。

Azure AD アプリケーションと Azure AD サービス プリンシパルは、いくつかの方法で作成できます。 現在、最も簡単で時間のかからない方法は、Azure CLI 2.0 を使用する方法です。[こちらからダウンロードして Windows、Linux、Mac にインストール](/cli/azure/install-azure-cli)できます。

次のコマンドを実行してサインインし、Azure サブスクリプションの管理を行います。

   `az login`

複数の Azure サブスクリプションを使用している場合は、`az login` コマンドを実行するとそれぞれの詳細が返されます。 使用するサブスクリプションから返された `id` フィールドの値を `SUBSCRIPTION_ID` 環境変数に設定します。 

このセッションで使用するサブスクリプションを設定します。

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

アカウントを照会してサブスクリプション ID とテナント ID の値を取得します。

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

次に、Terraform 用に別個の資格情報を作成します。

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

appId、password、sp_name、tenant が返されます。 appId と password を書き留めておいてください。

資格情報をテストするには、新しいシェルを開き、sp_name、パスワード、およびテナントの戻り値を使用して次のコマンドを実行します。

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Terraform 環境変数の構成

Azure リソースを作成するときに、サービス プリンシパルのテナント ID、サブスクリプション ID、クライアント ID、およびクライアント シークレットを使用するように Terraform を構成します。 Azure パブリック以外の Azure クラウドで作業している場合は、環境を設定することもできます。 次の環境変数を設定します。これらは [Azure Terraform モジュール](https://registry.terraform.io/modules/Azure)で自動的に使用されます。

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

これらの変数は、次のサンプル シェル スクリプトを使用して設定できます。

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>サンプル スクリプトの実行

空のディレクトリに `test.tf` というファイルを作成し、次のスクリプトを貼り付けます。 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

ファイルを保存して `terraform init` を実行します。 このコマンドによって、Azure リソース グループを作成するために必要な Azure モジュールがダウンロードされます。 次の出力が表示されます。

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

`terraform plan` を使用してスクリプトをプレビューし、`terraform apply` を使用して `testResouceGroup` リソース グループを作成します。

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>次の手順

Azure サブスクリプションへのインフラストラクチャのデプロイを開始できるように、Terraform のインストールと Azure 資格情報の構成を完了しました。 その後、空の Azure リソース グループを作成してインストールをテストしました。

> [!div class="nextstepaction"]
> [Terraform がインストールされている Azure VM を作成する](terraform-create-complete-vm.md)

