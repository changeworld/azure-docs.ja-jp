---
title: "Terraform で HCL を使用して Azure VM スケール セットを作成する"
description: "Terraform を使用して、仮想ネットワークと管理対象のアタッチされたディスクを含む Azure 仮想マシン スケール セットの構成とバージョン管理を行います。"
keywords: "terraform, 開発, スケール セット, 仮想マシン, ネットワーク, ストレージ, モジュール"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>Terraform を使用した、管理対象ストレージとネットワーク接続された Azure VM スケール セットの計画と作成

この記事では、[Terraform](https://www.terraform.io/) で [Hashicorp 構成言語](https://www.terraform.io/docs/configuration/syntax.html) (HCL) を使用して、管理対象ディスクを含む [Azure 仮想マシン スケール セット](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview)を作成し、デプロイします。  

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Terraform のデプロイを設定する
> * Terraform のデプロイの変数と出力を使用する 
> * ネットワーク インフラストラクチャを作成してデプロイする
> * 仮想マシン スケール セットを作成してデプロイし、ネットワークにアタッチする
> * SSH 経由で VM に接続するジャンプボックスを作成してデプロイする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

- [Terraform をインストールし、Azure へのアクセスを構成します](/azure/virtual-machines/linux/terraform-install-configure)
- [SSH キーペアがまだない場合は作成します](/azure/virtual-machines/linux/mac-create-ssh-keys)

## <a name="create-the-file-structure"></a>ファイル構造を作成します

空のディレクトリに次の名前で 3 つの新しいファイルを作成します。

- `variables.tf` このファイルは、テンプレートに使用される変数の値を保持します。
- `output.tf` このファイルには、デプロイ後に表示される設定が記述されています。
- `vmss.tf` 仮想マシン スケール セット インフラストラクチャのコードです。

## <a name="create-the-variables-and-output-definitions"></a>変数を作成と定義の出力

この手順では、Terraform で作成されたリソースをカスタマイズする変数を定義します。

`variables.tf` ファイルを編集し、次のコードをコピーしてから、変更を保存します。

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

`output.tf` ファイルを編集し、次のコードをコピーして、仮想マシンの完全修飾ドメイン名を表示します。 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>テンプレートでネットワーク インフラストラクチャを定義する

この手順では、新しい Azure リソース グループに次のネットワーク インフラストラクチャを作成します。 

  - 10.0.0.0/16 のアドレス空間を使用する 1 つの VNET 
  - 10.0.2.0/24 のアドレス空間を使用する 1 つのサブネット
  - 2 つのパブリック IP アドレス。 1 つは仮想マシン スケール セット ロード バランサーに使用され、もう 1 つは SSH ジャンプボックスへの接続に使用されます。


次のコードを編集し、`vmss.tf` ファイルにコピーします。 

```tf
resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> 今後識別しやすいように、Azure にデプロイするリソースにタグを付けることをお勧めします。

## <a name="create-the-network-infrastructure"></a>ネットワーク インフラストラクチャを作成する。

`.tf` ファイルを作成したディレクトリで次のコマンドを実行して、Terraform 環境を初期化します。

```bash
terraform init 
```

次のコマンドを実行して、Azure にインフラストラクチャをデプロイします。

```bash
terraform apply
```

パブリック IP アドレスの FQDN が実際の構成と対応していることを確認します。![パブリック IP アドレスの VMSS Terraform FQDN](./media/tf-create-vmss-step4-fqdn.png)


このリソース グループには次のようなリソースがあります。![VMSS Terraform ネットワークのリソース](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>インフラストラクチャを編集して仮想マシン スケール セットを追加する

この手順では、次のリソースをテンプレートに追加します。

- 1 つの Azure ロード バランサーと、アプリケーションにサービスを提供し、前述の手順で構成したパブリック IP アドレスにアタッチするルール。
- Azure バックエンド アドレス プールと、そのロード バランサーへの割り当て 
- アプリケーションに使用され、ロード バランサーに構成される正常性プローブ ポート 
- ロード バランサーの背後に構成される仮想マシン スケール セット (前述の手順でデプロイした VNET 上で実行されます)
- カスタム スクリプト拡張機能を使用する、仮想マシン スケールのノード上の [Nginx](http://nginx.org/)。

`vmss.tf` ファイルの末尾に次のコードを追加します。

```tf
resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
    resource_group_name            = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id                = "${azurerm_lb.vmss.id}"
    name                           = "http"
    protocol                       = "Tcp"
    frontend_port                  = "${var.application_port}"
    backend_port                   = "${var.application_port}"
    backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
    frontend_ip_configuration_name = "PublicIPAddress"
    probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
  }

  tags {
    environment = "codelab"
  }
}
```

次のコードを `variables.tf` に追加してデプロイをカスタマイズします。

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Azure で仮想マシン スケール セットをデプロイする

次のコマンドを実行して、仮想マシン スケール セットのデプロイを視覚化します。

```bash
terraform plan
```

コマンドの出力は次のようになります。
![Terraform で vmss の計画を追加する](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

次に、Azure でその他のリソースをデプロイします。 

```bash
terraform apply 
```

リソース グループの内容は次のようになります。

![Terraform vm スケールセットのリソース グループ](./media/tf-create-vmss-step6-apply.png)

ブラウザーを開き、コマンドから返された FQDN に接続します。 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>SSH ジャンプボックスを既存のネットワークに追加する 

この手順では、次のリソースを構成します。
- 仮想マシン スケール セットと同じサブネットに接続されているネットワーク インターフェイス。
- このネットワーク インターフェイスと接続されている仮想マシン。 この "ジャンプボックス" にはリモートからアクセスできます。 接続すると、スケール セット内の任意の仮想マシンに対して SSH を実行できるようになります。



`vmss.tf` ファイルの末尾に次のコードを追加します。

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

`outputs.tf` を編集し、次のコードを追加して、デプロイの完了時にジャンプボックスのホスト名が表示されるようにします。

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>ジャンプボックスをデプロイする

ジャンプボックスをデプロイします。

```bash
terraform apply 
```

デプロイが完了すると、リソース グループの内容は次のようになります。

![Terraform vm スケールセットのリソース グループ](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> デプロイしたジャンプボックスと仮想マシン スケール セットでは、パスワードによるログインは無効にされています。 VM にアクセスするには、SSH を使用してログインします。

## <a name="clean-up-the-environment"></a>環境のクリーンアップ

次のコマンドを実行して、このチュートリアルで作成したリソースを削除します。

```bash
terraform destroy
```

リソースの削除について確認を求められたら、`yes` と入力します。 削除プロセスが完了するまでに数分かかります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Terraform を使用して仮想マシン スケール セットを Azure にデプロイしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Terraform のデプロイを初期化する
> * Terraform のデプロイの変数と出力を使用する 
> * ネットワーク インフラストラクチャを作成してデプロイする
> * 仮想マシン スケール セットを作成してデプロイし、既存の環境にアタッチする
> * SSH 経由で VM に接続するジャンプボックスを作成してデプロイする 
