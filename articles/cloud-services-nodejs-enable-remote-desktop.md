<properties linkid="dev-node-remotedesktop" urlDisplayName="リモート デスクトップの有効化" pageTitle="クラウド サービスのリモート デスクトップの有効化 (Node.js)" metaKeywords="Azure Node.js リモート アクセス, Azure Node.js リモート接続, Azure Node.js VM アクセス, Azure Node.js 仮想マシン アクセス" description="Azure Node.js アプリケーションをホストする仮想マシンについてリモート デスクトップ アクセスを有効にする方法を説明します。" metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Azure でのリモート デスクトップの有効化" authors="" solutions="" manager="" editor="" />





# Azure でのリモート デスクトップの有効化

リモート デスクトップを使用して、Azure で実行されているロール インスタンスの
デスクトップにアクセスできます。リモート デスクトップ接続を使用して仮想マシンの構成や
アプリケーションの問題のトラブルシューティングを行うことが
できます。

<div class="dev-callout">
	<b>注</b>
	<p>この記事で説明する手順は、Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。</p>
	</div>

このタスクの手順は次のとおりです。

-   [手順 1. Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する]
-   [手順 2. ロール インスタンスに接続する]
-   [手順 3. Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する]

## <a name="step1"> </a>手順 1. Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する

リモート デスクトップを使用するには、ユーザー名、パスワード、および証明書を
使ってクラウドのロール インスタンスで認証するようにサービス定義と
サービス構成を構成する必要があります。[Azure PowerShell] には **Enable-AzureServiceProjectRemoteDesktop** コマンドレットが含まれており、
これを使ってこの構成を行うことができます。

サービス定義を作成したコンピューターから、
次の手順を実行します。

1.  **[スタート]** メニューの **[Azure PowerShell]** を選択します。

	![[スタート] メニューの Azure PowerShell のエントリ][powershell-menu]

2.  サービス ディレクトリに移動し、
    「**Enable-AzureServiceProjectRemoteDesktop**」と入力した後、
    クラウドのロール インスタンスで認証するときに使用するユーザー名とパスワードを
    入力します。

	![Enable-AzureServiceProjectRemoteDesktop][enable-rdp]

3.  サービスの構成変更をクラウドに発行します。**Azure 
    PowerShell** プロンプトで、「**Publish-
    AzureServiceProject**」と入力します。

	![Publish-AzureServiceProject][publish-project]

これらの手順を完了すると、クラウドのサービスのロール インスタンスで、リモート デスクトップ 
アクセスが構成されます。

## <a name="step2"> </a>手順 2. ロール インスタンスに接続する

Azure で展開を実行できるようになったため、ロール インスタンスに
接続できます。

1.  [Azure 管理ポータル]で **[クラウド サービス]** を選択し、手順 1. で展開したサービスを選択します。

	![Azure 管理ポータル][cloud-services]

2.  **[インスタンス]** をクリックし、**[運用]** または **[ステージング]** をクリックしてサービスのインスタンスを表示します。インスタンスを選択し、ページの下部にある **[接続]** をクリックします。

    ![[インスタンス] ページ][3]

2.  **[接続]** をクリックすると、.rdp ファイルを保存するように促すメッセージが Web ブラウザーに
    表示されます。Internet Explorer を使用している場合は、**[開く]** をクリックします。

    ![.rdp ファイルを開くまたは保存することを促すメッセージ][4]

3.  ファイルが開くと、セキュリティに関する次のメッセージが表示されます。

    ![Windows のセキュリティに関するメッセージ][5]

4.  **[接続]** をクリックすると、インスタンスにアクセスするための資格情報を入力する
    ように求めるセキュリティ メッセージが表示されます。[手順 1. ][手順 1. 
    Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する]で作成したパスワードを入力し、**[OK]** をクリックします。

    ![ユーザー名/パスワードの入力を求めるメッセージ][6]

接続すると、リモート デスクトップ接続に、Azure 内のインスタンスのデスクトップが
表示されます。インスタンスへのリモート アクセスが
正常に確立されたので、アプリケーションの管理に必要なタスクを
実行できます。

![リモート デスクトップ セッション][7]

## <a name="step3"> </a>手順 3. Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する

クラウドのロール インスタンスに対するリモート デスクトップ接続が不要になった場合は、[Azure PowerShell] を使ってリモート デスクトップ アクセスを無効にします。

1.  **[スタート]** メニューの **[Azure PowerShell]** を選択します。

2.  サービス ディレクトリに移動し、「**Disable-
    AzureServiceProjectRemoteDesktop**」と入力します。

3.  サービスの構成変更をクラウドに発行します。**Azure 
    PowerShell** プロンプトで、「**Publish-
    AzureServiceProject**」と入力します。

## その他のリソース

- [Azure ロールのリモート デスクトップ接続をセットアップする]
- [Azure ロールでのリモート デスクトップの使用]

  [手順 1. Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスを構成する]: #step1
  [手順 2. ロール インスタンスに接続する]: #step2
  [手順 3. Azure PowerShell を使用して、サービスのリモート デスクトップ アクセスが無効になるように構成する]: #step3
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
  
  [Azure ロールのリモート デスクトップ接続をセットアップする]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh124107.aspx
  [Azure ロールでのリモート デスクトップの使用]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg443832.aspx

