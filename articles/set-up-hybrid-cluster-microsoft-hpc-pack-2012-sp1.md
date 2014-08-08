<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="Microsoft HPC Pack と Azure を使用して、小規模のハイブリッド ハイパフォーマンス コンピューティング (HPC) クラスターをセットアップする方法について説明します。" metaCanonical="" services="" documentationCenter="" title="Microsoft HPC Pack を使用してハイブリッド クラスターをセットアップする" authors="danlep" solutions="" manager="dongill" editor="mattshel" />



#Microsoft HPC Pack を使用してハイブリッド クラスターをセットアップする
このチュートリアルでは、Microsoft HPC Pack 2012 R2 と Azure を使用して、小規模のハイブリッド ハイパフォーマンス コンピューティング (HPC) クラスターをセットアップする方法について説明します。クラスターは、内部設置型のヘッド ノード (Windows Server オペレーティング システムと HPC Pack を実行するコンピューター) と、Azure クラウド サービス上に worker ロール インスタンスとしてオンデマンドで展開されるいくつかの計算ノードで構成されます。コンピューティング ジョブを、ハイブリッド クラスター上で実行することができます。
 
![Hybrid HPC cluster][Overview] 

このチュートリアルでは、「クラウド バースト」とも呼ばれる方法を紹介します。Azure 上で多くのコンピューティング処理を要するアプリケーションを実行するためにコンピューティング リソースをオンデマンドで使用する、拡張性に優れた方法です。

このチュートリアルは、コンピューティング クラスターまたは HPC Pack を使用した経験がない読者を対象に作成されています。ハイブリッド クラスターを展開する方法をデモンストレーションを目的として説明します。大規模なハイブリッド HPC Pack クラスターを運用環境に展開するための考慮事項と手順については、[詳細なガイダンス](http://go.microsoft.com/fwlink/p/?LinkID=200493)を参照してください。

>[WACOM.NOTE] Azure では、各種のワークロードに適合するように、コンピューティング リソース向けに[さまざまなサイズ](http://go.microsoft.com/fwlink/p/?LinkId=389844)が用意されています。たとえば、A8 と A9 インスタンスでは、高いパフォーマンスとアクセスが組み合わされ、特定の HPC アプリケーションで必要となる、待機時間が短い高スループットのアプリケーション ネットワークが実現されます。詳細については、[A8/A9 コンピューティング集中型インスタンス (HPC Pack を使用したクイック スタート) に関するページ](http://go.microsoft.com/fwlink/p/?Linkid=328042)を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

* [前提条件](#BKMK_Prereq)
* [HPC Pack をヘッド ノードにインストールする](#BKMK_DeployHN)
* [Azure サブスクリプションを準備する](#BKMK_Prpare)
* [ヘッド ノードを構成する](#BKMK_ConfigHN)
* [クラスターに Azure ノードを追加する](#BKMK_worker)
* [Azure ノードを開始する](#BKMK_start)
* [クラスター全体にコマンドを実行する](#BKMK_RunCommand)
* [テスト ジョブを実行する](#BKMK_RunJob)
* [Azure ノードを停止する](#BKMK_stop)

<h2 id="BKMK_Prereq">前提条件</h2>

>[WACOM.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/" target="_blank">Azure の無料評価版サイト</a>を参照してください。

さらに、このチュートリアルでは次のものが必要です。

*Windows Server 2012 R2 または Windows Server 2012 を実行する内部設置型のコンピューター。このコンピューターが HPC クラスターのヘッド ノードになります。Windows Server をまだ実行していない場合は、[評価版](http://technet.microsoft.com/evalcenter/dn205286.aspx)をダウンロードしてインストールできます。

	* このコンピューターを Active Directory ドメインに結合します。

	* 追加のサーバー ロールまたはロール サービスをインストールしていないことを確認します。

	* HPC Pack をサポートするには、オペレーティング システムのインストールを英語、日本語、または中国語 (簡体字) で行う必要があります。

	* 重要な更新プログラムをインストール済みであることを確認します。
	
* HPC Pack 2012 R2 のインストール ファイル。無料で利用できます。フル インストール パッケージを[ダウンロード](http://go.microsoft.com/fwlink/p/?linkid=389557)して、ヘッド ノード コンピューターに、またはネットワーク上にコピーします。Windows Server のインストールと同じ言語を選択してください。

* ヘッド ノード上にローカル管理者権限があるドメイン アカウント。

* ヘッド ノードから Azure への TCP 接続 (ポート 443)。

<h2 id="BKMK_DeployHN">HPC Pack をヘッド ノードにインストールする</h2>

最初に、HPC クラスターのヘッド ノードにする内部設置型のコンピューターに Microsoft HPC Pack をインストールします。

1. ローカル管理者権限があるドメイン アカウントでヘッド ノードにログオンします。

2. HPC Pack のインストール ファイルから Setup.exe を実行して、HPC Pack のインストール ウィザードを開始します。

3. **[HPC Pack 2012 R2 セットアップ]** 画面で、**[新たにインストールするか、新機能を既存のインストールに追加する]** をクリックします。

	![HPC Pack 2012 セットアップ][install_hpc1]

4. **[Microsoft ソフトウェア ユーザー契約書]** ページで、**[次へ]** をクリックします。

5. **[インストールの種類の選択]** ページで、**[ヘッド ノードを作成することで新しい HPC クラスターを作成する]** をクリックし、**[次へ]** をクリックします。

	![インストールの種類の選択][install_hpc2]

6. ウィザードによってインストール前のテストがいくつか実行されます。すべてのテストが成功したら、**[インストール規則]** ページで **[次へ]** をクリックします。失敗した場合は、表示された情報を確認して、必要な変更を環境に加えます。テストをもう一度実行するか、必要に応じてインストール ウィザードをもう一度開始します。

	![インストール規則][install_hpc3]

7. **[HPC DB 構成]** ページで、すべての HPC データベースについて **[ヘッド ノード]** が選択されていることを確認し、**[次へ]** をクリックします。

	![DB 構成][install_hpc4]

8. ウィザードの残りのページは既定の設定のままにします。**[必要なコンポーネントのインストール]** ページで、**[インストール]** をクリックします。

	![インストール][install_hpc6]

9. インストールが完了したら、**[HPC クラスター マネージャーを開始する]** をオフにして、**[完了]** をクリックします (ヘッド ノードの構成を完了するために HPC クラスター マネージャーを後の手順で開始します)。

	![完了][install_hpc7]

<h2 id="BKMK_Prepare">Azure サブスクリプションを準備する</h2>
[管理ポータル](https://manage.windowsazure.com)を使用して、Azure サブスクリプションで次の手順を使用します。これは、後で、内部設置型のヘッド ノードから Azure ノードを展開するために必要です。

- 管理証明書のアップロード (ヘッド ノードと Azure サービス間の接続をセキュリティ保護するために必要です)
 
- Azure ノード (worker ロール インスタンス) を実行する Azure クラウド サービスの作成

- Azure のストレージ アカウントの作成
	
	>[WACOM.NOTE]Azure サブスクリプション ID を記録しておきます。これは後で必要になります。この ID を確認するには、Azure の<a href="[https://account.windowsazure.com/Subscriptions">アカウント情報</a>を調べてください。

<h3>既定の管理証明書のアップロード</h3>
HPC Pack ではヘッド ノードに自己署名証明書 (既定の Microsoft HPC Azure 管理証明書) がインストールされます。これを Azure 管理証明書としてアップロードできます。この証明書は、テスト目的および概念実証の展開用に提供されます。

1. ヘッド ノード コンピューターから[管理ポータル](https://manage.windowsazure.com)にサインインします。

2. **[設定]** をクリックし、**[管理証明書]** をクリックします。

3. コマンド バーで、**[アップロード]** をクリックします。

	![証明書の設定][upload_cert1]

4. ヘッド ノードでファイル C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer を参照し、**チェック マーク**のボタンをクリックします。

	![証明書のアップロード][install_hpc10]

管理証明書のリストに **[既定の Microsoft HPC Azure 管理]** が表示されます。

<h3>Azure クラウド サービスの作成</h3>

>[WACOM.NOTE]パフォーマンスを最高にするには、クラウド サービスとストレージ アカウントを同じリージョンまたはアフィニティ グループ (構成済みの場合) に作成します。

1. 管理ポータルで、コマンド バーの **[新規]** をクリックします。

2. **[コンピューティング]**、**[クラウド サービス]**、**[簡易作成]** の順にクリックします。

3. クラウド サービスの URL を入力し、**[クラウド サービスを作成する]** をクリックします。

	![サービスの作成][createservice1]

<h3>Azure のストレージ アカウントの作成</h3>

1. 管理ポータルで、コマンド バーの **[新規]** をクリックします。

2. **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

3. アカウントの URL を入力し、**[ストレージ アカウントの作成]** をクリックします。

	![ストレージの作成][createstorage1]

<h2 id="BKMK_ConfigHN">ヘッド ノードを構成する</h2>

HPC クラスター マネージャーを使用して Azure ノードを展開し、ジョブを送信するには、最初にクラスターの構成手順をいくつか実行する必要があります。

1. ヘッド ノードで、HPC クラスター マネージャーを開始します。**[ヘッド ノードの選択]** ダイアログ ボックスが表示されたら、**[ローカル コンピューター]** をクリックします。**[展開作業一覧]** が表示されます。

2. **[必須の展開タスク]** で、**[ネットワークの構成]** をクリックします。

	![ネットワークの構成][config_hpc2]

3. ネットワーク構成ウィザードで、**[企業ネットワークのみのすべてのノード]** (トポロジ 5) を選択します。

	![トポロジ 5][config_hpc3]

	>[WACOM.NOTE]これはデモンストレーションを目的とした最も単純な構成であり、ヘッド ノードには、Active Directory とインターネットに接続するための 1 つのネットワーク アダプターのみが必要です。このチュートリアルでは、追加のネットワークを必要とするクラスター シナリオは扱いません。
	 
4. ウィザードの残りのページは既定の設定のままにして **[次へ]** をクリックします。**[レビュー]** タブで、**[構成]** をクリックして、ネットワーク構成を完了します。

5. **[展開作業一覧]** で、**[インストール資格情報の提供]** をクリックします。

6. **[インストール資格情報]** ダイアログ ボックスで、HPC Pack のインストールに使用するドメイン アカウントの資格情報を入力します。次に、**[OK]** をクリックします。

	![インストール資格情報][config_hpc6]

	>[WACOM.NOTE]HPC Pack サービスでは、インストール資格情報のみを使用して、内部設置型の計算ノードを展開します。
	
7. **[展開作業一覧]** で、**[新しいノードの名前付けの構成]** をクリックします。

8. **[ノードの名前付け系列の指定]** ダイアログ ボックスで、既定の命名系のままにして、**[OK]** をクリックします。

	![ノードの名前付け][config_hpc8]

	>[WACOM.NOTE]この命名系では、ドメインに参加している計算ノードの名前のみが生成されます。Azure ノードは自動的に命名されます。
	  
9. **[展開作業一覧]** で、**[ノード テンプレートの作成]** をクリックします。ノード テンプレートを使用して、Azure ノードをクラスターに追加します。

10. ノード テンプレートの作成ウィザードで、次の操作を行います。

	a. **[ノード テンプレートの種類の選択]** ページで、**[Azure ノード テンプレート]** をクリックし、**[次へ]** をクリックします。

	![ノード テンプレート][config_hpc10]

	b. **[次へ]** をクリックし、既定のテンプレート名をそのまま使用します。

	c. **[サブスクリプション情報の入力]** ページで、Azure サブスクリプション ID (Azure の<a href="[https://account.windowsazure.com/Subscriptions">アカウント情報</a>で参照できます) を入力します。次に、**[管理証明書]** で **[参照]** をクリックして、**[既定の Microsoft HPC Azure 管理]** を選択します。その後で **[次へ]** をクリックします。

	![ノード テンプレート][config_hpc12]

	d. **[サービス情報の入力]** ページで、前の手順で作成したクラウド サービスとストレージ アカウントを選択します。その後で **[次へ]** をクリックします。

	![ノード テンプレート][config_hpc13]

	e. ウィザードの残りのページは既定の設定のままにして **[次へ]** をクリックします。**[レビュー]** タブで、**[作成]** をクリックしてノード テンプレートを作成します。

	>[WACOM.NOTE]既定では、Azure ノード テンプレートに、ノードを手動で開始 (プロビジョニング) および停止するための設定が含まれています。Azure ノードを自動的に開始および停止するスケジュールを構成することもできます。
	
<h2 id="#BKMK_worker">クラスターに Azure ノードを追加する</h2>

ここでは、ノード テンプレートを使用して、Azure ノードをクラスターに追加します。ノードをクラスターに追加すると、ノードの構成情報が保存されるため、ノードをいつでもクラウド サービスのロール インスタンスとして開始 (プロビジョニング) することができます。Azure ノードについてサブスクリプションに課金されるのは、クラウド サービスでロール インスタンスを実行した後だけです。

このチュートリアルでは、2 つの小規模ノードを追加します。

1. HPC クラスター マネージャーの **[ノード管理]** で、**[アクション]** ウィンドウの **[ノードの追加]** をクリックします。

	![ノードの追加][add_node1]

2. ノードの追加ウィザードの **[展開方法の選択]** ページで、**[Azure ノードの追加]** をクリックして、**[次へ]** をクリックします。

	![Azure ノードの追加][add_node1_1]

3. **[新規ノードの追加]** ページで、前に作成した Azure ノード テンプレート (「**既定の AzureNode テンプレート**」という名前が付いています) を選択します。ノードの数に **[2]** を、ノードのサイズに **[S]** を選択し、**[次へ]** ボタンをクリックします。

	![ノードの指定][add_node2]

	使用できる仮想マシンのサイズの詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx)」を参照してください。

4. **[ノードの追加の完了]** ページで、**[完了]** をクリックします。

	 HPC クラスター マネージャーに、**AzureCN-0001** と **AzureCN-0002** という名前の 2 つの Azure ノードが表示されます。どちらも **[未展開]** 状態です。

	![追加されたノード][add_node3]

<h2 id="BKMK_Start">Azure ノードを開始する</h2>
Azure のクラスター リソースを使用する場合、HPC クラスター マネージャーを使用して、Azure ノードを開始 (プロビジョニング) してオンラインにします。

1.	HPC クラスター マネージャーの **[ノード管理]** で、**[アクション]** ウィンドウに表示されているどちらか 1 つまたは両方のノードをクリックして、**[開始]** をクリックします。

	![ノードの開始][add_node4]

2. **[Azure ノードの開始]** ダイアログ ボックスで、**[開始]** をクリックします。

	![ノードの開始][add_node5]
 
	ノードが **[プロビジョニング]** 状態に移行します。プロビジョニング ログを表示して、プロビジョニング進捗状況を確認できます。

	![ノードのプロビジョニング][add_node6]

3. 数分後、Azure ノードのプロビジョニングが完了し、ノードが **[オフライン]** 状態になります。この状態では、ロール インスタンスは実行中ですが、クラスター ジョブは受け入れられません。

4. ロール インスタンスが実行中であることを確認するために、[管理ポータル](https://manage.windowsazure.com)で、**[クラウド サービス]**、クラウド サービスの名前、**[インスタンス]** を順にクリックします。

	![実行中のインスタンス][view_instances1]

	2 つの worker ロール インスタンスがサービスで実行中であることがわかります。HPC Pack では、ヘッド ノードと Azure 間の通信を処理するために、2 つの **HpcProxy** インスタンス (サイズは M) も自動的に展開されます。

5. Azure ノードをオンラインにしてクラスター ジョブを実行するには、ノードを選択し右クリックして、**[オンラインにする]** をクリックします。

	![オフラインのノード][add_node7]
 
	ノードが **[オンライン]** 状態であることが表示されます。

<h2 id="BKMK_RunCommand">クラスター全体にコマンドを実行する</h2>	
HPC Pack の **clusrun** コマンドを使用すると、1 つ以上のクラスター ノードでコマンドまたはアプリケーションを実行できます。簡単な例として、**clusrun** を使用して、Azure ノードの IP 構成を取得する手順を紹介します。

1. ヘッド ノードで、コマンド プロンプトを開きます。

2. 次のコマンドを入力します。

	`clusrun /nodes:azurecn* ipconfig`

3. 次のような出力が表示されます。

	![clusrun][clusrun1]

<h2 id="BKMK_RunJob">テスト ジョブを実行する</h2>

ハイブリッド クラスター上で実行するテスト ジョブを送信できます。この例は単純な "パラメーター スイープ" ジョブ (並列コンピューティングの一種) です。**set /a** コマンドを使用して、自身に整数を追加するサブタスクを実行します。クラスター内のすべてのノードが、1 ～ 100 の整数に関するサブタスクを完了します。

1. HPC クラスター マネージャーの **[ジョブ管理]** で、**[アクション]** ウィンドウの **[新しいパラメーター スイープ ジョブ]** をクリックします。

	![新しいジョブ][test_job1]

2. **[新しいパラメーター スイープ ジョブ]** ダイアログ ボックスの **[コマンド ライン]** で、「`set /a *+*`」と入力します (表示されている既定のコマンド ラインを上書きしてください)。残りの設定については既定値のままにして、**[送信]** をクリックしてジョブを送信します。

	![パラメーター スイープ][param_sweep1]

3. ジョブが完了したら、**[マイ スイープ タスク]** ジョブをダブルクリックします。

4. **[タスクの表示]** をクリックして、サブタスクの計算された出力を表示します。

	![タスクの結果][view_job361]

5. サブタスクの計算を実行したノードを表示するには、**[割り当て済みのノード]** をクリックします (クラスターによっては異なるノード名が表示されます)。

	![タスクの結果][view_job362]

<h2 id="BKMK_stop">Azure ノードを停止する</h2>

クラスターを試した後は、HPC クラスター マネージャーを使用して Azure ノードを停止し、アカウントへの不必要な課金を避けることができます。ノードを停止するとクラウド サービスが停止し、Azure ロール インスタンスが削除されます。

1. HPC クラスター マネージャーの **[ノード管理]** で、両方の Azure ノードを選択します。次に、**[操作]** ウィンドウで **[停止]** をクリックします。

	![ノードの停止][stop_node1]

2. **[Azure ノードの停止]** ダイアログ ボックスで、**[停止]** をクリックします。

	![ノードの停止][stop_node2]

3. ノードが **[停止中]** 状態に移行します。数分後、HPC クラスター マネージャーに、ノードが **[未展開]** 状態であることが表示されます。

	![未配置のノード][stop_node4]

4. ロール インスタンスが Azure で実行されていないことを確認するために、[管理ポータル](https://manage.windowsazure.com)で、**[クラウド サービス]**、クラウド サービスの名前、**[インスタンス]** を順にクリックします。運用環境にはインスタンスは展開されません。

	![インスタンスなし][view_instances2]

	このチュートリアルはこれで終わりです。

<h2 id="">関連リソース</h2>

* [HPC Pack 2012 R2 と HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697)
* [Microsoft HPC Pack を使用した Azure へのバースト](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [Azure ノードへのアプリケーションの展開](http://go.microsoft.com/fwlink/p/?LinkId=325317)
* [Azure 仮想マシンでの Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?linkid=330375)


[概要]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_クラスター_overview.png
[install_hpc1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
[install_hpc2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
[install_hpc3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
[install_hpc4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
[install_hpc6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
[install_hpc7]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
[install_hpc10]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
[upload_cert1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
[createstorage1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
[createservice1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
[config_hpc2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
[config_hpc3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
[config_hpc6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
[config_hpc8]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
[config_hpc10]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
[config_hpc12]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
[config_hpc13]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
[add_node1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
[add_node1_1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
[add_node2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
[add_node3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
[add_node4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
[add_node5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
[add_node6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
[add_node7]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
[view_instances1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
[clusrun1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
[test_job1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
[param_sweep1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
[view_job361]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
[view_job362]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
[stop_node1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
[stop_node2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
[stop_node4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
[view_instances2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png

