<properties 
   pageTitle="PowerShell を使用したインターネットに接続するロード バランサー (クラシック) の作成の開始 | Microsoft Azure"
   description="PowerShell を使用し、インターネットに接続するクラシック モードのロード バランサーを作成する方法について説明します"
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
   ms.date="09/23/2015"
   ms.author="joaoma" />

# PowerShell を使用したインターネットに接続するロード バランサー (クラシック) の作成の開始

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、クラシック デプロイメント モデルについて説明します。Azure リソース マネージャー デプロイ モデルについて確認したい場合は、「[リソース マネージャーを使用したインターネットに接続するロード バランサーの作成の開始](load-balancer-get-started-internet-arm-ps.md)」を参照してください。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## PowerShell を使用してロード バランサーを設定する

PowerShell を使用してロード バランサーを設定するには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。


2. 仮想マシンを作成した後は、PowerShell コマンドレットを使用し、同じクラウド サービス内の仮想マシンにロード バランサーを追加できます。

次の例では、クラウド サービス "mytestcloud" (または myctestcloud.cloudapp.net) に "webfarm" というロード バランサーのセットを、"web1" および" web2" という名前の仮想マシンにロード バランサーのエンドポイントに追加して追加します。ロード バランサーはポート 80 でネットワーク トラフィックを受信し、TCP を利用して (この場合はポート 80 の) ローカル エンドポイントで定義されている仮想マシン間の負荷を分散します。


### 手順 1.
最初の VM "web1" に負荷分散のためのエンドポイントを作成します

	Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### 手順 2. 

同じロード バランサー セット名を使用して、2 番目の VM " web2" にまたエンドポイントを作成します

	Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## ロード バランサーから仮想マシンを削除する

ロード バランサーから仮想マシンのエンドポイントを削除するには、Remove-AzureEndpoint を使用します

	Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## 次のステップ

[内部ロード バランサーの構成の開始](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->