<properties
	pageTitle="リモート デスクトップの詳細なトラブルシューティング |Microsoft Azure"
	description="Windows を実行する Azure 仮想マシンへの RDP 接続に関する詳細なトラブルシューティングの手順について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関する詳細なトラブルシューティング

この記事では、リモート デスクトップの複雑なエラーの詳細なトラブルシューティング手順を示します。

> [AZURE.IMPORTANT]リモート デスクトップのより一般的なエラーを解消する場合は、先に進む前に、必ず[リモート デスクトップの基本的なトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)をお読みください。

## Azure カスタマー サポートへの問い合わせ

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](http://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。

または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](http://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。Azure サポートの使用方法の詳細については、「[Microsoft Azure サポートに関する FAQ](http://azure.microsoft.com/support/faq/)」を参照してください。


## 一般的なリモート デスクトップのエラー メッセージ

リモート デスクトップ接続のメッセージ ウィンドウに「_以下のいずれかの理由によって、リモート デスクトップがリモート コンピューターに接続できません_」というエラー メッセージが表示される場合があります。

このエラーは、リモート デスクトップ クライアントが、仮想マシンのリモート デスクトップ サービスに接続できない場合に発生します。このエラーにはさまざまな理由が考えられます。

関連のある一連のコンポーネントを次に示します。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_0.png)

段階的なトラブルシューティング プロセスを開始する前に、正常にリモート デスクトップ接続を作成できた当初から何が変更されたのかを振り返り、その変更を、問題解決のための出発点にすることをお勧めします。次に例を示します。

- リモート デスクトップ接続を作成できていたときに、ご利用の仮想マシンを含む仮想マシンまたはクラウド サービスのパブリック IP アドレス (仮想 IP アドレス (VIP) とも呼ばれる) を変更したのであれば、DNS クライアント キャッシュに、DNS 名のエントリおよび*古い IP アドレス*が含まれている可能性があります。DNS クライアント キャッシュをフラッシュして、もう一度やり直してくださいまたは、新しい VIP を使用して接続を試してみてください。
- Azure ポータルまたはAzure プレビュー ポータルの使用から、リモート デスクトップ接続の管理用アプリケーションの使用に切り替えたのであれば、ランダムに決定されるリモート デスクトップのトラフィック向け TCP ポートがアプリケーションの構成に含まれていることを確認します。

次のセクションでは、この問題のさまざまな根本的原因を特定して判断する手順を説明しながら、解決策と回避策を紹介します。


## 準備作業

詳細なトラブルシューティングに進む前に、以下の手順を実行します。

- Azure ポータルまたは Azure プレビュー ポータル内の仮想マシンの状態を確認します。
- 仮想マシンを再起動します。
- [仮想マシンのサイズを変更します。](virtual-machines-size-specs.md)

これらの手順を完了したら、もう一度リモート デスクトップ接続を試してみてください。


## 詳細なトラブルシューティング

リモート デスクトップ クライアントから Azure 仮想マシンのリモート デスクトップ サービスに接続できないのは、以下のソースに問題があるか、あるいはその構成が誤っていることが原因である可能性があります。

- リモート デスクトップ クライアント コンピューター
- 組織のイントラネットのエッジ デバイス
- クラウド サービス エンドポイントとアクセス制御リスト (ACL)
- ネットワーク セキュリティ グループ
- Windows ベースのAzure 仮想マシン

### ソース 1: リモート デスクトップ クライアント コンピューター

問題のソースとなっている、または構成が正しくないという理由でコンピューターを排除する場合、そのコンピューターから他のオンプレミスの Windows ベース コンピューターにリモート デスクトップ接続できることを確認します。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_1.png)

接続できない場合は、そのコンピューターで以下を確認してください。

- リモート デスクトップのトラフィックをブロックしているローカル ファイアウォールの設定
- リモート デスクトップ接続を妨げている、ローカルでインストールされたクライアント プロキシのソフトウェア
- リモート デスクトップ接続を妨げている、ローカルでインストールされたネットワーク監視ソフトウェア
- リモート デスクトップ接続を妨げている、トラフィックを監視する、あるいは特定種類のトラフィックを許可または禁止する別の種類のセキュリティ ソフトウェア

いずれの場合も、該当ソフトウェアを一時的に無効にし、オンプレミス コンピューターへのリモート デスクトップ接続を試すことで、根本的な原因を解明してください。次に、ネットワーク管理者とともに、リモート デスクトップ接続が可能になるようにそのソフトウェアの設定を修正してください。

### ソース 2: 組織のイントラネットのエッジ デバイス

問題のソースとなっている、または構成が正しくないという理由で組織のイントラネットのエッジ デバイスを排除する場合、インターネットに直接接続されているコンピューターから Azure 仮想マシンにリモート デスクトップ接続できるかを確認します。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_2.png)

インターネットに直接接続されているコンピューターがない場合は、新しい Azure 仮想マシンを独自のリソース グループまたはクラウド サービス内に簡単に作成して使用できます。詳細については、「[Azure 上で Windows を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md)」を参照してください。テストの完了後に、そのリソース グループまたは仮想マシンと、クラウド サービスを削除します。

インターネットに直接接続されているコンピューターとのリモート デスクトップ接続を作成できるのなら、組織のイントラネットのエッジ デバイスで以下を確認してください。

- インターネットへの HTTPS 接続をブロックしている内部ファイアウォール
- リモート デスクトップ接続を妨げているプロキシ サーバー
- リモート デスクトップ接続を妨げている、エッジ ネットワーク内のデバイスで実行されている侵入検出またはネットワーク監視のソフトウェア

ネットワーク管理者とともに、インターネットへの HTTPS ベースのリモート デスクトップ接続が可能になるように、組織のイントラネットのエッジ デバイスの設定を修正してください。

### ソース 3: クラウド サービス エンドポイントと ACL

サービス管理 API を使用して作成された仮想マシンで問題や構成ミスの原因になっているクラウド サービス エンドポイントと ACL を排除する場合、同じクラウド サービスまたは仮想ネットワーク内にある別の Azure 仮想マシンから自身の Azure 仮想マシンにリモート デスクトップ接続できることを確認します。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_3.png)

> [AZURE.NOTE]リソース マネージャーで作成された仮想マシンについては、「[ソース 4: ネットワーク セキュリティ グループ](#nsgs)」はスキップしてください。

同じクラウド サービスまたは仮想ネットワーク内に別の仮想マシンがない場合、新しい仮想マシンを簡単に作成することができます。詳細については、「[Azure 上で Windows を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md)」を参照してください。テストの完了後に、追加した仮想マシンを削除してください。

同じクラウド サービスまたは仮想ネットワーク内の仮想マシンへのリモート デスクトップ接続を作成できる場合は、以下を確認してください。

- ターゲットの仮想マシンでの、リモート デスクトップのトラフィック向けエンドポイントの構成。エンドポイントのプライベート TCP ポートは、仮想マシン上のリモート デスクトップ サービスのサービスがリッスンする TCP ポートと一致する必要があります。この TCP ポートの既定設定は 3389 です。
- ターゲットの仮想マシンでの、リモート デスクトップのトラフィック向けエンドポイントの ACL。ACL を使用すると、発信元 IP アドレスに基づいて、インターネットからの受信トラフィックを許可または拒否するかを指定できます。ACL が正しく構成されていないと、そのエンドポイントへのリモート デスクトップの受信トラフィックを受け取れない場合があります。ご利用になっているプロキシのパブリック IP アドレスからの受信トラフィック、または他のエッジ サーバーからの受信トラフィックが許可されているかを ACL で確認してください。詳細については、「[ネットワーク アクセス制御リスト (ACL) とは](../virtual-network/virtual-networks-acl.md)」をご覧ください。

問題の原因としてをエンドポイントを排除する場合、現在使用されているエンドポイントを削除してから新しいエンドポイントを作成します。このとき、外部ポート番号の範囲 49152 ～ 65535 からランダムなポート番号を選択します。詳細については、「[仮想マシンに対してエンドポイントを設定する方法](virtual-machines-set-up-endpoints.md)」を参照してください。

### <a id="nsgs"></a>ソース 4: ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループでは、許可された受信トラフィックと送信トラフィックをより細かく制御できます。Azure 仮想ネットワーク内のサブネットまたはクラウド サービスの全体に適用されるルールを作成することができます。ネットワークのセキュリティ グループのルールによって、インターネットからのリモート デスクトップ トラフィックが許可されていることを確認してください。

詳細については、「[ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)」を参照してください。

### ソース 5: Windows ベースの Azure 仮想マシン

最後に考えられる問題は、Azure 仮想マシン自体に関連するものです。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_5.png)

[リモート デスクトップの基本的なトラブルシューティングに関する記事](virtual-machines-troubleshoot-remote-desktop-connections.md)では、[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)の使用方法について説明されています。この診断パッケージで **Azure VM との RDP 接続 (再起動が必要)** に関する問題を解決できなかった場合は、[この記事](virtual-machines-windows-reset-password.md)の指示に従って、仮想マシンのリモート デスクトップ サービスのサービスをリセットします。リセットすると、以下のようになります。

- 「リモート デスクトップ」 の Windows ファイアウォールの既定ルール (TCP ポート 3389) が有効になる。
- HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections レジストリ値が 0 に設定されるため、リモート デスクトップ接続が有効になる。

もう一度、コンピューターから接続を試みてください。接続できなければ、以下の問題が考えらえます。

- ターゲット仮想マシン上で、リモート デスクトップ サービスのサービスが実行されていない。
- リモート デスクトップ サービスのサービスが、TCP ポート 3389 でリッスンしていない。
- Windows ファイアウォールまたは他のローカルのファイアウォールで、リモート デスクトップのトラフィックを妨げている送信ルールが設定されている。
- Azure 仮想マシンで実行されている侵入検出ソフトウェアまたは監視ソフトウェアが、リモート デスクトップ接続を妨げている。

サービス管理 API を使用して作成された仮想マシンについて考えられるこれらの問題を修正するには、リモート Azure PowerShell セッションを Azure 仮想マシンに使用します。最初に、仮想マシンをホストするクラウド サービスの証明書をインストールする必要があります。「[Azure 仮想マシンへの安全なリモート PowerShell アクセスを構成する](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)」を参照し、**InstallWinRMCertAzureVM.ps1** スクリプト ファイルを、ローカル コンピューター上のフォルダーにダウンロードします。

次に、まだ Azure PowerShell がインストールされていなければ、インストールします。「[Azure PowerShell のインストールと構成の方法](../install-configure-powershell.md)」を参照してください。

次に、Azure PowerShell のコマンド プロンプトを開き、現在のフォルダーを、**InstallWinRMCertAzureVM.ps1** スクリプト ファイルのある場所に変更します。Azure PowerShell スクリプトを実行するには、適切な実行ポリシーを設定する必要があります。**Get-executionpolicy** コマンドを実行して、現在のポリシー レベルを決定します。適切なレベルの設定方法の詳細については、「[Set-executionpolicy](https://technet.microsoft.com/library/hh849812.aspx)」 を参照してください。

次に、Azure のサブスクリプション名、クラウド サービス名、および仮想マシン名を入力してから (< and > 文字を削除する)、これらのコマンドを実行します。

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

正しいサブスクリプション名は、**Get-AzureSubscription** コマンドで表示される _SubscriptionName_ プロパティから取得できます。仮想マシンのクラウド サービス名は、**Get-AzureVM** コマンドに表示される _ServiceName_ 列から取得できます。

この新しい証明書があることを確かめるには、現在のユーザー用の証明書スナップインを開き、**[信頼されたルート証明機関] > [証明書]** フォルダーを調べます。クラウド サービスの DNS 名が付けられた証明書が、[発行先] 列に表示されます (例: cloudservice4testing.cloudapp.net)、、

次に以下のコマンドを使用して、リモート Azure PowerShell セッションを開始します。

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

有効な管理者の資格情報を入力すると、Azure PowerShell プロンプトで次のように表示されます。

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

プロンプトの最初の部分は、ターゲットの仮想マシンを含むクラウド サービスに Azure PowerShell コマンドを発行していることを示しています。cloudservice4testing.cloudapp.net はクラウド サービス名ではありません。

これで Azure PowerShell コマンドを発行して、前述したその他の問題を調査して構成を修正することができます。

### リモート デスクトップ サービスでリッスンする TCP ポートの手動修正

**Azure VM との RDP 接続 (再起動が必要)** に関する問題を解決するために [Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)を実行できなかった場合、リモートの Azure PowerShell セッションのプロンプトで、このコマンドを実行します。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

PortNumber プロパティは、現在のポート番号を表示します。必要に応じて、次のコマンドを使用してリモート デスクトップのポート番号を既定値 (3389) に戻してください。

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

次のコマンドを使用して、ポートが 3389 に変更されたことを確認します。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

次のコマンドを使用して、リモートの Azure PowerShell セッションを終了します。

	Exit-PSSession

Azure 仮想マシンのリモート デスクトップのエンドポイントでも、内部ポートとして TCP ポート 3398 が使用されていることを確認します。Azure 仮想マシンを再起動し、もう一度リモート デスクトップ接続を試してください。


## その他のリソース

[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-password.md)

[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)

[Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](virtual-machines-troubleshoot-ssh-connections.md)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Sept15_HO4-->