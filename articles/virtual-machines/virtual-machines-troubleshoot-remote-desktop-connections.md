<properties
	pageTitle="Azure VM へのリモート デスクトップ接続のトラブルシューティング |Microsoft Azure"
	description="Windows VM に対するリモート デスクトップ接続のエラーをトラブルシューティングします。簡単な軽減手順、エラー メッセージごとのヘルプ、および詳細なネットワーク トラブルシューティングを説明します。"
	keywords="リモート デスクトップ エラー,リモート デスクトップ接続エラー,VM に接続できない,リモート デスクトップのトラブルシューティング"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="dkshir"/>

# Windows を実行する Azure 仮想マシンへの Remote Desktop 接続に関するトラブルシューティング

Windows ベースの Azure 仮想マシンに対するリモート デスクトップ (RDP) 接続  
は、さまざまな理由で失敗する可能性があります。  
VM 上のリモート デスクトップ サービス、ネットワーク接続、またはホスト コンピューター上のリモート デスクトップ クライアントに問題がある可能性があります。この記事は、原因を特定して修正するために役立ちます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

この記事は、Windows を実行する Azure 仮想マシンに適用されます。Linux を実行する Azure 仮想マシンの場合は、「[Linux ベースの Azure Virtual Machines に対する Secure Shell (SSH) 接続のトラブルシューティング](virtual-machines-troubleshoot-ssh-connections.md)」を参照してください。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。


<a id="quickfixrdp"></a>
## リモート デスクトップの一般的なエラーを修正する

このセクションでは、一般的なリモート デスクトップ接続の問題をすばやく修正するための手順を示します。

### クラシック デプロイ モデルを使用して作成した仮想マシン

クラシック デプロイ モデルを使用して作成された Azure 仮想マシンのリモート デスクトップ接続に関する一般的なエラーの大半は、次の手順で解決できる可能性があります。各手順を実行した後、VM に再接続してみてください。

- RDP サーバーでの起動の問題を解決するには、[Azure ポータル](https://portal.azure.com)からリモート デスクトップ サービスをリセットします。<br> **[参照]**、**[仮想マシン (クラシック)]**、ご使用の Windows 仮想マシン、**[リモートのリセット]** の順にクリックします。

- その他の起動の問題に対処するには仮想マシンを再起動します。<br> **[参照]**、**[仮想マシン (クラシック)]**、ご使用の Windows 仮想マシン、**[再起動]** の順にクリックします。

- ホストの問題を解決するには、VM のサイズを変更します。<br> **[参照]**、**[仮想マシン (クラシック)]**、ご使用の Windows 仮想マシン、**[設定]**、**[サイズ]** の順にクリックします。詳細な手順については、「[Resize the virtual machine (仮想マシンのサイズの変更)](https://msdn.microsoft.com/library/dn168976.aspx)」を参照してください。

- 起動の問題を修正するには、VM のコンソール ログまたはスクリーンショットを確認します。<br> **[参照]**、**[仮想マシン (クラシック)]**、ご使用の Windows 仮想マシン、**[設定]**、**[起動の診断]** の順にクリックします。

- プラットフォームの問題の有無について VM のリソースの状態を確認します。<br> **[参照]**、**[仮想マシン (クラシック)]**、ご使用の Windows 仮想マシン、**[設定]**、**[正常性の確認]** の順にクリックします。

### リソース マネージャー デプロイ モデルを使用して作成した仮想マシン

リソース マネージャー デプロイ モデルを使用して作成された Azure 仮想マシンのリモート デスクトップ接続に関する一般的なエラーの大半は、次の手順で解決できる可能性があります。各手順を実行した後、VM に再接続してみてください。

- Powershell を使用して_リモート アクセスをリセット_します。<br> a.まだインストールしていない場合は、[Azure PowerShell をインストールし、Azure AD メソッドを使用して Azure サブスクリプションに接続します](../powershell-install-configure.md)。新しい Azure PowerShell のバージョン 1.0.x ではリソース マネージャー モードに切り替える必要はありません。

	b.次の Azure PowerShell コマンドのいずれかを使用して、RDP 接続をリセットします。`myRG`、`myVM`、`myVMAccessExtension`、および場所を、自分のセットアップに関連する値に置き換えます。

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	または<br>

  ```
  Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
  ```

- その他の起動の問題に対処するには仮想マシンを再起動します。<br> **[参照]**、**[仮想マシン]**、ご使用の Windows 仮想マシン、**[再起動]** の順にクリックします。

- ホストの問題を解決するには、VM のサイズを変更します。<br> **[すべて参照]**、**[仮想マシン]**、ご使用の Windows 仮想マシン、**[設定]**、**[サイズ]** の順にクリックします。

- 起動の問題を修正するには、VM のコンソール ログまたはスクリーンショットを確認します。<br> **[参照]**、**[仮想マシン]**、ご使用の Windows 仮想マシン、**[設定]**、**[起動の診断]** の順にクリックします。


リモート デスクトップ接続エラーが上記の手順で解決しなかった場合は、次のセクションに進んでください。

## 特定のリモート デスクトップ接続エラーのトラブルシューティング

Azure 仮想マシンにリモート デスクトップ接続しようとしたときに発生する可能性がある一般的なエラーは次のとおりです。

1. [Remote Desktop 接続エラー: ライセンスを提供する Remote Desktop ライセンス サーバーがないため、リモート セッションが切断されました](#rdplicense)。

2. [Remote Desktop 接続エラー: Remote Desktop は、コンピューター "name" を見つけることができません](#rdpname)。

3. [Remote Desktop 接続エラー: 認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。](#rdpauth)

4. [Windows セキュリティ エラー: 資格情報が正しくありません](#wincred)。

5. [Remote Desktop 接続エラー: このコンピューターはリモート コンピューターに接続できません](#rdpconnect)。

<a id="rdplicense"></a>
### Remote Desktop 接続エラー: ライセンスを提供する Remote Desktop ライセンス サーバーがないため、リモート セッションが切断されました。

原因: リモート デスクトップ サーバー ロールの 120 日間のライセンス有効期限が切れているため、ライセンスをインストールする必要がある。

回避策として、ポータルから RDP ファイルのローカル コピーを保存し、Windows PowerShell コマンド プロンプトで次のコマンドを実行して接続します。これによって、この接続のみに対するライセンスが無効になります。

		mstsc <File name>.RDP /admin

VM に対して 2 つ以上のリモート デスクトップ接続が同時に必要でない場合、サーバー マネージャーを使用して、リモート デスクトップ サーバーのロールを削除することができます。

「[Azure 仮想マシンに問題が発生し、エラー メッセージ『使用できるリモート デスクトップ ライセンス サーバーがありません』が表示された場合](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx) 」のブログ投稿も参照してください。

<a id="rdpname"></a>
### Remote Desktop 接続エラー: Remote Desktop は、コンピューター "name" を見つけることができません。

原因: お使いのコンピューターの Remote Desktop クライアントが、RDP ファイルに設定されたコンピューターの名前を解決できません。

考えられる解決策:

- 組織のイントラネットを使用している場合は、コンピューターからプロキシ サーバーにアクセスでき、そのサーバーに HTTPS トラフィックを送信できることを確認してください。
- ローカルに保存した RDP ファイルを使用している場合は、ポータルで生成されたファイルを試してください。仮想マシンまたはクラウド サービスの適切な DNS 名と、仮想マシンのエンドポイント ポートが使用されます。ポータルで生成される RDP ファイルの例を次に示します。

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

この RDP ファイルのアドレス部分には、VM を含むクラウド サービスの完全修飾ドメイン名 (この例では tailspin-azdatatier.cloudapp.net) と、Remote Desktop トラフィック用のエンドポイントの外部 TCP ポート (55919) が含まれます。

<a id="rdpauth"></a>
### Remote Desktop 接続エラー: 認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。

原因: ターゲット VM が、資格情報のユーザー名の部分でセキュリティ機関を見つけることができませんでした。

ユーザー名が *SecurityAuthority\UserName* という形式 (例: CORP\\User1) の場合、*SecurityAuthority* の部分は、仮想マシンのコンピューター名 (ローカル セキュリティ機関) または Active Directory ドメイン名になります。

考えられる解決策:

- ユーザー アカウントが VM にとってローカルの場合は、VM 名のスペルが正しいことを確認します。
- ユーザー アカウントが Active Directory ドメイン アカウントの場合は、ドメイン名のスペルを確認します。
- ユーザー アカウントが Active Directory ドメイン アカウントであり、ドメイン名のスペルが正しい場合は、ドメイン コント ローラーがそのドメインで利用可能であることを確認します。この問題は、Azure Virtual Network にドメイン コント ローラーを含まれていて、ドメイン コント ローラー コンピューターが起動されていない場合によく発生します。回避策として、ドメイン アカウントではなく、ローカル管理者アカウントを使用できます。

<a id="wincred"></a>
### Windows セキュリティ エラー: 資格情報が正しくありません。

原因: ターゲット VM が、アカウント名とパスワードを検証できませんでした。

Windows ベースのコンピューターでは、ローカル アカウントとドメイン アカウントの資格情報を認証できます。

- ローカル アカウントの場合は、*ComputerName\UserName* という構文を使用します (例: SQL1\\Admin4798)。
- ドメイン アカウントの場合は、*DomainName\UserName* という構文を使用します (例: CONTOSO\\johndoe)。

新しい AD フォレスト内で VM がドメイン コントローラーに昇格している場合、ログイン時に使用したローカル管理者アカウントも、新しいフォレストとドメイン内で同じパスワードを持つ同等のアカウントに変換されます。その後、ローカル アカウントは削除されます。たとえば、ローカルアカウント DC1\\DCAdmin でログインして、新しいフォレストで仮想マシンを corp.contoso.com ドメインのドメイン コントローラーとして昇格すると、DC1\\DCAdmin のローカル アカウントは削除され、新しいドメイン アカウント (CORP\\DCAdmin) が同じパスワードで作成されます。

アカウント名が仮想マシンで有効なアカウントとして認証される名前であること、パスワードが正しいことを確認します。

ローカル管理者アカウントのパスワードを変更する必要がある場合は、「[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-password.md)」をご覧ください。

<a id="rdpconnect"></a>
### Remote Desktop 接続エラー: このコンピューターはリモート コンピューターに接続できません。

原因: 接続に使用しているアカウントに、Remote Desktop ログオン権限がありません。

すべての Windows コンピューターには Remote Desktop ユーザーのローカル グループがあり、このグループには、リモートでログオンできるアカウントとグループが含まれます。ローカルの Administrators グループのメンバーもアクセスできますが、これらのアカウントは Remote Desktop ユーザーのローカル グループのメンバーとしてリストされません。ドメインに参加しているマシンの場合、ローカルの Administrators グループにはドメインのドメイン管理者も含まれます。

接続するために使用しているアカウントに、Remote Desktop ログオン権限があることを確認してください。回避策として、ドメイン管理者アカウントまたはローカル管理者アカウントを使用して Remote Desktop で接続した後、コンピューターの管理スナップイン (**[システム ツール] > [ローカル ユーザーとグループ] > [グループ] > [リモート デスクトップ ユーザー]**) を使用して、必要なアカウントをリモート デスクトップ ユーザーのローカル グループに追加します。

## 一般的なリモート デスクトップ エラーのトラブルシューティング

上記のエラーが発生していないにもかかわらず、リモート デスクトップを介して VM に接続できない場合は、「[リモート デスクトップ接続に関する詳細なトラブルシューティング](virtual-machines-rdp-detailed-troubleshoot.md)」を参照してください。


## その他のリソース

[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-password.md)

[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)

[Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](virtual-machines-troubleshoot-ssh-connections.md)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0204_2016-->