---
title: PowerShell を使用したインターネットに接続するロード バランサー (クラシック) の作成の開始 | Microsoft Docs
description: PowerShell を使用し、インターネットに接続するクラシック モードのロード バランサーを作成する方法について説明します
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-service-management

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: sewhee

---
# PowerShell を使用したインターネットに接続するロード バランサー (クラシック) の作成の開始
[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイメント モデルについて説明します。[Azure リソース マネージャーを使用し、インターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-arm-ps.md)についても説明します。

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## PowerShell を使用してロード バランサーを設定する
PowerShell を使用してロード バランサーを設定するには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。
2. 仮想マシンを作成した後は、PowerShell コマンドレットを使用し、同じクラウド サービス内の仮想マシンにロード バランサーを追加できます。

次の例では、クラウド サービス "mytestcloud" (または myctestcloud.cloudapp.net) に "webfarm" というロード バランサーのセットを、"web1" および" web2" という名前の仮想マシンにロード バランサーのエンドポイントに追加して追加します。ロード バランサーはポート 80 でネットワーク トラフィックを受信し、TCP を利用して (この場合はポート 80 の) ローカル エンドポイントで定義されている仮想マシン間の負荷を分散します。

### 手順 1
最初の VM "web1" に負荷分散のためのエンドポイントを作成します

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### 手順 2.
同じロード バランサー セット名を使用して、2 番目の VM " web2" にまたエンドポイントを作成します

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## ロード バランサーから仮想マシンを削除する
ロード バランサーから仮想マシンのエンドポイントを削除するには、Remove-AzureEndpoint を使用します

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## 次のステップ
[内部ロード バランサーの作成を開始](load-balancer-get-started-ilb-classic-ps.md)し、特定のロード バランサーのネットワーク トラフィックの動作に適した種類の[分散モード](load-balancer-distribution-mode.md)を構成することもできます。

アプリケーションでロード バランサーの背後にあるサーバーの接続を維持する必要がある場合は、[ロード バランサーの TCP アイドル タイムアウトの設定](load-balancer-tcp-idle-timeout.md)に関する詳細を確認してください。Azure Load Balancer 使用時にアイドル接続動作について理解するのに役立ちます。

<!---HONumber=AcomDC_0824_2016-->