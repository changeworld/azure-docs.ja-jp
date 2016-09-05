
<properties
   pageTitle="Azure CLI を使用した完全な Linux 環境の作成 | Microsoft Azure"
   description="Azure CLI を使用して、ストレージ、Linux VM、仮想ネットワークとサブネット、ロード バランサー、NIC、パブリック IP、ネットワーク セキュリティ グループすべてを新しく作成します。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# Azure CLI を使用して、完全な Linux 環境を作成する

この記事では、開発と単純なコンピューティングに役立つ VM のペアを含む単純なネットワークとロード バランサーを構築します。ここでは、インターネット上のどこからでも接続できる、2 台のセキュリティで保護された実用的な Linux VM を構築するまで、各コマンドの説明を交えながらプロセスについて説明します。この記事を理解すると、より複雑なネットワークや環境に進むことができます。

その過程で、Resource Manager デプロイメント モデルによって提供される依存関係階層とその性能についても説明します。システムがどのように構築されているかをいったん理解すると、[Azure Resource Manager テンプレート](../resource-group-authoring-templates.md) を使用して、より短時間でシステムを再構築することができます。環境の各部分がどのように組み合わさっているかがわかると、それらを自動化するためのテンプレートの作成はより簡単になります。

環境には以下を含みます。

- 可用性セット内の 2 つの VM
- ポート 80 の負荷分散規則が構成されたロード バランサー
- 不要なトラフィックからVM を保護するネットワーク セキュリティ グループ ルール (NSG)

![基本的な環境の概要](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

このカスタム環境を作成するには、Resource Manager モード (`azure config mode arm`) の最新の [Azure CLI](../xplat-cli-install.md) が必要です。JSON 解析ツールも必要です。この例では、[jq](https://stedolan.github.io/jq/) を使用します。

## クイック コマンド
次のクイック コマンドは、カスタム環境の構築に使用できます。環境構築を行うと各コマンドがどのように機能するかの詳細については、[詳細なチュートリアル](#detailed-walkthrough)を参照してください。

リソース グループを作成します。

```bash
azure group create TestRG -l westeurope
```

JSON パーサーを使用して、リソース グループを確認します。

```bash
azure group show TestRG --json | jq '.'
```

ストレージ アカウントを作成します。

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

JSON パーサーを使用して、ストレージ アカウントを確認します。

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

仮想ネットワークを作成します。

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

サブネットを作成します。

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

JSON パーサーを使用して、仮想ネットワークとサブネットを確認します。


```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

パブリック IP を作成します。

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

ロード バランサーを作成します。

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

ロード バランサーのフロントエンド IP プールを作成して、パブリック IP を関連付けます。

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

ロード バランサーのバックエンド IP プールを作成します。

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

ロード バランサーの SSH 受信 NAT 規則を作成します。

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

ロード バランサーの Web 受信 NAT 規則を作成します。

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

ロード バランサーの正常性プローブを作成します。

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

JSON パーサーを使用したロード バランサー、IP プール、および NAT 規則を確認します。

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

最初のネットワーク インターフェイス カード (NIC) を作成します。

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

2 つ目の NIC を作成します。

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

JSON パーサーを使用した NIC を確認します。

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

NSG を作成します。

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

NSG の受信規則を追加します。

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

JSON パーサーを使用した NSG および受信規則を確認します。

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

NSG を NIC にバインドします。

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

可用性セットを作成します。

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

最初の Linux VM を作成します。

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
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

2 つ目の Linux VM を作成します。

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
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

すべてが構築されたことを確認するのに JSON パーサーを使用します。

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

新しいインスタンスを簡単に再作成するためのテンプレートに構築した環境をエクスポートします。

```bash
azure resource export TestRG
```

## 詳細なチュートリアル
次の手順では、環境を構築する際に各コマンドがどのように機能するかについて説明します。これらの概念は、開発または実稼働用のカスタム環境を作成するのに役立ちます。

## リソース グループの作成とデプロイ先の選択

Azure リソース グループは、リソース デプロイの論理的な管理を可能にする構成情報とメタデータを含む論理的なデプロイ エンティティです。

```bash
azure group create TestRG westeurope
```

出力:

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

VM ディスクと追加するその他のデータ ディスク用のストレージ アカウントが必要になります。リソース グループを作成したら、そのほぼ直後にストレージ アカウントを作成します。

ここでは、`azure storage account create` コマンドを使用し、アカウントの場所、アカウントを制御するリソース グループ、必要なストレージ サポートの種類を渡します。

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

出力:

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

`--json` Azure CLI オプションと [jq](https://stedolan.github.io/jq/) ツールで、`azure group show` コマンドを使ってリソース グループを調べます。(JSON を解析する**jsawk** または任意の言語ライブラリを使用します。)

```bash
azure group show TestRG --json | jq                                                                                      
```


出力:

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

CLI を使用して、ストレージ アカウントを調査するためには、まず、次のコマンドのバリエーションでアカウント名とキーを設定する必要があります。次の例にあるストレージ アカウントの名前を選択したものに置き換えます。

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

その後、ストレージ情報は簡単に表示できるようになります。

```bash
azure storage container list
```

出力:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## 仮想ネットワークとサブネットの作成

次に、VM をインストールできるように、Azure で実行中の仮想ネットワークとサブネットを作成する必要があります。

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

出力:

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

出力:

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

次に、VM のデプロイ先となるサブネットを `TestVnet` 仮想ネットワーク内に作成します。既に作成したリソース (`TestRG` リソース グループと `TestVNet` 仮想ネットワーク) と `azure network vnet subnet create` コマンドを使用します。以下の通り、サブネット名 `FrontEnd` とサブネットのアドレス プレフィックス `192.168.1.0/24` を追加します。

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

出力:

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

サブネットは論理的に仮想ネットワーク内に存在するため、サブネットの情報を探すには、若干異なるコマンドを使用します。使用するコマンドは `azure network vnet show` ですが、**jq** で JSON 出力を確認し続けます。

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

出力:

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

パブリック IP アドレス (PIP) を作成して、ロード バランサーに割り当てます。`azure network public-ip create` コマンドを使用して、インターネットから VM に接続できます。既定のアドレスは動的であるため、`-d testsubdomain` オプションを使用して、**cloudapp.azure.com** ドメインに名前付き DNS エントリを作成します。

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

出力:

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

パブリック IP アドレスは最上位のリソースでもあるため、`azure group show` を使用して確認できます。

```bash
azure group show TestRG --json | jq '.'
```

出力:

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

完全な `azure network public-ip show` コマンドを使用して、サブドメインの完全修飾ドメイン名 (FQDN) など、リソースの詳細を調査することができます。パブリック IP アドレス リソースは論理的に割り当てられていますが、特定のアドレスはまだ割り当てられていません。IP アドレスを取得するには、ロード バランサーが必要ですが、まだ作成されていません。

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

出力:

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
ロード バランサーを作成するときに複数の VM にトラフィックを分散できます。メンテナンスや大きな負荷が発生した場合に、ユーザー要求に応答する複数の VM を実行することで、アプリケーションに冗長性も加わります。

次によってロード バランサーを作成します。

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

出力:

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
現在ロード バランサーは全く空であるため、いくつかの IP プールを作成しましょう。ロード バランサーには、フロントエンドに 1 つ、バックエンドに 1 つの 2 つの IP プールを作成します。フロントエンド IP プールは公開されます。以前に作成した PIP を割り当てる場所にもなります。接続する VM の保存先として、バックエンド プールを使用します。これにより、トラフィックは、VM にロード バランサーを経由できます。

まず、フロント エンド IP プールを作成しましょう。

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

出力:

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

以前に作成した TestLBPIP を渡すために、`--public-ip-name` スイッチを使用している方法に注意してください。パブリック IP アドレスをロード バランサーに割り当てることで、インターネット経由で VM に到達できるようになります。

次に、今度はバック エンド トラフィック用に 2 つ目の IP プールを作成しましょう。

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

出力:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

`azure network lb show` によって JSON 出力を調べて、ロード バランサーの様子を確認できます。

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

出力:

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
トラフィックがロード バランサーを経由するようにするには、受信アクションまたは送信アクションを指定する NAT 規則を作成する必要があります。使用するプロトコルを指定して、必要に応じて、外部ポートを内部ポートにマップします。この環境では、SSH がロード バランサーを経由して VM に到達できるようにするいくつかの規則を作成しましょう。VM の TCP ポート 22 (これは後で作成します) に送信する TCP ポート 4222 と 4223 を設定します。

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

出力:

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

さらに、規則を IP プールに接続する TCP ポート 80 の NAT 規則の作成に進みましょう。規則を個々の VM に接続するのではなく、IP プールに接続すると、IP プールから VM を追加または削除するだけで対応できます。ロード バランサーは、トラフィックのフローを自動的に調整します。

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

出力:

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

正常性プローブは、ロード バランサーの背後にある VM を定期的にチェックして、それらが定義されているとおりに動作し、要求に応答していることを確認します。動作していない場合は、運用から削除され、ユーザーがそれらに転送されないようにします。間隔およびタイムアウト値と共に、正常性プローブのカスタム チェックを定義できます。正常性プローブの詳細については、[Load Balancer のプローブ](../load-balancer/load-balancer-custom-probe-overview.md)に関するページをご覧ください。

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

出力:

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

ここでは、正常性チェックの間隔を 15 秒に指定しました。4 回のプローブ (1 分間) までは、ホストが機能していないとロード バランサーで判断されません。

## ロード バランサーの確認
ロード バランサーの構成を確認できました。実行する手順を次に示します。

1. 最初に、ロード バランサーを作成します。
2. フロントエンド IP プールを作成し、パブリック IP が割り当てられます。
3. 次に、VM が接続できるバックエンド IP プールを作成します。
4. その後、管理用に VM への SSH 通信を許可する NAT 規則と Web アプリ用に TCP ポート 80 を許可する規則を作成します。
5. 最後に、VM を定期的にチェックする正常性プローブを追加します。この正常性プローブで、機能しないまたはコンテンツを提供しない VM にユーザーがアクセスを試みなくてもよくなります。

ロード バランサーがどのように見えるようになったか確認しましょう。

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

出力:

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

 ルールを適用するため、NIC はプログラムで使用できます。複数でも可能になります。次の `azure network nic create` コマンドでは、NIC をロード バックエンド IP プールに接続し、SSH トラフィックを許可する NAT 規則を関連付けています。これを行うには、`<GUID>` の代わりに、Azure サブスクリプションのサブスクリプション ID を指定する必要があります。

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

出力:

```bash
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

詳細を確認するには、直接リソースを調査します。リソースを確認するには、`azure network nic show` コマンドを使用します。

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

出力:

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

次に、バックエンド IP プールにもう一度フックして、2 つ目の NIC を作成します。ここで 2 つ目の NAT 規則が SSH トラフィックを許可します。

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## ネットワーク セキュリティ グループと規則の作成

ここで、NSG と、NIC へのアクセスを統制する受信規則を作成します。

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

> [AZURE.NOTE] 受信規則は、受信ネットワーク接続のフィルターです。この例では、NSG を VM の仮想 NIC にバインドします。これは、ポート 22 へのすべての要求が VM の NIC に渡されることを意味します。この受信規則は、エンドポイントではなく、ネットワーク接続に関するルールであり、つまり従来の配置でのことです。ポートを開くには、`--source-port-range` のセットを ' *' (既定値) にして、**任意の** 要求ポートから受信要求を受け入れる必要があります。ポートは、通常動的です。

## NIC へのバインド

NSG を NIC にバインドします。

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## 可用性セットの作成
可用性セットにより、障害ドメインおよびアップグレード ドメインに VM を分散できます。VM の可用性セットを作成しましょう。

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

障害ドメインは共通の電源とネットワーク スイッチを使用する仮想マシンのグループを定義します。既定で、可用性セット内に構成された仮想マシンは、最大 3 つの障害ドメイン間で分散されます。つまり、これらの障害ドメインのいずれかのハードウェアの問題が、アプリを実行している各 VM に影響しません。VM を可用性セットに配置すると、Azure は自動的に VM を障害ドメイン間で分散します。

アップグレード ドメインは、仮想マシンと、同時に再起動できる基礎となる物理ハードウェアのグループを示しています。計画済みメンテナンス中、アップグレード ドメインの再起動は順次ではない場合がありますが、一度に再起動されるアップグレードは 1 つのみです。また、VM を可用性サイトに配置すると、Azure は自動的に VM をアップグレード ドメイン間で分散します。

詳細については、「[VMの可用性管理](./virtual-machines-linux-manage-availability.md)」を参照してください。

## Linux VM の作成

これまでの手順で、インターネットにアクセス可能な VM をサポートするためのストレージおよびネットワーク リソースを作成しました。ここでは、その VM を作成し、パスワードのない SSH キーを使用してそれらをセキュリティで保護します。この場合は、最新の LTS に基づいて Ubuntu VM を作成します。[Azure での仮想マシン イメージの検索](virtual-machines-linux-cli-ps-findimage.md)に関するページで説明されているように、`azure vm image list` を使用してそのイメージの情報を見つけます。

コマンド `azure vm image list westeurope canonical | grep LTS` を使用してイメージを選択します。この例では、`canonical:UbuntuServer:16.04.0-LTS:16.04.201608150` を使用します。最後のフィールドでは、今後常に直近に構築されたように `latest` を渡します(使用する文字列は `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150` です)。

この次の手順は、Linux または Mac で **ssh-keygen -t rsa -b 2048** を使用して SSH RSA の公開キーと秘密キーのペアを既に作成した経験があるユーザーであればだれでもよく知っています。`~/.ssh` ディレクトリに証明書キーのペアがない場合、作成することができます。

- 自動的に `azure vm create --generate-ssh-keys` オプションを使用します。
- 手動では [自分で作成する手順](virtual-machines-linux-mac-create-ssh-keys.md) を使用します。

また、VM を作成した後、SSH 接続を認証するのに--管理パスワード方法を使用できます。この方法は通常安全性が低いです。

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
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

出力:

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
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

すぐに、既定の SSH キーを使用して VM に接続できるようになります。ロード バランサー経由で渡されるため、適切なポートを指定することを確認します。(最初の VM では、ポート 4222 を VM に転送する NAT 規則を設定します。)

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

出力:

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

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
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

ここで、`azure vm show testrg testvm` コマンドを使用して、これまでに作成したものを確認できます。この時点では、Azure のロード バランサーの背後で Ubuntu VM が実行中です。この VM には、所有する SSH キー ペアのみを使用してサインインできます (パスワードは無効になっているため)。nginx または httpd をインストールし、Web アプリをデプロイして、ロード バランサーを経由して両方の VM に到達するトラフィック フローを確認できます。

```bash
azure vm show TestRG TestVM1
```

出力:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
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
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## 環境をテンプレートとしてエクスポートします。
これで環境を構築できました。同じパラメーターを使用して追加の開発環境を作成する場合、または、一致する運用環境を作成する場合はどのようにすべきでしょうか。 リソース マネージャーでは、環境に合ったすべてのパラメーターを定義する JSON テンプレートを使用します。この JSON テンプレートを参照することで全体の環境を構築します。[JSON テンプレートを手動で構築](../resource-group-authoring-templates.md) できます。または単純にJSON テンプレートを作成する既存の環境をエクスポートすることもできます。

```bash
azure group export TestRG
```

このコマンドで、現在の作業ディレクトリ内に `TestRG.json` ファイルが作成されます。このテンプレートから新しい環境を作成すると、ロード バランサー、ネットワーク インターフェイス、または VM を含むリソース名が表示されます。前に示した `azure group export` コマンドに `-p` または `--includeParameterDefaultValue` パラメーターを追加することでテンプレート ファイルにこれらの名前を入力できます。リソース名を指定する JSON テンプレートを編集するか、リソース名を指定する [parameters.json ファイルを作成](../resource-group-authoring-templates.md#parameters) します。

テンプレートから環境を作成するには:

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

[テンプレートからデプロイする方法に関する詳細](../resource-group-template-deploy-cli.md) をご確認ください。段階的な環境の更新、パラメーター ファイルの使用、単一の保存場所からテンプレートにアクセスする方法を確認してください。

## 次のステップ

これで、複数のネットワーク コンポーネントと VM の操作を開始する準備が整いました。ここで紹介した主要なコンポーネントを使用して、アプリケーションを構築するためにこのサンプル環境を使用できます。

<!---HONumber=AcomDC_0824_2016-->