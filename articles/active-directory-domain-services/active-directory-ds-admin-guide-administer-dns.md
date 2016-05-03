<properties
	pageTitle="Azure Active Directory ドメイン サービス プレビュー: 管理ガイド | Microsoft Azure"
	description="Azure Active Directory ドメイン サービスを使用して管理対象ドメインの DNS を管理する"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="maheshu"/>

# Azure AD ドメイン サービスで管理されているドメイン上の DNS の管理
Azure Active Directory ドメイン サービスには、管理対象ドメインの DNS 解決を提供する DNS (ドメイン名解決) サーバーが含まれています。場合によっては、管理対象ドメインの DNS を、ドメイン、ロード バランサーの仮想 IP、または外部 DNS フォワーダーに結合されていないコンピューターのレコードの順に構成する必要があります。このため、AAD DC 管理者グループに属するユーザーは、管理対象ドメインの DNS 管理者権限が付与されます。

## ドメインに参加している仮想マシンをプロビジョニングして、管理対象ドメインの DNS をリモートで管理する
Azure AD ドメイン サービスの管理対象ドメインは、Active Directory 管理センター (ADAC) や AD PowerShell などの使い慣れた Active Directory 管理ツールでリモート管理することができます。同様に、管理対象ドメインの DNS は、DNS サーバーの管理ツールを使用してリモートで管理できます。テナント管理者には、管理対象ドメイン上のドメイン コントローラーにリモート デスクトップで接続する権限はありません。そのため、"AAD DC Administrators" グループのメンバーは、管理対象ドメインに参加している Windows Server とクライアント コンピューターから、DNS サーバー ツールを使用して、リモートで管理対象ドメインの DNS を管理できます。DNS サーバー ツールは、管理対象ドメインに参加している Windows Server とクライアント コンピューターで、リモート サーバー管理ツール (RSAT) のオプション機能の一部としてインストールできます。

最初の手順では、管理対象ドメインに参加している Windows Server 仮想マシンを設定します。この手順については、「[join a Windows Server virtual machine to an Azure AD Domain Services managed domain (Windows Server 仮想マシンの Azure AD ドメイン サービス管理対象ドメインへの参加)](active-directory-ds-admin-guide-join-windows-vm.md)」を参照してください。これらの手順では、Azure AD ドメイン サービスの管理対象ドメインを管理するために、Windows Server 仮想マシンを使用することに注意してください。Windows クライアント (Windows 10 など) の仮想マシンを使用することもできます。この場合は、仮想マシンにリモート サーバー管理ツールのオプション機能をインストールできます。


## 仮想マシンに DNS サーバー ツールをインストールする
ドメインに参加している仮想マシンに DNS 管理ツールをインストールするには、次の手順を実行します。[リモート サーバー管理ツールのインストールおよび使用](https://technet.microsoft.com/library/hh831501.aspx)の詳細については、TechNet を参照してください。

1. Azure クラシック ポータルの** [仮想マシン] **ノードに移動します。作成した仮想マシンを選択し、ウィンドウ下部にあるコマンド バーで** [接続] **をクリックします。

    ![Windows 仮想マシンに接続する](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. クラシック ポータルでは、仮想マシンへの接続に使用される .rdp ファイルを開くか保存するように求められます。ダウンロードが完了したら、.rdp ファイルをクリックします。

3. ログイン プロンプトで、'AAD DC Administrators' グループに属しているユーザーの資格情報を使用します。この例では " bob@domainservicespreview.onmicrosoft.com" です。

4. スタート画面で、**[サーバー マネージャー]** を開きます。[サーバー マネージャー] ウィンドウの中央ペインで **[役割と機能の追加]** をクリックします。

    ![仮想マシンでのサーバー マネージャーの起動](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. **[役割と機能の追加]** ウィザードの **[開始する前に]** ページで **[次へ]** をクリックします。

    ![ページを開始する前に](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. **[インストールの種類]** ページで、**[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** をクリックします。

	![インストールの種類のページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. **[サーバーの選択]** ページで、サーバー プールから現在の仮想マシンを選択して **[次へ]** をクリックします。

	![サーバー選択ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. **[サーバーの役割]** ページで、**[次へ]** をクリックします。サーバーに役割をインストールしないため、このページはスキップします。

9. **[機能]** ページで、**[リモート サーバー管理ツール]** ノードをクリックして展開し、次に **[役割管理ツール]** ノードをクリックして展開します。役割管理ツールの一覧から、次のように **[DNS サーバー ツール]** 機能を選択します。

	![機能ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. **[確認]** ページで、**[インストール]** をクリックして仮想マシンに DNS サーバー ツールの機能をインストールします。機能のインストールが正常に完了したら、**[閉じる]** をクリックして **[役割と機能の追加]** ウィザードを終了します。

	![確認ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## DNS 管理コンソールを起動する
ドメインに参加している仮想マシンに DNS サーバー ツール機能がインストールされると、DNS ツールを使用して管理対象ドメインの DNS を管理できるようになります。

1. スタート画面で、**[管理ツール]** をクリックします。仮想マシンにインストールされた **DNS** コンソールを確認できます。

	![管理ツール： DNS コンソール](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. **DNS** をクリックして、DNS 管理コンソールを起動します。

3. **[DNS サーバーに接続]** ダイアログ ボックスで、**[次のコンピューター]** というオプションを選択して、管理対象ドメインの DNS ドメイン名 (例: 'contoso100.com') を入力します。

    ![DNS コンソール: ドメインへの接続](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. DNS コンソールが管理対象ドメインに接続されます。次のようなビューが表示されます。

    ![DNS コンソール: ドメインの管理](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. この DNS コンソールを使用して、AAD ドメイン サービスを有効にしている仮想ネットワーク内で、コンピューターに DNS エントリを追加できます。

> [AZURE.WARNING] DNS 管理ツールを使用して管理対象ドメインの DNS を管理する際は、特に注意してください。**ドメイン内のドメイン サービスで使用される組み込みの DNS レコードを削除または変更**しないでください。これには、ドメインの DNS レコード、ネーム サーバー レコード、および DC の検出に使用されるその他のレコードが含まれます。これらのレコードを変更する場合は、仮想ネットワークのドメイン サービスが中断されます。


DNS の管理について詳しくは、[Technet の DNS ツールの記事](https://technet.microsoft.com/library/cc753579.aspx)を参照してください。

<!---HONumber=AcomDC_0420_2016-->