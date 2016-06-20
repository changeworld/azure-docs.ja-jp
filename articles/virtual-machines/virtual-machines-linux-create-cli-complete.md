<properties
   pageTitle="Azure CLI を使用して新しく Linux VM を作成する | Microsoft Azure"
   description="Azure CLI を使用して、Linux VM、ストレージ、仮想ネットワークとサブネット、NIC、パブリック IP、ネットワーク セキュリティ グループすべてを新しく作成します。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/29/2016"
   ms.author="iainfou"/>

# Azure CLI を使用して新しく Linux VM を作成する

Linux VM を作成するには、Resource Manager モードの [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`) と、JSON 解析ツールが必要になります。このドキュメントでは [jq](https://stedolan.github.io/jq/) を使用します。

## クイック コマンド

リソース グループの作成

```bash
azure group create TestRG -l westeurope
```

JSON パーサーを使用した RG の確認

```bash
azure group show TestRG --json | jq '.'
```

ストレージ アカウントの作成

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

JSON パーサーを使用したストレージの確認

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

仮想ネットワークの作成

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

サブネットの作成

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

JSON パーサーを使用した VNet およびサブネットの確認

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

パブリック IP の作成

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

ロード バランサーの作成

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

ロード バランサーのフロント エンド IP プールを作成して、パブリック IP を関連付ける

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

ロード バランサーのバック エンド IP プールの作成

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

ロード バランサーの SSH 受信 NAT 規則の作成

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

ロード バランサーの Web 受信 NAT 規則の作成

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

ロード バランサーの正常性プローブの作成

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

JSON パーサーを使用したロード バランサー、IP プール、および NAT 規則の確認

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

最初の NIC の作成

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

2 つ目の NIC の作成

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

JSON パーサーを使用した NIC の確認

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

NSG の作成

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

NSG の受信規則の追加

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

JSON パーサーを使用した NSG および受信規則の確認

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

NSG の NIC へのバインド

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

可用性セットの作成

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

最初の Linux VM の作成

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

2 つ目の Linux VM の作成

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

JSON パーサーを使用して構築されたすべてのものの確認

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

## 詳細なチュートリアル

### はじめに

この記事では、ロード バランサーの背後に 2 つの Linux VM を置くデプロイを構築します。ここでは、インターネット上のどこからでも接続できる、セキュリティで保護された実用的な VM を構築するまで、各コマンドの説明を交えながら基本的なデプロイ全体を説明します。

その過程で、Resource Manager デプロイ モデルによって提供される依存関係階層とその性能についても説明します。システムがどのように構築されているかをいったん理解すると、より直接的な Azure CLI コマンドを使用してより短時間でシステムを再構築することも (`azure vm quick-create` コマンドを使用したほぼ同じデプロイについては、[こちら](virtual-machines-linux-quick-create-cli.md)を参照してください)、[Azure Resource Manager テンプレート](../resource-group-authoring-templates.md)を使用して、ネットワークとアプリケーションのデプロイ全体を設計して自動化し、それらを更新する方法の習得に移ることもできます。デプロイの各部分がどのように組み合わさっているかがわかると、それらを自動化するためのテンプレートの作成はより簡単になります。

ここでは、開発と単純なコンピューティングに役立つ VM のペアを含む単純なネットワークとロード バランサーを構築し、それについて説明します。この記事を理解すると、より複雑なネットワークやデプロイに進むことができます。

## リソース グループの作成とデプロイ先の選択

Azure リソース グループは、リソース デプロイの論理的な管理を可能にする構成とその他のメタデータを含む論理的なデプロイ エンティティです。

```bash
azure group create TestRG westeurope
```

出力

```bash                        
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

## ストレージ アカウントの作成

数あるシナリオの中で特に、VM ディスクと追加するその他のデータ ディスク用のストレージ アカウントが必要になります。つまり、リソース グループを作成したら、そのほぼ直後には必ずストレージ アカウントを作成します。

ここでは、`azure storage account create` コマンドを使用し、アカウントの場所、アカウントを制御するリソース グループ、必要なストレージ サポートの種類を渡します。

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

出力

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
ahmet•~/workspace/keygen» azure group show testrg
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

```bash
azure group show TestRG --json | jq                                                                                      
```


出力

```bash
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

```bash
azure storage container list
```

出力

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## 仮想ネットワークとサブネットの作成

VM をインストールできるように、Azure Virtual Network とサブネットを作成する必要があります。

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

出力

```bash
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

ここで、もう一度 `azure group show` の --json オプションと **jq** を使用して、リソースをどのように作成するかを見てみましょう。この段階で、`storageAccounts` リソースと `virtualNetworks` リソースが作成されています。

```bash
azure group show TestRG --json | jq '.'
```

出力

```bash
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

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

出力

```bash
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

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

出力

```bash
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

## パブリック IP アドレス (PIP) の作成

ここでは、`azure network public-ip create` コマンドを使用して、ロード バランサーに割り当て、インターネットから VM に接続できるようにするためのパブリック IP アドレス (PIP) を作成します。既定値は動的アドレスであるため、`-d testsubdomain` オプションを使用して、**cloudapp.azure.com** ドメインに名前付き DNS エントリを作成します。

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

出力

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "TestPIP"
+ Creating public ip address "TestPIP"
+ Looking up the public ip "TestPIP"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
data:    Name                            : TestPIP
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

```bash
azure group show TestRG --json | jq '.'
```

出力

```bash
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

また、通常どおり、より完全な `azure network public-ip show` コマンドを使用して、サブドメインの完全修飾ドメイン名 (FQDN) など、リソースの詳細を調査することができます。パブリック IP アドレス リソースは論理的に割り当てられていますが、特定のアドレスはまだ割り当てられていないことに注意してください。そのため、ロード バランサーが必要になりますが、これはまだ作成されていません。

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

出力

```bash
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

## ロード バランサーと IP プールの作成
ロード バランサーを作成すると、Web アプリケーションの実行時などに、複数の VM にトラフィックを分散することができます。メンテナンスや大きな負荷が発生した場合に、ユーザー要求に応答する複数の VM を実行することで、アプリケーションに冗長性も加わります。

次によってロード バランサーを作成します。

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

出力

```bash
info:    Executing command network lb create
+ Looking up the load balancer "TestLB"
+ Creating load balancer "TestLB"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB
data:    Name                            : TestLB
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```
現在ロード バランサーは全く空であるため、いくつかの IP プールを作成しましょう。ロード バランサーには、フロント エンドに 1 つ、バックエンドに 1 つの 2 つの IP プールを作成します。フロント エンド IP プールは、公開するもので、以前に作成した PIP を割り当てます。バック エンド プールは、VM の接続先に使用し、トラフィックがロード バランサーを経由してそれらに流れるようにします。

まず、フロント エンド IP プールを作成しましょう。

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

出力

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "TestLB"
+ Looking up the public ip "TestLBPIP"
+ Updating load balancer "TestLB"
data:    Name                            : TestFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP
info:    network lb frontend-ip create command OK
```

以前に作成した TestLBPIP を渡すために、`--public-ip-name` スイッチを使用している方法に注意してください。これは、インターネット経由で VM に到達できるように、パブリック IP アドレスをロード バランサーに割り当てます。

次に、今度はバック エンド トラフィック用に 2 つ目の IP プールを作成しましょう。

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

出力

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

`azure network lb show` によって JSON 出力を調べて、ロード バランサーの様子をチェックできます。

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

出力

```bash
{
  "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## ロード バランサーの NAT 規則の作成
トラフィックが実際にロード バランサーを経由するようにするには、受信アクションまたは送信アクションを指定する NAT 規則を作成する必要があります。使用するプロトコルを指定して、必要に応じて、外部ポートを内部ポートにマップします。この環境では、SSH がロード バランサーを経由して VM に到達できるようにするいくつかの規則を作成しましょう。VM の TCP ポート 22 (これは後で作成します) に送信する TCP ポート 4222 と 4223 を設定します。

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

出力

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "TestLB"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default frontend IP configuration "TestFrontEndPool"
+ Updating load balancer "TestLB"
data:    Name                            : VM1-SSH
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

SSH に対する 2 つ目の NAT 規則について、手順を繰り返します。

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

さらに、規則を IP プールに接続する TCP ポート 80 の NAT 規則の作成に進みましょう。規則を VM に個別に接続する代わりに、VM を IP プールに追加または削除するだけで、ロード バランサーに自動的にトラフィックのフローを調整させることができることを意味します。

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

出力

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "TestLB"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "TestLB"
data:    Name                            : WebRule
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
info:    network lb rule create command OK
```

## ロード バランサーの正常性プローブの作成

正常性プローブは、ロード バランサーの背後にある VM を定期的にチェックして、それらが定義されているとおりに動作し、要求に応答していることを確認します。動作していない場合は、運用から削除され、ユーザーがそれらに転送されないようにします。間隔およびタイムアウト値と共に、正常性プローブのカスタム チェックを定義できます。正常性プローブの詳細については、「[Load Balancer プローブ](../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

出力

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Http
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

ここでは、正常性チェックに 15 秒の間隔を指定したため、最大 4 プローブ (1 分) 失敗すると、ロード バランサーによりホストが機能しなくなったと見なされます。

## ロード バランサーの確認
これでロード バランサーの構成はすべて完了しました。ロード バランサーを作成し、フロント エンド IP プールを作成して、パブリック IP をそれに割り当て、VM に接続するバックエンド IP プールを作成しました。次に、管理用に VM への SSH 通信を許可する NAT 規則と Web アプリ用に TCP ポート 80 を許可する規則を作成しました。最後に、機能しなくなり、コンテンツを提供しなくなった VM にユーザーがアクセスを試みないようにするため、VM を定期的にチェックする正常性プローブを追加しました。

ロード バランサーがどのように見えるようになったか確認しましょう。

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

出力

```bash
{
  "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM1-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM2-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "WebRule",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "frontendPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe",
      "protocol": "Http",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 4,
      "requestPath": "healthprobe.aspx",
      "provisioningState": "Succeeded",
      "name": "HealthProbe"
    }
  ]
}
```

## Linux VM で使用する NIC の作成

NIC はプログラムで使用できるほか、その使用に規則を適用したり、複数の NIC を使用したりすることができます。次の `azure network nic create` コマンドでは、NIC をロード バックエンド IP プールに接続し、SSH トラフィックを許可する NAT 規則を関連付けていることに注意してください。これを行うには、`<GUID>` の代わりに、Azure サブスクリプションのサブスクリプション ID を指定する必要があります。

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

出力

```bash 
/subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
info:    Executing command network nic create
+ Looking up the subnet "FrontEnd"
+ Looking up the network interface "LB-NIC1"
+ Creating network interface "LB-NIC1"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1
data:    Name                            : LB-NIC1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
data:
info:    network nic create command OK
```

詳細を確認するには、`azure network nic show` コマンドを使用して、直接リソースを調査します。

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

出力

```bash
{
  "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1",
  "name": "LB-NIC1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

再度バック エンド IP プールに接続する 2 つ目の NIC と今度は SSH トラフィックを許可する 2 つ目の NAT 規則を作成しましょう。

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

出力

```bash
 /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## ネットワーク セキュリティ グループと規則の作成

ここで、ネットワーク セキュリティ グループ (NSG) と、NIC へのアクセスを統制する受信規則を作成します。

```bash
azure network nsg create TestRG TestNSG westeurope
```

NSG の受信規則を追加して、(SSH をサポートするために) ポート 22 での受信接続を許可します。

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow TestRG TestNSG SSHRule
```

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

> [AZURE.NOTE] 受信規則は、受信ネットワーク接続のフィルターです。この例では、NSG を VM の仮想ネットワーク インターフェイス カード (NIC) にバインドします。これは、ポート 22 へのすべての要求が VM の NIC に渡されることを意味します。これは、ポートを開くためのネットワーク接続に関する規則であり、クラシック デプロイにおけるエンドポイントではないため、通常は動的である**任意の**要求元ポートからの受信要求を受け入れるために `--source-port-range` を "*" (既定値) にしておく必要があります。

## NIC へのバインド

NSG を NIC にバインドします。

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## 可用性セットの作成
可用性セットにより、障害ドメインおよびアップグレード ドメインと呼ばれるドメインに VM を分散できます。VM の可用性セットを作成しましょう。

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

障害ドメインは共通の電源とネットワーク スイッチを使用する仮想マシンのグループを定義します。既定で、可用性セット内に構成された仮想マシンは、最大 3 つの障害ドメイン間で分散されます。つまり、これらの障害ドメインのいずれかのハードウェアの問題が、アプリを実行している各 VM に影響しません。VM を可用性セットに配置すると、Azure は自動的に VM を障害ドメイン間で分散します。

アップグレード ドメインは、仮想マシンと、同時に再起動できる基礎となる物理ハードウェアのグループを示しています。計画済みメンテナンス中、アップグレード ドメインの再起動は順番に処理されない場合がありますが、一度に再起動されるアップグレードは 1 つのみです。ここでも、VM を可用性セットに配置すると、Azure は自動的に VM をアップグレード ドメイン間で分散します。

詳細については、「[仮想マシンの可用性管理](./virtual-machines-linux-manage-availability.md)」を参照してください

## Linux VM の作成

これまでの手順で、インターネットにアクセス可能な VM をサポートするためのストレージおよびネットワーク リソースを作成しました。ここでは、その VM を作成し、パスワードなしの SSH キーを使用してそれらをセキュリティで保護します。この場合は、最新の LTS に基づいて Ubuntu VM を作成します。「[CLI または PowerShell を使用した Azure での Linux 仮想マシン イメージへの移動と選択](virtual-machines-linux-cli-ps-findimage.md)」で説明されているように、`azure vm image list` を使用してそのイメージの情報を見つけます。イメージは `azure vm image list westeurope canonical | grep LTS` コマンドを使用して選択しました。この場合は、`canonical:UbuntuServer:14.04.4-LTS:14.04.201604060` を使用しますが、最後のフィールドでは `latest` を渡して、今後常に最新ビルドを入手できるようにします (使用する文字列は `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060` となります)。

> [AZURE.NOTE] この次の手順は、Linux または Mac で **ssh-keygen -t rsa -b 2048** を使用して SSH RSA の公開キーと秘密キーのペアを既に作成した経験があるユーザーであればだれでもよく知っています。`~/.ssh` ディレクトリに証明書キー ペアがない場合は、次のいずれかの方法で作成できます。<br /> 1. `azure vm create --generate-ssh-keys` オプションを使用して自動的に作成する。2. [手順に従って自分で手動で作成する](virtual-machines-linux-ssh-from-linux.md)。<br /> また、`azure vm create --admin-username --admin-password` オプションを使用することもできます。この場合、VM が作成されると、一般的に安全性の低いユーザー名とパスワードを使用して SSH 接続が認証されます。

`azure vm create` コマンドにすべてのリソースと情報を指定して、VM を作成します。

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

出力

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ifoulds/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the availability set "TestAvailSet"
info:    Found an Availability set "TestAvailSet"
+ Looking up the NIC "LB-NIC1"
info:    Found an existing NIC "LB-NIC1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "LB-NIC1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://computeteststore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

すぐに既定の SSH キーを使用して VM に接続でき、ロード バランサーを通過しているため、適切なポートを指定していることを確認できます (この例の最初の VM の場合は、ポート 4222 から VM に送信する NAT 規則を設定しました)。

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

出力

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Apr 27 23:44:06 UTC 2016

  System load: 0.37              Memory usage: 5%   Processes:       81
  Usage of /:  37.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

ops@TestVM1:~$
```

同じように、2 つ目の VM を作成します。

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

ここで、`azure vm show testrg testvm` コマンドを使用して、これまでに作成したものを確認できます。この時点では、Azure にはロード バランサーの背後に実行中の Ubuntu VM があります。この VM には、所有する SSH キー ペアのみを使用してログインできます。パスワードは無効になっています。nginx または httpd をインストールし、Web アプリをデプロイして、ロード バランサーを経由して両方の VM に到達するトラフィック フローを確認できます。

```bash
azure vm show TestRG TestVM1
```

出力

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/8fa5cd83-7fbb-431a-af16-4a20dede8802/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli1cca1d20a1dcf56c-os-1461800591317
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli1cca1d20a1dcf56c-os-1461800591317.vhd
data:
data:    OS Profile:
data:      Computer Name                 :TestVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-20-F8-8B
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK
```

## 次のステップ

これで、複数のネットワーク コンポーネントと VM を操作する準備が整いました。

<!---HONumber=AcomDC_0608_2016-->