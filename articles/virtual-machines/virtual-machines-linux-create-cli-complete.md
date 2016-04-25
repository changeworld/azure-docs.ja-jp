<properties
   pageTitle="Azure CLI を使用して新しく Linux VM を作成する | Microsoft Azure"
   description="Azure CLI を使用して、Linux VM、ストレージ、仮想ネットワークとサブネット、NIC、パブリック IP、ネットワーク セキュリティ グループすべてを新しく作成します。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>

# Azure CLI を使用して新しく Linux VM を作成する

Linux VM を作成するには、Resource Manager モードの [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`) と、JSON 解析ツールが必要になります。このドキュメントでは [jq](https://stedolan.github.io/jq/) を使用します。

## クイック コマンド

```bash
# Create the Resource Group
chrisL@fedora$ azure group create TestRG westeurope

# Create the Storage Account
chrisL@fedora$ azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
chrisL@fedora$ azure group show testrg --json | jq '.'

# Create the Virtual Network
chrisL@fedora$ azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
chrisL@fedora$ azure group show testrg --json | jq '.'

# Create the Subnet
chrisL@fedora$ azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
chrisL@fedora$ azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
chrisL@fedora$ azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
chrisL@fedora$ azure network nic show testrg testnic --json | jq '.'

# Create the NSG
chrisL@fedora$ azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
chrisL@fedora$ azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
chrisL@fedora$ azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
chrisL@fedora$ azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
chrisL@fedora$ azure network nic set --public-ip-name testpip testrg testnic

# Bind the NSG to the NIC
chrisL@fedora$ azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
chrisL@fedora$ azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops

# Verify everything built
chrisL@fedora$ azure vm show testrg testvm

```

## 詳細なチュートリアル

### はじめに

この記事では、VNetwork サブネット内に 1 つの Linux VM を持つデプロイを構築します。ここでは、インターネット上のどこからでも接続できる、セキュリティで保護された実用的な VM を構築するまで、各コマンドの説明を交えながら基本的なデプロイ全体を説明します。

その過程で、Resource Manager デプロイ モデルによって提供される依存関係階層とその性能についても説明します。システムがどのように構築されているかをいったん理解すると、より直接的な Azure CLI コマンドを使用してより短時間でシステムを再構築することも (`azure vm quick-create` コマンドを使用したほぼ同じデプロイについては、[こちら](virtual-machines-linux-quick-create-cli.md)を参照してください)、[Azure Resource Manager テンプレート](../resource-group-authoring-templates.md)を使用して、ネットワークとアプリケーションのデプロイ全体を設計して自動化し、それらを更新する方法の習得に移ることもできます。デプロイの各部分がどのように組み合わさっているかがわかると、それらを自動化するためのテンプレートの作成はより簡単になります。

ここでは、開発と単純なコンピューティングに役立つ VM を含む単純なネットワークを構築し、それについて説明します。この記事を理解すると、より複雑なネットワークやデプロイに進むことができます。

### リソース グループの作成とデプロイ先の選択

Azure リソース グループは、リソース デプロイの論理的な管理を可能にする構成とその他のメタデータを含む論理的なデプロイ エンティティです。

```
chrisL@fedora$ azure group create TestRG westeurope                        
info:    Executing command group create
+ Getting resource group TestRG
+ Creating resource group TestRG
info:    Created resource group TestRG
data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

### ストレージ アカウントの作成

数あるシナリオの中で特に、VM ディスクと追加するその他のデータ ディスク用のストレージ アカウントが必要になります。つまり、リソース グループを作成したら、そのほぼ直後には必ずストレージ アカウントを作成します。

ここでは、`azure storage account create` コマンドを使用し、アカウントの場所、アカウントを制御するリソース グループ、必要なストレージ サポートの種類を渡します。

```
chrisL@fedora$ azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
rasquill•~/workspace/keygen» azure group show testrg
info:    Executing command group show
+ Listing resource groups
+ Listing resources for the group
data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    Resources:
data:
data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
data:      Name    : computeteststore
data:      Type    : storageAccounts
data:      Location: westeurope
data:      Tags    :
data:
data:    Permissions:
data:      Actions: *
data:      NotActions:
data:
info:    group show command OK
```

ここでは、`--json` Azure CLI オプションと共に [jq](https://stedolan.github.io/jq/) ツール (JSON の解析には **jsawk** やお好みの言語ライブラリを使用できます) を使用し、`azure group show` コマンドを使用してリソース グループを調べることができます。

```
chrisL@fedora$ azure group show testrg --json | jq '.'                                                                                        
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

CLI を使用してストレージ アカウントを調査するには、まず次のコマンドの一種を使用してアカウント名とキーを設定する必要があるため、この記事のストレージ アカウント名を自身のストレージ アカウント名に置き換えます。

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

その後、ストレージ情報は簡単に表示できるようになります。

```
chrisL@fedora$ azure storage container list
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```
### 仮想ネットワークとサブネットの作成

VM をインストールできるように、Azure 仮想ネットワークとサブネットを作成する必要があります。

```
chrisL@fedora$ azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
info:    Executing command network vnet create
+ Looking up virtual network "TestVNet"
+ Creating virtual network "TestVNet"
+ Loading virtual network state
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
data:    Name                            : TestVNet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

ここで、もう一度 `azure group show` の --json オプションと **jq** を使用して、リソースがどのように作成されているかを見てみましょう。この段階で、`storageAccounts` リソースと `virtualNetworks` リソースが作成されています。

```
chrisL@fedora$ azure group show testrg --json | jq '.'
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

次に、VM のデプロイ先となるサブネットを `TestVnet` 仮想ネットワーク内に作成します。次に示すように、`azure network vnet subnet create` コマンドで既に作成済みのリソース (`TestRG` リソース グループ、`TestVNet` 仮想ネットワーク) を使用し、さらにサブネット名 `FrontEnd` とサブネット アドレス プレフィックス `192.168.1.0/24` を追加します。

```
chrisL@fedora$ azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
info:    Executing command network vnet subnet create
+ Looking up the subnet "FrontEnd"
+ Creating subnet "FrontEnd"
+ Looking up the subnet "FrontEnd"
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : FrontEnd
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

サブネットは論理的に仮想ネットワーク内に存在するため、サブネットの情報を探すには、若干異なるコマンド `azure network vnet show` を使用します。ただし、**jq** を使用して JSON 出力を調査するやり方は同じです。

```
chrisL@fedora$ azure network vnet show testrg testvnet --json | jq '.'
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "FrontEnd",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
  "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
  "name": "TestVNet",
  "location": "westeurope"
}
```

### Linux VM で使用する NIC の作成

NIC はプログラムで使用できるほか、その使用に規則を適用したり、複数の NIC を使用したりすることができます。

```
chrisL@fedora$ azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
info:    Executing command network nic create
+ Looking up the network interface "TestNIC"
+ Looking up the subnet "FrontEnd"
+ Creating network interface "TestNIC"
+ Looking up the network interface "TestNIC"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
data:    Name                            : TestNIC
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : NIC-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.101
data:      Private IP Allocation Method  : Static
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:
info:    network nic create command OK
```

NIC リソースは VM とネットワーク セキュリティ グループの両方に関連付けられているため、`TestRG` リソース グループを調査すると、最上位のリソースとして表示されます。

```
chrisL@fedora$ azure group show testrg --json | jq '.'
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/TestRG",
"name": "TestRG",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
    "name": "TestNIC",
    "type": "networkInterfaces",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
    "name": "TestVNet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
    "name": "computeteststore",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

詳細を確認するには、`azure network nic show` コマンドを使用して、直接リソースを調査します。

```
chrisL@fedora$ azure network nic show testrg testnic --json | jq '.'
{
"ipConfigurations": [
    {
    "loadBalancerBackendAddressPools": [],
    "loadBalancerInboundNatRules": [],
    "privateIpAddress": "192.168.1.101",
    "privateIpAllocationMethod": "Static",
    "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    },
    "provisioningState": "Succeeded",
    "name": "NIC-config",
    "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
    }
],
"tags": {},
"dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
},
"enableIPForwarding": false,
"provisioningState": "Succeeded",
"etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
"id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
"name": "TestNIC",
"location": "westeurope"
}
```

### ネットワーク セキュリティ グループと規則の作成

ここで、ネットワーク セキュリティ グループ (NSG) と、NIC へのアクセスを統制する受信規則を作成します。

```
chrisL@fedora$ azure network nsg create testrg testnsg westeurope
```

nsg の受信規則を追加して、(SSH をサポートするために) ポート 22 での受信接続を許可します。

```
chrisL@fedora$ azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule
```

> [AZURE.NOTE] 受信規則は、受信ネットワーク接続のフィルターです。この例では、NSG を VM の仮想ネットワーク インターフェイス カード (NIC) にバインドします。これは、ポート 22 へのすべての要求が VM の NIC に渡されることを意味します。これは、ポートを開くためのネットワーク接続に関する規則であり、クラシック デプロイにおけるエンドポイントではないため、通常は動的である**任意の**要求元ポートからの受信要求を受け入れるために `--source-port-range` を "*" (既定値) にしておく必要があります。

### パブリック IP アドレス (PIP) の作成

ここでは、`azure network public-ip create` コマンドを使用して、インターネットから VM に接続できるようにするためのパブリック IP アドレス (PIP) を作成します。既定値は動的アドレスであるため、`-d testsubdomain` オプションを使用して、**cloudapp.azure.com** ドメインに名前付き DNS エントリを作成します。

```
chrisL@fedora$ azure network public-ip create -d testsubdomain testrg testpip westeurope
info:    Executing command network public-ip create
+ Looking up the public ip "testpip"
+ Creating public ip address "testpip"
+ Looking up the public ip "testpip"
data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
data:    Name                            : testpip
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

これも最上位のリソースであるため、`azure group show` を使用して確認できます。

```
chrisL@fedora$ azure group show testrg --json | jq '.'
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/TestRG",
"name": "TestRG",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
    "name": "TestNIC",
    "type": "networkInterfaces",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
    "name": "testpip",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
    "name": "TestVNet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
    "name": "computeteststore",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

また、通常どおり、より完全な `azure network public-ip show` コマンドを使用して、サブドメインの完全修飾ドメイン名 (FQDN) など、リソースの詳細を調査することができます。パブリック IP アドレス リソースは論理的に割り当てられていますが、特定のアドレスはまだ割り当てられていないことに注意してください。そのため、VM が必要になりますが、これはまだ作成されていません。

```
azure network public-ip show testrg testpip --json | jq '.'
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "testsubdomain",
    "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
"id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
"name": "testpip",
"location": "westeurope"
}
```

### パブリック IP とネットワーク セキュリティ グループの NIC への関連付け

```
chrisL@fedora$ azure network nic set --public-ip-name testpip testrg testnic
```

NSG を NIC にバインドします。

```
chrisL@fedora$ azure network nic set --network-security-group-name testnsg testrg testnic
```

### Linux VM の作成

これまでの手順で、インターネットにアクセス可能な VM をサポートするためのストレージおよびネットワーク リソースを作成しました。ここでは、その VM を作成し、パスワードなしの SSH キーを使用してセキュリティで保護します。この場合は、最新の LTS に基づいて Ubuntu VM を作成します。「[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](virtual-machines-linux-cli-ps-findimage.md)」で説明されているように、`azure vm image list` を使用してそのイメージの情報を見つけます。イメージは `azure vm image list westeurope canonical | grep LTS` コマンドを使用して選択しました。この場合は、`canonical:UbuntuServer:14.04.3-LTS:14.04.201509080` を使用しますが、最後のフィールドでは `latest` を渡して、今後常に最新ビルドを入手できるようにします (使用する文字列は `canonical:UbuntuServer:14.04.3-LTS:latest` となります)。

> [AZURE.NOTE] この次の手順は、Linux または Mac で **ssh-keygen -t rsa -b 2048** を使用して SSH RSA の公開キーと秘密キーのペアを既に作成した経験があるユーザーであればだれでもよく知っています。`~/.ssh` ディレクトリに証明書キー ペアがない場合は、次のどちらかの方法で作成できます。<br /> 1. `azure vm create --generate-ssh-keys` オプションを使用して自動的に作成する。2. [手順に従って自分で手動で作成する](virtual-machines-linux-ssh-from-linux.md)。<br /> また、`azure vm create --admin-username --admin-password` オプションを使用することもできます。この場合、VM が作成されると、一般的に安全性の低いユーザー名とパスワードを使用して SSH 接続が認証されます。

`azure vm create` コマンドにすべてのリソースと情報を指定して、VM を作成します。

```
chrisL@fedora$ azure vm create \            
--resource-group testrg \
--name testvm \
--location westeurope \
--os-type linux \
--nic-name testnic \
--vnet-name testvnet \
--vnet-subnet-name FrontEnd \
--storage-account-name computeteststore \
--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username ops
info:    Executing command vm create
+ Looking up the VM "testvm"
info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the NIC "testnic"
info:    Found an existing NIC "testnic"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
+ Creating VM "testvm"
info:    vm create command OK
```

すぐに、既定の SSH キーを使用して VM に接続できるようになります。

```
chrisL@fedora$ ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Mon Sep 28 18:45:02 UTC 2015

System load: 0.64              Memory usage: 5%   Processes:       81
Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
    https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@testvm:~$
```

ここで、`azure vm show testrg testvm` コマンドを使用して、これまでに作成したものを確認できます。この時点では、Azure には実行中の Ubuntu VM があります。この VM には、所有する SSH キー ペアのみを使用してログインできます。パスワードは無効になっています。

```
chrisL@fedora$ azure vm show testrg testvm
info:    Executing command vm show
+ Looking up the VM "testvm"
+ Looking up the NIC "testnic"
+ Looking up the public ip "testpip"
data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
data:    ProvisioningState               :Succeeded
data:    Name                            :testvm
data:    Location                        :westeurope
data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.3-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli4eecdddc349d6015-os-1443465824206
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
data:
data:    OS Profile:
data:      Computer Name                 :testvm
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:        SSH Public Keys:
data:          Public Key #1:
data:            Path                    :/home/ops/.ssh/authorized_keys
data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
<snip>
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-21-8E-AE
data:          Provisioning State        :Succeeded
data:          Name                      :testnic
data:          Location                  :westeurope
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :192.168.1.101
data:            Public IP address       :40.115.48.189
data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

### 次のステップ

これで、複数のネットワーク コンポーネントと VM を操作する準備が整いました。

<!---HONumber=AcomDC_0413_2016-->