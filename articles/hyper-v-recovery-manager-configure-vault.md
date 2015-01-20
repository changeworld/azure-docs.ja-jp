<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Azure Site Recovery の概要:Hyper-V レプリケーションによる内部設置型サイトの内部設置型 VMM サイトへの保護、"metakeywords="Azure Site Recovery, VMM, クラウド, 障害復旧" description="Azure Site Recovery は、レプリケーション、フェールオーバーおよび内部設置型 VMM のサイト間での HYPER-V 仮想マシンの回復を調整します。" metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery:  On-Premises to On-Premises VMM Site Protection with Hyper-V Replication" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/19/2014" ms.author="raynew" />


# Azure Site Recovery の概要:Hyper-V レプリケーションによる内部設置型サイトの内部設置型 VMM サイトへの保護


<div class="dev-callout"> 

<p>Azure Site Recovery は、さまざまな展開シナリオでの仮想マシンのレプリケーション、フェールオーバー、回復を調整して、ビジネスとワークロードの継続性戦略に貢献します。<p>

<P>このチュートリアルでは、Hyper-V レプリケーションを使用して、内部設置型 VMM サイトで稼働するワークロードを別の内部設置型 VMM サイトへ保護する機能を調整および自動化するために、Azure Site Recovery をデプロイする方法について説明します。このチュートリアルでは、可能な限り、最も簡単な展開パスと既定の設定を使用します。</P>

<UL>
<LI>フル デプロイの手順については、「<a href="http://go.microsoft.com/fwlink/?LinkId=321294">計画</a>および<a href="http://go.microsoft.com/fwlink/?LinkId=321295">デプロイ</a> ガイド」をお読みください。</LI>
<LI>Azure Site Recovery の他のデプロイ シナリオについては、「<a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery の概要</a>」をお読みください。</LI>
<LI>このチュートリアルで問題が生じた場合は、wiki 記事「<a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery:Common Error Scenarios and Resolutions (一般的なエラーのシナリオと解決策)</a>」を確認するか、<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services Forum (Azure 復旧サービス フォーラム)</a> に質問を投稿してください。</LI>
</UL>

</div>


<h2><a id="before"></a>前提条件</h2> 
<div class="dev-callout"> 
<P>チュートリアルを開始する前に、すべての準備が整っていることを確認してください。</P>

<UL>
<LI><b>Azure アカウント</b> - Azure アカウントが必要です。お持ちでない場合は、<a href="http://aka.ms/try-azure">Azure の無料評価版</a>を参照してください。料金については、「<a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery Manager Pricing Details (Azure Site Recovery Manager の料金詳細)</a>」を参照してください。</LI>
<LI><b>VMM サーバー</b> - System Center 2012 SP1 または System Center 2012 R2 で実行されている、少なくとも 1 つの VMM サーバーが必要です。</LI>
<LI><b>VMM クラウド</b> - 保護するソース VMM サーバー上で少なくとも 1 つのクラウドが必要であり、ターゲット VMM サーバー上でも 1 つのクラウドが必要です。1 つの VMM サーバーを実行している場合は、2 つのクラウドが必要です。保護するプライマリ クラウドには、次のものが含まれている必要があります。<UL>
	<LI>1 つ以上の VMM ホスト グループ</LI>
	<LI>各ホスト グループ内に 1 つ以上の Hyper-V ホスト サーバーまたはクラスター。</LI>
	<li>クラウド内のソース Hyper-V サーバー上に配置された 1 つ以上の仮想マシン。</li>
		</UL></LI>
<LI>**ネットワーク** - 必要に応じてネットワーク マッピングを構成して、フェールオーバー後にレプリカの仮想マシンが Hyper-V ホスト サーバーにオプションで配置されるようにし、適切な VM ネットワークに接続することができます。ネットワーク マッピングを有効にすると、プライマリの場所にある仮想マシンはネットワークに接続され、ターゲットの場所にあるそのレプリカはマップされたネットワークに接続されます。ネットワーク マッピングを構成しないと、仮想マシンはフェールオーバー後に VM ネットワークに接続されません。このチュートリアルでは、最も簡単なチュートリアルの設定について説明しているので、ネットワーク マッピングは含まれていません。詳細については、</LI>
	<UL>
	<LI>計画ガイドの「<a href="http://go.microsoft.com/fwlink/?LinkId=522289">ネットワーク マッピング</a>」をお読みください。</LI>
	<LI>デプロイ ガイドの「<a href="http://go.microsoft.com/fwlink/?LinkId=522293">ネットワーク マッピングの有効化</a>」をお読みください。</LI>
	</UL></LI>

</UL>



<h2><a id="tutorial"></a>チュートリアルの手順</h2> 

前提条件を確認した後、以下の手順を実行します。
<UL>
<LI><a href="#vault">手順 1.コンテナーの作成</a> - Azure Site Recovery コンテナーを作成します。</LI>
<LI><a href="#download">手順 2.各 VMM サーバーへのプロバイダー アプリケーションのインストール</a> - コンテナーで登録キーを生成し、プロバイダーのセットアップ ファイルをダウンロードします。プロバイダーをインストールし、コンテナーに VMM サーバーを登録するには、各 VMM サーバーでセットアップを実行します。</LI>
<LI><a href="#clouds">手順 3.クラウドの保護の構成</a> - VMM クラウドの保護設定を構成します。これらの保護設定は、Azure Site Recovery 保護を有効にするクラウド内のすべての仮想マシンに適用されます。</LI>
<LI><a href="#storagemapping">手順 4.ストレージ マッピングの構成</a> - レプリケーション データの保存場所を指定する場合は、ストレージ マッピングを構成できます。これにより、ソース VMM サーバー上のストレージ分類は、対象サーバー上のストレージ分類にマップされます。</LI>
<LI><a href="#enablevirtual">手順 5.仮想マシンの保護の有効化</a> - 保護する VMM クラウドに配置された仮想マシンの保護を有効にします。</LI>
<LI><a href="#recovery plans">手順 6.復旧計画の構成と実行</a> - 復旧計画を作成して、計画を確認するためにテスト フェールオーバーを実行します。</LI>

</UL>




<a name="vault"></a> <h2>手順 1.コンテナーの作成</h2>

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。


2. <b>[データ サービス]</b> をクリックし、<b>[復旧サービス]</b> を展開して、<b>[Site Recovery コンテナー]</b> をクリックします。


3. <b>[新規作成]</b>、<b>[簡易作成]</b> の順にクリックします。
	
4. <b>[名前]</b> ボックスに、コンテナーを識別する表示名を入力します。

5. <b>[リージョン]</b> ボックスで、コンテナーのリージョンを選択します。サポートされているリージョンを確認するには、「<a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery Pricing Details (Azure Site Recovery の料金の詳細)</a>」で利用可能地域を参照してください。

6. <b>[コンテナーの作成]</b> をクリックします。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。メインの [復旧サービス] ページで、コンテナーは <b>[アクティブ]</b> と表示されています。</P>

<a name="upload"></a> <h2>手順 2.コンテナーの構成</h2>


1. <b>[復旧サービス]</b> ページで、コンテナーをクリックして [クイック スタート] ページを開きます。[クイック スタート] は、アイコンを使っていつでも開くことができます。

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. ドロップダウン リストで、**[2 つの内部設置型 Hyper-V サイト間]** を選択します。
3. **[VMM サーバーの準備]** で、**[登録キーの生成]** ファイルをクリックします。キーは生成後 5 日間有効です。VMM サーバーにファイルをコピーします。このファイルは、プロバイダーのセットアップ時に必要になります。

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png)
	



<a name="download"></a> <h2>手順 3.Azure Site Recovery プロバイダーのインストール</h2>
	

1. <b>[クイック スタート]</b> ページの **[VMM サーバーの準備]** で、<b>[VMM サーバーへのインストール用の Microsoft Azure Site Recovery プロバイダーのダウンロード]</b> をクリックして、最新バージョンのプロバイダー インストール ファイルを取得します。

2. ソース VMM サーバーとターゲット VMM サーバーでこのファイルを実行します。

3. **[前提条件の確認]** で、VMM サービスを停止して、プロバイダーのセットアップを開始します。VMM サービスは停止し、セットアップの終了時に自動的に再起動します。 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. **[Microsoft Update]** で、更新プログラムの内容を選択できます。この設定を行うことで、設定した Microsoft Update のポリシーに従って、プロバイダーの有効な更新がインストールされます。

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

プロバイダーのインストール後は、セットアップを続行し、サーバーをコンテナーに登録します。

5. [インターネット接続] ページで、VMM サーバーで実行中のプロバイダーがインターネットに接続する方法を指定します。<b>[既定のシステム プロキシ設定を使用]</b> を選択して、サーバー上に構成されている既定のインターネット接続設定を使用します。

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. **[登録キー]** で、Azure Site Recovery からダウンロードして VMM サーバーにコピーした登録キーを選択します。
7. **[コンテナー名]** で、サーバーが登録されるコンテナーの名前を確認します。
8. **[サーバー名]** に、コンテナーで VMM サーバーを識別する表示名を入力します。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)


9. **[初期クラウド メタデータ]** 同期で、VMM サーバー上のすべてのクラウドのメタデータをコンテナーと同期するかどうか選択します。この操作は、各サーバーで 1 回のみ実行する必要があります。すべてのクラウドを同期したくない場合は、この設定をオフのままにして、VMM コンソールのクラウドのプロパティで各クラウドを個別に同期できます。


7. **[データの暗号化]** で、Azure 保護されているデータの暗号化に使用する証明書を生成します。 
このオプションは、このチュートリアルで説明するシナリオには関係ありません。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. <b>[登録]</b> をクリックしてプロセスを完了します。登録後に、VMM サーバーからのメタデータが、Azure Site Recovery によって取得されます。サーバーは、コンテナーの **[サーバー]** ページの <b>[リソース]</b> タブに表示されます。




<h2><a id="clouds"></a>手順 4.クラウドの保護設定の構成</h2>

VMM サーバーを登録した後、クラウドの保護設定を構成することができます。プロバイダーのインストール時にオプション **[コンテナーとのクラウド データの同期]** を有効にしたので、VMM サーバー上のすべてのクラウドが、コンテナーの <b>[保護された項目]</b> タブに表示されます。

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)

1. [クイック スタート] ページで、**[VMM クラウドの保護の設定]** をクリックします。
2. **[保護された項目]** タブで、構成するクラウドを選択し、**[構成]** タブに移動します。以下の点に注意してください。
3. <b>[ターゲット]</b> で、<b>[VMM]</b> を選択します。
4. <b>[ターゲットの場所]</b> で、復旧時に使用するクラウドを管理するオンサイト VMM サーバーを選択します。
4. <b>[ターゲット クラウド]</b> で、ソース クラウド内の仮想マシンのフェールオーバー時に使用するターゲット クラウドを選択します。以下の点に注意してください。
	- 保護する仮想マシンの復旧要件を満たしたターゲット クラウドを選択することをお勧めします。
	- クラウドは、プライマリ クラウドまたはターゲット クラウドとして、1 つのクラウド ペアに属することしかできません。

6. <b>[コピーの頻度]</b> で、既定の設定をそのまま使用します。この値は、ソースとターゲットの場所の間でデータが同期される頻度を指定します。この設定は、Hyper-V ホストが Windows Server 2012 R2 を実行している場合にのみ該当します。他のサーバーでは、5 分間という既定の設定が使用されます。
7. <b>[追加の復旧ポイント]</b> で、規定の設定をそのまま使用します。この値は、追加の復旧ポイントを作成するかどうかを指定します。既定値である 0 を使用する場合は、プライマリ仮想マシンに対応する最新の復旧ポイントのみが、レプリカのホスト サーバーに格納されます。
8. <b>[アプリケーションの整合性スナップショットの頻度]</b> では、既定の設定をそのまま使用します。この値は、スナップショットを作成する頻度を指定します。スナップショットは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。チュートリアルでこの値を設定する場合は、構成する追加の復旧ポイントの値より小さくするように注意してください。
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png)
9. <b>[データ転送の圧縮]</b> で、転送されるレプリケート済みデータを圧縮するかどうかを指定します。 
10. <b>[認証]</b> で、プライマリおよび復旧用の Hyper-V ホスト サーバー間でトラフィックを認証する方法を指定します。動作している Kerberos 環境が既に構成済みの場合を除き、このチュートリアルでは、[HTTPS] を選択します。Azure Site Recovery は、HTTPS 認証に使用する証明書を自動的に構成します手動で構成する必要はありません。この設定は、Windows Server 2012 R2 で実行されている Hyper-V ホスト サーバーだけに関連することに注意してください。
11. <b>[ポート]</b> で、既定の設定をそのまま使用します。この値は、ソースとターゲットの Hyper-V ホスト コンピューターがレプリケーション トラフィックをリッスンするポートの番号を設定します。
12. <b>[レプリケーション方法]</b> で、通常のレプリケーションを開始する前に実行する初期レプリケーションで、ソースからターゲットにデータを複製する際の処理方法を指定します。 
	- <b>ネットワーク経由</b> - ネットワーク経由でデータをコピーすると、時間がかかり大量のリソースを消費します。クラウドの仮想マシンで使用する仮想ハード ディスクの容量が比較的小さい場合、そして、プライマリ VMM サーバーがセカンダリ VMM サーバーに高速回線で接続されている場合に、このオプションを使用することをお勧めします。コピーを直ちに開始することも、実行時刻を選択することもできます。ネットワーク レプリケーションを使用する場合は、ピーク時間以外に実行することをお勧めします。
	- <b>オフライン</b> - この方法は、外部メディアを使用して初期レプリケーションを実行することを指定します。これは、ネットワーク性能の低下を避ける場合、または、場所が地理的に離れている場合に便利です。この方法を使用するには、ソース クラウド上のエクスポート場所と、ターゲット クラウド上のインポート場所を指定します。仮想マシンの保護を有効にすると、指定されたエクスポート場所に仮想ハード ディスクがコピーされます。それをターゲット サイトに送信して、インポート場所にコピーします。インポートされた情報はレプリカ仮想マシンに自動的にコピーされます。オフライン レプリケーションの前提条件の完全な一覧については、<a href="http://go.microsoft.com/fwlink/?LinkId=323469">ステップ 3 をお読みください。Configure protection settings for VMM clouds (VMM クラウドの保護設定の構成)</a>
13. クラウドのプロパティにある [仮想マシン] タブの **[仮想マシンの保護を削除する]** オプションを選択して、仮想マシンの保護を停止する場合に削除するレプリカ仮想マシンを、**[レプリカ仮想マシンの削除]** を選択して指定します。この設定を有効にすると、保護を無効にしたときに、仮想マシンの Azure Site Recovery からの削除、VMM コンソールでの仮想マシンの Site Recovery 設定の削除、レプリカの削除が行われます。
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png)

<p>この設定を保存するとジョブが作成され、これを <b>[ジョブ]</b> タブで監視できます。VMM ソース クラウド内のすべての Hyper-V ホスト サーバーは、レプリケーション用に構成されます。クラウド設定は <b>[構成]</b> タブで変更できます。ターゲットの場所やターゲット クラウドを変更する場合は、クラウド構成を削除して、クラウドを再構成する必要があります。</p>

<h2><a id="storagemapping"></a>手順 5.ストレージ マッピングの構成</h2>

<p>このチュートリアルでは、テスト環境で Azure Site Recovery を展開する最も簡単な方法について説明します。このチュートリアルの一環としてストレージ マッピングを構成する場合は、展開ガイドの「<a href="http://go.microsoft.com/fwlink/?LinkId=402535">ストレージ マッピングの構成</a>」の手順に従ってください。</p>


<h2><a id="enablevirtual"></a>手順 6.仮想マシンの保護の有効化</h2>
<p>サーバー、クラウド、およびネットワークを正しく構成した後で、クラウド内の仮想マシンの保護を有効にすることができます。</p>
<OL>
<li>仮想マシンが配置されているクラウドの [<b>仮想マシン</b>] タブで、<b>[保護の有効化]</b> をクリックし、<b>[仮想マシンの追加]</b> を選択します。 </li>
<li>クラウド内の仮想マシンのリストから、保護する仮想マシンを選択します。</li> 
</OL>

![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png)


<P>**[ジョブ]** タブで、初期レプリケーションを含む [保護の有効化] アクションの進捗状況を確認します。保護の最終処理のジョブが実行されると、仮想マシンは、フェールオーバーを実行できる状態になります。保護が有効され仮想マシンが複製されると、Azure でそれらの状態を表示できます。</p>



![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="recoveryplans"></a>手順 7.展開をテスト</h2>

展開をテストして、仮想マシンとデータが想定どおりにフェールオーバーされるかどうかを確認します。そのためには、レプリケーション グループを選択して、復旧計画を作成します。次に、その計画に従ってテスト フェールオーバーを実行します。

1. **[復旧計画]** タブで、**[復旧計画の作成]** をクリックします。
2. 復旧計画の名前、ソースおよびターゲット VMM サーバーを指定します。ソース サーバーには、フェールオーバーと復旧が有効になった仮想マシンが必要になります。**[Hyper-V]** を選択し、Hyper-V レプリケーションが構成されたクラウドのみを表示します。

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP1.png)

3. **[仮想マシンの選択]** で、レプリケーション グループを選択します。レプリケーション グループに関連付けられているすべての仮想マシンを選択し、復旧計画に追加します。これらの仮想マシンは、復旧計画の既定のグループ「グループ 1」に追加されます。必要に応じて、他のグループを追加することもできます。レプリケーションの後、仮想マシンは復旧計画のグループの順序に従って起動することに注意してください。

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP2.png)	

4. 復旧計画が作成されると、**[復旧計画]** タブに一覧が表示されます。 
5. **[復旧計画]**] タブで、計画を選択し、**[テスト フェールオーバー]** をクリックします。
6. **[テスト フェールオーバーの確認]** ページで、**[なし]** を選択します。このオプションが有効な場合、フェールオーバーしたレプリカ仮想マシンはネットワークに接続されないことに注意してください。このテストでは、仮想マシンが想定どおりにフェールオーバーすることをテストしますが、レプリケーションのネットワーク環境はテストしません。より包括的なテスト フェールオーバーを実行する場合は、「<a href="http://go.microsoft.com/fwlink/?LinkId=522291">Test an on-premises deployment on MSDN (MSDN で内部設置型展開をテストする)</a>」をご覧ください。

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


7. レプリカ仮想マシンが存在するホストと同じホスト上に、テスト仮想マシンが作成されます。これは、レプリカ仮想マシンが配置されているクラウドには追加されません。
8. レプリケーション後に、レプリカ仮想マシンは、プライマリ仮想マシンの IP アドレスとは異なる IP アドレスを保有します。DHCP からアドレスを発行している場合、DNS は自動的に更新されます。DHCP を使用していない場合に、アドレスが同じかどうかを確認するには、複数のスクリプトを実行する必要があります。
9. 次のサンプル スクリプトを実行して、IP アドレスを取得します。
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. 前のサンプル スクリプトを使用して取得した IP アドレスを指定して、次のサンプル スクリプトを実行し、DNS を更新します。

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**


<h3><a id="runtest"></a>アクティビティの監視</h3>
<p><b>[ジョブ]</b> タブと <b>[ダッシュボード]</b> を使用して、Azure Site Recovery コンテナーで実行されるメイン ジョブを表示して監視できます。これには、クラウドに対する保護の構成、仮想マシンに対する保護の有効化と無効化、フェールオーバー (計画されたフェールオーバー、計画されていないフェールオーバー、またはテスト フェールオーバー) の実行、計画されていないフェールオーバーのコミットが含まれます。</p>

<p><b>[ジョブ]</b> タブからは、ジョブの表示、ジョブの詳細とエラーの表示、特定の条件に一致するジョブを取得するジョブ クエリの実行、ジョブの Excel へのエクスポート、失敗したジョブの再開を行うことができます。</p>

<p><b>[ダッシュボード]</b> からは、最新バージョンのプロバイダーとエージェントのインストール ファイルのダウンロード、コンテナーの構成情報の取得、コンテナーによって管理されている保護対象の仮想マシンの数の確認、最近のジョブの確認、コンテナー証明書の管理、仮想マシンの再同期を行うことができます。</p>

<p>ジョブの操作とダッシュボードの詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkId=398534">Operations and Monitoring Guide (運用と監視ガイド)</a>」を参照してください。</p>
	
<h2><a id="next"></a>次のステップ</h2>
<UL>
<LI>完全な運用環境で Azure Site Recovery の計画と展開を実行するには、「<a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planning Guide for Azure Site Recovery (Azure Site Recovery 計画ガイド)</a>」と「<a href="http://go.microsoft.com/fwlink/?LinkId=321295">Deployment Guide for Azure Site Recovery (Azure Site Recovery デプロイ ガイド)</a>」を参照してください。</LI>
<LI>疑問がある場合は、「<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services Forum (Azure 復旧サービス フォーラム)</a>」にアクセスしてください。</LI> 
</UL>

<!--HONumber=35.2-->
