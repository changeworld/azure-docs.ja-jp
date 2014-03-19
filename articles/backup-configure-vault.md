<properties linkid="manage-services-recovery-configure-backup-vault" urlDisplayName="バックアップ資格情報コンテナーの構成" pageTitle="Windows Server のバックアップをすばやく簡単に行うための Windows Azure 復旧サービスの構成" metaKeywords="障害復旧" description="このチュートリアルを使用し、Microsoft の Windows Azure クラウド ソリューションのバックアップ サービスを使って、Windows Server をクラウドにバックアップする方法について学習します。" metaCanonical="" services="recovery-services" documentationCenter="" title="Windows Server のバックアップをすばやく簡単に行うための Windows Azure のバックアップの構成" authors=""  solutions="" writer="starra" manager="cynthn" editor="tysonn"  />



<h1><a id="configure-a-backup-vault-tutorial"></a>Windows Server のバックアップをすばやく簡単に行うための Windows Azure のバックアップの構成</h1>
<div class="dev-callout"> 
<strong>注</strong>
 
<p>このチュートリアルを実行するには、Windows Azure アカウントで Windows Azure のバックアップ機能を有効にする必要があります。</p>
<ul> 
<li>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="/en-us/pricing/free-trial/">Windows Azure の無料評価版サイト</a>を参照してください。</li>
 
<li>所有しているアカウントで Windows Azure のバックアップ プレビューを有効にする必要がある場合は、「<a href="/en-us/develop/net/tutorials/create-a-windows-azure-account/#enable" target="_blank">Windows Azure アカウントを作成してプレビュー機能を有効にする</a>」を参照してください。</li>
</ul>
 
<p>バックアップ プレビュー プログラムへの参加を要求した後、自分の状態がアクティブになるのを待ちます。マイクロソフトはすべてのユーザーを自動的に承認しているため、承認に時間はかかりません。</p>
</div>
  

Windows Azure に Windows Server のファイルとデータをバックアップするには、データを保存するリージョンにバックアップ資格情報コンテナーを作成する必要があります。このチュートリアルでは、バックアップの保存に使用する資格情報コンテナーの作成、資格情報コンテナーへの証明書のアップロード、バックアップ エージェントのインストール、および管理ポータルで使用できるバックアップ管理タスクの概要について説明します。

<div class="dev-callout"> 
<strong>開始する前に</strong>
<p>このチュートリアルを完了するには、サーバーをバック
アップ資格情報コンテナーに登録するための X.509 v3 証明書が必要です。この証明書はキー長が 2048 ビット以上で、ローカル コンピューターの Personal 証明書ストアに存在します。サーバーに証明書がインストールされている場合、そのサーバーには証明書の秘密キーが含まれています。Windows Azure の管理ポータルに証明書をアップロードするには、公開キーを .cer 形式ファイルとしてエクスポートする必要があります。</p>

<p>次のいずれかを使用できます。</p>
<ul>
<li>makecert ツールを使用して作成された独自の自己署名証明書。</li>

<li>Microsoft が信頼する証明機関 (CA) によって発行された任意の有効な SSL 証明書。このルート証明書は Microsoft ルート証明書プログラムを介して配布されます。このプログラムの詳細については、「<a href="http://go.microsoft.com/fwlink/p/?LinkId=294666">Windows ルート証明書プログラムのメンバー</a>」を参照してください。</li>
</ul> 

<p>証明書では次の点を確認する必要があります。</p>

<ul>
<li>有効な ClientAuthentication EKU があること</li>

<li>現在有効であること (有効期間内にあり、3 年を超えていない)</li>
</ul>

<p>独自の自己署名証明書を使用するには、次の手順に従います。</p>
<ol>
<li><a href="http://go.microsoft.com/fwlink/p/?LinkID=294662">証明書作成ツール (MakeCert.exe)</a> をダウンロードします。</li>


<li>管理者特権でコマンド プロンプト (cmd.exe) を開き、次のコマンドを実行します。<i>CertificateName</i> は証明書の名前に置き換え、-e の後に実際の有効期限を指定します。
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code></li>
</ol>
<p>
証明書の作成に使用したものとは違うサーバーを登録する場合は、.pfx ファイル (秘密キーが含まれます) をエクスポートし、そのファイルをもう一方のサーバーにコピーして、Personal 証明書ストアにインポートします。
</p>
<p>
資格情報コンテナー証明書のアップロード処理の詳しい手順と、.pfx ファイルのエクスポートとインポートの詳細については、「<a href="http://go.microsoft.com/fwlink/p/?LinkID=294662">コンテナーの証明書の管理</a>」を参照してください。</p>
</div>

<h2><a id="create"></a>バックアップ コンテナーの作成</h2>

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。

	[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

2. **[復旧サービス]**、**[新規作成]** の順にクリックし、**[バックアップ資格情報コンテナー]** をポイントして、**[簡易作成]** をクリックします。

	![新しいバックアップ コンテナー][new-backup-vault]

3. **[名前]** ボックスに、バックアップ資格情報コンテナーを識別する表示名を入力します。

4. **[リージョン]** ボックスで、バックアップ資格情報コンテナーのリージョンを選択します。

5. **[サブスクリプション]** ボックスに、バックアップ資格情報コンテナーを使用する Windows Azure サブスクリプションを入力します。


6. **[バックアップ資格情報コンテナーの作成]** をクリックします。

	バックアップ資格情報コンテナーが作成されるまで時間がかかることがあります。状態を確認するには、ポータルの下部にある通知を監視します。バックアップ資格情報コンテナーが作成されたら、コンテナーが正常に作成されたことを示すメッセージが表示され、復旧サービスのリソースに **[オンライン]** として表示されます。

	![バックアップ コンテナーの作成][backup-vault-create]

<h2><a id="upload"></a>証明書のアップロード</h2>
1. [管理ポータル](https://manage.windowsazure.com)にサインインします。

2. **[復旧サービス]**、証明書によって識別されるバックアップ資格情報コンテナーの名前、**[証明書の管理]** の順にクリックします。
	
	![証明書の管理][manage-cert]

3. **[証明書の管理]** ダイアログで [コンピューターの参照] をクリックし、バックアップ資格情報コンテナーで使用する .cer ファイルを特定します。
<h2><a id="download"></a>バックアップ エージェントのダウンロードとインストール</h2>
1. [管理ポータル](https://manage.windowsazure.com)にサインインします。

2. **[復旧サービス]** をクリックし、バックアップ資格情報コンテナーの名前をクリックして、資格情報コンテナー ダッシュボードを表示します。

3.  **[エージェントのインストール]** をクリックします。
	
	![エージェントのインストール][install-agent]
4. ダウンロードするエージェントを選択できるダイアログが表示されます。
	* Windows Server 2012 および System Center 2012 SP1 - Data Protection Manager のエージェント
	* Windows Server 2012 Essentials 用エージェント
5. 適切なエージェントを選択します。エージェント ソフトウェアをダウンロードする Microsoft ダウンロード センターにリダイレクトされます。詳細情報

	* [Windows Server 2012 および System Center 2012 SP1 - Data Protection Manager の Windows Azure のバックアップ エージェントのインストール](http://technet.microsoft.com/en-us/library/hh831761.aspx#BKMK_installagent)
	* [Windows Server 2012 Essentials の Windows Azure のバックアップ エージェントのインストール](http://technet.microsoft.com/en-us/library/jj884318.aspx)

エージェントがインストールされたら、適切なローカル管理インターフェイス (Microsoft 管理コンソール スナップイン、System Center Data Protection Manager コンソール、Windows Server Essentials ダッシュボードなど) を使用して、サーバーのバックアップ ポリシーを構成できます。

<h2><a id="manage"></a>バックアップ コンテナーとサーバーの管理</h2>
1. [管理ポータル](https://manage.windowsazure.com)にサインインします。

2. **[復旧サービス]** をクリックし、バックアップ資格情報コンテナーの名前をクリックして、資格情報コンテナー ダッシュボードを表示します。ここでは、次のタスクを実行できます。
	* **証明書の管理**。以前にアップロードされた証明書の更新に使用されます。
	* **削除**。現在のバックアップ コンテナーを削除します。バックアップ コンテナーがもう使用されていない場合は、そのコンテナーを削除してストレージ領域を解放できます。**[削除]** は、登録されているサーバーすべてが資格情報コンテナーから削除された場合にのみ有効になります。

3. **[保護された項目]** をクリックして、サーバーからバックアップされた項目を表示します。この一覧は、情報の提供のみを目的としています。
![保護された項目][protected-itmes]

4. **[サーバー]** をクリックして、この資格情報コンテナーに登録されたサーバーの名前を表示します。ここでは、次のタスクを実行できます。
	* **再登録を許可**。サーバーに対してこのオプションが選択されている場合、エージェントで登録ウィザードを使用して、サーバーをバックアップ コンテナーにもう一度登録できます。証明書でエラーが発生したとき、またはサーバーを再構築する必要があった場合などに、再登録が必要になる可能性があります。再登録を行えるのは、サーバー名ごとに 1 回だけです。
	* **削除**。バックアップ コンテナーからサーバーを削除します。サーバーに関連付けられている保存されたデータすべてがすぐに削除されます。

		![削除されたサーバー][deleted-server]

<h2><a id="next"></a>次の手順</h2>

- Windows Azure のバックアップの詳細については、「[Windows Azure のバックアップの概要](http://go.microsoft.com/fwlink/p/?LinkId=222425)」を参照してください。

- [Windows Azure のバックアップ フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)にアクセスします。

[new-backup-vault]: ./media/backup-configure-vault/RS_howtobackup1.png
[backup-vault-create]: ./media/backup-configure-vault/RS_howtobackup2.png
[manage-cert]: ./media/backup-configure-vault/RS_howtoupload1.png
[install-agent]: ./media/backup-configure-vault/RS_howtodownload1.png
[deleted-server]: ./media/backup-configure-vault/RS_deletedserver.png
[protected-itmes]: ./media/backup-configure-vault/RS_protecteditems.png


