<properties linkid="dev-node-remotedesktop" urlDisplayName="Enable Remote Desktop" pageTitle="Enable remote desktop for cloud services (Node.js)" metaKeywords="Azure Node.js remote access, Azure Node.js remote connection, Azure Node.js VM access, Azure Node.js virtual machine access" description="Learn how to enable remote-desktop access for the virtual machines hosting your Azure Node.js application. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Azure でのリモート デスクトップの有効化

リモート デスクトップを使用して、Azure で実行されているロール インスタンスのデスクトップにアクセスできます。リモート デスクトップ接続を使用して仮想マシンの構成やアプリケーションの問題のトラブルシューティングを行うことができます。

<div class="dev-callout">
<b>注</b>
<p>この記事で説明する手順は、Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。</p>
    </div>

このタスクの手順は次のとおりです。

-   [手順 1.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する][手順 1.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する]
-   [手順 2.ロール インスタンスに接続する][手順 2.ロール インスタンスに接続する]
-   [手順 3.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する][手順 3.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する]

## <a name="step1"> </a>手順 1.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する

リモート デスクトップを使用するには、ユーザー名、パスワード、および証明書を使ってクラウドのロール インスタンスで認証するようにサービス定義とサービス構成を構成する必要があります。[Azure PowerShell][Azure PowerShell] には、この構成を行うことができる**Enable-AzureServiceProjectRemoteDesktop** コマンドレットが含まれています。

サービス定義を作成したコンピューターから、次の手順を実行します。

1.  **[スタート]** メニューの **[Azure PowerShell]** を選択します。

    ![[スタート] メニューの Azure PowerShell のエントリ](./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png)

2.  サービス ディレクトリに移動し、「**Enable-AzureServiceProjectRemoteDesktop**」と入力した後、クラウドのロール インスタンスで認証するときに使用するユーザー名とパスワードを入力します。

    ![Enable-AzureServiceProjectRemoteDesktop][Enable-AzureServiceProjectRemoteDesktop]

3.  サービスの構成変更をクラウドに発行します。**Azure PowerShell** プロンプトで、「**Publish- AzureServiceProject**」と入力します。

    ![Publish-AzureServiceProject][Publish-AzureServiceProject]

これらの手順を完了すると、クラウドのサービスのロール インスタンスで、リモート デスクトップ アクセスが構成されます。

## <a name="step2"> </a>手順 2.ロール インスタンスに接続する

Azure でデプロイメントを実行できるようになったため、ロール インスタンスに接続できます。

1.  [Azure 管理ポータル][Azure 管理ポータル]で **[クラウド サービス]** を選択し、手順 1. でデプロイしたサービスを選択します。

    ![Azure 管理ポータル][1]

2.  **[インスタンス]** をクリックし、**[運用]** または **[ステージング]** をクリックしてサービスのインスタンスを表示します。インスタンスを選択し、ページの下部にある **[接続]** をクリックします。

    ![[インスタンス] ページ](./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png)

3.  **[接続]** をクリックすると、.rdp ファイルを保存するように促すメッセージが Web ブラウザーに表示されます。Internet Explorer を使用している場合は、**[開く]** をクリックします。

    ![.rdp ファイルを開くまたは保存することを促すメッセージ][.rdp ファイルを開くまたは保存することを促すメッセージ]

4.  ファイルが開くと、セキュリティに関する次のメッセージが表示されます。

    ![Windows のセキュリティに関するメッセージ][Windows のセキュリティに関するメッセージ]

5.  **[接続]** をクリックすると、インスタンスにアクセスするための資格情報を入力するように求めるセキュリティ メッセージが表示されます。[手順 1.][手順 1.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する] で作成したパスワードを入力し、 **[OK]** をクリックします。

    ![ユーザー名/パスワードの入力を求めるメッセージ][ユーザー名/パスワードの入力を求めるメッセージ]

接続すると、リモート デスクトップ接続に、Azure 内のインスタンスのデスクトップが表示されます。インスタンスへのリモート アクセスが正常に確立されたので、アプリケーションの管理に必要なタスクを実行できます。

![リモート デスクトップ セッション][リモート デスクトップ セッション]

## <a name="step3"> </a>手順 3.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する

クラウドのロール インスタンスに対するリモート デスクトップ接続が不要になった場合は、[Azure PowerShell][Azure PowerShell] を使ってリモート デスクトップ アクセスを無効にします。

1.  **[スタート]** メニューの **[Azure PowerShell]** を選択します。

2.  サービス ディレクトリに移動し、「**Disable-AzureServiceProjectRemoteDesktop**」と入力します。

3.  サービスの構成変更をクラウドに発行します。**Azure PowerShell** プロンプトで、「**Publish- AzureServiceProject**」と入力します。

## その他のリソース

-   [Windows Azure ロールのリモート デスクトップ接続をセットアップする][Windows Azure ロールのリモート デスクトップ接続をセットアップする]
-   [Windows Azure ロールでのリモート デスクトップの使用][Windows Azure ロールでのリモート デスクトップの使用]

  [手順 1.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する]: #step1
  [手順 2.ロール インスタンスに接続する]: #step2
  [手順 3.Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

  [Enable-AzureServiceProjectRemoteDesktop]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
  [Publish-AzureServiceProject]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [1]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png

  [.rdp ファイルを開くまたは保存することを促すメッセージ]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [Windows のセキュリティに関するメッセージ]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [ユーザー名/パスワードの入力を求めるメッセージ]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [リモート デスクトップ セッション]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  [Windows Azure ロールのリモート デスクトップ接続をセットアップする]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh124107.aspx
  [Windows Azure ロールでのリモート デスクトップの使用]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg443832.aspx
