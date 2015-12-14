<properties 
	pageTitle="クラウド サービスのリモート デスクトップの有効化 (Node.js)" 
	description="Azure Node.js アプリケーションをホストする仮想マシンについてリモート デスクトップ アクセスを有効にする方法を説明します。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="robmcm"/>


# Azure でのリモート デスクトップの有効化

リモート デスクトップを使用して、Azure で実行されているロール インスタンスのデスクトップにアクセスできます。リモート デスクトップ接続を使用して仮想マシンの構成やアプリケーションの問題のトラブルシューティングを行うことができます。

> [AZURE.NOTE]この記事は、Azure クラウド サービスとしてホストされる Node.js アプリケーションに適用されます。


## 前提条件

- [Azure Powershell](../install-configure-powershell.md) のインストールおよび構成。
- Azure クラウド サービスへの Node.js アプリのデプロイ。詳細については、「[Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ](cloud-services-nodejs-develop-deploy-app.md)」を参照してください。


## 手順 1. Azure PowerShell を使用して、リモート デスクトップ アクセス用にサービスを構成する

リモート デスクトップを使用するには、ユーザー名、パスワード、および証明書を使用して、Azure サービス定義および構成を更新する必要があります。

アプリのソース ファイルが含まれているコンピューターから次の手順を実行します。

1. **Azure PowerShell** を管理者として実行します。(**[スタート] メニュー**または**スタート画面**で、**Azure PowerShell** を検索します。)

2.  サービス定義 (.csdef) ファイルおよびサービス構成 (.cscfg) ファイルを含むディレクトリに移動します。

3. 次の PowerShell コマンドレットに入ります。

		Enable-AzureServiceProjectRemoteDesktop

4. プロンプトに、ユーザー名とパスワードを入力します。

	![Enable-AzureServiceProjectRemoteDesktop][enable-rdp]

3.  次の PowerShell コマンドレットに入り、変更を発行します。

    	Publish-AzureServiceProject

	![Publish-AzureServiceProject][publish-project]

## 手順 2. ロール インスタンスに接続する

サービス定義の更新を発行後は、ロール インスタンスに接続することができます。

1.  [Azure クラシック ポータル]で **[クラウド サービス]** を選択し、サービスを選択します。

	![Azure クラシック ポータル][cloud-services]

2.  **[インスタンス]** をクリックし、**[運用]** または **[ステージング]** をクリックしてサービスのインスタンスを表示します。インスタンスを選択し、ページの下部にある **[接続]** をクリックします。

    ![[インスタンス] ページ][3]

2.  **[接続]** をクリックすると、.rdp ファイルを保存するように促すメッセージが Web ブラウザーに表示されます。このファイルを開きます。(たとえば、Internet Explorer を使用している場合は、**[開く]** をクリックします。)

    ![.rdp ファイルを開くまたは保存することを促すメッセージ][4]

3.  ファイルが開くと、セキュリティに関する次のメッセージが表示されます。

    ![Windows のセキュリティに関するメッセージ][5]

4.  **[接続]** をクリックすると、インスタンスにアクセスするための資格情報を入力するように求めるセキュリティ メッセージが表示されます。[手順 1.][手順 1: Azure PowerShell を使用して、リモート デスクトップ アクセス用にサービスを構成する] で作成したパスワードを入力し、**[OK]** をクリックします。

    ![ユーザー名/パスワードの入力を求めるメッセージ][6]

接続すると、リモート デスクトップ接続に、Azure 内のインスタンスのデスクトップが表示されます。

![リモート デスクトップ セッション][7]

## 手順 3. リモート デスクトップ アクセスが無効になるようにサービスを構成する 

クラウドのロール インスタンスに対するリモート デスクトップ接続が不要になった場合は、[Azure PowerShell ]を使ってリモート デスクトップ アクセスを無効にします。

1.  次の PowerShell コマンドレットに入ります。

    	Disable-AzureServiceProjectRemoteDesktop

2.  次の PowerShell コマンドレットに入り、変更を発行します。

    	Publish-AzureServiceProject

## その他のリソース

- [Azure のロール インスタンスへのリモート アクセス] 
- [Azure ロールでのリモート デスクトップの使用]
- [Node.js デベロッパー センター](/develop/nodejs/)

  [Azure PowerShell ]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure クラシック ポータル]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Azure のロール インスタンスへのリモート アクセス]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Azure ロールでのリモート デスクトップの使用]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 

<!---HONumber=AcomDC_1203_2015-->