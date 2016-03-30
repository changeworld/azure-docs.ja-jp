<properties
   pageTitle="Windows VM に関する技術解説記事 | Microsoft Azure"
   description="Microsoft Azure ドキュメントから Windows 仮想マシンに関連した全記事を紹介しています。"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="danlep"/>

# Azure の Windows VM に関する技術解説記事


ここでは、Microsoft Azure で Windows ベースの仮想マシンを作成、管理するために必要なすべてのドキュメントを紹介しています。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## リソース マネージャー デプロイ モデルにおける Windows VM

### 概要

[仮想マシンについて](virtual-machines-windows-about.md)

[VM、Web サイト、およびクラウド サービスの比較](../app-service-web/choose-web-site-cloud-service-vm.md)

[Azure リソース マネージャーにおける Compute、Network、Strage プロバイダー](virtual-machines-windows-compare-deployment-models.md)

[Azure Resource Manager の概要](../resource-group-overview.md)

[Virtual Machines とコンテナーが Azure にもたらすメリット](virtual-machines-windows-containers.md)

[仮想マシン スケール セットの概要](virtual-machines-windows-scale-sets-overview.md)



### 環境のセットアップ

[無料アカウント](https://azure.microsoft.com/free/)
 
[Azure PowerShell をインストールするには](../powershell-install-configure.md)

[Azure CLI のインストール](../xplat-cli-install.md)



### 作業開始
[Windows VM のラーニング パス](https://azure.microsoft.com/ja-JP/documentation/learning-paths/virtual-machines/)

[Azure ポータルで Windows 仮想マシンを作成する](virtual-machines-windows-classic-tutorial.md)

[リソース マネージャーで Windows 仮想マシンを作成するさまざまな方法](virtual-machines-windows-creation-choices.md)

[リソース マネージャーと PowerShell で Windows VM を作成する](virtual-machines-windows-ps-create.md)

[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-windows-connect-logon.md)




### プラン
[仮想マシンのサイズ](virtual-machines-windows-sizes.md)

[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](virtual-machines-windows-a8-a9-a10-a11-specs.md)

[Azure Virtual Machines の計画済みメンテナンス](virtual-machines-windows-planned-maintenance.md) [Azure インフラストラクチャ サービスの実装ガイドライン](virtual-machines-windows-infrastructure-service-guidelines.md)

[仮想マシンの可用性管理](virtual-machines-windows-manage-availability.md)

[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)

[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)


### デプロイ
[.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ](virtual-machines-windows-csharp-template.md)

[リソース マネージャー デプロイメント モデルで Windows 仮想マシンをキャプチャする方法](virtual-machines-windows-capture-image.md)

[VM イメージのアップロード](virtual-machines-windows-upload-image.md)

[Chef で Azure 仮想マシンのデプロイメントを自動化する](virtual-machines-windows-chef-automation.md)

[リソース マネージャーと Azure PowerShell を使用して、Windows 仮想マシンを作成し、構成する](virtual-machines-windows-create-powershell.md)

[リソース マネージャー テンプレートで Windows 仮想マシンを作成する](virtual-machines-windows-ps-template.md)

[Create a Windows Virtual machine with monitoring and diagnostics using Azure Resource Manager template (Azure Resource Manager テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する)](virtual-machines-windows-extensions-diagnostics-template.md)

[Azure リソース マネージャー テンプレートを使用した一般的なアプリケーション フレームワークのデプロイ](virtual-machines-windows-app-frameworks.md)



### 管理
[Azure コマンドライン インターフェイスで VM タスクに使用するリソース マネージャーとサービス管理の同等のコマンド](virtual-machines-windows-cli-manage.md)
	
[従来の VNet を新しい VNet に接続する](../virtual-network/virtual-networks-arm-asm-s2s-howto.md)
	
[Azure で仮想マシンにタグを付ける方法](virtual-machines-windows-tag.md)

[Azure リソース マネージャー テンプレートでのカスタム スクリプト拡張機能の使用](virtual-machines-windows-extensions-customscript.md)

[Azure リソース マネージャーと PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)
	
[Azure リソース マネージャー テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](virtual-machines-windows-cli-deploy-templates.md)
		
[Azure Virtual Machines での SQL Server の自動バックアップ](virtual-machines-windows-classic-ps-sql-backup.md)

[Azure Virtual Machines での SQL Server の自動修正](virtual-machines-windows-classic-ps-sql-patch.md)

[仮想マシン スケール セットでのマシンの自動スケール](virtual-machines-windows-ps-vmss-create.md)



### 構成

[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-rdp.md)

[仮想マシンの拡張機能とその機能について](virtual-machines-windows-extensions-features.md)
	
[Azure ポータルでの完全修飾ドメイン名の作成](virtual-machines-windows-portal-create-fqdn.md)

### Storage

[Azure 仮想マシン用のディスクと VHD について](virtual-machines-windows-about-disks-vhds.md)
	
[Azure ポータルでデータ ディスクを接続する方法](virtual-machines-windows-attach-disk-portal.md)


### ネットワーク

[Virtual Network の概要](../virtual-network/virtual-networks-overview.md)
	
[プレビュー ポータルを使用して NSG を管理する方法](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
	
[ロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-arm-ps.md)

	

### 開発

[Compute、Network、および Storage の .NET ライブラリを使用した Azure リソースのデプロイ](virtual-machines-windows-csharp.md)


[VM 拡張機能を使用した Azure Resource Manager テンプレートの作成](virtual-machines-windows-extensions-authoring-templates.md)

[Visual Studio での Azure リソース グループの作成とデプロイ](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
		
[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)


### ワークロード

[HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md)

[SQL Server](virtual-machines-windows-classic-sql-overview.md)

[Azure インフラストラクチャ サービスのワークロード: 高可用な基幹業務アプリケーション](virtual-machines-windows-lob.md)

[SharePoint](virtual-machines-windows-sharepoint-farm.md)


### リファレンス
[Azure リソース マネージャーでの、Mac、Linux、および Windows 用 Azure CLI の使用](azure-cli-arm-commands.md)

[コンピューティング REST API](https://msdn.microsoft.com/library/azure/mt163647.aspx)

[ネットワーク REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx)

[ストレージ REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[コンピューティング .NET API](https://msdn.microsoft.com/library/azure/mt131911.aspx)

[ネットワーク .NET API](https://msdn.microsoft.com/library/azure/dn973320.aspx)

[ストレージ .NET API](https://msdn.microsoft.com/library/azure/mt131037.aspx)

[PowerShell コマンドレット リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn708514.aspx)

[Azure テンプレート コミュニティのリファレンス ページ](https://azure.microsoft.com/documentation/templates/)



### トラブルシューティング

[Windows を実行する Azure 仮想マシンへの Remote Desktop 接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)

[Azure VM 拡張機能のエラーのトラブルシューティング](virtual-machines-windows-extensions-troubleshoot.md)

[リソース グループのデプロイのトラブルシューティング](resource-group-deploy-debug.md)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-windows-troubleshoot-app-connection.md)

[Azure で VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング](virtual-machines-windows-allocation-failure.md)




## クラシック デプロイ モデルにおける Windows VM

### 概要

[仮想マシンについて](virtual-machines-windows-about.md)

[クラシック デプロイ モデルで作成された Azure Virtual Machines についてよく寄せられる質問](virtual-machines-windows-classic-faq.md)

[VM、Web サイト、およびクラウド サービスの比較](../app-service-web/choose-web-site-cloud-service-vm.md)

[Virtual Machines とコンテナーが Azure にもたらすメリット](virtual-machines-windows-containers.md)



### 環境のセットアップ

[無料アカウント](https://azure.microsoft.com/free/)
 
[Azure PowerShell をインストールするには](../powershell-install-configure.md)

[Azure CLI のインストール](../xplat-cli-install.md)


### 作業開始
[Windows VM のラーニング パス](https://azure.microsoft.com/ja-JP/documentation/learning-paths/virtual-machines/)

[Azure クラシック ポータルで Windows 仮想マシンを作成する](virtual-machines-windows-classic-tutorial.md)

[Windows Server が実行されているクラシック仮想マシンにログオンする方法](virtual-machines-windows-classic-connect-logon.md)




### プラン

[クラシック仮想マシンのイメージについて](virtual-machines-windows-classic-about-images.md)

[仮想マシンのサイズ](virtual-machines-windows-sizes.md)

[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](virtual-machines-windows-a8-a9-a10-a11-specs.md)

[Azure Virtual Machines に対する計画的なメンテナンス](virtual-machines-windows-planned-maintenance.md)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-windows-infrastructure-service-guidelines.md)

[仮想マシンの可用性セットの作成](virtual-machines-windows-classic-configure-availability.md)


### デプロイ

[Windows を実行するカスタム仮想マシンの作成](virtual-machines-windows-classic-createportal.md)

[クラシック デプロイ モデルで作成された Windows 仮想マシンをキャプチャする](virtual-machines-windows-classic-capture-image.md)

[Powershell を使用したクラシック Windows Server VHD の作成とアップロード](virtual-machines-windows-classic-createupload-vhd.md)

[Chef で Azure 仮想マシンのデプロイメントを自動化する](virtual-machines-windows-chef-automation.md)

[Azure PowerShell でのクラシック Windows 仮想マシンの作成と構成](virtual-machines-windows-classic-create-powershell.md)

[Azure の仮想マシンにカスタム データを挿入する](virtual-machines-windows-classic-inject-custom-data.md)


### 管理

[Azure PowerShell を使用した仮想マシンの管理](virtual-machines-windows-classic-manage-psh.md)

[Azure コマンドライン インターフェイスで VM タスクに使用するリソース マネージャーとサービス管理の同等のコマンド](virtual-machines-windows-cli-manage.md)
	
[従来の VNet を新しい VNet に接続する](../virtual-network/virtual-networks-arm-asm-s2s-howto.md)
	
[仮想マシンのエージェントおよび拡張機能について](virtual-machines-windows-classic-agents-and-extensions.md)

[仮想マシン拡張機能の管理](virtual-machines-windows-classic-manage-extensions.md)

[クラシック Windows 仮想マシンのカスタム スクリプト拡張機能](virtual-machines-windows-classic-extensions-customscript.md)

[Azure Virtual Machines での SQL Server の自動バックアップ](virtual-machines-windows-classic-ps-sql-backup.md)

[Azure Virtual Machines での SQL Server の自動修正](virtual-machines-windows-classic-ps-sql-patch.md)



### 構成

[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-rdp.md)

[仮想マシンの拡張機能とその機能について](virtual-machines-windows-extensions-features.md)

[Windows VM に Symantec Endpoint Protection をインストールし、構成する方法](virtual-machines-windows-classic-install-symantec.md)
	
[Windows VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](virtual-machines-windows-classic-install-trend.md)

[クラシック デプロイ モデルに仮想マシンの可用性セットを構成する方法](virtual-machines-windows-classic-configure-availability.md)

[クラシック Azure 仮想マシンでエンドポイントをセットアップする方法](virtual-machines-windows-classic-setup-endpoints.md)

### Storage

[Azure 仮想マシン用のディスクと VHD について](virtual-machines-windows-about-disks-vhds.md)
	
[クラシック Windows 仮想マシンにデータ ディスクを接続する方法](virtual-machines-windows-classic-attach-disk.md)

[クラシック Windows 仮想マシンからデータ ディスクを切断する方法](virtual-machines-windows-classic-detach-disk.md)

[Windows 仮想マシンのデータ ドライブとしての D ドライブの使用](virtual-machines-windows-classic-change-drive-letter.md)

### ネットワーク

[Virtual Network の概要](../virtual-network/virtual-networks-overview.md)

[仮想ネットワークまたはクラウド サービスで構成されるクラシック デプロイ モデルで作成された仮想マシンに接続します。](virtual-machines-windows-classic-connect-vms.md)
	
[Azure PowerShell で NSG を管理する方法](../virtual-network/virtual-networks-create-nsg-classic-ps.md)
	
[ロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-classic-portal.md)

	

### 開発

[Visual Studio での Azure Virtual Machines の作成と管理](virtual-machines-windows-classic-manage-visual-studio.md)

[Visual Studio での Web アプリケーション用仮想マシンの作成](virtual-machines-windows-classic-web-app-visual-studio.md)

[Azure の仮想マシンで多くのコンピューティング処理を要する .NET タスクを実行する方法](virtual-machines-windows-classic-run-compute-intensive-task.md)

[仮想マシンで多くのコンピューティング処理を要する Java タスクを実行する方法](virtual-machines-windows-classic-java-run-compute-intensive-task.md)

[Windows Server VM での Django Hello World Web アプリケーション](virtual-machines-windows-classic-python-django-web-app.md)
		


### ワークロード

[高可用性基幹業務アプリケーション](virtual-machines-windows-lob.md)

[HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md)

[ハイブリッド クラウド テスト環境](virtual-machines-windows-classic-hybrid-test-env.md)

[MongoDB](virtual-machines-windows-classic-install-mongodb.md)

[MySQL](virtual-machines-windows-classic-mysql-2008r2.md)

[Oracle](virtual-machines-windows-classic-oracle.md)

[SQL Server](virtual-machines-windows-classic-sql-overview.md)

[SharePoint](virtual-machines-windows-sharepoint-farm.md)

[Tomcat](virtual-machines-windows-classic-java-run-tomcat-app-server.md)

### リファレンス
[Azure サービス管理での Mac、Linux、および Windows 用 Azure CLI の使用](../virtual-machines-command-line-tools.md)

[サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[サービス管理 .NET API](https://msdn.microsoft.com/library/azure/mt420161.aspx)

[Azure サービス管理 PowerShell コマンドレットのリファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn708504.aspx)


### トラブルシューティング

[Windows を実行する Azure 仮想マシンへの Remote Desktop 接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-windows-troubleshoot-app-connection.md)

[Azure で VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング](virtual-machines-windows-allocation-failure.md)

<!---HONumber=AcomDC_0323_2016-->