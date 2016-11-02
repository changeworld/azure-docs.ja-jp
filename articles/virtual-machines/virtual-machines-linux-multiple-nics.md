<properties
   pageTitle="Linux VM での複数 NIC の構成 | Microsoft Azure"
   description="Azure CLI または Resource Manager テンプレートを使用して、複数の NIC を持つ VM を作成する方法について説明します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/02/2016"
   ms.author="iainfou"/>

# 複数 NIC を持つ VM の作成
Azure では、複数の仮想ネットワーク インターフェイス (NIC) を持つ仮想マシン (VM) を作成できます。一般的なシナリオは、フロント エンドおよびバック エンド接続用に別々のサブネットを使用するか、監視またはバックアップ ソリューション専用のネットワークを用意することです。この記事では、複数の NIC を持つ VM を作成するためのクイック コマンドを紹介します。独自の Bash スクリプト内に複数の NIC を作成する方法など、詳しくは、[複数 NIC の VM のデプロイ](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)に関する記事を参照してください。[VM のサイズ](virtual-machines-linux-sizes.md)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。

>[AZURE.WARNING] VM の作成時に複数の NIC をアタッチする必要があります。既存の VM に NIC を追加することはできません。[元の仮想ディスクに基づいて新しい VM を作成](virtual-machines-linux-copy-vm.md)し、VM をデプロイするときに複数の NIC を作成できます。

## クイック コマンド
[Azure CLI](../xplat-cli-install.md) でログインし、Resource Manager モードを使用していることを確認します (`azure config mode arm`)。

まず、リソース グループを作成します。

```bash
azure group create TestRG --location WestUS
```

VM を保持するストレージ アカウントを作成します。

```bash
azure storage account create teststorage --resource-group TestRG \
    --location WestUS --kind Storage --sku-name PLRS
```

VM を接続する仮想ネットワークを作成します。

```bash
azure network vnet create --resource-group TestRG --location WestUS \
    --name TestVNet --address-prefixes 192.168.0.0/16 
```

2 つの仮想ネットワーク サブネットを作成します。1 つはフロント エンド トラフィック用、もう 1 つはバック エンド トラフィック用です。

```bash
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name FrontEnd --address-prefix 192.168.1.0/24
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name BackEnd --address-prefix 192.168.2.0/24
```

2 つの NIC を作成します。1 つをフロント エンド サブネットに、もう 1 つをバック エンド サブネットにアタッチします。

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

最後に VM を作成します。ここに、先ほど作成した 2 つの NIC をアタッチします。

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Azure CLI を使用して複数の NIC を作成する
Azure CLI を使用して VM を作成済みであれば、クイック コマンドは難しくありません。NIC を 1 つ作成する場合も、複数作成する場合も、プロセスは同じです。詳しくは、「[Azure CLI を使用した複数の NIC VM のデプロイ](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)」をご覧ください。ここでは、すべての NIC を作成するループ プロセスのスクリプトを作成する方法についても解説しています。

次の例では 2 つの NIC を作成し、それぞれサブネットに接続します。

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

通常は、VM 間でトラフィックを管理、分散するために、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)や[ロード バランサー](../load-balancer/load-balancer-overview.md)も作成します。ここでもまた、コマンドは複数の NIC を扱う場合と同じです。作成した NIC は、`azure network nic set` を使用して、ネットワーク セキュリティ グループまたはロード バランサーにバインドします。例を次に示します。

```bash
azure network nic set --resource-group TestRG --name NIC1 \
    --network-security-group-name TestNSG
```

VM を作成するときに、複数の NIC を指定します。`--nic-name` を使用して 1 つの NIC を指定する代わりに、`--nic-names` を使用して NIC のコンマ区切りのリストを指定します。VM のサイズを選択する際には注意が必要です。1 つの VM に追加できる NIC の合計数には制限があります。詳しくは、[Linux VM のサイズ](virtual-machines-linux-sizes.md)に関する記事をご覧ください。次の例は、複数の NIC を指定し、複数 NIC の使用をサポートする VM のサイズを指定する方法を示しています (`Standard_DS2_v2`)。

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Resource Manager テンプレートを使用して複数の NIC を作成する
Azure Resource Manager テンプレートで宣言型の JSON ファイルを使用して環境を定義します。詳しくは、「[Azure Resource Manager の概要](../resource-group-overview.md)」をご覧ください。Resource Manager テンプレートでは、複数の NIC の作成など、デプロイ時にリソースの複数のインスタンスを作成することができます。*copy* を使用して、作成するインスタンスの数を指定します。

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

詳しくは、[*copy* を使用した複数のインスタンスの作成](../resource-group-create-multiple.md)に関する記事を参照してください。

`copyIndex()` を使用してリソース名に数値を追加することもできます。これにより、`NIC1`、`NIC2` などを作成することができます。インデックス値を追加する例を次に示します。

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

完全な例については、「[Resource Manager テンプレートを使用して複数の NIC を作成する](../virtual-network/virtual-network-deploy-multinic-arm-template.md)」を参照してください。

## 次のステップ
複数の NIC を持つ VM を作成する際は、[Linux VM のサイズ](virtual-machines-linux-sizes.md)を必ず確認してください。各 VM サイズがサポートしている NIC の最大数に注意してください。

既存の VM に NIC を追加することはできません。VM をデプロイするときに、すべての NIC を作成する必要があります。デプロイメントの計画時に、初めから必要なすべてのネットワーク接続があることを確認してください。

<!---HONumber=AcomDC_0817_2016-->