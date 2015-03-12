<properties 
	pageTitle="クラウド サービスのリモート デスクトップの有効化 (Node.js)" 
	description="Azure Node.js アプリケーションをホストする仮想マシンについてリモート デスクトップ アクセスを有効にする方法を説明します。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Azure でのリモート デスクトップの有効化

リモート デスクトップを使用して、Azure で実行されているロール インスタンスのデスクトップにアクセスできます
。リモート デスクトップ接続を使用して
仮想マシンの構成やアプリケーションの問題のトラブルシューティングを行うことができます
。

> [AZURE.NOTE] この記事で説明する手順は、Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。

このタスクの手順は次のとおりです。

-   [手順 1:Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する]
-   [手順 2:ロール インスタンスに接続する]
-   [手順 3:Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが
    無効になるように構成する]

## <a name="step1"> </a>手順 1:Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する

リモート デスクトップを使用するには、
ユーザー名、パスワード、および証明書を使ってクラウドのロール インスタンスで認証するように
サービス定義とサービス構成を構成する必要があります。[Azure PowerShell] には、この構成を行うことができる **Enable-AzureServiceProjectRemoteDesktop** コマンドレットが含まれています
。

サービス定義を作成したコンピューターから、次の手順を実行します
。

1.  **[スタート]** メニューの **[Azure PowerShell]** を選択します。

	![Azure PowerShell start menu entry][powershell-menu]

2.  サービス ディレクトリに移動し、
    「**Enable-AzureServiceProjectRemoteDesktop**」と入力した後、
    クラウドのロール インスタンスで認証するときに使用するユーザー名とパスワードを入力します
    。

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  サービスの構成変更をクラウドに発行します。次に、
    **Azure PowerShell** プロンプトで、
    「**Publish-AzureServiceProject**」と入力します。

	![publish-azureserviceproject][publish-project]

これらの手順を完了すると、クラウドのサービスのロール インスタンスで、
リモート デスクトップ アクセスが構成されます。

## <a name="step2"> </a>手順 2:ロール インスタンスに接続する

Azure でデプロイを実行できるようになったため、ロール インスタンスに接続できます
。

1.  [Azure 管理ポータル]で **[クラウド サービス]** を選択し、手順 1. でデプロイしたサービスを選択します。

	![azure management portal][cloud-services]

2.  **[インスタンス]** をクリックし、**[運用]** または **[ステージング]** をクリックしてサービスのインスタンスを表示します。インスタンスを選択し、ページの下部にある **[接続]** をクリックします。

    ![The instances page][3]

2.  **[接続]** をクリックすると、.rdp ファイルを保存するように促すメッセージが Web ブラウザーに表示されます
    。Internet Explorer を使用している場合は、**[開く]** をクリックします。

    ![prompt to open or save the .rdp file][4]

3.  ファイルが開くと、セキュリティに関する次のメッセージが表示されます。

    ![Windows security prompt][5]

4.  **[接続]** をクリックすると、インスタンスにアクセスするための資格情報を入力するように求めるセキュリティ メッセージが表示されます
    。これに対して、
    [手順 1][手順 1:Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する] で作成したパスワードを入力し、**[OK]** をクリックします。

    ![username/password prompt][6]

接続すると、リモート デスクトップ接続に、
Azure 内のインスタンスのデスクトップが表示されます。インスタンスへのリモート アクセスが正常に確立されたので、
アプリケーションの管理に必要なタスクを実行できます
。

![Remote desktop session][7]

## <a name="step3"> </a>手順 3:Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する

クラウドのロール インスタンスに対するリモート デスクトップ接続が不要になった場合は、
[Azure PowerShell] を使ってリモート デスクトップ アクセスを無効にします。

1.  **[スタート]** メニューの **[Azure PowerShell]** を選択します。

2.  サービス ディレクトリに移動し、
    「**Disable-AzureServiceProjectRemoteDesktop**」と入力します。

3.  サービスの構成変更をクラウドに発行します。次に、
    **Azure PowerShell** プロンプトで、
    「**Publish-AzureServiceProject**」と入力します。

## その他のリソース

- [Azure でのロール インスタンスへのリモート アクセス] 
- [Azure ロールでのリモート デスクトップの使用]

  [手順 1.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する]: #step1
  [手順 2:ロール インスタンスに接続する]: #step2
  [手順 3:Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure 管理ポータル]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Azure でのロール インスタンスへのリモート アクセス]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Azure ロールでのリモート デスクトップの使用]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=45--> 
