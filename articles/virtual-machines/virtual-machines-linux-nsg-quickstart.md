<properties
   pageTitle="Linux 仮想マシンへのポートおよびエンドポイントの開放 |Microsoft Azure"
   description="Resource Manager デプロイメント モデルと Azure CLI を使用して、Linux VM に対する外部アクセスを許可するポートを開き、エンドポイントを作成する方法について説明します。"
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
   ms.workload="infrastructure-services"
   ms.date="05/24/2016"
   ms.author="iainfou"/>

# ポートおよびエンドポイントの開放
サブネットまたは仮想マシン (VM) ネットワークポ インターフェイス上の選択したポートへのトラフィックを許可するネットワーク フィルターを作成して、Azure でポートを開くかエンドポイントを作成します。着信および発信の両方のトラフィックを制御するこれらのフィルターは、ネットワーク セキュリティ グループに配置され、トラフィックを受信するリソースに接続されます。ポート 80 での Web トラフィックの一般的な例を使用して説明します。

## クイック コマンド
ネットワーク セキュリティ グループとルールを作成するには、Resource Manager モード (`azure config mode arm`) の [Azure CLI](../xplat-cli-install.md) が必要になります。

独自の名前と場所を適宜入力してネットワーク セキュリティ グループを作成します。

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

Web サーバーへの HTTP トラフィックを許可するルールを追加します (SSH アクセスやデータベース接続などの独自のシナリオに合わせて調整できます)。

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resource-group TestRG --nsg-name TestNSG --name AllowHTTP
```

ネットワーク セキュリティ グループと仮想マシンのネットワーク インターフェイスを関連付けます。

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

また、ネットワーク セキュリティ グループは、単一の VM のネットワーク インターフェイスに関連付けるのではなく、仮想ネットワークのサブネットに関連付けることもできます。

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。詳細については、[ネットワーク セキュリティ グループと ACL 規則の作成](../virtual-network/virtual-networks-create-nsg-arm-cli.md)に関するページをご覧ください。

ネットワーク セキュリティ グループと ACL 規則は、Azure Resource Manager のテンプレートの一部としても定義できます。詳細については、[テンプレートを使用したネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-template.md)に関するページをご覧ください。

ポート転送を使用して一意の外部ポートを VM 上の内部ポートにマップする必要がある場合は、ロード バランサーとネットワーク アドレス変換 (NAT) 規則を使用する必要があります。たとえば、TCP ポート 8080 を外部に公開し、VM 上の TCP ポート 80 にトラフィックを送ることができます。詳細については、[インターネットに接続するロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-arm-cli.md)に関するページをご覧ください。

## 次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。より精密な環境の作成については、次の記事で確認できます。

- [Azure リソース マネージャーの概要](../resource-group-overview.md)
- [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)
- [ロード バランサーのための Azure Resource Manager の概要](../load-balancer2 /load-balancer-arm.md)

<!---HONumber=AcomDC_0629_2016-->