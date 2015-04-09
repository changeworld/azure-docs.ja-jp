<properties 
	pageTitle="チュートリアル:内部設置型 VMM サイトと Azure の間の保護の設定" 
	description="Azure Site Recovery は、内部設置型 VMM クラウドに配置された Hyper-V 仮想マシンの Azure へのレプリケーション、フェイルオーバー、および回復を調整します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# チュートリアル:内部設置型 VMM サイトと Azure の間の保護の設定

<h2><a id="overview" name="overview" href="#overview"></a>概要</h2>
<p>Azure Site Recovery は、さまざまな展開シナリオでの仮想マシンのレプリケーション、フェールオーバー、回復を調整して、ビジネスとワークロードの継続性戦略に貢献します。<p>

<P>このチュートリアルでは、Hyper-V レプリケーションを使用して、内部設置型 VMM サイトと Azure との間の保護を調整するため、Azure Site Recovery をデプロイする方法について説明します。このチュートリアルでは、可能な限り、最も簡単な展開パスと既定の設定を使用します。</P>

<UL>
<LI>フル デプロイの詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=321294">計画</a>ガイドおよび<a href="http://go.microsoft.com/fwlink/?LinkId=402679">デプロイ</a> ガイドを参照してください。</LI>
<LI>Azure Site Recovery の他のデプロイ シナリオについては、「<a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery の概要</a>」を参照してください。</LI>
<LI>このチュートリアルで問題が生じた場合は、<a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery の一般的なエラーのシナリオと解決策</a>に関する wiki 記事を確認するか、<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services フォーラム</a>に質問を投稿してください。</LI>
</UL>


<h2><a id="prerequisites" name="prerequisites" href="#prerequisites"></a>前提条件</h2>
<div class="dev-callout"> 
<P>チュートリアルを開始する前に、すべての準備が整っていることを確認してください。</P>

<UL>
<LI><b>Azure アカウント</b> - Azure アカウントが必要です。お持ちでない場合は、<a href="http://aka.ms/try-azure">Azure の無料評価版</a>に関するページを参照してください。サブスクリプションの料金については、<a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery Manager の料金</a>に関するページを参照してください。</LI>

<LI><b>Azure ストレージのアカウント</b> - Azure にレプリケートしたデータを格納するために Azure ストレージのアカウントが必要になります。アカウントではジオ (主要地域) レプリケーションを有効にする必要があります。アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。Azure ストレージのセットアップの詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkId=398704">Microsoft Azure ストレージの概要</a>」を参照してください。</LI><LI><b>VMM サーバー</b> - System Center 2012 R2 上で実行される VMM サーバー。</LI>
<LI><b>VMM クラウド</b> - VMM サーバー上の少なくとも 1 つのクラウド。このクラウドには以下のものが含まれている必要があります。
	<UL>
	<LI>1 つ以上の VMM ホスト グループ</LI>
	<LI>各ホスト グループ内に 1 つ以上の Hyper-V ホスト サーバーまたはクラスター。</LI>
	<li>クラウド内のソース Hyper-V サーバー上に配置された 1 つ以上の仮想マシン。仮想マシンは世代 1 にする必要があります。</li>
		</UL></LI>	
<LI><b>仮想マシン</b> - Azure 要件に準じた仮想マシンが必要です。計画ガイドの「<a href="http://go.microsoft.com/fwlink/?LinkId=402602">前提条件とサポート</a>」を参照してください。</LI>
<LI>Azure にフェイルオーバーするための仮想マシンのサポート要件の詳細については、を参照してください。  </LI>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>チュートリアルの手順</h2>
前提条件を確認した後、以下の手順を実行します。
<UL>

<LI><a href="#vault">手順 1:コンテナーの作成</a>- Azure Site Recovery コンテナーを作成します。</LI>
<LI><a href="#download">手順 2:VMM サーバーへのプロバイダー アプリケーションのインストール</a>- コンテナーで登録キーを生成してから、プロバイダーのセットアップ ファイルをダウンロードします。プロバイダーをインストールし、コンテナーに VMM サーバーを登録するには、VMM サーバーでセットアップを実行します。</LI>
<LI><a href="#storage">手順 3:Azure のストレージ アカウントの追加</a>- アカウントが存在しない場合に作成します。 </LI>
<LI><a href="#agent">手順 4:エージェント アプリケーションのインストール</a>- 保護する VMM クラウド内にある各 Hyper-V ホストに、Microsoft Azure Recovery Services エージェントをインストールします。</LI>
<LI><a href="#clouds">手順 5:クラウドの保護の構成</a>- VMM クラウドの保護設定を構成します。</LI>
<LI><a href="#NetworkMapping">手順 6:ネットワーク マッピングの構成</a>- ソース VM ネットワークをターゲット Azure ネットワークにマッピングするネットワーク マッピングを任意で構成できます。</LI>
<LI><a href="#virtualmachines">手順 7:仮想マシンの保護の有効化</a>- 保護されている VMM クラウド内にある仮想マシンの保護を有効にします。</LI>
<LI><a href="#test">手順 8:展開をテスト</a>- 1 台の仮想マシンに対するテスト フェールオーバーを実行することや、復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。</LI>
</UL>



<a name="vault"></a> <h3>手順 1:コンテナーの作成</h3>

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。


2. <b>[データ サービス]</b> をクリックし、<b>[復旧サービス]</b> を展開して、<b>[Site Recovery コンテナー]</b> をクリックします。

3. <b>[新規作成]</b>、<b>[簡易作成]</b> の順にクリックします。
	

4. <b>[名前]</b> ボックスに、コンテナーを識別する表示名を入力します。

5. <b>[リージョン]</b> ボックスで、コンテナーのリージョンを選択します。利用可能なリージョンには、東アジア、西ヨーロッパ、米国西部、米国東部、北ヨーロッパ、東南アジアが含まれています。
6. <b>[コンテナーの作成]</b> をクリックします。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。メインの [復旧サービス] ページで、コンテナーは <b>[アクティブ]</b> と表示されています。</P>





 <a name="download"></a> <h3>手順 2:登録キーの生成と Azure Site Recovery プロバイダーのインストール</h3>
 

1. <b>[復旧サービス]</b> ページで、コンテナーをクリックして [クイック スタート] ページを開きます。[クイック スタート] は、アイコンを使っていつでも開くことができます。

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. ドロップダウン リストで、**[内部設置型 Hyper-V サイトと Microsoft Azure 間]**を選択します。
3. **[VMM サーバーの準備]** で、**[登録キーの生成]** ファイルをクリックします。キーは生成後 5 日間有効です。VMM サーバーにファイルをコピーします。このファイルは、プロバイダーのセットアップ時に必要になります。

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png)

4. <b>[クイック スタート]</b> ページの **[VMM サーバーの準備]** で、<b>[VMM サーバーへのインストール用の Microsoft Azure Site Recovery プロバイダーのダウンロード]</b> をクリックして、最新バージョンのプロバイダー インストール ファイルを取得します。

2. ソース VMM サーバーでこのファイルを実行します。


3. **[前提条件の確認]** で、VMM サービスを停止して、プロバイダーのセットアップを開始します。VMM サービスは停止し、セットアップの終了時に自動的に再起動します。

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. **[Microsoft Update]** で、更新プログラムの内容を選択できます。この設定を行うことで、設定した Microsoft Update のポリシーに従って、プロバイダーの有効な更新がインストールされます。

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

プロバイダーのインストール後は、セットアップを続行し、サーバーをコンテナーに登録します。

5. **[インターネット接続]** で、VMM サーバーで実行中のプロバイダーがインターネットに接続する方法を指定します。<b>[既定のシステム プロキシ設定を使用]</b> を選択して、サーバー上に構成されている既定のインターネット接続設定を使用します。

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. **[登録キー]** で、Azure Site Recovery からダウンロードして VMM サーバーにコピーした登録キーを選択します。
7. **[コンテナー名]** で、サーバーが登録されるコンテナーの名前を確認します。
8. **[サーバー名]** に、コンテナーで VMM サーバーを識別する表示名を入力します。

	
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)

8. **[初期クラウド メタデータ]** 同期で、VMM サーバー上のすべてのクラウドのメタデータをコンテナーと同期するかどうか選択します。この操作は、各サーバーで 1 回のみ実行する必要があります。すべてのクラウドを同期したくない場合は、この設定をオフのままにして、VMM コンソールのクラウドのプロパティで各クラウドを個別に同期できます。


9. **[データ暗号化]** で、データの暗号化用に自動的に生成された SSL 証明書を保存する場所を指定します。この証明書は、Azure Site Recovery ポータル内で Azure によって保護されているクラウドのデータ暗号化が有効な場合に使用されます。この証明書を安全な場所に保管します。Azure へのフェールオーバーを実行する際に、暗号化されたデータの暗号化を解除するために、この証明書を選択します。 
内部設置型サイトから別の内部設置型サイトにレプリケートする場合は、このオプションは該当しません。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. <b>[登録]</b> をクリックしてプロセスを完了します。登録後に、VMM サーバーからのメタデータが、Azure Site Recovery によって取得されます。サーバーは、コンテナーの **[サーバー]** ページの <b>[リソース]</b> タブに表示されます。

<h3><a id="storage"></a>手順 3:Azure のストレージ アカウントの作成</h3>
Azure ストレージ アカウントがない場合は、**[Azure ストレージ アカウントの追加]** をクリックします。アカウントでは geo レプリケーションを有効にする必要があります。アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。

<P>このチュートリアルを利用すると、オンプレミスと Azure 間のデプロイの Azure Site Recovery の概念実証をすばやく行うことができます。このチュートリアルでは、可能な場合は、最も短時間ですむ方法と既定の設定を使用しています。このチュートリアルでは、Azure Site Recovery 資格情報コンテナーの作成、ソース VMM サーバーへの Azure Site Recovery プロバイダーのインストール、VMM クラウド内の Hyper-V ホストへの Azure Recovery Services エージェントのインストール、クラウドの保護設定の構成、仮想マシンの保護の有効化、デプロイメントのテストを行います。</P>

![Storage account](./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png)

<h3><a id="agent"></a>手順 4:Azure Recovery Services エージェントの Hyper-V ホストへのインストール</h3>

保護する VMM クラウドに配置されている Hyper-V ホスト サーバーごとに、Azure Recovery Services Agent をインストールします。

1. [クイック スタート] ページで、<b>[Azure Site Recovery Services Agent をダウンロードしてホストにインストールする]</b> をクリックして、最新バージョンの Agent のインストール ファイルを取得します。

	![Install Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png)

2. 保護する VMMM クラウドに配置されている Hyper-V ホスト サーバーごとにインストール ファイルを実行します。
3. **[前提条件のチェック]** ページで <b>[次へ]</b> をクリックします。不足している前提条件があると自動的にインストールされます。

	![Prerequisites Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png)

4. **[インストール設定]** ページでエージェントのインストール先を指定し、バックアップのメタデータをインストールするキャッシュの場所を選択します。その後、<b>[インストール]</b> をクリックします。


<h3><a id="clouds"></a>手順 5:クラウドの保護設定の構成</h3>

VMM サーバーを登録した後、クラウドの保護設定を構成することができます。プロバイダーのインストール時にオプション **[コンテナーとのクラウド データの同期]** を有効にしたので、VMM サーバー上のすべてのクラウドが、コンテナーの <b>[保護された項目]</b> タブに表示されます。

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)


1. [クイック スタート] ページで、**[VMM クラウドの保護の設定]** をクリックします。
2. **[保護された項目]** タブで、構成するクラウドをクリックし、**[構成]** タブに移動します。
3. <b>[ターゲット]</b> で、<b>[Microsoft Azure]</b> を選択します。
4. <b>[ストレージ アカウント]</b> で、仮想マシンを格納する Azure ストレージを選択します。
5. <b>[格納データの暗号化]</b> を <b>[オフ]</b> に設定します。この設定は、内部設置型サイトと Azure 間のレプリケートでデータを暗号化する必要があることを指定します。
6. <b>[コピーの頻度]</b> で、既定の設定をそのまま使用します。この値は、ソースとターゲットの場所の間でデータが同期される頻度を指定します。
7. <b>[保持する復旧ポイント]</b> で、既定の設定をそのまま使用します。既定値である 0 を使用する場合は、プライマリ仮想マシンに対応する最新の復旧ポイントのみが、レプリカのホスト サーバーに格納されます。
8. <b>[アプリケーション整合性スナップショットの頻度]</b> では、既定の設定をそのまま使用します。この値は、スナップショットを作成する頻度を指定します。スナップショットは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。値を設定する場合は、構成する追加の復旧ポイント数よりも少ない値にしてください。
9. <b>[レプリケーションの開始時刻]</b> で、Azure へのデータの初期レプリケーションを開始する時刻を指定します。Hyper-V ホスト サーバーのタイムゾーンが使用されます。初期レプリケーションはピーク時以外にスケジュールすることをお勧めします。 

	![Cloud replication settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png)

この設定を保存するとジョブが作成され、これを <b>[ジョブ]</b> タブで監視できます。VMM ソース クラウド内のすべての Hyper-V ホスト サーバーは、レプリケーション用に構成されます。

保存後は、クラウド設定は <b>[構成]</b> タブで変更できます。ターゲットの場所またはターゲットのストレージを変更するには、クラウド構成を削除してから、クラウドを再構成する必要があります。ストレージ アカウントを変更する場合は、ストレージ アカウントの修正後に保護を有効にした仮想マシンにのみ、その変更が適用されることに注意してください。既存の仮想マシンは新しいストレージ アカウントに移行されません。</p>

<h3><a id="networkmapping"></a>手順 6:ネットワーク マッピングの構成</h3>

<p>このチュートリアルでは、テスト環境で Azure Site Recovery を展開する最も簡単な方法について説明します。このチュートリアルの一環としてネットワーク マッピングを構成することを希望する場合は、計画ガイドの<a href="http://go.microsoft.com/fwlink/?LinkId=324817">ネットワーク マッピングの準備</a>に関するページを参照してください。マッピングを構成するには、デプロイ ガイドの<a href="http://go.microsoft.com/fwlink/?LinkId=402533">ネットワーク マッピングの構成</a>に関するページの手順に従ってください。</p>




<h3><a id="virtualmachines"></a>手順 7:仮想マシンの保護の有効化</h3>

サーバー、クラウド、およびネットワークを正しく構成した後で、クラウド内の仮想マシンの保護を有効にすることができます。以下の点に注意してください。

- 仮想マシンは Azure 要件を満たしている必要があります。計画ガイドの「<a href="http://go.microsoft.com/fwlink/?LinkId=402602">前提条件とサポート</a>」で確認してください。
- オペレーティング システムとオペレーティング システム ディスクの保護を有効にするには、仮想マシンにプロパティを設定する必要があります。仮想マシン テンプレートを使用して VMM 内で仮想マシンを作成する際に、プロパティを設定できます。また、仮想マシンのプロパティの **[全般]** タブと **[ハードウェア構成]** タブで、既存の仮想マシンに対してこれらのプロパティを設定することもできます。VMM でこれらのプロパティを設定していない場合は、Azure Site Recovery ポータルで構成できます。

![Create virtual machine](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png)

![Modify virtual machine properties](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png)


1. 保護を有効にするために、仮想マシンが配置されているクラウドの <b>[仮想マシン]</b> タブで、<b>[保護を有効にする]</b> をクリックしてから <b>[仮想マシンを追加する]</b> を選択します。
2. クラウド内の仮想マシンのリストから、保護する仮想マシンを選択します。 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png)

3. 仮想マシンのプロパティを確認し、必要に応じて変更します。

	![Verify virtual machines](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png)

**[ジョブ]** タブで、初期レプリケーションを含む [保護を有効にする] アクションの進捗状況を確認します。保護の最終処理のジョブが実行されると、仮想マシンは、フェールオーバーを実行できる状態になります。保護が有効され仮想マシンが複製されると、Azure でそれらの状態を表示できます。


![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h3><a id="test"></a>手順 8:展開をテスト</h3>
展開をテストするために、1 台の仮想マシンに対するテスト フェールオーバーを実行することや、複数の仮想マシンで構成される復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。以下の点に注意してください。
<UL>
<li>フェールオーバー後に Azure でリモート デスクトップを使用して仮想マシンに接続する場合は、テストのフェールオーバーを実行する前に、仮想マシンのリモート デスクトップ接続を有効にします。</li>
<li>フェールオーバー後、パブリック IP アドレスを使用して、Azure の仮想マシンにリモート デスクトップで接続します。この接続では、パブリック アドレスを使用する仮想マシンの接続を妨げるドメイン ポリシーを使用していないことを確認してください。</li>
</UL>

1. **[復旧計画]** タブで、新しい計画を追加します。名前を指定し、**[ソースの種類]** として **[VMM]**、**[ソース]** として「ソースの VMM サーバー」、[ターゲット]として 「Azure」を指定します。

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRAzure_RP1.png)

2. **[テスト フェールオーバーの確認]** ページで、**[なし]** を選択します。この設定では、テスト フェールオーバーで、仮想マシンが Azure に正しくレプリケートされたことを確認しますが、レプリケーションのネットワーク構成は確認されないことに注意してください。指定された Azure ネットワークでテストを実行したい場合は、「<a href="http://go.microsoft.com/fwlink/?LinkId=522292">オンプレミスと Azure 間のデプロイのテスト</a>」を参照してください。

	![No network](./media/hyper-v-recovery-manager-configure-vault/SRAzure_TestFailoverNoNetwork.png)


3. フェールオーバーが**テストの完了**フェーズに達したら、**[テストの完了]** をクリックして、テスト フェールオーバーを終了します。**[ジョブ]** タブまでドリルダウンし、フェールオーバーの進行状況と状態を追跡して、必要な操作を実行します。
4. フェールオーバーが完了したら、以下の手順を実行します。
	- 仮想マシンが正常に起動することを確認します。
	- **[メモ]** をクリックして、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。
	- **[テスト フェールオーバーが完了しました]** をクリックします。テスト環境をクリーンアップして、テスト仮想マシンの電源を自動的にオフにし、テスト Azure ネットワークを削除します。
5. フェールオーバー後、Azure ポータルで、仮想マシンのテスト レプリカを確認できます。内部設置型ネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。



<h2><a id="runtest" name="runtest" href="#runtest"></a>アクティビティの監視</h2>
<p><b>[ジョブ]</b> タブと <b>[ダッシュボード]</b> を使用して、Azure Site Recovery コンテナーで実行されるメイン ジョブを表示して監視できます。これには、クラウドに対する保護の構成、仮想マシンに対する保護の有効化と無効化、フェールオーバー (計画されたフェールオーバー、計画されていないフェールオーバー、またはテスト フェールオーバー) の実行、計画されていないフェールオーバーのコミットが含まれます。</p>

<p><b>[ジョブ]</b> タブからは、ジョブの表示、ジョブの詳細とエラーの表示、特定の条件に一致するジョブを取得するジョブ クエリの実行、ジョブの Excel へのエクスポート、失敗したジョブの再開を行うことができます。</p>

<p><b>[ダッシュボード]</b> からは、最新バージョンのプロバイダーとエージェントのインストール ファイルのダウンロード、コンテナーの構成情報の取得、コンテナーによって管理されている保護対象の仮想マシンの数の確認、最近のジョブの確認、コンテナー証明書の管理、仮想マシンの再同期を行うことができます。</p>

<p>ジョブの操作とダッシュボードの詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=398534">運用と監視ガイド</a>を参照してください。</p>

<h2><a id="next" name="next" href="#next"></a>次のステップ</h2>
<UL>
<LI>完全な運用環境で Azure Site Recovery の計画とデプロイを実行するには、<a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure Site Recovery 計画ガイド</a>および <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure Site Recovery デプロイ ガイド</a>を参照してください。</LI>


<LI>疑問がある場合は、<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services フォーラム</a>にアクセスしてください。</LI> 
</UL>

<!--HONumber=49-->