<properties
	pageTitle="Azure VM へのリモート デスクトップ接続のトラブルシューティング |Microsoft Azure"
	description="Windows VM に対するリモート デスクトップ接続のエラーをトラブルシューティングします。簡単な軽減手順、エラー メッセージごとのヘルプ、および詳細なネットワーク トラブルシューティングを説明します。"
	keywords="リモート デスクトップ エラー,リモート デスクトップ接続エラー,VM に接続できない,リモート デスクトップのトラブルシューティング"
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
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Windows を実行する Azure 仮想マシンへの Remote Desktop 接続に関するトラブルシューティング

Windows ベースの Azure 仮想マシンに対するリモート デスクトップ プロトコル (RDP) 接続は、さまざまな理由で失敗する可能性があります。VM 上のリモート デスクトップ サービス、ネットワーク接続、またはホスト コンピューター上のリモート デスクトップ クライアントに問題がある可能性があります。この記事は、エラーの理由を発見してエラーを修正するために役立ちます。


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

この記事は、Windows を実行している Azure 仮想マシンに適用されます。Linux を実行している Azure 仮想マシンでは、「[Linux ベースの Azure 仮想マシンに対する Secure Shell 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md)」を参照してください。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。

<a id="quickfixrdp"></a>

## リモート デスクトップの一般的なエラーを修正する

このセクションでは、一般的なリモート デスクトップ接続の問題をすばやく修正するための手順を示します。

### クラシック デプロイ モデルを使用して作成した仮想マシンのトラブルシューティング

次の手順は、クラシック デプロイ モデルを使用して作成された Azure 仮想マシンのリモート デスクトップ接続に関する一般的なエラーの大半を解決するために役立つ可能性があります。各手順を実行した後、VM に再接続してみてください。

- RDP サーバーでの起動の問題を解決するには、[Azure ポータル](https://portal.azure.com)からリモート デスクトップ サービスをリセットします。**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Windows 仮想マシン*、**[リモートのリセット]** の順に選択します。

- その他の起動の問題に対処するには仮想マシンを再起動します。**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Windows 仮想マシン*、**[再起動]** の順に選択します。

- 仮想マシンを新しい Azure ノードに再デプロイします。「[新しい Azure ノードへの仮想マシンの再デプロイ](virtual-machines-windows-redeploy-to-new-node.md)」を参照してください。

	この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されることに注意してください。

- 起動の問題を修正するには、VM のコンソール ログまたはスクリーンショットを確認してください。**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Windows 仮想マシン*、**[設定]**、**[起動の診断]** の順に選択します。


- プラットフォームの問題の有無について VM のリソースの状態を確認します。**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Windows 仮想マシン*、**[設定]**、**[正常性の確認]** の順に選択します。


### Resource Manager デプロイ モデルを使用して作成された仮想マシンのトラブルシューティング

次の手順は、Resource Manager デプロイ モデルを使用して作成された Azure 仮想マシンのリモート デスクトップ接続に関する一般的なエラーの大半を解決するために役立つ可能性があります。各手順を実行した後、VM に再接続してみてください。

> [AZURE.TIP] ポータルの [接続] ボタンが灰色表示され、[Express Route](../expressroute/expressroute-introduction.md) や[サイト間 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 接続で Azure に接続されない場合は、VM を作成してパブリック IP アドレスを割り当ててから RDP を使用する必要があります。[Azure におけるパブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)に関するページをご覧ください。

- Powershell を使用してリモート アクセスをリセットします。
	- まだインストールしていない場合は、[PowerShell をインストール](../powershell-install-configure.md)し、Azure Active Directory メソッドを使用して Azure サブスクリプションに接続します。PowerShell のバージョン 1.0.x では Resource Manager モードに切り替える必要はありません。

	- 次の PowerShell コマンドのいずれかを使用して、RDP 接続をリセットします。`myRG`、`myVM`、`myVMAccessExtension`、および場所を、自分のセットアップに関連する値に置き換えます。

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	または

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] これが RDP 接続の初めてのリセットの場合、VMAccessAgent はおそらく存在していません。上記の例で、`myVMAccessExtension` または `MyVMAccess` は、プロセスの一部としてインストールされる新しい拡張機能に対して指定する名前です。多くの場合、これは、VM の名前を設定するだけです。VMAccessAgent を前に操作したことがある場合は、`Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"` を使用して VM のプロパティをチェックすることで、既存の名前を取得できます。出力の 'Extensions' セクションを調べます。１ 台の VM には VMAccessAgent が 1 つだけ存在できるため、`Set-AzureRmVMExtension.` を使用するときに `-ForceReRun` パラメーターも追加する必要があります。これにより、エージェントが強制的に再登録されます。

- その他の起動の問題に対処するには仮想マシンを再起動します。**[参照]**、**[仮想マシン]**、*ご使用の Windows 仮想マシン*、**[再起動]** の順に選択します。

- ホストの問題を解決するには、VM のサイズを変更します。**[すべて参照]**、**[仮想マシン]**、*ご使用の Windows 仮想マシン*、**[設定]**、**[サイズ]** の順に選択します。

- 起動の問題を修正するには、VM のコンソール ログまたはスクリーンショットを確認します。**[参照]**、**[仮想マシン]**、*ご使用の Windows 仮想マシン*、**[設定]**、**[起動の診断]** の順に選択します。


リモート デスクトップ接続エラーが上記の手順で解決しなかった場合は、次のセクションに進んでください。

## 特定のリモート デスクトップ接続エラーのトラブルシューティング

リモート デスクトップを使用してAzure 仮想マシンに接続しようとしたときに発生する可能性がある一般的なエラーは次のとおりです。

- [Remote Desktop 接続エラー: ライセンスを提供する Remote Desktop ライセンス サーバーがないため、リモート セッションが切断されました](#rdplicense)。

- [Remote Desktop 接続エラー: Remote Desktop は、コンピューター "name" を見つけることができません](#rdpname)。

- [Remote Desktop 接続エラー: 認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。](#rdpauth)

- [Windows セキュリティ エラー: 資格情報が正しくありません](#wincred)。

- [Remote Desktop 接続エラー: このコンピューターはリモート コンピューターに接続できません](#rdpconnect)。

<a id="rdplicense"></a>
### Remote Desktop 接続エラー: ライセンスを提供する Remote Desktop ライセンス サーバーがないため、リモート セッションが切断されました。

原因: リモート デスクトップ サーバー ロールの 120 日間のライセンス有効期限が切れているため、ライセンスをインストールする必要がある。

回避策として、ポータルから RDP ファイルのローカル コピーを保存し、PowerShell コマンド プロンプトで次のコマンドを実行して接続します。これによって、この接続のみに対するライセンスが無効になります。

		mstsc <File name>.RDP /admin

VM に対して 2 つ以上のリモート デスクトップ接続が同時に必要でない場合、サーバー マネージャーを使用して、リモート デスクトップ サーバーのロールを削除することができます。

詳細については、ブログ投稿「[Azure VM fails with "No Remote Desktop License Servers available"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx)」 (Azure VM が "使用できるリモート デスクトップ ライセンス サーバーがありません" で失敗する) も参照してください。

<a id="rdpname"></a>
### Remote Desktop 接続エラー: Remote Desktop は、コンピューター "name" を見つけることができません。

原因: コンピューター上のリモート デスクトップ クライアントが、RDP ファイルで設定されたコンピューターの名前を解決できない。

考えられる解決策:

- 組織のイントラネットを使用している場合は、コンピューターからプロキシ サーバーにアクセスでき、そのサーバーに HTTPS トラフィックを送信できることを確認してください。

- ローカルに保存した RDP ファイルを使用している場合は、ポータルで生成されたファイルを試してください。仮想マシンまたはクラウド サービスの適切な DNS 名と、仮想マシンのエンドポイント ポートが使用されます。ポータルで生成される RDP ファイルの例を次に示します。

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

この RDP ファイルのアドレス部分には、次のデータが含まれます。
- VM を含むクラウド サービスの完全修飾ドメイン名 (この例では "tailspin-azdatatier.cloudapp.net")。

- リモート デスクトップ トラフィックの外部 TCP ポート (55919)。

<a id="rdpauth"></a>
### Remote Desktop 接続エラー: 認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。

原因: ターゲット VM が、資格情報のユーザー名の部分でセキュリティ機関を見つけることができません。

ユーザー名が *SecurityAuthority*\*UserName* という形式 (例: CORP\\User1) の場合、*SecurityAuthority* の部分は、仮想マシンのコンピューター名 (ローカル セキュリティ機関) または Active Directory ドメイン名になります。

考えられる解決策:

- アカウントが VM にとってローカルの場合は、VM 名のスペルが正しいことを確認します。

- アカウントが Active Directory ドメイン アカウントの場合は、ドメイン名のスペルを確認します。

- ユーザー アカウントが Active Directory ドメイン アカウントであり、ドメイン名のスペルが正しい場合は、ドメイン コント ローラーがそのドメインで利用可能であることを確認します。ドメイン コントローラーがある Azure 仮想ネットワークで、起動していないためにドメイン コントローラーを利用できないというのは、よく起こる問題です。回避策として、ドメイン アカウントの代わりにローカル管理者アカウントを使用できます。

<a id="wincred"></a>
### Windows セキュリティ エラー: 資格情報が正しくありません。

原因: ターゲット VM が、アカウント名とパスワードを検証できません。

Windows ベースのコンピューターでは、ローカル アカウントとドメイン アカウントの資格情報を認証できます。

- ローカル アカウントの場合は、*ComputerName*\*UserName* という構文を使用します (例: SQL1\\Admin4798)。
- ドメイン アカウントの場合は、*DomainName*\*UserName* という構文を使用します (例: CONTOSO\\peterodman)。

新しい Active Directory フォレスト内で VM がドメイン コントローラーに昇格している場合、サインイン時に使用したローカル管理者アカウントが、新しいフォレストとドメイン内で同じパスワードを持つ同等のアカウントに変換されます。その後、ローカル アカウントは削除されます。

たとえば、ローカルアカウント DC1\\DCAdmin でサインインし、新しいフォレストで仮想マシンを corp.contoso.com ドメインのドメイン コントローラーとして昇格すると、DC1\\DCAdmin のローカル アカウントは削除され、新しいドメイン アカウント (CORP\\DCAdmin) が同じパスワードで作成されます。

アカウント名が仮想マシンで有効なアカウントとして認証される名前であること、パスワードが正しいことを確認します。

ローカル管理者アカウントのパスワードを変更する必要がある場合は、「[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-rdp.md)」をご覧ください。

<a id="rdpconnect"></a>
### Remote Desktop 接続エラー: このコンピューターはリモート コンピューターに接続できません。

原因: 接続に使用しているアカウントに、リモート デスクトップ サインイン権限がありません。

すべての Windows コンピューターには、リモート デスクトップ ユーザーのローカル グループがあり、このグループには、リモートでサインインできるアカウントとグループが含まれます。ローカルの Administrators グループのメンバーもアクセスできますが、これらのアカウントは、リモート デスクトップユーザーのローカル グループのメンバーとしてリストされません。ドメインに参加しているマシンの場合、ローカルの Administrators グループにはドメインのドメイン管理者も含まれます。

接続するために使用しているアカウントに、リモート デスクトップ サインイン権限があることを確認してください。回避策として、ドメインまたはローカル管理者アカウントを使用して、リモート デスクトップで接続します。その後、Microsoft 管理コンソール スナップイン (**[システム ツール]、[ローカル ユーザーとグループ]、[グループ]、[リモート デスクトップ ユーザー] の順に選択します**) を使用して、目的のアカウントをリモート デスクトップ ユーザーのローカル グループに追加します。

## 一般的なリモート デスクトップ エラーのトラブルシューティング

上記のエラーが発生していないにもかかわらず、リモート デスクトップを介して VM に接続できない場合は、「[リモート デスクトップ接続に関する詳細なトラブルシューティング](virtual-machines-windows-detailed-troubleshoot-rdp.md)」を参照してください。


## その他のリソース

[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-rdp.md)

[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)

[Linux ベースの Azure 仮想マシンに対する Secure Shell 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0518_2016-->