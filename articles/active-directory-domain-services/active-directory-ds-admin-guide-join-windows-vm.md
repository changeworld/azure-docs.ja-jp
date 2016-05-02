<properties
	pageTitle="Azure Active Directory ドメイン サービス プレビュー: 管理ガイド | Microsoft Azure"
	description="Windows Server 仮想マシンの Azure AD ドメイン サービスへの参加"
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

# Windows Server 仮想マシンの管理対象ドメインへの参加

> [AZURE.SELECTOR]
- [Azure クラシック ポータル - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

この記事では、Azure クラシック ポータルを使用して、Windows Server 2012 R2 を実行する仮想マシンを、Azure AD ドメイン サービスによって管理されているドメインに参加させる方法を示します。


## ステップ 1: Windows Server 仮想マシンを作成する
「[Windows を実行する仮想マシンを Azure クラシック ポータルで作成する](../virtual-machines/virtual-machines-windows-classic-tutorial.md)」チュートリアルで説明されている手順に従います。新しく作成した仮想マシンが、Azure AD ドメイン サービスを有効にしたのと同じ仮想ネットワークに確実に参加していることが非常に重要です。'簡易作成' オプションを使用すると、仮想マシンを仮想ネットワークに参加させることができないことに注意してください。そのため、'ギャラリーから' オプションを使用して仮想マシンを作成する必要があります。

Azure AD ドメイン サービスを有効にした仮想ネットワークに参加する Windows 仮想マシンを作成するには、次の手順を実行します。

1. Azure クラシック ポータルのウィンドウの下部にあるコマンド バーで、**[新規]** をクリックします。

2. **[Compute]** で、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

3. 最初の画面の **[イメージの選択]** で、利用できるイメージの一覧から仮想マシンのイメージを選択できます。適切なイメージを選択します。

    ![イメージの選択](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. 2 番目の画面では、コンピューターの名前、サイズ、管理ユーザーの名前とパスワードを選択します。アプリやワークロードの実行に必要な階層とサイズを選択してください。ここでは選択するユーザー名は、マシンのローカル管理者のユーザーです。ここではドメイン ユーザー アカウントの資格情報を入力しないでください。

    ![仮想マシンの構成](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. 3 番目の画面では、ネットワーク、ストレージ、可用性のリソースを構成できます。**[リージョン/アフィニティ グループ/仮想ネットワーク]** ボックスで、Azure AD ドメイン サービスを有効にした仮想ネットワークを選択していることを確認します。仮想マシンに対して適切な **[クラウド サービス DNS 名]** を指定します。

    ![仮想マシンの仮想ネットワークの選択](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    作成した仮想マシンを、Azure AD ドメイン サービスを有効にしたのと同じ仮想ネットワークに参加させていることを確認します。これにより、仮想マシンがドメインを '参照' し、ドメイン参加などのタスクを実行できるようになります。別の仮想ネットワークの仮想マシンを作成する場合は、仮想ネットワークが、Azure AD ドメイン サービスを有効にした仮想ネットワークに接続されていることを確認します。

6. 4 番目の画面では、VM エージェントをインストールし、使用可能な一部の拡張機能を構成できます。

    ![完了](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. 仮想マシンが作成されると、クラシック ポータルの **[Virtual Machines]** ノードに新しい仮想マシンが一覧表示されます。仮想マシンとクラウド サービスが自動的に起動し、その状態がいずれも "**実行中**" として表示されます。

    ![稼働している仮想マシン](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## ステップ 2: ローカル管理者アカウントを使用して Windows Server 仮想マシンに接続する
次に、新たに作成された Windows Server 仮想マシンをドメインに参加させるため、その仮想マシンに接続します。仮想マシンに接続するには、仮想マシンの作成時に指定したローカル管理者の資格情報を使用します。

仮想マシンに接続するには、次の手順を実行します。

1. クラシック ポータルの **[Virtual Machines]** ノードに移動します。作成した仮想マシンを選択し、ウィンドウ下部にあるコマンド バーで **[接続]** をクリックします。

    ![Windows 仮想マシンに接続する](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. クラシック ポータルでは、仮想マシンへの接続に使用される .rdp ファイルを開くか保存するように求められます。ダウンロードが完了したら、.rdp ファイルをクリックします。

3. ログイン プロンプトで、仮想マシンの作成時に指定した**ローカル管理者の資格情報**を入力します。たとえば、上記の例では、'localhost\\mahesh' です。

この時点で、ローカル管理者の資格情報を使用して、新しく作成した Windows 仮想マシンにログインしている必要があります。次のステップでは、仮想マシンをドメインに参加させます。


## ステップ 3: Windows Server 仮想マシンを AAD-DS 管理対象ドメインに参加させる
Windows Server 仮想マシンを AAD-DS 管理対象ドメインに参加させるには、次の手順を実行します。

1. 上記のステップ 2 で示したように、Windows Server に接続します。スタート画面で、**[サーバー マネージャー]** を開きます。

2. [サーバー マネージャー] ウィンドウの左側のウィンドウで **[ローカル サーバー]** をクリックします。

    ![仮想マシンでのサーバー マネージャーの起動](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. **[プロパティ]** セクションで **[ワークグループ]** をクリックします。**[システムのプロパティ]** プロパティ ページが開きます。ドメインに参加するには、**[変更]** をクリックします。

    ![システムのプロパティ ページ](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. **[ドメイン]** テキスト ボックスで、Azure AD ドメイン サービス管理対象ドメインのドメイン名を指定し、**[OK]** をクリックします。

    ![参加するドメインの指定](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. ドメインに参加するために、資格情報を入力するように求められます。**AAD DC 管理者グループに属しているユーザーの資格情報を指定**します。このグループのメンバーだけが、マシンを管理対象のドメインに参加させる権限を有します。

    ![ドメインに参加する資格情報を指定](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. 次のいずれかの方法で、資格情報を指定できます。

    - UPN 形式: Azure AD で構成されたユーザー アカウントの UPN サフィックスです。この例では、ユーザー 'bob' の UPN サフィックスは、'bob@domainservicespreview.onmicrosoft.com' です。

    - SAMAccountName 形式: SAMAccountName 形式でアカウント名を指定することができます。この例では、ユーザー 'bob' は、「CONTOSO100\\bob」を入力する必要があります。複数のユーザーが Azure AD テナントで同じ UPN プレフィックス(例: 'bob') を使用している場合は、SAMAccountName 形式を使用してドメインにログインする際に問題が発生すること注意してください。このような場合は、UPN 形式を使用するとドメインに確実にログインすることができます。

7. ドメイン参加が成功すると、ドメインへの歓迎を通知する次のメッセージが表示されます。ドメイン参加操作が完了するためには、仮想マシンを再起動する必要があります。

    ![ドメインへようこそ](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## ドメイン参加のトラブルシューティング
### 接続に関する問題
仮想マシンでドメインを検索できない場合は、次のトラブルシューティング手順を参照してください。

- ドメイン サービスを有効にしているのと同じ仮想ネットワークに仮想マシンが接続されていることを確認します。そうでない場合、仮想マシンがドメインに接続できず、そのため、ドメインに参加することできません。

- 仮想マシンが別の仮想ネットワークに接続されている場合は、この仮想ネットワークが、ドメイン サービスを有効にした仮想ネットワークに接続されていることを確認します。

- 管理対象のドメインのドメイン名を使用して、ドメインの ping を試行してください (例: 'ping contoso100.com')。これを実行できない場合は、Azure AD ドメイン サービスを有効にしたページに表示されている IP アドレスに対して ping を試行してください (例: 'ping 10.0.0.4')。ドメインではなく IP アドレスを ping できた場合は、DNS が正しく構成されていない場合があります。ドメインの IP アドレスを仮想ネットワークの DNS サーバーとして構成していない可能性があります。

- 仮想マシンのDNS リゾルバー キャッシュのフラッシュを試行します ('ipconfig/flushdns')。

ドメインに参加するために資格情報の入力を求めるダイアログ ボックスが表示される場合、接続の問題はありません。


### 資格情報関連の問題
資格情報で問題があり、ドメインに参加することができない場合は、次の手順を参照してください。

- 'AAD DC 管理者' グループに属しているユーザー アカウントの資格情報を使用していることを確認します。このグループに属していないユーザーは、マシンを管理対象のドメインに参加させることはできません。

- ファースト ステップ ガイドで説明されている手順に従って[パスワード同期が有効になっている](active-directory-ds-getting-started-password-sync.md)ことを確認します。

- サインインするためには、Azure AD で構成されているユーザーの UPN (例:'bob@domainservicespreview.onmicrosoft.com') を使用します。

- ファースト ステップ ガイドで指定されているとおり、パスワード同期が完了するまで十分な時間待機します。

<!---HONumber=AcomDC_0420_2016-->