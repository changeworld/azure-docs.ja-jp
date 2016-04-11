<properties
	pageTitle="クラシック Windows VM でのエンドポイントのセットアップ | Microsoft Azure"
	description="Azure 上で Windows 仮想マシンとの通信を許可するように Azure クラシック ポータルでエンドポイントをセットアップする方法について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Azure 上でクラシック Windows 仮想マシンにエンドポイントをセットアップする方法



クラシック デプロイメント モデルを使用して Azure で作成したすべての Windows 仮想マシンが、プライベート ネットワーク チャネルを介して、同じクラウド サービスまたは仮想ネットワーク内の他の仮想マシンと自動的に通信できます。ただし、インターネットまたは他の仮想ネットワークにあるコンピューターと通信するには、仮想マシンへの着信ネットワーク トラフィックを転送するエンドポイントが必要になります。Linux VM 上のエンドポイントに関する情報が必要な場合は、「[How to set up endpoints on a classic Linux virtual machine in Azure (Azure 上でクラシック Linux 仮想マシンにエンドポイントをセットアップする方法)](virtual-machines-linux-classic-setup-endpoints.md)」をご覧ください。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。

Azure クラシック ポータルで Windows 仮想マシンを作成すると、選択したオペレーティング システムに応じて、共通エンドポイント (リモート デスクトップや Windows PowerShell リモート処理用のエンドポイントなど) が通常は自動的に作成されます。必要に応じて、仮想マシンの作成中や作成後に、追加のエンドポイントを構成できます。



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## 次のステップ

* Azure PowerShell コマンドレットを使用して VM エンドポイントをセットアップするには、[Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx) をご覧ください。

* Azure PowerShell コマンドレットを使用してエンドポイントの ACL を管理するには、「[PowerShell を使用してアクセス制御リスト (ACL) のエンドポイントを管理する方法](../virtual-network/virtual-networks-acl-powershell.md)」をご覧ください。

* リソース マネージャーのデプロイメント モデルで仮想マシンを作成した場合、Azure PowerShell を使用して、[インターネットに接続するロード バランサーを作成する](../load-balancer/load-balancer-get-started-internet-arm-ps.md)こともできます。

<!---HONumber=AcomDC_0330_2016-->