<properties 
   pageTitle="クラシック デプロイ モデルで Azure CLI を使用した内部ロード バランサーの作成 | Microsoft Azure"
   description="クラシック デプロイ モデルで Azure CLI を使用して、内部ロード バランサーを作成する方法について説明します。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Azure CLI を使用した内部ロード バランサー (クラシック) の作成の概要

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager モデルを使用してこれらの手順を実行する](load-balancer-get-started-ilb-arm-cli.md)方法について説明します。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## 仮想マシンの内部ロード バランサー セットを作成するには

内部ロード バランサー セットと、このセットにトラフィックを送信するサーバーを作成するには、次の手順を実行する必要があります。

1. 負荷分散セットのサーバー間で負荷分散される着信トラフィックのエンドポイントとなる内部負荷分散のインスタンスを作成します。

1. 着信トラフィックを受信する仮想マシンに対応するエンドポイントを追加します。

1. 内部負荷分散インスタンスの仮想 IP (VIP) アドレスにトラフィックを送信するように負荷分散されたトラフィックを送信するサーバーを構成します。

## CLI を使用した内部ロード バランサーの作成手順

このガイドでは、前述のシナリオに基づいてインターネット ロード バランサーを作成する方法を説明します。

1. Azure CLI を初めて使用する場合は、[Azure CLI のインストールと構成](../../articles/xplat-cli-install.md)に関するページを参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。

2. 次に示すように、**azure config mode** コマンドを実行して、以下に示すようにクラシック モードに切り替えます。

		azure config mode asm

	予想される出力:

		info:    New mode is asm


## エンドポイントとロード バランサー セットの作成 

シナリオでは、mytestcloud と呼ばれるクラウド サービス内に仮想マシン DB1 および DB2 があることを前提としています。両方の仮想マシンは、サブネット subnet-1 付きの testvnet と呼ばれる仮想ネットワークを使用します。

このガイドでは、ポート 1433 をプライベート ポートとして、また 1433 をローカル ポートとして、内部ロード バランサー セットを作成します。

これは、内部ロード バランサーを使用して SQL 仮想マシンをバック エンドに配置し、パブリック IP アドレスを直接使用してデータベース サーバーが公開されないようにするための一般的なシナリオです。


### 手順 1 

`azure network service internal-load-balancer add` を使用して、内部ロード バランサー セットを作成します。

	 azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

使用されるパラメーター:

**-r** - クラウド サービス名<BR> **-n** - 内部 Load Balancer 名<BR> **-t** - サブネット名 (内部ロード バランサーに追加する仮想マシンと同じサブネット)<BR> **-a** - (オプション) 静的プライベート IP アドレスを追加する<BR>

詳細については、「`azure service internal-load-balancer --help`」を参照してください。
 
内部ロード バランサーのプロパティを確認するには、コマンド `azure service internal-load-balancer list` *クラウド サービス名*を使用します。

出力の例を次に示します。

	azure service internal-load-balancer list my-testcloud
	info:    Executing command service internal-load-balancer list
	+ Getting cloud service deployment
	data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
	data:    ------  -------  ----------  -----------------------------
	data:    ilbset  Private  subnet-1    192.168.2.7
	info:    service internal-load-balancer list command OK


## 手順 2. 

最初のエンドポイントの追加時に、内部ロード バランサー セットを構成します。この手順では、エンドポイント、仮想マシン、およびプローブ ポートを内部ロード バランサーに関連付けます。

	azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

使用されるパラメーター:

**-k** - ローカル仮想マシン ポート<BR> **-t** - プローブ ポート<BR> **-r** - プローブ プロトコル<BR> **-e** - プローブの間隔 (秒)<BR> **-f** - タイムアウトまでの時間 (秒) <BR> **-i** - 内部ロード バランサー名 <BR>


## 手順 3. 

`azure vm show` *仮想マシン名*を使用して、ロード バランサーの構成を確認する

	azure vm show DB1 

次のように出力されます。

		azure vm show DB1
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "mytestcloud.cloudapp.net"
	data:    Location "East US 2"
	data:    VMName "DB1"
	data:    IPAddress "192.168.2.4"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Standard_D1"
	data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "db1-DB1-0-201511120457370846"
	data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
	data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk operatingSystem "Windows"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "137.116.64.107"
	data:    VirtualIPAddresses 0 name "db1ContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    VirtualIPAddresses 1 address "192.168.2.7"
	data:    VirtualIPAddresses 1 name "ilbset"
	data:    Network Endpoints 0 localPort 5986
	data:    Network Endpoints 0 name "PowerShell"
	data:    Network Endpoints 0 port 5986
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"	
	data:    Network Endpoints 0 enableDirectServerReturn false
	data:    Network Endpoints 1 localPort 3389
	data:    Network Endpoints 1 name "Remote Desktop"
	data:    Network Endpoints 1 port 60173
	data:    Network Endpoints 1 protocol "tcp"
	data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
	data:    Network Endpoints 1 enableDirectServerReturn false
	data:    Network Endpoints 2 localPort 1433
	data:    Network Endpoints 2 name "tcp-1433-1433"
	data:    Network Endpoints 2 port 1433
	data:    Network Endpoints 2 loadBalancerProbe port 1433
	data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
	data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
	data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
	data:    Network Endpoints 2 protocol "tcp"
	data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
	data:    Network Endpoints 2 enableDirectServerReturn false
	data:    Network Endpoints 2 loadBalancerName "ilbset"
	info:    vm show command OK


## 仮想マシン用のリモート デスクトップ エンドポイントの作成

`azure vm endpoint create` を使用し、特定の仮想マシンに、ネットワーク トラフィックをパブリック ポートからローカル ポートに送信するリモート デスクトップ エンドポイントを作成することができます。

	azure vm endpoint create web1 54580 -k 3389 


## ロード バランサーからの仮想マシンの削除

仮想マシンを内部ロード バランサー セットから削除するには、関連付けられたエンドポイントを削除します。エンドポイントを削除すると、その仮想マシンはそのロード バランサー セットには属さなくなります。

 前述の例を使用して、`azure vm endpoint delete` コマンドを使用して、内部ロード バランサー ilbset から仮想マシン "DB1" に作成したエンドポイントを削除できます。

	azure vm endpoint delete DB1 tcp-1433-1433


詳細については、「`azure vm endpoint --help`」を参照してください。


## 次のステップ

[ソース IP アフィニティを使用したロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0629_2016-->