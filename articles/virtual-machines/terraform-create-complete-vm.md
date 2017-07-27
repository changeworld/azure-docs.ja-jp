---
title: "Terraform を使用して Azure に基本的なインフラストラクチャを作成する | Microsoft Docs"
description: "Terraform を使用して Azure リソースを作成する方法について説明します。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 8b8b3b0b46f79058ee69b9a2014581df433f8d3f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---

# <a name="create-basic-infrastructure-in-azure-using-terraform"></a>Terraform を使用して Azure に基本的なインフラストラクチャを作成する
この記事では、仮想マシンとその基になるインフラストラクチャを Azure にプロビジョニングするために必要な手順を詳しく説明します。 Terraform スクリプトを記述する方法のほか、クラウド インフラストラクチャに変更を加える前にそれらを視覚化する方法が記載されています。 また、Terraform を使用して Azure にインフラストラクチャを作成する方法についても説明します。

まず、任意のテキスト エディター (Visual Studio Code、Sublime、Vim など) で _terraform_azure101.tf_ というファイルを作成します。 厳密なファイル名は重要ではありません。Terraform にフォルダー名をパラメーターとして渡すと、そのフォルダー内のすべてのスクリプトが実行されます。 次のコードを新しいファイルに貼り付けます。

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_client_id_from_script_execution"
  client_secret   = "your_client_secret_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
スクリプトの "provider" セクションで、スクリプトに記述されたリソースを Azure プロバイダーでプロビジョニングするよう Terraform に命令します。 subscription_id、client_id、client_secret、tenant_id の値を取得する方法については、[Terraform のインストールと構成](terraform-install-configure.md)に関するガイドを参照してください。 なお、このブロックでこれらの値の環境変数を作成した場合、このブロックをインクルードする必要はありません。 

"azure_rm_resource_group" リソースでは、新しいリソース グループを作成するよう Terraform に命令しています。 以降のスクリプトを見るとわかるように、Terraform では他にもさまざまな種類のリソースを利用できます。

## <a name="executing-the-script"></a>スクリプトの実行
スクリプトを保存してコンソール/コマンド ラインに戻り、次のように入力します。
```
terraform plan terraformscripts
```
このコマンドの "terraformscripts" は、スクリプトの保存先フォルダーです。 Terraform の "plan" コマンドは、スクリプトに定義されているリソースを探し、Terraform によって保存されている状態情報と比較したうえで、実際には Azure にリソースを "_作成せずに_"、予定された実行内容を出力します。 

上記のコマンドを実行すると、次のような画面が表示されます。

![Terraform の plan コマンドの画像](linux/media/terraform/tf_plan2.png)

問題がないようなので、この新しいリソース グループを Azure にプロビジョニングしましょう。次のコマンドを実行します。 
```
terraform apply terraformscripts
```
ここで Azure Portal を見ると、新しく "terraformtest" という空のリソース グループを確認できます。 次のセクションでは、仮想マシンとそれに関連して必要なすべてのインフラストラクチャをリソース グループに追加します。

## <a name="provisioning-ubuntu-vm-with-terraform"></a>Terraform を使用した Ubuntu VM のプロビジョニング
先ほど作成した Terraform スクリプトに手を加えて、Ubuntu を実行する仮想マシンのプロビジョニングに必要な情報を追加しましょう。 以降の各セクションでプロビジョニングするリソースは、サブネットが 1 つ含まれたネットワークと、ネットワーク インターフェイス カード、ストレージ コンテナーが含まれたストレージ アカウント、パブリック IP、そしてこれらすべてのリソースを利用する仮想マシンです。 Azure Terraform リソースごとの詳しい説明については、[Terraform のドキュメント](https://www.terraform.io/docs/providers/azurerm/index.html)を参照してください。

完全版の[プロビジョニング スクリプト](#complete-terraform-script)もぜひご利用ください。

### <a name="extending-the-terraform-script"></a>Terraform スクリプトの拡張
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
このスクリプトでは、仮想ネットワークを作成し、その仮想ネットワーク内にサブネットを作成します。 あらかじめ "${azurerm_resource_group.helloterraform.name}" で作成しておいたリソース グループが、仮想ネットワークとサブネットの両方の定義で参照されている点に注意してください。

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
上のスクリプト スニペットは、パブリック IP を作成すると共に、その作成したパブリック IP を利用するネットワーク インターフェイスを作成しています。 subnet_id と public_ip_address_id の参照に注目してください。 それらのリソースにネットワーク インターフェイスが依存していて、ネットワーク インターフェイスを作成する前にそれらのリソースを作成しておく必要があることが、Terraform に組み込まれているインテリジェンス機能によって認識されます。

~~~~
# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
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
このコードでは、ストレージ アカウントを作成し、そこにストレージ コンテナーを定義しています。今から作成しようとしている仮想マシンの VHD は、このストレージ コンテナーに格納することになります。

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
最後に、上のスニペットで仮想マシンを作成します。これまでプロビジョニングしてきたすべてのリソース、つまり仮想ハード ディスク (VHD) のストレージ アカウントとコンテナー、パブリック IP とサブネットを指定したネットワーク インターフェイス、既に作成したリソース グループは、この仮想マシンで利用します。 vm_size プロパティに注目してください。このスクリプトでは、Azure A0 SKU を指定しています。

### <a name="executing-the-script"></a>スクリプトの実行
完全なスクリプトを保存してコンソール/コマンド ラインに戻り、次のように入力します。
```
terraform apply terraformscripts
```
しばらくすると、Azure Portal の "terraformtest" リソース グループに一連のリソース (仮想マシンを含む) が表示されます。

## <a name="complete-terraform-script"></a>完全な Terraform スクリプト

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

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
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
Terraform を使用して Azure に基本的なインフラストラクチャを作成しました。 ロード バランサーや仮想マシン スケール セットを使用した例など、さらに複雑なシナリオについては、[Azure を対象とした Terraform の例](https://github.com/hashicorp/terraform/tree/master/examples)が数多く用意されているので、そちらを参照してください。 サポートされている Azure プロバイダーを網羅した最新の一覧は、[Terraform のドキュメント](https://www.terraform.io/docs/providers/azurerm/index.html)に記載されています。
