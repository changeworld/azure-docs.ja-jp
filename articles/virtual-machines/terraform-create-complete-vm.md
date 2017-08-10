---
title: "Terraform を使用して Azure に基本的なインフラストラクチャを作成する | Microsoft Docs"
description: "Terraform を使用して Azure リソースを作成する方法について説明します"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 9660a95b440c2e4311829979e270d9f10099f624
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---

# <a name="create-basic-infrastructure-in-azure-by-using-terraform"></a>Terraform を使用して Azure に基本的なインフラストラクチャを作成する
この記事では、仮想マシンとその基になるインフラストラクチャを Azure にプロビジョニングするために実行する必要がある手順を示します。 Terraform スクリプトを記述する方法のほか、クラウド インフラストラクチャに変更を加える前にそれらを視覚化する方法を説明します。 さらに、Terraform を使用して Azure にインフラストラクチャを作成する方法についても説明します。

開始するには、任意のテキスト エディター (Visual Studio Code、Sublime、Vim など) で \terraform_azure101.tf という名前のファイルを作成します。 Terraform では、フォルダー名がパラメーターとして使用され、そのフォルダー内のすべてのスクリプトが実行されるため、ファイルの名前は重要ではありません。 次のコードを新しいファイルに貼り付けます。

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_appId_from_script_execution"
  client_secret   = "your_password_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
スクリプトの `provider` セクションで、このスクリプトに記述されたリソースを Azure プロバイダーでプロビジョニングするように Terraform に命令しています。 subscription_id、appId、password、および tenant_id の値を取得するには、『[Install and configure Terraform (Terraform のインストールおよび構成)](terraform-install-configure.md)』ガイドを参照してください。 このブロックに含まれるこれらの値を環境変数で作成している場合は、このブロックをインクルードする必要はありません。 

resource `azurerm_resource_group` は、新しいリソース グループを作成するように Terraform に命令しています。 Terraform で使用できるリソースの種類については、この記事で後述します。

## <a name="execute-the-script"></a>スクリプトを実行する
スクリプトを保存したら、コンソール/コマンド ラインに戻り、次のように入力します。
```
terraform init
```
Azure 用の Terraform プロバイダーを初期化します。 その後、次のように入力します。
```
terraform plan terraformscripts
```
ここでは、スクリプトが保存されたフォルダーが `terraformscripts` であると想定しています。 `plan` Terraform コマンドを使用していますが、これはスクリプトに定義されているリソースを調べるものです。 このコマンドは、リソースを Terraform によって保存されている状態情報と比較した後、予定される実行の内容を出力します。Azure にリソースが実際に作成されることは "_ありません_"。 

上記のコマンドを実行すると、次のような画面が表示されます。

![Terraform プラン](linux/media/terraform/tf_plan2.png)

問題がないようであれば、次のコマンドを実行して、この新しいリソース グループを Azure にプロビジョニングします。 
```
terraform apply terraformscripts
```
Azure ポータルに、`terraformtest` という名前の新しい空のリソース グループが表示されます。 次のセクションでは、仮想マシンと、その仮想マシンをサポートするすべてのインフラストラクチャをリソース グループに追加します。

## <a name="provision-an-ubuntu-vm-with-terraform"></a>Terraform を使用して Ubuntu VM をプロビジョニングする
先ほど作成した Terraform スクリプトに手を加えて、Ubuntu を実行する仮想マシンをプロビジョニングするために必要な情報を追加します。 この後のセクションでプロビジョニングするリソースを次に示します。

* サブネットが 1 つあるネットワーク
* ネットワーク インターフェイス カード 
* ストレージ コンテナーを使用するストレージ アカウント
* パブリック IP
* 上記のすべてのリソースを使用する仮想マシン 

各 Azure Terraform リソースの詳細なドキュメントについては、[Terraform のドキュメント](https://www.terraform.io/docs/providers/azurerm/index.html)を参照してください。

完全版の[プロビジョニング スクリプト](#complete-terraform-script)もぜひご利用ください。

### <a name="extend-the-terraform-script"></a>Terraform スクリプトを拡張する
先ほど作成したスクリプトを次のリソースを使用して拡張します。 
~~~~
# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}
~~~~
上のスクリプトは、仮想ネットワークを作成し、その仮想ネットワーク内にサブネットを作成します。 "${azurerm_resource_group.helloterraform.name}" で作成しておいたリソース グループが、仮想ネットワークとサブネットの両方の定義で参照されている点に注目してください。

~~~~
# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}
~~~~
上のスクリプト スニペットは、パブリック IP を作成し、そのパブリック IP を利用するネットワーク インターフェイスを作成しています。 subnet_id と public_ip_address_id の参照に注目してください。 Terraform には、ネットワーク インターフェイスが特定のリソースに依存していること、ネットワーク インターフェイスを作成する前にそのリソースを作成する必要があることを認識するインテリジェンス機能が組み込まれています。

~~~~
# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}
~~~~
ここでは、ストレージ アカウントを作成し、そのストレージ アカウント内にストレージ コンテナーを定義しています。 このストレージ アカウントは、これから作成する仮想マシンの仮想ハード ディスク (VHD) の格納場所です。

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
~~~~
最後に、上のスニペットは、既にプロビジョニングされたすべてのリソースを使用する仮想マシンを作成しています。 すべてのリソースとは、VHD 用のストレージ アカウントとコンテナー、パブリック IP とサブネットが指定されたネットワーク インターフェイス、および作成済みのリソース グループです。 vm_size プロパティに注目してください。このスクリプトでは、Azure A0 SKU を指定しています。

### <a name="execute-the-script"></a>スクリプトを実行する
完全なスクリプトを保存したら、コンソール/コマンド ラインに戻り、次のように入力します。
```
terraform apply terraformscripts
```
しばらくすると、Azure ポータルの `terraformtest` リソース グループに、仮想マシンを含むリソースが表示されます。

## <a name="complete-terraform-script"></a>完全な Terraform スクリプト

便宜のため、下に示すのは、この記事で説明するすべてのインフラストラクチャをプロビジョニングする完全な Terraform スクリプトです。

```
variable "resourcesname" {
  default = "helloterraform"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "XXX"
  client_id       = "XXX"
  client_secret   = "XXX"
  tenant_id       = "XXX"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}

# create virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "tfvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "tfsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}


# create public IPs
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}

# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
```

## <a name="next-steps"></a>次のステップ
Terraform を使用して、Azure に基本的なインフラストラクチャを作成しました。 さらに複雑なシナリオ (ロード バランサーや仮想マシン スケール セットを使用する例など) については、[Azure を対象とした Terraform の例](https://github.com/hashicorp/terraform/tree/master/examples)を参照してください。 サポートされている Azure プロバイダーの最新の一覧については、[Terraform のドキュメント](https://www.terraform.io/docs/providers/azurerm/index.html)を参照してください。

