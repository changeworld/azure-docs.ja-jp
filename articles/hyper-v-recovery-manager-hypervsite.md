<properties 
	pageTitle="チュートリアル:内部設置型 Hyper-V サイトと Azure の間の保護の設定" 
	description="Azure Site Recovery は、内部設置型 Hyper-V サイトと Azure の間で、仮想マシンのレプリケーション、フェールオーバー、および回復を調整します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# チュートリアル:内部設置型 Hyper-V サイトと Azure の間の保護の設定


<h2><a id="overview" name="overview" href="#overview"></a>概要</h2>
<p>Azure Site Recovery は、さまざまな展開シナリオでの仮想マシンのレプリケーション、フェールオーバー、回復を調整して、ビジネスとワークロードの継続性戦略に貢献します。<p>

このチュートリアルでは、内部設置型サイトの Windows Server 2012 R2 で稼働する Hyper-V サーバーで実行されているワークロードを保護するために、Azure Site Recovery をデプロイする方法について説明します。Hyper-V サーバー上の仮想マシンは、Hyper-V レプリケーションを使用して Azure にレプリケートされます。この展開は、Hyper-V サーバーがオフィスや支社に存在するが、System Center VMM がデプロイされていない場合に、特に役立ちます。


<LI>このチュートリアルでは、最も簡単な展開パスと、最小限の設定 (既定の設定) を使用します。
フル デプロイの手順については、<a href="http://go.microsoft.com/fwlink/?LinkId=522087">計画</a>ガイドおよび<a href=" http://go.microsoft.com/fwlink/?LinkId=522088">デプロイ</a> ガイドを参照してください。</LI>
<LI>Azure Site Recovery の他のデプロイ シナリオについては、「<a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery の概要</a>」を参照してください。</LI>
<LI>このチュートリアルで問題が生じた場合は、<a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery の一般的なエラーのシナリオと解決策</a>に関する wiki 記事を確認するか、<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services フォーラム</a>に質問を投稿してください。</LI>
</UL>

</div>

<h2><a id="before" name="before" href="#before"></a>前提条件</h2>
<div class="dev-callout"> 
<P>開始する前に、すべての準備が整っていることを確認してください。</P>

<UL>
<LI><b>Azure アカウント</b> - Azure アカウントが必要です。お持ちでない場合は、<a href="http://aka.ms/try-azure">Azure の無料評価版</a>に関するページを参照してください。料金については、<a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery Manager の料金</a>に関するページを参照してください。</LI>
<LI><b>Hyper-V</b> - ソースの内部設置型サイトには、Hyper-V の役割を持ち、Windows Server 2012 R2 を実行している少なくとも 1 つのサーバーが必要になります。Hyper-V サーバーには、1 つ以上の仮想マシンが搭載されます。Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続します。</LI>
<LI><b>仮想マシン</b> - 保護する仮想マシンは、Azure の仮想マシンの前提条件に従う必要があります。<a href="http://go.microsoft.com/fwlink/?LinkId=522287">仮想マシンのサポート</a>に関するセクションを参照してください。</LI>

</UL>
<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>チュートリアルの手順</h2>
前提条件を確認した後、以下の手順を実行します。
<UL>
<LI><a href="#vault">手順 1:コンテナーの作成</a>- Azure Site Recovery コンテナーを作成します。</LI>
<LI><a href="#site">手順 2:Hyper-V サイトの作成</a>- 保護する仮想マシンが含まれているすべての Hyper-V サーバーの論理コンテナーとして、Hyper-V サイトを作成します。</LI>
<LI><a href="#download">手順 3:Hyper-V サーバーの準備</a>- 登録キーを生成してから、プロバイダーのセットアップ ファイルをダウンロードします。サイト内の各 Hyper-V サーバー上のファイルを実行し、コンテナー内のサーバーを登録するキーを選択します。</LI>
<LI><a href="#resources">手順 4:リソースの準備</a>- レプリケートされた仮想マシンを格納する Azure のストレージ アカウントを作成します。</LI>
<LI><a href="#protectiongroup">手順 5:保護グループの作成と構成</a>- 保護グループを作成し、保護設定を適用します。保護設定は、グループに追加するすべての仮想マシンに適用されます。</LI>
<LI><a href="#enablevirtual">手順 6:仮想マシンの保護の有効化</a>- 仮想マシンを保護グループに追加し、保護を有効にします。</LI>
<LI><a href="#recovery plans">手順 7:展開をテスト</a>- 仮想マシンのテスト フェールオーバーを実行します。</LI>

</UL>




<a name="vault"></a> <h3>手順 1:コンテナーの作成</h3>

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。


2. <b>[データ サービス]</b> をクリックし、<b>[復旧サービス]</b> を展開して、<b>[Site Recovery コンテナー]</b> をクリックします。


3. <b>[新規作成]</b>、<b>[簡易作成]</b> の順にクリックします。
	
4. <b>[名前]</b> ボックスに、コンテナーを識別する表示名を入力します。

5. <b>[リージョン]</b> ボックスで、コンテナーのリージョンを選択します。サポートされているリージョンについては、<a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery の料金</a>に関するページで利用可能なリージョンを確認してください。

6. <b>[コンテナーの作成]</b> をクリックします。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。メインの [復旧サービス] ページで、コンテナーは <b>[アクティブ]</b> と表示されています。</P>

<a name="site"></a> <h3>手順 2:Hyper-V サイトの作成</h3>

1. [復旧サービス] ページで、コンテナーをクリックして [クイック スタート] ページを開きます。[クイック スタート] は、アイコンを使っていつでも開くことができます。

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. ドロップダウン リストで、**[内部設置型 Hyper-V サイトと Microsoft Azure 間]**を選択します。

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. **[Hyper-V サイトの作成]** で、**[Hyper-V サイトの作成]** をクリックします。サイトの名前を指定して、保存します。

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h3>手順 3:Hyper-V サーバーの準備</h3>
	

1. **[Hyper-V サーバーの準備]** で、**[登録キーのダウンロード]** ファイルをクリックします。
2. **[登録キーのダウンロード]** ページで、サイトの横にある **[ダウンロード]** をクリックします。Hyper-V サーバーが簡単にアクセスできる安全な場所に、キーをダウンロードします。キーは生成後 5 日間有効です。

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. <b>[プロバイダーのダウンロード]</b> をクリックして最新のバージョンを取得します。
5. コンテナーに登録する各 Hyper-V サーバーで、ファイルを実行します。このファイルにより、2 つのコンポーネントがインストールされます。
	- **Azure Site Recovery プロバイダー** - Hyper-V サーバーと Azure Site Recovery ポータルとの間で、通信と調整を処理します。 
	- **Azure Recovery Services エージェント** - ソースの Hyper-V サーバーで稼動する仮想マシンと Azure ストレージの間のデータ転送を処理します。 
6. **[Microsoft Update]** で、更新プログラムの内容を選択できます。この設定を有効にすることで、Microsoft Update のポリシーに従って、プロバイダーとエージェントのアップデートがインストールされます。

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. **[インストール]** で、プロバイダーとエージェントを Hyper-V サーバーにインストールする場所を指定します。

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. インストール後は、セットアップを続行し、サーバーをコンテナーに登録します。

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. **[インターネット接続]** ページで、プロバイダーが Azure Site Recovery に接続する方法を指定します。<b>[既定のシステム プロキシ設定を使用]</b> を選択して、サーバー上に構成されている既定のインターネット接続設定を使用します。 

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. **[コンテナーの設定]** ページで **[参照]** をクリックし、キー ファイルを選択します。Azure Site Recovery のサブスクリプション、コンテナー名、Hyper-V サーバーが属している Hyper-V サイトを指定します。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. サーバーのコンテナーへの登録が開始されます。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. 登録が完了すると、Azure Site Recovery により Hyper-V サーバーからメタデータが取得され、コンテナーの **[サーバー]** ページにある **[Hyper-V サイト]** タブにサーバーが表示されます。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h3><a id="resources"></a>手順 4:リソースの準備</h3>


1. [クイック スタート] ページの **[リソースの準備]** で、**[ストレージ アカウントの作成]** を選択して Azure ストレージ アカウントを作成します (保有していない場合)。アカウントでは geo レプリケーションを有効にする必要があります。アカウントは Azure Site Recovery コンテナーと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h3><a id="protectiongroup"></a>手順 5:保護グループの作成と構成</h3>

<p>保護グループは、同じ保護設定を持つ仮想マシンをグループ化します。保護設定を保護グループに適用すると、これらの設定はグループに追加するすべての仮想マシンに適用されます。</p>
1. **[保護グループの作成と構成]** で、**[保護グループの作成]** をクリックします。前提条件が満たされていない場合は、メッセージが発行され、**[詳細の表示]** をクリックすると、詳細情報が表示されます。

2. **[保護グループ]** タブで、保護グループを追加します。名前、ソースの Hyper-V サイト、ターゲットの **Azure**、Azure Site Recovery のサブスクリプション名、Azure ストレージ アカウントを指定します。

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. **[レプリケーションの設定]** で、既定の設定をそのまま使用します。

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h3><a id="enablevirtual"></a>手順 6:仮想マシンの保護の有効化</h3>
<p>仮想マシンを保護グループに追加し、保護を有効にします。</p>

1. 保護グループの <b>[仮想マシン]</b> タブで、<b>[仮想マシンを保護グループに追加して保護を有効にする]</b> をクリックします。
2. **[仮想マシンの保護の有効化]** ページで、保護する仮想マシンを選択します。 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

保護の有効化ジョブが開始されます。ジョブの進捗状況は **[ジョブ]** タブで追跡できます。保護の最終処理のジョブが実行されると、仮想マシンは、フェールオーバーを実行できる状態になります。保護を有効にし、初期レプリケーションが完了すると、Azure で仮想マシンを表示できます。

Azure Site Recovery で保護された仮想マシンを表示するには、**[保護された項目]**、**[保護グループ]**、 *protectiongroup_name*、**[仮想マシン]** の順に移動します。 


<h3><a id="recoveryplans"></a>手順 7:展開をテスト</h3>

実稼働環境に影響を与えずに、孤立したネットワークでフェールオーバーと復旧のメカニズムをシミュレートし、展開をテストします。そのためには、保護された仮想マシンに対してテスト フェールオーバーを実行します。

1. **[保護された項目]**、**[保護グループ]**、 *protectiongroup_name*、**[仮想マシン]** の順に移動し、フェールオーバーを行う仮想マシンを選択して、**[テスト フェールオーバー]** をクリックします。
2. **[テスト フェールオーバーの確認]** ページで、**[なし]** を選択します。 

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. フェールオーバーの進行状況と状態は、**[ジョブ]** タブで追跡できます。
4. フェールオーバーが**テストの完了**フェーズに達したら、次の手順に従って検証を行います。
	- フェールオーバー後に、Azure ポータルで、レプリカ仮想マシンを表示します。仮想マシンが正常に起動することを確認します。
	- 内部設置型ネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。
	- **[テストの完了]** をクリックして終了します。
	- **[メモ]** をクリックして、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。
	- **[テスト フェールオーバーが完了しました]** をクリックします。テスト環境をクリーンアップして、自動的に電源をオフにし、テスト仮想マシンを削除します。

<h2><a id="runtest" name="runtest" href="#runtest"></a>アクティビティの監視</h2>
<p><b>[ジョブ]</b> タブと <b>[ダッシュボード]</b> を使用して、Azure Site Recovery コンテナーで実行されるメイン ジョブを表示して監視できます。これには、クラウドに対する保護の構成、仮想マシンに対する保護の有効化と無効化、フェールオーバー (計画されたフェールオーバー、計画されていないフェールオーバー、またはテスト フェールオーバー) の実行、計画されていないフェールオーバーのコミットが含まれます。</p>

<p><b>[ジョブ]</b> タブからは、ジョブの表示、ジョブの詳細とエラーの表示、特定の条件に一致するジョブを取得するジョブ クエリの実行、ジョブの Excel へのエクスポート、失敗したジョブの再開を行うことができます。</p>

<p><b>[ダッシュボード]</b> からは、最新バージョンのプロバイダーとエージェントのインストール ファイルのダウンロード、コンテナーの構成情報の取得、コンテナーによって管理されている保護対象の仮想マシンの数の確認、最近のジョブの確認、コンテナー証明書の管理、仮想マシンの再同期を行うことができます。</p>

<p>ジョブの操作とダッシュボードの詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=398534">運用と監視ガイド</a>を参照してください。</p>

<h2><a id="next" name="next" href="#next"></a>次のステップ</h2>
<UL>
<LI>完全な運用環境で Azure Site Recovery の計画とデプロイを実行するには、<a href="http://go.microsoft.com/fwlink/?LinkId=522087">Azure Site Recovery 計画ガイド</a>および <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Azure Site Recovery デプロイ ガイド</a>を参照してください。</LI>
<LI>疑問がある場合は、<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services フォーラム</a>にアクセスしてください。</LI> 
</UL>

<!--HONumber=49-->