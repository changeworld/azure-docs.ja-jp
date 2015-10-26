<properties
	pageTitle="Microsoft HPC Pack を使用したハイブリッド コンピューティング クラスターのセットアップ"
	description="Microsoft HPC Pack と Azure を使用して、小規模なハイブリッド ハイパフォーマンス コンピューティング (HPC) クラスターをセットアップする方法について説明します。"
	services="cloud-services"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/29/2015"
	ms.author="danlep"/>


# Microsoft HPC Pack を使用したハイブリッド コンピューティング クラスターのセットアップ
このチュートリアルでは、Microsoft HPC Pack 2012 R2 と Azure を使用して、小規模なハイブリッド ハイパフォーマンス コンピューティング (HPC) クラスターをセットアップする方法について説明します。クラスターは、オンプレミスのヘッド ノード (Windows Server オペレーティング システムと HPC Pack を実行するコンピューター) と、Azure クラウド サービス上に worker ロール インスタンスとしてオンデマンドでデプロイされるいくつかの計算ノードで構成されます。コンピューティング ジョブを、ハイブリッド クラスター上で実行することができます。

![ハイブリッド HPC クラスター][Overview]

このチュートリアルでは、「クラウド バースト」とも呼ばれる方法を紹介します。Azure 上で多くのコンピューティング処理を要するアプリケーションを実行するためにコンピューティング リソースをオンデマンドで使用する、拡張性に優れた方法です。

このチュートリアルは、コンピューティング クラスターまたは HPC Pack を使用した経験がない読者を対象に作成されています。これは、ハイブリッド コンピューティング クラスターを迅速にデプロイする方法のデモンストレーションを目的としています。大規模なハイブリッド HPC Pack クラスターを運用環境にデプロイするための考慮事項と手順については、[詳細なガイダンス](http://go.microsoft.com/fwlink/p/?LinkID=200493)を参照してください。HPC Pack クラスター全体を Azure にセットアップする場合は、「[Azure での Microsoft HPC Pack を使用した HPC クラスター オプション](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)」を参照してください。

>[AZURE.NOTE]Azure では、各種のワークロードに適合するように、コンピューティング リソース向けに[さまざまなサイズ](../virtual-machines/virtual-machines-size-specs.md)が用意されています。たとえば、A8 と A9 インスタンスでは、高いパフォーマンスとアクセスが組み合わされ、特定の HPC アプリケーションで必要となる、待機時間が短い高スループットのアプリケーション ネットワークが実現されます。「[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md)」を参照してください。

## 前提条件

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure アカウントの作成に関するページ](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/)を参照してください。

さらに、このチュートリアルでは次のものが必要です。

* Windows Server 2012 R2 または Windows Server 2012 のいずれのエディションかを実行しているオンプレミスのコンピューターが必要です。このコンピューターが HPC クラスターのヘッド ノードになります。Windows Server をまだ実行していない場合は、[評価版](http://technet.microsoft.com/evalcenter/dn205286.aspx)をダウンロードしてインストールできます。

	* コンピューターは Active Directory ドメインに結合されている必要があります。

	* 追加のサーバー ロールまたはロール サービスがインストールされていないことを確認します。

	* HPC Pack をサポートするには、オペレーティング システムは、英語、日本語、中国語 (簡体字) のいずれかでインストールする必要があります。

	* 重要な更新プログラムがインストールされていることを確認します。

* HPC Pack 2012 R2 のインストール ファイル。無料で利用できます。最新バージョンを[ダウンロード](http://go.microsoft.com/fwlink/p/?linkid=328024)して、ヘッド ノード コンピューターに、またはネットワーク上にコピーします。Windows Server のインストールと同じ言語を選択してください。

* ヘッド ノード上にローカル管理者権限があるドメイン アカウント。

* ヘッド ノードから Azure への TCP 接続 (ポート 443)。

## HPC Pack をヘッド ノードにインストールする

最初に、HPC クラスターのヘッド ノードにする、Windows Server が実行されているオンプレミスのコンピューターに Microsoft HPC Pack をインストールします。

1. ローカル管理者権限があるドメイン アカウントでヘッド ノードにログオンします。

2. HPC Pack のインストール ファイルから Setup.exe を実行して、HPC Pack のインストール ウィザードを開始します。

3. **[HPC Pack 2012 R2 セットアップ]** 画面で、**[新たにインストールするか、新機能を既存のインストールに追加する]** をクリックします。

	![HPC Pack 2012 セットアップ][install_hpc1]

4. **[Microsoft ソフトウェア使用許諾契約書]** ページで、**[次へ]** をクリックします。

5. **[インストールの種類の選択]** ページで、**[ヘッド ノードを作成することで新しい HPC クラスターを作成する]** をクリックし、**[次へ]** をクリックします。

	![インストールの種類の選択][install_hpc2]

6. ウィザードによってインストール前のテストがいくつか実行されます。すべてのテストが成功したら、**[インストール規則]** ページで **[次へ]** をクリックします。失敗した場合は、表示された情報を確認して、必要な変更を環境に加えます。テストをもう一度実行するか、必要に応じてインストール ウィザードをもう一度開始します。

	![インストール規則][install_hpc3]

7. **[HPC DB 構成]** ページで、すべての HPC データベースについて **[ヘッド ノード]** が選択されていることを確認し、**[次へ]** をクリックします。

	![DB 構成][install_hpc4]

8. ウィザードの残りのページは既定の設定のままにします。**[必要なコンポーネントのインストール]** ページで、**[インストール]** をクリックします。

	![インストール][install_hpc6]

9. インストールが完了したら、**[HPC クラスター マネージャーを開始する]** をオフにして、**[完了]** をクリックします(ヘッド ノードの構成を完了するために HPC クラスター マネージャーを後の手順で開始します)。

	![完了][install_hpc7]

## Azure サブスクリプションを準備する
[Azure の管理ポータル](https://manage.windowsazure.com)を使用して、Azure サブスクリプションで次の手順を使用します。これは、後で、オンプレミスのヘッド ノードから Azure ノードをデプロイするために必要です。

- 管理証明書のアップロード (ヘッド ノードと Azure サービス間の接続をセキュリティ保護するために必要です)

- Azure ノード (worker ロール インスタンス) を実行する Azure クラウド サービスの作成

- Azure のストレージ アカウントの作成

	>[AZURE.NOTE]Azure サブスクリプション ID を記録しておきます。これは後で必要になります。この ID を確認するには、Azure の<a href="[https://account.windowsazure.com/Subscriptions">アカウント情報</a>を調べてください。

### <a>既定の管理証明書のアップロード</a>
HPC Pack ではヘッド ノードに自己署名証明書 (既定の Microsoft HPC Azure 管理証明書) がインストールされます。これを Azure 管理証明書としてアップロードできます。この証明書は、テスト目的および概念実証のデプロイ用に提供されます。

1. ヘッド ノード コンピューターから、[Azure ポータル](https://manage.windowsazure.com)にサインインします。

2. **[設定]** をクリックし、**[管理証明書]** をクリックします。

3. コマンド バーで、**[アップロード]** をクリックします。

	![証明書の設定][upload_cert1]

4. ヘッド ノードでファイル C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer を参照し、**チェック** マークのボタンをクリックします。

	![証明書のアップロード][install_hpc10]

管理証明書のリストに **[既定の Microsoft HPC Azure 管理]** が表示されます。

### <a>Azure クラウド サービスの作成</a>

>[AZURE.NOTE]パフォーマンスを最適化するには、クラウド サービスとストレージ アカウントを同じリージョンに作成します。

1. ポータルで、コマンド バーの **[新規]** をクリックします。

2. **[コンピューティング]**、**[クラウド サービス]**、**[簡易作成]** の順にクリックします。

3. クラウド サービスの URL を入力し、**[クラウド サービスを作成する]** をクリックします。

	![サービスの作成][createservice1]

### <a>Azure のストレージ アカウントの作成</a>

1. ポータルで、コマンド バーの **[新規]** をクリックします。

2. **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

3. アカウントの URL を入力し、**[ストレージ アカウントの作成]** をクリックします。

	![ストレージの作成][createstorage1]

## ヘッド ノードを構成する

HPC クラスター マネージャーを使用して Azure ノードをデプロイし、ジョブを送信するには、最初にクラスターの構成手順をいくつか実行する必要があります。

1. ヘッド ノードで、HPC クラスター マネージャーを開始します。**[ヘッド ノードの選択]** ダイアログ ボックスが表示されたら、**[ローカル コンピューター]** をクリックします。**[展開作業一覧]** が表示されます。

2. **[必須のデプロイ タスク]** で、**[ネットワークの構成]** をクリックします。

	![ネットワークの構成][config_hpc2]

3. ネットワーク構成ウィザードで、**[企業ネットワークのみのすべてのノード]** (トポロジ 5) を選択します。

	![トポロジ 5][config_hpc3]

	>[AZURE.NOTE]これはデモンストレーションを目的とした最も単純な構成であり、ヘッド ノードには、Active Directory とインターネットに接続するための 1 つのネットワーク アダプターのみが必要です。このチュートリアルでは、追加のネットワークを必要とするクラスター シナリオは扱いません。

4. ウィザードの残りのページは既定の設定のままにして **[次へ]** をクリックします。**[レビュー]** タブで、**[構成]** をクリックして、ネットワーク構成を完了します。

5. **[デプロイ To-do リスト]** で、**[インストール資格情報の提供]** をクリックします。

6. **[インストール資格情報]** ダイアログ ボックスで、HPC Pack のインストールに使用するドメイン アカウントの資格情報を入力します。次に、 **[OK]** をクリックします

	![インストール資格情報][config_hpc6]

	>[AZURE.NOTE]HPC Pack サービスでは、インストール資格情報のみを使用して、ドメインに参加している計算ノードをデプロイします。このチュートリアルで追加する Azure ノードはドメインに参加していません。

7. **[デプロイ To-do リスト]** で、**[新しいノードの名前付けの構成]** をクリックします。

8. **[ノードの名前付け系列の指定]** ダイアログ ボックスで、既定の命名系のままにして、**[OK]** をクリックします。

	![ノードの名前付け][config_hpc8]

	>[AZURE.NOTE]この命名系では、ドメインに参加している計算ノードの名前のみが生成されます。Azure ノードは自動的に命名されます。

9. **[デプロイ To-do リスト]** で、**[ノード テンプレートの作成]** をクリックします。ノード テンプレートを使用して、Azure ノードをクラスターに追加します。

10. ノード テンプレートの作成ウィザードで、次の操作を行います。

	a.**[ノード テンプレートの種類の選択]** ページで、**[Azure ノード テンプレート]** をクリックし、**[次へ]** をクリックします。

	![ノード テンプレート][config_hpc10]

	b.**[次へ]** をクリックし、既定のテンプレート名をそのまま使用します。

	c.**[サブスクリプション情報の入力]** ページで、Azure サブスクリプション ID (Azure の<a href="[https://account.windowsazure.com/Subscriptions">アカウント情報</a>で参照できます) を入力します。次に、**[管理証明書]** で **[参照]** をクリックして、**[既定の Microsoft HPC Azure 管理]** を選択します。 その後、**[次へ]** をクリックします。

	![ノード テンプレート][config_hpc12]

	d.**[サービス情報の入力]** ページで、前の手順で作成したクラウド サービスとストレージ アカウントを選択します。その後、**[次へ]** をクリックします。

	![ノード テンプレート][config_hpc13]

	e.ウィザードの残りのページは既定の設定のままにして **[次へ]** をクリックします。**[レビュー]** タブで、**[作成]** をクリックしてノード テンプレートを作成します。

	>[AZURE.NOTE]既定では、Azure ノード テンプレートに、ノードを手動で開始 (プロビジョニング) と停止するための設定が含まれています。Azure ノードを自動的に開始および停止するスケジュールを構成することもできます。

## クラスターに Azure ノードを追加する

ここでは、ノード テンプレートを使用して、Azure ノードをクラスターに追加します。ノードをクラスターに追加すると、ノードの構成情報が保存されるため、ノードをいつでもクラウド サービスのロール インスタンスとして開始 (プロビジョニング) することができます。Azure ノードについてサブスクリプションに課金されるのは、クラウド サービスでロール インスタンスを実行した後だけです。

このチュートリアルでは、2 つの小規模ノードを追加します。

1. HPC クラスター マネージャーの **[ノード管理]** で、**[アクション]** ウィンドウの **[ノードの追加]** をクリックします。

	![ノードの追加][add_node1]

2. ノードの追加ウィザードの **[デプロイ方法の選択]** ページで、**[Azure ノードの追加]** をクリックして、**[次へ]** をクリックします。

	![Azure ノードの追加][add_node1_1]

3. **[新しいのノードの指定]** ページで、前に作成した Azure ノード テンプレート (「**既定の AzureNode テンプレート**」という名前が付いています) を選択します。ノードの数に **[2]** を、ノードのサイズに **[S]** を選択し、**[次へ]** ボタンをクリックします。

	![ノードの指定][add_node2]

	使用できる仮想マシンのサイズの詳細については、「[Windows Azure の仮想マシンおよびクラウド サービスのサイズ](https://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。

4. **[ノード追加ウィザードの完了]** ページで、**[完了]** をクリックします。

	 HPC クラスター マネージャーに、**AzureCN-0001** と **AzureCN-0002** という名前の 2 つの Azure ノードが表示されます。どちらも **[未展開]** 状態です。

	![追加されたノード][add_node3]

## Azure ノード
Azure のクラスター リソースを使用する場合、HPC クラスター マネージャーを使用して、Azure ノードを開始 (プロビジョニング) してオンラインにします。

1.	HPC クラスター マネージャーの **[ノード管理]** で、**[アクション]** ウィンドウに表示されているどちらか 1 つまたは両方のノードをクリックして、**[開始]** をクリックします。

	![ノードの開始][add_node4]

2. **[Azure ノードの開始]** ダイアログ ボックスで、**[開始]** をクリックします。

	![ノードの開始][add_node5]

	ノードが **[プロビジョニング]** 状態に移行します。プロビジョニング ログを表示して、プロビジョニング進捗状況を確認できます。

	![ノードのプロビジョニング][add_node6]

3. 数分後、Azure ノードのプロビジョニングが完了し、ノードが **[オフライン]** 状態になります。この状態では、ロール インスタンスは実行中ですが、クラスター ジョブは受け入れられません。

4. ロール インスタンスが実行中であることを確認するために、[ポータル](https://manage.windowsazure.com)で、**[クラウド サービス]**、クラウド サービスの名前、**[インスタンス]** の順にクリックします。

	![実行中のインスタンス][view_instances1]

	2 つの worker ロール インスタンスがサービスで実行中であることがわかります。HPC Pack では、ヘッド ノードと Azure 間の通信を処理するために、2 つの **HpcProxy** インスタンス (サイズは M) も自動的にデプロイされます。

5. Azure ノードをオンラインにしてクラスター ジョブを実行するには、ノードを選択し右クリックして、**[オンラインにする]** をクリックします。

	![オフラインのノード][add_node7]

	ノードが **[オンライン]** 状態であることが表示されます。

## クラスター全体にコマンドを実行する
HPC Pack の **clusrun** コマンドを使用すると、1 つ以上のクラスター ノードでコマンドまたはアプリケーションを実行できます。簡単な例として、**clusrun** を使用して、Azure ノードの IP 構成を取得する手順を紹介します。

1. ヘッド ノードで、コマンド プロンプトを開きます。

2. 次のコマンドを入力します。

	`clusrun /nodes:azurecn* ipconfig`

3. 次のような出力が表示されます。

	![clusrun][clusrun1]

## テスト ジョブを実行する

ハイブリッド クラスター上で実行するテスト ジョブを送信できます。この例は単純な "パラメーター スイープ" ジョブ (並列コンピューティングの一種) です。**set /a** コマンドを使用して、自身に整数を追加するサブタスクを実行します。クラスター内のすべてのノードが、1 ～ 100 の整数に関するサブタスクを完了します。

1. HPC クラスター マネージャーの **[ジョブ管理]** で、**[アクション]** ウィンドウの **[新しいパラメーター スイープ ジョブ]** をクリックします。

	![新しいジョブ][test_job1]

2. **[新しいパラメーター スイープ ジョブ]** ダイアログ ボックスの **[コマンド ライン]** で、「`set /a *+*`」と入力します (表示されている既定のコマンド ラインを上書きしてください)。残りの設定については既定値のままにして、**[送信]** をクリックしてジョブを送信します。

	![パラメーター スイープ][param_sweep1]

3. ジョブが完了したら、**[マイ スイープ タスク]** ジョブをダブルクリックします。

4. **[タスクの表示]** をクリックして、サブタスクの計算された出力を表示します。

	![タスクの結果][view_job361]

5. サブタスクの計算を実行したノードを表示するには、**[割り当て済みのノード]** をクリックします(クラスターによっては異なるノード名が表示されます)。

	![タスクの結果][view_job362]

## Azure ノードを停止する

クラスターを試した後は、HPC クラスター マネージャーを使用して Azure ノードを停止し、アカウントへの不必要な課金を避けることができます。ノードを停止するとクラウド サービスが停止し、Azure ロール インスタンスが削除されます。

1. HPC クラスター マネージャーの **[ノード管理]** で、両方の Azure ノードを選択します。次に、**[操作]** ウィンドウで **[停止]** をクリックします。

	![ノードの停止][stop_node1]

2. **[Azure ノードの停止]** ダイアログ ボックスで、**[停止]** をクリックします。

	![ノードの停止][stop_node2]

3. ノードが **[停止中]** 状態に移行します。数分後、HPC クラスター マネージャーに、ノードが **[未展開]** 状態であることが表示されます。

	![未配置のノード][stop_node4]

4. ロール インスタンスが Azure で実行されていないことを確認するために、[クラシック ポータル](https://manage.windowsazure.com)で、**[クラウド サービス]**、クラウド サービスの名前、**[インスタンス]** を順にクリックします。運用環境にはインスタンスはデプロイされません。

	![インスタンスなし][view_instances2]

	このチュートリアルはこれで終わりです。

## 関連リソース

* [HPC Pack 2012 R2 と HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697)
* [Microsoft HPC Pack を使用した Azure へのバースト](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [Azure での Microsoft HPC Pack を使用した HPC クラスター オプション](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)
* [Azure における大規模なコンピューティング: Batch とハイ パフォーマンス コンピューティング (HPC) に関するテクニカル リソース](big-compute-resources.md)


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png

<!---HONumber=Oct15_HO3-->