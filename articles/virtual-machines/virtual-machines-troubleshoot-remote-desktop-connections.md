<properties
	pageTitle="Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング"
	description="Windows ベースの Azure 仮想マシンに接続できない場合は、以下の診断と手順を使用して問題の原因を特定してください。"
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
	ms.date="07/07/2015"
	ms.author="dkshir"/>

# Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング

この記事では、Windows ベースの Azure 仮想マシンにリモート デスクトップ接続できない場合に、その原因を特定し、問題を解決するための系統的アプローチについて説明します。

## 手順 1: Azure IaaS 診断パッケージを実行します。

リモート デスクトップ接続の作成時によく発生する多くの問題を解決できるように、Microsoft は [Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)を作成しました。

1.	このページで [**Microsoft Azure IaaS (Windows) 診断パッケージ**] をクリックして、新しい診断セッションを作成します。
2.	[**Azure VM で発生している問題**] ページで、[**Azure VM との RDP 接続 (再起動が必要)**] の問題を選択します。

詳細については、[Microsoft Azure IaaS (Windows) 診断パッケージについてのナレッジベース](http://support.microsoft.com/kb/2976864)の記事を参照してください。

Azure IaaS 診断パッケージを実行しても問題が解決されない場合、または診断パッケージを実行できない場合は、これ以降の手順で説明する、より詳細なトラブルシューティングが必要になる場合があります。

> [AZURE.NOTE]Azure IaaS (Windows) 診断パッケージは、Windows 8、Windows 8.1、Windows Server 2012、または Windows Server 2012 R2 を実行するコンピューター上で実行する必要があります。

## 手順 2: リモート デスクトップ クライアントからのエラー メッセージを確認します。

接続を試行した際に表示されたエラー メッセージに応じて、以下のセクションを参照してください。

### リモート デスクトップ接続のメッセージ ウィンドウ: ライセンスを提供するリモート デスクトップ ライセンス サーバーがないため、リモート セッションが切断されました。

原因: リモート デスクトップ サーバー ロールの 120 日間のライセンス有効期限が切れているため、ライセンスをインストールする必要がある。

一時的な回避策として、Azure ポータルにある RDP ファイルのローカル コピーを保存し、Windows PowerShell コマンド プロンプトで次のコマンドを実行して接続します。

	mstsc <File name>.RDP /admin

これによって、この接続のみに対するライセンスが無効になります。

仮想マシンに対して 2 つ以上のリモート デスクトップ接続が同時に必要でない場合、サーバー マネージャーを使用して、リモート デスクトップ サーバーのロールを削除することができます。

[使用できるリモート デスクトップ ライセンス サーバーがないことを示すエラー メッセージが表示され、Azure VM に問題が発生する](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx)件に関するブログ投稿も参照してください。

### リモート デスクトップ接続のメッセージ ウィンドウ: リモート デスクトップは、コンピューター "name" を見つけることができません。

原因: コンピューター上のリモート デスクトップ クライアントが、RDP ファイルで設定されたコンピューターの名前を解決できない

Azure ポータルによって生成された RDP ファイルの例を次に示します。

	full address:s:tailspin-azdatatier.cloudapp.net:55919
	prompt for credentials:i:1

この例では、アドレス部分は仮想マシンを含むクラウド サービスの完全修飾ドメイン名 (この例では tailspin azdatatier.cloudapp.net) と、リモート デスクトップのトラフィック向けエンドポイントの外部 TCP ポート (55919) で構成されています。

考えられるこの問題の解決策

- 組織のイントラネットをご利用の場合は、コンピューターからプロキシ サーバーにアクセスでき、そのサーバーに HTTPS トラフィックを送信できることを確認してください。
- ローカルに保存された RDP ファイルを使用している場合は、Azure ポータルによって生成された RDP ファイル使用して、仮想マシンやクラウド サービスの DNS 名と、仮想マシンのエンドポイント ポートが正しいことを確認してください。

### リモート デスクトップ接続のメッセージ ウィンドウ: 認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。

原因: 接続先の仮想マシンが、資格情報のユーザー名部分に示されているセキュリティ機関を特定できない。

ユーザー名が *SecurityAuthority**UserName* という形式 (例: CORP\\User1) の場合、*SecurityAuthority* の部分には、仮想マシンのコンピューター名 (ローカル セキュリティ機関) か、Active Directory ドメイン名を指定します。

考えられるこの問題の解決策

- ユーザー アカウントが仮想マシンに対してローカルである場合は、仮想マシン名のスペルを確認してください。
- ユーザー アカウントが Active Directory ドメイン アカウントである場合は、ドメイン名のスペルを確認してください。
- ユーザー アカウントが Active Directory ドメイン アカウントで、ドメイン名のスペルが正しい場合は、Active Directory ドメインのドメイン コント ローラーが利用可能であることを確認してください。この問題は、Azure 仮想ネットワークにドメイン コント ローラーを含まれていて、ドメイン コント ローラー コンピューターが起動されていない場合によく発生します。一時的な回避策として、ドメイン アカウントの代わりにローカルの管理者アカウントを使用する方法があります。

### Windows セキュリティ メッセージ ウィンドウ: 資格情報が正しくありません。

原因: 送信したアカウント名とパスワードが、接続先の仮想マシンで認証されない。

Windows ベースのコンピューターでは、ローカル アカウントまたはドメイン ベース アカウントのいずれかの資格情報が認証されます。

- ローカル アカウントの場合は、 *ComputerName**UserName* という構文を使用します (例: SQL1\\Admin4798)。
- ドメイン アカウントの場合は、 *DomainName**UserName* という構文を使用します (例: CONTOSO\\johndoe)。

新しい AD フォレスト内でドメイン コント ローラーに昇格するコンピューターの場合、昇格実行時のログインに使用するローカル管理者アカウントは、新しいフォレストとドメインで、同じパスワードを含む同等のアカウントに変換されます。前のローカル管理者アカウントは削除されます。たとえば、ローカル管理者アカウント DC1\\DCAdmin でログインして、新しいフォレストで仮想マシンを corp.contoso.com ドメインのドメイン コント ローラーとして昇格すると、DC1\\DCAdmin のローカル アカウントは削除され、新しいドメイン アカウント (CORP\\DCAdmin) が同じパスワードで作成されます。

アカウント名が、仮想マシンで有効アカウントとして認証される名前であることを再確認してください。パスワードが正しいことを再確認してください。

ローカル管理者アカウントのパスワードを変更する必要がある場合は、[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-password.md)をご覧ください。

### リモート デスクトップ接続のメッセージ ウィンドウ: このコンピューターはリモート コンピューターに接続できません。

原因: 接続に使用しているアカウントに、リモート デスクトップのログオン権限がない。

各 Windows ベースのコンピューターにはリモート デスクトップ ユーザーのローカル グループがあり、このグループには、リモート デスクトップ接続にログオンする権利を持つアカウントおよびグループが含まれます。ローカルの Administrators グループのメンバーも、アカウントはリモート デスクトップ ユーザーのローカル グループのメンバーとしてリストされていませんが、アクセスできます。ドメインに参加しているマシンの場合、ローカルの Administrators グループにはドメインのドメイン管理者も含まれます。

接続を開始するために使用しているアカウントに、リモート デスクトップへのログオン権限があることを確認してください。一時的な回避策として、ドメイン管理者アカウントまたはローカル管理者アカウントを使用してリモート デスクトップ接続を作成し、コンピューターの管理スナップインを使用して、必要なアカウントをリモート デスクトップ ユーザーのローカル グループに追加します (**[システム ツール] > [ローカル ユーザーとグループ] > [グループ] > [リモート デスクトップ ユーザー]**)。

### リモート デスクトップ接続のメッセージ ウィンドウ: 以下のいずれかの理由によって、リモート デスクトップがリモート コンピューターに接続できません。

原因: リモート デスクトップ クライアントが、仮想マシンのリモート デスクトップ サービスのサービスに接続できない。このような問題の場合、多くの根本原因が考えられます。

関連のある一連のコンポーネントを次に示します。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_0.png)

段階的なトラブルシューティング プロセスを開始する前に、正常にリモート デスクトップ接続を作成できた当初から何が変更されたのかを振り返り、その変更を、問題解決のための出発点にすることをお勧めします。次に例を示します。

- リモート デスクトップ接続を作成できていたときに、ご利用の仮想マシンを含む仮想マシンまたはクラウド サービスのパブリック IP アドレス (仮想 IP アドレス (VIP) とも呼ばれる) を変更したのであれば、DNS クライアント キャッシュに、DNS 名のエントリおよび*古い IP アドレス*が含まれている可能性があります。DNS クライアント キャッシュをフラッシュして、もう一度やり直してくださいまたは、新しい VIP を使用して接続を試してみてください。
- Azure ポータルまたはAzure プレビュー ポータルの使用から、リモート デスクトップ接続の管理用アプリケーションの使用に切り替えたのであれば、ランダムに決定されるリモート デスクトップのトラフィック向け TCP ポートがアプリケーションの構成に含まれていることを確認します。

次のセクションでは、この問題のさまざまな根本的原因を特定して判断する手順を説明しながら、解決策と回避策を紹介します。

## 手順 3: 詳細なトラブルシューティングする前の準備作業をします。

次の手順を実行します。

- Azure ポータルまたは Azure プレビュー ポータル内の仮想マシンの状態を確認します。
- [仮想マシンを再起動します。](https://msdn.microsoft.com/library/azure/dn763934.aspx)
- [仮想マシンのサイズを変更します。](https://msdn.microsoft.com/library/dn168976.aspx)

これらの手順を完了したら、もう一度リモート デスクトップ接続を試してみてください。

## 手順 4: 詳細なトラブルシューティングを行います。

リモート デスクトップ クライアントから Azure 仮想マシンのリモート デスクトップ サービスのサービスに接続できないのは、以下のソースに問題あるか、あるいはその構成が誤っていることが原因である可能性があります。

- リモート デスクトップ クライアント コンピューター
- 組織のイントラネットのエッジ デバイス
- クラウド サービス エンドポイントとアクセス制御リスト (ACL)
- ネットワーク セキュリティ グループ
- Windows ベースのAzure 仮想マシン

### ソース 1: リモート デスクトップ クライアント コンピューター

問題のソースとなっている、または構成が正しくないという理由でコンピューターを排除する場合、そのコンピューターから他のオンプレミスの Windows ベース コンピューターにリモート デスクトップ接続できることを確認します。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_1.png)

接続できない場合は、そのコンピューターで以下を確認してください。

- リモート デスクトップのトラフィックをブロックしているローカル ファイアウォールの設定
- リモート デスクトップ接続を妨げている、ローカルでインストールされたクライアント プロキシのソフトウェア
- リモート デスクトップ接続を妨げている、ローカルでインストールされたネットワーク監視ソフトウェア
- リモート デスクトップ接続を妨げている、トラフィックを監視する、あるいは特定種類のトラフィックを許可または禁止する別の種類のセキュリティ ソフトウェア

いずれの場合も、該当ソフトウェアを一時的に無効にし、オンプレミス コンピュータへのリモート デスクトップ接続を試すことで、根本的な原因を判断してください。次に、ネットワーク管理者とともに、リモート デスクトップ接続が可能になるようにそのソフトウェアの設定を修正してください。

### ソース 2: 組織のイントラネットのエッジ デバイス

問題のソースとなっている、または構成が正しくないという理由で組織のイントラネットのエッジ デバイスを排除する場合、インターネットに直接接続されているコンピューターから Azure 仮想マシンにリモート デスクトップ接続できるかを確認します。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_2.png)

インターネットに直接接続されているコンピューターがない場合は、新しい Azure 仮想マシンをリソース グループまたはクラウド サービス内に簡単に作成して使用できます。詳細については、「[Azure 上で Windows を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md)」を参照してください。テストの完了後に、そのリソース グループまたは仮想マシンと、クラウド サービスを削除します。

インターネットに直接接続されているコンピューターとのリモート デスクトップ接続を作成できるのなら、組織のイントラネットのエッジ デバイスで以下を確認してください。

- インターネットへの HTTPS 接続をブロックしている内部ファイアウォール
- リモート デスクトップ接続を妨げているプロキシ サーバー
- リモート デスクトップ接続を妨げている、エッジ ネットワーク内のデバイスで実行されている侵入検出またはネットワーク監視のソフトウェア

ネットワーク管理者とともに、インターネットへの HTTPS ベースのリモート デスクトップ接続が可能になるように、組織のイントラネットのエッジ デバイスの設定を修正してください。

### ソース 3: クラウド サービス エンドポイントと ACL

サービス管理 API を使用して作成された仮想マシンで問題や構成ミスの原因になっているクラウド サービス エンドポイントと ACL を排除する場合、同じクラウド サービスまたは仮想ネットワーク内にある別の Azure 仮想マシンから自身の Azure 仮想マシンにリモート デスクトップ接続できることを確認します。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_3.png)

> [AZURE.NOTE]リソース マネージャーで作成された仮想マシンについては、「[ソース 4: ネットワーク セキュリティ グループ](#nsgs)」はスキップしてください。

同じクラウド サービスまたは仮想ネットワーク内に別の仮想マシンがない場合、新しい仮想マシンを簡単に作成することができます。詳細については、[Azure 上で Windows を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md)を参照してください。テストの完了後に、追加した仮想マシンを削除してください。

同じクラウド サービスまたは仮想ネットワーク内の仮想マシンへのリモート デスクトップ接続を作成できる場合は、以下を確認してください。

- ターゲットの仮想マシンでの、リモート デスクトップのトラフィック向けエンドポイントの構成。エンドポイントのプライベート TCP ポートは、仮想マシン上のリモート デスクトップ サービスのサービスがリッスンする TCP ポートと一致する必要があります。この TCP ポートの既定設定は 3389 です。
- ターゲットの仮想マシンでの、リモート デスクトップのトラフィック向けエンドポイントの ACL。ACL を使用すると、発信元 IP アドレスに基づいて、インターネットからの受信トラフィックを許可または拒否するかを指定できます。ACL が正しく構成されていないと、そのエンドポイントへのリモート デスクトップの受信トラフィックを受け取れない場合があります。ご利用になっているプロキシのパブリック IP アドレスからの受信トラフィック、または他のエッジ サーバーからの受信トラフィックが許可されているかを ACL で確認してください。詳細については、[ネットワーク アクセス制御リスト (ACL) とは](../virtual-network/virtual-networks-acl.md)をご覧ください。

問題の原因としてをエンドポイントを排除する場合、現在使用されているエンドポイントを削除してから新しいエンドポイントを作成します。このとき、外部ポート番号の範囲 49152 ～ 65535 からランダムなポート番号を選択します。詳細については、[仮想マシンに対してエンドポイントを設定する方法](virtual-machines-set-up-endpoints.md)を参照してください。

### <a id="nsgs"></a>ソース 4: ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループでは、許可された受信トラフィックと送信トラフィックをより細かく制御できます。Azure 仮想ネットワーク内のサブネットまたはクラウド サービスの全体に適用されるルールを作成することができます。ネットワークのセキュリティ グループのルールによって、インターネットからのリモート デスクトップ トラフィックが許可されていることを確認してください。

詳細については、[ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)を参照してください。

### ソース 5: Windows ベースの Azure 仮想マシン

最後に考えられる問題は、Azure 仮想マシン自体に関連するものです。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_5.png)

まず、**Azure VM との RDP 接続 (再起動が必要)** に関する問題を解決するために [Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)を実行できなかった場合、 [Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-password.md)の指示に従って、仮想マシンのリモート デスクトップ サービスのサービスをリセットします。リセットすると、以下のようになります。

- 「リモート デスクトップ」 の Windows ファイアウォールの既定ルール (TCP ポート 3389) が有効になる。
- HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections レジストリ値が 0 に設定されるため、リモート デスクトップ接続が有効になる。

もう一度、コンピューターから接続を試みてください。接続できなければ、以下の問題が考えらえます。

- ターゲット仮想マシン上で、リモート デスクトップ サービスのサービスが実行されていない。
- リモート デスクトップ サービスのサービスが、TCP ポート 3389 でリッスンしていない。
- Windows ファイアウォールまたは他のローカルのファイアウォールで、リモート デスクトップのトラフィックを妨げている送信ルールが設定されている。
- Azure 仮想マシンで実行されている侵入検出ソフトウェアまたは監視ソフトウェアが、リモート デスクトップ接続を妨げている。

サービス管理 API を使用して作成された仮想マシンについて考えられるこれらの問題を修正するには、リモート Azure PowerShell セッションを Azure 仮想マシンに使用します。最初に、仮想マシンをホストするクラウド サービスの証明書をインストールする必要があります。[Azure 仮想マシンへの安全なリモート PowerShell アクセスを構成する](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)を参照し、**InstallWinRMCertAzureVM.ps1** スクリプト ファイルを、ローカル コンピューター上のフォルダーにダウンロードします。

次に、まだ Azure PowerShell がインストールされていなければ、インストールします。[Azure PowerShell のインストールと構成の方法](../install-configure-powershell.md)を参照してください。

次に、Azure PowerShell のコマンド プロンプトを開き、現在のフォルダーを、**InstallWinRMCertAzureVM.ps1** スクリプト ファイルのある場所に変更します。Azure PowerShell スクリプトを実行するには、適切な実行ポリシーを設定する必要があります。**Get-executionpolicy** コマンドを実行して、現在のポリシー レベルを決定します。適切なレベルの設定方法の詳細については、[Set-executionpolicy](https://technet.microsoft.com/library/hh849812.aspx)を参照してください。

次に、Azure のサブスクリプション名、クラウド サービス名、および仮想マシン名を入力してから (< and > 文字を削除する)、これらのコマンドを実行します。

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

正しいサブスクリプション名は、**Get-AzureSubscription** コマンドで表示される SubscriptionName プロパティから取得できます。仮想マシンのクラウド サービス名は、**Get-azurevm** コマンドに表示される ServiceName 列から取得できます 。

この新しい証明書があることを確かめるには、[現在のユーザー] 用の証明書スナップインを開き、 **[信頼されたルート証明機関] > [証明書]** フォルダーを開きます。クラウド サービスの DNS 名が付けられた証明書が、[発行先] 列に表示されます (例: cloudservice4testing.cloudapp.net)、、

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

## 手順 5: Azure サポート フォーラムに問題を送信します。

世界中の Azure 専門家に質問するには、MSDN Azure フォーラムまたは Stack Overflow Azure フォーラムのいずれかに問題を送信してください。詳細については、[Microsoft Azure フォーラム](http://azure.microsoft.com/support/forums/)を参照してください。

## 手順 6: Azure サポート インシデントを送信します。

**Azure VM との RDP 接続 (再起動が必要)** に関する問題を解決するために [Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)やこの記事の手順 2～5 を実行し、その問題を Azure サポート フォーラムに送信したにもかかわらず、 リモート デスクトップ接続を作成できない場合、ユーザーができることは、仮想マシンを再作成できるかどうかを確認することです。

仮想マシンを再作成できなければ、Azureサポート インシデントを送信することをお勧めします。

インシデントを送信するには、[Azure サポートのサイト](http://azure.microsoft.com/support/options/)で [**サポートの要求**] をクリックします。

Azure サポートの使用方法の詳細については、[Microsoft Azure サポートに関する FAQ](http://azure.microsoft.com/support/faq/)を参照してください。

## その他のリソース

[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-password.md)

[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)

[Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](virtual-machines-troubleshoot-ssh-connections.md)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=August15_HO9-->