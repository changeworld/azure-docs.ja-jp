<properties
	pageTitle="リモート デスクトップの詳細なトラブルシューティング |Microsoft Azure"
	description="Windows を実行する Azure 仮想マシンへの RDP 接続に関する詳細なトラブルシューティングの手順について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="04/06/2016"
	ms.author="iainfou"/>

# Windows ベースの Azure Virtual Machines へのリモート デスクトップ接続に関する詳細なトラブルシューティング

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

この記事では、Windows ベースの Azure 仮想マシンの複雑なリモート デスクトップのエラーを診断して修正するための詳細なトラブルシューティング手順を説明します。

> [AZURE.IMPORTANT] 先に進む前に、リモート デスクトップのより一般的なエラーを解消するために、必ず[リモート デスクトップの基本的なトラブルシューティングの記事](virtual-machines-windows-troubleshoot-rdp-connection.md)をお読みください。

[基本的なリモート デスクトップのトラブルシューティング ガイド](virtual-machines-windows-troubleshoot-rdp-connection.md)で説明されているどのエラー メッセージとも似ていないリモート デスクトップ エラー メッセージが表示される場合は、以下の手順に従って、リモート デスクトップ ([RDP](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)) クライアントが Azure VM 上の RDP サービスに接続できない理由を調べてみることができます。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。Azure サポートの使用方法の詳細については、「[Azure Support FAQ (Microsoft Azure サポートに関する FAQ)](https://azure.microsoft.com/support/faq/)」を参照してください。


## リモート デスクトップ接続のコンポーネント

以下は、RDP 接続に関連するコンポーネントです。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

先に進む前に、前回 VM に正常にリモート デスクトップ接続できたとき以降、何を変更したかを思い返してみると、役に立つかもしれません。次に例を示します。

- VM のパブリック IP アドレスまたは VM を含むクラウド サービスのパブリック IP アドレス (仮想 IP アドレス ([VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) とも呼ばれます) を変更した場合、DNS クライアント キャッシュが DNS 名のために登録された*古い IP アドレス*をまだ持っているために、RDP が失敗することがあります。DNS クライアント キャッシュをフラッシュしてから、もう一度 VM に接続してみてください。または、新しい VIP に直接接続してみてください。
- リモート デスクトップ接続を管理するために、Azure ポータルのいずれかではなく、サード パーティ製のアプリケーションを使用している場合は、アプリケーションの構成にリモート デスクトップ トラフィックのための正しい TCP ポートが含まれていることを確認してください。クラシック仮想マシンのこのポートを確認するには、[Azure ポータル](https://portal.azure.com)で VM の [設定]、[エンドポイント] の順にクリックします。


## 準備作業

詳細なトラブルシューティングに進む前に、以下の操作を実行します。

- Azure クラシック ポータルまたは Azure ポータルで、仮想マシンの状態に明らかな問題があるかどうかを確認します。
- [基本的なトラブルシューティング ガイドの一般的な RDP エラーに対する応急処置の手順](virtual-machines-windows-troubleshoot-rdp-connection.md#quickfixrdp)に従います。


これらの手順を実行してから、リモート デスクトップを介して VM に再接続してみてください。


## 詳細なトラブルシューティング

リモート デスクトップ クライアントから Azure VM のリモート デスクトップ サービスに接続できないのは、以下のソースに問題があることが原因である可能性があります。

- リモート デスクトップ クライアント コンピューター
- 組織のイントラネットのエッジ デバイス
- クラウド サービス エンドポイントとアクセス制御リスト (ACL)
- ネットワーク セキュリティ グループ
- Windows ベースのAzure 仮想マシン

### ソース 1: リモート デスクトップ クライアント コンピューター

コンピューターが他のオンプレミスの Windows ベースのコンピューターにリモート デスクトップ接続を実行できることを確認します。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

接続できない場合は、そのコンピューターで以下を確認してください。

- リモート デスクトップのトラフィックをブロックしているローカル ファイアウォールの設定
- リモート デスクトップ接続を妨げている、ローカルでインストールされたクライアント プロキシのソフトウェア
- リモート デスクトップ接続を妨げている、ローカルでインストールされたネットワーク監視ソフトウェア
- リモート デスクトップ接続を妨げている、トラフィックを監視する、あるいは特定種類のトラフィックを許可または禁止する別の種類のセキュリティ ソフトウェア

いずれの場合も、該当ソフトウェアを一時的に無効にし、リモート デスクトップ経由でオンプレミス コンピューターへの接続を試してください。この方法で実際の原因が判明した場合は、ネットワーク管理者と共に、リモート デスクトップ接続が可能になるようにそのソフトウェアの設定を修正してください。

### ソース 2: 組織のイントラネットのエッジ デバイス

インターネットに直接接続されているコンピューターで、Azure 仮想マシンへのリモート デスクトップ接続を実行できることを確認します。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

インターネットに直接接続されているコンピューターがない場合は、リソース グループまたはクラウド サービス内に新しい Azure 仮想マシンを作成してテストします。詳細については、「[Azure 上で Windows を実行する仮想マシンの作成](virtual-machines-windows-hero-tutorial.md)」を参照してください。仮想マシンとリソース グループまたはクラウド サービスは、テスト後に削除できます。

インターネットに直接接続されているコンピューターとのリモート デスクトップ接続を作成できるのなら、組織のイントラネットのエッジ デバイスで以下を確認してください。

- インターネットへの HTTPS 接続をブロックしている内部ファイアウォール
- リモート デスクトップ接続を妨げているプロキシ サーバー
- リモート デスクトップ接続を妨げている、エッジ ネットワーク内のデバイスで実行されている侵入検出またはネットワーク監視のソフトウェア

ネットワーク管理者とともに、インターネットへの HTTPS ベースのリモート デスクトップ接続が可能になるように、組織のイントラネットのエッジ デバイスの設定を修正してください。

### ソース 3: クラウド サービス エンドポイントと ACL

クラシック デプロイメント モデルを使用して作成された仮想マシンの場合は、同じクラウド サービスまたは仮想ネットワーク内にある別の Azure 仮想マシンが自分の Azure 仮想マシンにリモート デスクトップ接続できることを確認してください。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] リソース マネージャーで作成された仮想マシンについては、「[ソース 4: ネットワーク セキュリティ グループ](#nsgs)」はスキップしてください。

同じクラウド サービスまたは仮想ネットワーク内に別の仮想マシンがない場合は、「[Azure 上で Windows を実行する仮想マシンの作成](virtual-machines-windows-hero-tutorial.md)」の手順を使用して、新しい仮想マシンを作成できます。テストが完了した後は、余分な仮想マシンを削除します。

同じクラウド サービスまたは仮想ネットワーク内の仮想マシンにリモート デスクトップ経由で接続できる場合は、以下を確認してください。

- ターゲット VM でのリモート デスクトップ トラフィック用のエンドポイント構成: エンドポイントのプライベート TCP ポートは、VM のリモート デスクトップ サービスがリッスンしている TCP ポートと一致する必要があります (既定では 3389)。
- ターゲット VM でのリモート デスクトップ トラフィック エンドポイント用の ACL: ACL を使用すると、発信元 IP アドレスに基づいて、インターネットからの受信トラフィックの許可または拒否を指定できます。ACL が正しく構成されていないと、そのエンドポイントへのリモート デスクトップの受信トラフィックを受け取れない場合があります。プロキシまたは他のエッジ サーバーのパブリック IP アドレスからの受信トラフィックが ACL で許可されていることを確認してください。詳細については、「[ネットワーク アクセス制御リスト (ACL) とは](../virtual-network/virtual-networks-acl.md)」をご覧ください。

問題の原因がエンドポイントかどうかを確認するには、現在使用されているエンドポイントを削除してから新しいエンドポイントを作成します。このとき、外部ポート番号の範囲 49152 ～ 65535 からランダムなポート番号を選択します。詳細については、「[仮想マシンに対してエンドポイントを設定する方法](virtual-machines-windows-classic-setup-endpoints.md)」を参照してください。

### <a id="nsgs"></a>ソース 4: ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループでは、許可された受信トラフィックと送信トラフィックをより細かく制御できます。Azure 仮想ネットワーク内のサブネットまたはクラウド サービスの全体に適用されるルールを作成することができます。ネットワークのセキュリティ グループのルールによって、インターネットからのリモート デスクトップ トラフィックが許可されていることを確認してください。

詳細については、「[ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)」を参照してください。

### ソース 5: Windows ベースの Azure 仮想マシン

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)を使用して、失敗の原因が Azure 仮想マシン自体であるかどうかを確認してください。この診断パッケージで **Azure VM への RDP 接続 (再起動が必要)** の問題が解決できなかった場合は、[この記事](virtual-machines-windows-reset-rdp.md)の指示に従って、仮想マシンのリモート デスクトップ サービスをリセットします。リセットすると、以下のようになります。

- 「リモート デスクトップ」 の Windows ファイアウォールの既定ルール (TCP ポート 3389) が有効になる。
- HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections レジストリ値が 0 に設定されるため、リモート デスクトップ接続が有効になる。

もう一度、コンピューターから接続を試みてください。まだリモート デスクトップ経由で接続できない場合は、以下の問題の可能性を調べてください。

- ターゲット VM 上でリモート デスクトップ サービスが実行されていない。
- リモート デスクトップ サービスが TCP ポート 3389 をリッスンしていない。
- Windows ファイアウォールまたは他のローカルのファイアウォールで、リモート デスクトップのトラフィックを妨げている送信ルールが設定されている。
- Azure 仮想マシンで実行されている侵入検出ソフトウェアまたは監視ソフトウェアが、リモート デスクトップ接続を妨げている。

クラシック デプロイメント モデルを使用して作成された VM の場合は、Azure 仮想マシンへのリモート Azure PowerShell セッションを使用できます。最初に、仮想マシンをホストするクラウド サービスの証明書をインストールする必要があります。「[Azure Virtual Machines への安全なリモート PowerShell アクセスを構成する](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)」を参照し、**InstallWinRMCertAzureVM.ps1** スクリプト ファイルを、ローカル コンピューター上にダウンロードします。

次に、まだ Azure PowerShell がインストールされていなければ、インストールします。「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

次に、Azure PowerShell のコマンド プロンプトを開き、現在のフォルダーを、**InstallWinRMCertAzureVM.ps1** スクリプト ファイルのある場所に変更します。Azure PowerShell スクリプトを実行するには、適切な実行ポリシーを設定する必要があります。**Get-executionpolicy** コマンドを実行して、現在のポリシー レベルを決定します。適切なレベルの設定方法の詳細については、「[Set-executionpolicy](https://technet.microsoft.com/library/hh849812.aspx)」 を参照してください。

次に、Azure のサブスクリプション名、クラウド サービス名、および仮想マシン名を入力してから (< and > 文字を削除する)、これらのコマンドを実行します。

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

正しいサブスクリプション名は、**Get-AzureSubscription** コマンドで表示される _SubscriptionName_ プロパティから取得できます。仮想マシンのクラウド サービス名は、**Get-AzureVM** コマンドに表示される _ServiceName_ 列から取得できます。

新しい証明書があることを確認し、現在のユーザー用の証明書スナップインを開き、**[信頼されたルート証明機関] > [証明書]** フォルダーを調べます。クラウド サービスの DNS 名が付けられた証明書が、[発行先] 列に表示されます (例: cloudservice4testing.cloudapp.net)、、

次に以下のコマンドを使用して、リモート Azure PowerShell セッションを開始します。

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

有効な管理者の資格情報を入力すると、Azure PowerShell プロンプトで次のように表示されます。

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

このメッセージの最初の部分は、ターゲット VM を含むクラウド サービス名です。これは、"cloudservice4testing.cloudapp.net" とは異なる場合もあります。これでこのクラウド サービスに対する Azure PowerShell コマンドを発行して、前述したその他の問題を調査して構成を修正することができます。

### リモート デスクトップ サービスでリッスンする TCP ポートの手動修正

**Azure VM との RDP 接続 (再起動が必要)** に関する問題を解決するために [Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)を実行できなかった場合、リモートの Azure PowerShell セッションのプロンプトで、このコマンドを実行します。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

PortNumber プロパティは、現在のポート番号を表示します。必要に応じて、次のコマンドを使用してリモート デスクトップのポート番号を既定値 (3389) に戻してください。

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

次のコマンドを使用して、ポートが 3389 に変更されたことを確認します。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

次のコマンドを使用して、リモートの Azure PowerShell セッションを終了します。

	Exit-PSSession

Azure VM のリモート デスクトップのエンドポイントでも、内部ポートとして TCP ポート 3398 が使用されていることを確認します。Azure VM を再起動し、もう一度リモート デスクトップ接続を試してみてください。


## その他のリソース

[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-rdp.md)

[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)

[Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0420_2016-->