---
title: チュートリアル - モジュール レジストリを使用した Terraform での Azure VM クラスターの作成
description: このチュートリアルでは、Terraform モジュールを使用して Windows 仮想マシン クラスターを Azure に作成します
keywords: Azure DevOps Terraform VM 仮想マシン クラスター モジュール レジストリ
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945283"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>チュートリアル:モジュール レジストリを使用した Terraform での Azure VM クラスターの作成

この記事では、Terraform [Azure コンピューティング モジュール](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2)を使用した小さな VM クラスターの作成について説明します。 このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * Azure で認証を設定する
> * Terraform テンプレートを作成する
> * プランを使用して変更を視覚化する
> * 構成を適用して VM クラスターを作成する

Terraform の詳細については、[Terraform のドキュメント](https://www.terraform.io/docs/index.html)を参照してください。

## <a name="set-up-authentication-with-azure"></a>Azure で認証を設定する

> [!TIP]
> [Terraform 環境変数を使用する](terraform-install-configure.md)か、または [Azure Cloud Shell](/azure/cloud-shell/overview) でこのチュートリアルを実行する場合は、この手順を省略します。

 Azure サービス プリンシパルを作成するには、「[Install Terraform and configure access to Azure (Terraform のインストールおよび Azure へのアクセスの構成)](terraform-install-configure.md)」を確認してください。 このサービス プリンシパルを使用して、空のディレクトリ内の新しいファイル `azureProviderAndCreds.tf` に次のコードを入力します。

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>テンプレートを作成する

次のコードを使用して、`main.tf` という名前の新しい Terraform テンプレートを作成します。

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    is_windows_image = "true"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

構成ディレクトリで `terraform init` を実行します。 0\.10.6 以上の Terraform バージョンを使用すると、次の出力が表示されます。

![Terraform の初期化](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>プランを使用して変更を視覚化する

テンプレートによって作成された仮想マシン インフラストラクチャをプレビューするには、`terraform plan` を実行します。

![Terraform プラン](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>適用によって仮想マシンを作成する

Azure に VM をプロビジョニングするには、`terraform apply` を実行します。

![Terraform の適用](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"] 
> [Azure Terraform モジュールの一覧を参照する](https://registry.terraform.io/modules/Azure)