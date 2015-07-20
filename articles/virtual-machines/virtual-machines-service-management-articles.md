<properties
	pageTitle="サービス管理での Virtual Machines の操作に関する記事 | Microsoft Azure"
	description="以下に示すのは、Azure サービス管理での仮想マシンの作成や管理に関する記事の一覧です。"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-multiple"
	ms.workload="infrastructure-services"
	ms.date="06/30/2015"
	ms.author="danlep"/>

# サービス管理での仮想マシンの操作に関する記事
以下に示すのは、Azure サービス管理での仮想マシンの操作に関する資料の一覧です。新規のデプロイメントについては、Azure リソース マネージャー ベースの仮想マシンとその他のリソースを使用することをお勧めします。リソース マネージャーを使用する利点の詳細については、「[Azure リソース マネージャーにおける Azure コンピューティング、ネットワーク、ストレージ プロバイダー](virtual-machines-azurerm-versus-azuresm.md)」を参照してください。

## はじめに

[Windows を実行する仮想マシンを Azure 管理ポータルで作成する](virtual-machines-windows-tutorial-classic-portal.md)

[Windows を実行するカスタム仮想マシンを作成する方法](virtual-machines-windows-create-custom.md)

[Azure 上で Linux を実行するカスタム仮想マシンを作成する方法](virtual-machines-linux-create-custom.md)

[チュートリアル: Azure でのクラウド専用仮想ネットワークの作成](create-virtual-network.md)

## 自動化

[Azure PowerShell を使用して Linux ベースの仮想マシンを作成と事前構成する](virtual-machines-ps-create-preconfigure-linux-vms.md)

[Azure PowerShell を使用して Windows ベースの仮想マシンを作成と事前構成する](virtual-machines-ps-create-preconfigure-windows-vms.md)

[PowerShell と Azure サービス管理で Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-service-manager.md)

[Azure コマンドライン インターフェイス (Azure CLI) を使用して VM を作成する](virtual-machines-xplat-getting-started.md)

[Azure CLI を使用して複数の VM のデプロイを作成する](virtual-machines-create-multi-vm-deployment-xplat-cli.md)

## 計画

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure VM 構成設定について](https://msdn.microsoft.com/library/azure/dn763935.aspx)

[仮想ネットワークの概要](https://msdn.microsoft.com/library/azure/jj156007.aspx)

## 作成

[Windows Server VHD の作成と Azure へのアップロード](virtual-machines-create-upload-vhd-windows-server.md)

[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](virtual-machines-linux-create-upload-vhd.md)

[テンプレートとして使用する Windows 仮想マシンのキャプチャ方法](virtual-machines-capture-image-windows-server.md)


[テンプレートとして使用するために Linux 仮想マシンをキャプチャする方法](virtual-machines-linux-capture-image.md)

[テンプレートとして使用するために CLI を使って Linux 仮想マシンをキャプチャする方法](virtual-machines-vm-capture-image-cli.md)


## 管理

[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)

[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)

[データ ディスクを Windows 仮想マシンに接続する方法](storage-windows-attach-disk.md)

[Windows 仮想マシンからディスクを切断する方法](storage-windows-detach-disk.md)

[データ ディスクを Linux 仮想マシンに接続する方法](virtual-machines-linux-how-to-attach-disk.md)

[データ ディスクを仮想マシンから切断する方法](virtual-machines-linux-how-to-detach-disk.md)

[仮想マシンを仮想ネットワークまたはクラウド サービスと接続する方法](cloud-services-connect-virtual-machine.md)

[仮想マシンの可用性セットを構成する方法](virtual-machines-how-to-configure-availability.md)

[内部ロード バランサーの構成の開始](../load-balancer/load-balancer-internal-getstarted.md)

[インターネットに接続するロード バランサーの構成の開始](../load-balancer/load-balancer-internet-getstarted.md)

[仮想マシンに対してエンドポイントを設定する方法](virtual-machines-set-up-endpoints.md)

## ワークロードの作成

[Active Directory](https://msdn.microsoft.com/library/azure/jj156090.aspx)

[Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)

[Docker](virtual-machines-docker-with-xplat-cli.md)

[Dynamics NAV](https://msdn.microsoft.com/library/azure/dn168977.aspx)

[HPC Pack](https://msdn.microsoft.com/library/azure/dn518135.aspx)

[LAMP Stack](virtual-machines-linux-install-lamp-stack.md)

[Windows Server を実行する仮想マシンに MongoDB をインストールする](virtual-machines-install-mongodb-windows-server.md)

[Azure で Windows Server 2012 R2 を実行する仮想マシンへの MySQL のインストール](virtual-machines-mysql-windows-server-2008r2.md)

[Oracle](virtual-machines-oracle-azure-virtual-machines.md)

[SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

[SQL Server](virtual-machines-sql-server-infrastructure-services.md)

## 監視

[Azure の監視アラートと通知について](https://msdn.microsoft.com/library/azure/dn306639.aspx)

[Azure のクラウド サービスおよび仮想マシンの診断機能](../cloud-services/cloud-services-dotnet-diagnostics.md)

[Visual Studio でのクラウド サービスまたは仮想マシンのデバッグ](https://msdn.microsoft.com/library/azure/ff683670.aspx)

[Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行](https://msdn.microsoft.com/library/azure/dn642480.aspx)

## トラブルシューティング

[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](virtual-machines-troubleshoot-ssh-connections.md)

## リファレンス

[PowerShell cmdlets for Service Management (サービス管理用の PowerShell コマンドレット)](https://msdn.microsoft.com/library/azure/dn708504.aspx)

[サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[Azure サービス管理での Mac、Linux、および Windows 用 Azure CLI の使用](virtual-machines-command-line-tools.md)

<!---HONumber=July15_HO2-->