---
title: クイックスタート - Terraform を使用して完全な Linux VM を Azure に作成する
description: このクイックスタートでは、Terraform を使用して、完全な Linux 仮想マシン環境を Azure に作成して管理します
keywords: Azure DevOps Terraform Linux VM 仮想マシン
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 03974d68477855d4ff55b7179312c91ba7d0d055
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943523"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>クイック スタート:Terraform によって Azure に完全な Linux 仮想マシンのインフラストラクチャを作成する

Terraform を利用すれば、Azure で完全なインフラストラクチャ デプロイを定義し、作成できます。 整合性があり、再現可能な方法で Azure リソースを作成し、構成する Terraform テンプレートを人間が読める形式でビルドします。 この記事では、Terraform を使用して、完全な Linux 環境とサポート リソースを作成する方法を示します。 [Terraform をインストールし、構成する](terraform-install-configure.md)方法についても説明します。

> [!NOTE]
> Terraform 固有のサポートについては、コミュニティ チャネルのいずれかを使用して Terraform に直接問い合わせてください。
>
>    * コミュニティ ポータルの [Terraform セクション](https://discuss.hashicorp.com/c/terraform-core)には、質問、ユース ケース、および役立つパターンが含まれています。
>
>    * プロバイダー関連の質問については、コミュニティ ポータルの [Terraform プロバイダー](https://discuss.hashicorp.com/c/terraform-providers) セクションにアクセスしてください。


## <a name="create-azure-connection-and-resource-group"></a>Azure 接続とリソース グループを作成する

それでは、Terraform テンプレートの各セクションに進みましょう。 [Terraform テンプレート](#complete-terraform-script)の完全版も表示できます。それをコピーし、貼り付けることができます。

`provider` セクションは、Azure プロバイダーを使用するように Terraform に伝えます。 *subscription_id*、*client_id*、*client_secret*、*tenant_id* の値を取得する方法については、[Terraform のインストールと構成](terraform-install-configure.md)に関するページを参照してください。 

> [!TIP]
> これらの値の環境変数を作成する場合や、[Azure Cloud Shell Bash エクスペリエンス](/azure/cloud-shell/overview)を使用している場合は、このセクションに変数の宣言を含める必要はありません。

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

次のセクションでは、`myResourceGroup` という名前のリソース グループを `eastus` という場所に作成します。

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

追加のセクションでは、 *${azurerm_resource_group.myterraformgroup.name}* でリソース グループを参照します。

## <a name="create-virtual-network"></a>Create virtual network
次のセクションでは、*10.0.0.0/16* アドレス空間に、*myVnet* という名前の仮想ネットワークを作成しています。

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

次のセクションでは、*myVnet* 仮想ネットワークに *mySubnet* という名前のサブネットを作成します。

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>パブリック IP アドレスの作成
インターネット経由でリソースにアクセスするには、パブリック IP アドレスを作成して、VM に割り当てます。 次のセクションでは、*myPublicIP* という名前のパブリック IP アドレスを作成します。

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループは、VM から送受信されるネットワーク トラフィック フローを制御します。 次のセクションでは、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成して、TCP ポート 22 で SSH トラフィックを許可するルールを定義しています。

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>仮想ネットワーク インターフェイス カードの作成
仮想ネットワーク インターフェイス カード (NIC) は、VM を特定の仮想ネットワーク、パブリック IP アドレス、およびネットワーク セキュリティ グループに接続します。 Terraform テンプレートの次のセクションでは、作成した仮想ネットワーク リソースに接続された *myNIC* という名前の仮想 NIC を作成しています。

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>診断のためのストレージ アカウントを作成する
VM のブート診断を格納するには、ストレージ アカウントを作成する必要があります。 このブート診断は、問題の解決や VM 状態の監視に役立ちます。 ここでは、ブート診断データを保存するためだけにストレージ アカウントを作成します。 ストレージ アカウントごとに一意の名前を使用する必要があるため、次のセクションはランダム テキストを生成します。

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

これでストレージ アカウントを作成できます。 次のセクションはストレージ アカウントを作成します。前の手順で生成されたランダム テキストに基づく名前が使用されます。

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>仮想マシンの作成

最後の手順は、VM を作成し、作成したすべてのリソースを使用することです。 次のセクションでは、*myVM* という名前の VM を作成し、*myNIC* という名前の仮想 NIC を接続しています。 最新の *Ubuntu 16.04-LTS* イメージが使用されます。*azureuser* という名前のユーザーが作成されます。その際、パスワード認証は無効になります。

 SSH キー データは *ssh_keys* セクションで与えられます。 *key_data* フィールドに有効な SSH 公開キーを与えます。

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>完全な Terraform スクリプト

以上のセクションをまとめ、Terraform の動作を確認するには、*terraform_azure.tf* という名前のファイルを作成し、次のコンテンツを貼り付けます。

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>インフラストラクチャをビルドし、デプロイする
Terraform テンプレートが作成されたら、最初に Terraform を初期化します。 これによって、Azure でテンプレートをビルドするためのあらゆる前提条件が Terraform に与えられます。

```bash
terraform init
```

次に、Terraform にテンプレートを検証させます。 この手順では、要求されたリソースが Terraform によって保存された状態情報と比較され、予定されている実行が出力されます。 リソースは Azure では*作成されません*。

```bash
terraform plan
```

上記のコマンドを実行すると、次のような画面が表示されます。

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

すべてが正しく思われ、Azure でインフラストラクチャをビルドする準備ができていれば、Terraform でテンプレートを適用します。

```bash
terraform apply
```

Terraform が完了すると、VM インフラストラクチャが準備完了となります。 [az vm show](/cli/azure/vm) で VM のパブリック IP アドレスを取得します。

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

VM に SSH 接続できます。

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure での Terraform の使用について詳細を参照](/azure/terraform)