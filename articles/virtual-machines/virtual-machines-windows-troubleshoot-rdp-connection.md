<properties
	pageTitle="Azure VM へのリモート デスクトップ接続のトラブルシューティング |Microsoft Azure"
	description="Azure VM にアクセスできない場合の、簡単な RDP トラブルシューティング手順、エラー メッセージごとのヘルプ、および詳細なネットワーク トラブルシューティング手順を説明します。"
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
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# Windows を実行する Azure Virtual Machines への Remote Desktop 接続に関するトラブルシューティング

Windows ベースの Azure 仮想マシン (VM) に対するリモート デスクトップ プロトコル (RDP) 接続は、さまざまな理由で失敗する可能性があり、VM にアクセスできない場合があります。VM 上のリモート デスクトップ サービス、ネットワーク接続、またはホスト コンピューター上のリモート デスクトップ クライアントに問題がある可能性があります。この記事では、RDP の接続問題を解決する、最も一般的な方法について説明します。発生している問題がまだ掲載されていない場合、または、RDP 経由で VM に接続できない場合は、[RDP トラブルシューティングの考え方と手順](virtual-machines-windows-detailed-troubleshoot-rdp.md)に関する記事をご覧ください。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。

<a id="quickfixrdp"></a>

## 簡単なトラブルシューティング手順
トラブルシューティングの各手順を実行した後、VM に再接続してみてください。

1. Azure ポータルまたは Azure PowerShell を使用してリモート アクセスをリセットする
2. VM を再起動する
3. VM を再デプロイする
4. ネットワーク セキュリティ グループ/クラウド サービス エンドポイントの規則を確認する
5. Azure ポータルまたは Azure PowerShell で VM コンソールのログを確認する
6. Azure ポータルで VM Resource Health を確認する
7. VM のパスワードをリセットする

さらに詳細な手順が必要な場合、このドキュメントを引き続きお読みください。以降のセクションでは、Resource Manager とクラシック デプロイメント モデルについて説明します。


<a id="fix-common-remote-desktop-errors"></a>
## Resource Manager デプロイ モデルを使用して作成された VM のトラブルシューティング

トラブルシューティングの各手順を実行した後、VM に再接続してみてください。

> [AZURE.TIP] ポータルの [接続] ボタンが淡色表示され、[Express Route](../expressroute/expressroute-introduction.md) や[サイト間 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 接続で Azure に接続されていない場合は、VM を作成してパブリック IP アドレスを割り当ててから RDP を使用する必要があります。[Azure におけるパブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)に関するページをご覧ください。

1. Powershell を使用してリモート アクセスをリセットします。
	- まだインストールしていない場合は、[最新の Azure PowerShell をインストールして構成します](../powershell-install-configure.md)。

	- 次の PowerShell コマンドのいずれかを使用して、RDP 接続をリセットします。`myRG`、`myVM`、`myVMAccessExtension`、および場所を、自分のセットアップに関連する値に置き換えます。

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" `
		-Location Westus
	```
	または

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" `
		-VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] 上記の例で、`myVMAccessExtension` または `MyVMAccess` は、プロセスの一環としてインストールする新しい拡張機能に対して指定する名前です。多くの場合、これには、VM の名前を設定します。VMAccessAgent を前に操作したことがある場合は、`Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"` を使用して VM のプロパティをチェックすることで、既存の拡張機能の名前を取得できます。出力の "Extensions" セクションで名前を確認します。1 つの VM には VMAccessAgent が 1 つだけ存在できるため、`Set-AzureRmVMExtension` を使用するときに `-ForceReRun True` パラメーターも追加する必要があります。これにより、エージェントが再登録されます。

2. その他のスタートアップの問題に対処するには VM を再起動します。**[参照]**、**[仮想マシン]**、<ご使用の VM>、**[再起動]** の順に選択します。

3. [新しい Azure ノードに VM を再デプロイします](virtual-machines-windows-redeploy-to-new-node.md)。

	この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されます。
	
4. [ネットワーク セキュリティ グループの規則](../virtual-network/virtual-networks-nsg.md)で RDP トラフィック (TCP ポート 3389) が許可されていることを確認します。

5. 起動の問題を修正するには、VM のコンソール ログまたはスクリーンショットを確認してください。**[参照]**、**[仮想マシン]**、<ご使用の Windows 仮想マシン>、**[サポートとトラブルシューティング]**、**[起動の診断]** の順に選択します。

6. [VM のパスワードをリセットします](virtual-machines-windows-reset-rdp.md)。

RDP の問題が解決しない場合は、[サポート要求を申請](https://azure.microsoft.com/support/options/)するか、[RDP トラブルシューティングの考え方と手順](virtual-machines-windows-detailed-troubleshoot-rdp.md)に関する記事をご覧ください。


## クラシック デプロイメント モデルを使用して作成された VM のトラブルシューティング

トラブルシューティングの各手順を実行した後、VM に再接続してみてください。

1. リモート デスクトップ サービスを [Azure ポータル](https://portal.azure.com)からリセットします。**[参照]**、**[仮想マシン (クラシック)]**、<ご使用の VM>、**[リモートのリセット]** の順に選択します。

2. その他のスタートアップの問題に対処するには VM を再起動します。**[参照]**、**[仮想マシン (クラシック)]**、<ご使用の VM>、**[再起動]** の順に選択します。

3. [新しい Azure ノードに VM を再デプロイします](virtual-machines-windows-redeploy-to-new-node.md)。

	この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されます。
	
4. [Cloud Services エンドポイントによって RDP トラフィックが許可されている](../cloud-services/cloud-services-role-enable-remote-desktop.md)ことを確認します。

5. 起動の問題を修正するには、VM のコンソール ログまたはスクリーンショットを確認してください。**[参照]**、**[仮想マシン (クラシック)]**、<ご使用の VM>、**[設定]**、**[起動の診断]** の順に選択します。

6. プラットフォームの問題の有無について VM のリソースの状態を確認します。**[参照]**、**[仮想マシン (クラシック)]**、<ご使用の VM>、**[設定]**、**[正常性の確認]** の順に選択します。

7. [VM のパスワードをリセットします](virtual-machines-windows-reset-rdp.md)。

RDP の問題が解決しない場合は、[サポート要求を申請](https://azure.microsoft.com/support/options/)するか、[RDP トラブルシューティングの考え方と手順](virtual-machines-windows-detailed-troubleshoot-rdp.md)に関する記事をご覧ください。


## 特定のリモート デスクトップ接続エラーのトラブルシューティング

お使いの VM に RDP 経由で接続しようとすると、特定のエラーが表示される場合があります。一般的なエラー メッセージは次のとおりです。

- [ライセンスを提供するためのリモート デスクトップ ライセンス サーバーがないため、リモート セッションは切断されました](#rdplicense)。

- [リモート デスクトップがコンピューター "name" を見つけることができません](#rdpname)。

- [認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。](#rdpauth)

- [Windows セキュリティ エラー: 資格情報が正しくありません](#wincred)。

- [このコンピューターはリモート コンピューターに接続できません](#rdpconnect)。

<a id="rdplicense"></a>
### ライセンスを提供するためのリモート デスクトップ ライセンス サーバーがないため、リモート セッションは切断されました。

原因: リモート デスクトップ サーバー ロールの 120 日間のライセンス有効期限が切れているため、ライセンスをインストールする必要がある。

回避策として、ポータルから RDP ファイルのローカル コピーを保存し、PowerShell コマンド プロンプトで次のコマンドを実行して接続します。これにより、この接続についてのみライセンスが無効になります。

		mstsc <File name>.RDP /admin

VM に対して 2 つ以上のリモート デスクトップ接続が同時に必要でない場合、サーバー マネージャーを使用して、リモート デスクトップ サーバーのロールを削除することができます。

詳細については、["使用できるリモート デスクトップ ライセンス サーバーがありません" で失敗する Azure VM](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/) に関するブログ投稿をご覧ください。

<a id="rdpname"></a>
### Remote Desktop は、コンピューター "name" を見つけることができません。

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
### 認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。

原因: ターゲット VM が、資格情報のユーザー名の部分でセキュリティ機関を見つけることができません。

ユーザー名が *SecurityAuthority*\*UserName* という形式 (例: CORP\\User1) の場合、*SecurityAuthority* の部分は、VM のコンピューター名 (ローカル セキュリティ機関) または Active Directory ドメイン名になります。

考えられる解決策:

- アカウントが VM にとってローカルの場合は、VM 名のスペルが正しいことを確認します。

- アカウントが Active Directory ドメイン アカウントの場合は、ドメイン名のスペルを確認します。

- ユーザー アカウントが Active Directory ドメイン アカウントであり、ドメイン名のスペルが正しい場合は、ドメイン コント ローラーがそのドメインで利用可能であることを確認します。ドメイン コントローラーがある Azure 仮想ネットワークで、起動していないためにドメイン コントローラーを利用できないというのは、よく起こる問題です。回避策として、ドメイン アカウントではなく、ローカル管理者アカウントを使用できます。

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
### このコンピューターはリモート コンピューターに接続できません。

原因: 接続に使用しているアカウントに、リモート デスクトップ サインイン権限がありません。

すべての Windows コンピューターには、リモート デスクトップ ユーザーのローカル グループがあり、このグループには、リモートでサインインできるアカウントとグループが含まれます。ローカルの Administrators グループのメンバーもアクセスできますが、これらのアカウントは、リモート デスクトップユーザーのローカル グループのメンバーとしてリストされません。ドメインに参加しているマシンの場合、ローカルの Administrators グループにはドメインのドメイン管理者も含まれます。

接続するために使用しているアカウントに、リモート デスクトップ サインイン権限があることを確認してください。回避策として、ドメインまたはローカル管理者アカウントを使用して、リモート デスクトップで接続します。目的のアカウントをリモート デスクトップ ユーザーのローカル グループに追加するには、Microsoft 管理コンソール スナップイン (**[システム ツール]、[ローカル ユーザーとグループ]、[グループ]、[リモート デスクトップ ユーザー] の順に選択します**) を使用します。

## 一般的なリモート デスクトップ エラーのトラブルシューティング

上記のエラーが発生していないにもかかわらず、リモート デスクトップを介して VM に接続できない場合は、[リモート デスクトップのトラブルシューティング](virtual-machines-windows-detailed-troubleshoot-rdp.md)に関するページをご覧ください。


## その他のリソース

[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-rdp.md)

[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)

[Linux ベースの Azure 仮想マシンに対する Secure Shell 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0907_2016-->