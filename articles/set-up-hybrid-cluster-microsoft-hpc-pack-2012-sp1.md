<properties linkid="itpro-windows-howto-howto-setup-hybrid-cluster-hpcpack" urlDisplayName="" pageTitle="Set up a Hybrid Compute Cluster in Azure with Microsoft HPC Pack" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Compute Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="cloud-services" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep"></tags>

# Microsoft HPC Pack を使用したハイブリッド コンピューティング クラスターのセットアップ

このチュートリアルでは、Microsoft HPC Pack 2012 R2 と Azure を使用して、小規模なハイブリッド ハイパフォーマンス コンピューティング (HPC) クラスターをセットアップする方法について説明します。クラスターは、オンプレミスのヘッド ノード (Windows Server オペレーティング システムと HPC Pack を実行するコンピューター) と、Azure クラウド サービス上に worker ロール インスタンスとしてオンデマンドでデプロイされるいくつかの計算ノードで構成されます。コンピューティング ジョブを、ハイブリッド クラスター上で実行することができます。

![ハイブリッド HPC クラスター][ハイブリッド HPC クラスター]

このチュートリアルでは、「クラウド バースト」とも呼ばれる方法を紹介します。Azure 上で多くのコンピューティング処理を要するアプリケーションを実行するためにコンピューティング リソースをオンデマンドで使用する、拡張性に優れた方法です。

このチュートリアルは、コンピューティング クラスターまたは HPC Pack を使用した経験がない読者を対象に作成されています。これは、ハイブリッド コンピューティング クラスターを迅速にデプロイする方法のデモンストレーションを目的としています。大規模なハイブリッド HPC Pack クラスターを運用環境にデプロイするための考慮事項と手順については、[詳細なガイダンス][詳細なガイダンス]を参照してください。

> [WACOM.NOTE] Azure では、各種のワークロードに適合するように、コンピューティング リソース向けに[さまざまなサイズ][さまざまなサイズ]が用意されています。たとえば、A8 と A9 インスタンスでは、高いパフォーマンスとアクセスが組み合わされ、特定の HPC アプリケーションで必要となる、待機時間が短い高スループットのアプリケーション ネットワークが実現されます。詳細については、「[A8/A9 コンピューティング集中型インスタンスについて][A8/A9 コンピューティング集中型インスタンスについて]」を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

-   [前提条件][前提条件]
-   [HPC Pack をヘッド ノードにインストールする][HPC Pack をヘッド ノードにインストールする]
-   [Azure サブスクリプションを準備する][Azure サブスクリプションを準備する]
-   [ヘッド ノードを構成する][ヘッド ノードを構成する]
-   [クラスターに Azure ノードを追加する][クラスターに Azure ノードを追加する]
-   [Azure ノードを開始する][Azure ノードを開始する]
-   [クラスター全体にコマンドを実行する][クラスター全体にコマンドを実行する]
-   [テスト ジョブを実行する][テスト ジョブを実行する]
-   [Azure ノードを停止する][Azure ノードを停止する]

## 前提条件

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure アカウントの作成に関するページ][Azure アカウントの作成に関するページ]を参照してください。

さらに、このチュートリアルでは次のものが必要です。

-   Windows Server 2012 R2 または Windows Server 2012 を実行するオンプレミスのコンピューター。このコンピューターが HPC クラスターのヘッド ノードになります。Windows Server をまだ実行していない場合は、[評価版][評価版]をダウンロードしてインストールできます。

    -   コンピューターは Active Directory ドメインに結合されている必要があります。

    -   追加のサーバー ロールまたはロール サービスがインストールされていないことを確認します。

    -   HPC Pack をサポートするには、オペレーティング システムは、英語、日本語、中国語 (簡体字) のいずれかでインストールする必要があります。

    -   重要な更新プログラムがインストールされていることを確認します。

-   HPC Pack 2012 R2 のインストール ファイル。無料で利用できます。フル インストール パッケージを[ダウンロード][ダウンロード]して、ヘッド ノード コンピューターに、またはネットワーク上にコピーします。Windows Server のインストールと同じ言語を選択してください。

-   ヘッド ノード上にローカル管理者権限があるドメイン アカウント。

-   ヘッド ノードから Azure への TCP 接続 (ポート 443)。

## HPC Pack をヘッド ノードにインストールする

最初に、HPC クラスターのヘッド ノードにする、Windows Server が実行されているオンプレミスのコンピューターに Microsoft HPC Pack をインストールします。

1.  ローカル管理者権限があるドメイン アカウントでヘッド ノードにログオンします。

2.  HPC Pack のインストール ファイルから Setup.exe を実行して、HPC Pack のインストール ウィザードを開始します。

3.  **[HPC Pack 2012 R2 セットアップ]** 画面で、**[新たにインストールするか、新機能を既存のインストールに追加する]** をクリックします。

    ![HPC Pack 2012 セットアップ][HPC Pack 2012 セットアップ]

4.  **[Microsoft ソフトウェア使用許諾契約書]** ページで、**[次へ]** をクリックします。

5.  **\[インストールの種類の選択]** ページで、**[ヘッド ノードを作成することで新しい HPC クラスターを作成する]** をクリックし、**[次へ]** をクリックします。

    ![インストールの種類の選択][インストールの種類の選択]

6.  ウィザードによってインストール前のテストがいくつか実行されます。すべてのテストが成功したら、**\[インストール規則]** ページで **[次へ]** をクリックします。失敗した場合は、表示された情報を確認して、必要な変更を環境に加えます。テストをもう一度実行するか、必要に応じてインストール ウィザードをもう一度開始します。

    ![インストール規則][インストール規則]

7.  **[HPC DB 構成]** ページで、すべての HPC データベースについて **[ヘッド ノード]** が選択されていることを確認し、**[次へ]** をクリックします。

    ![DB 構成][DB 構成]

8.  ウィザードの残りのページは既定の設定のままにします。**[必要なコンポーネントのインストール]** ページで、**\[インストール]** をクリックします。

    ![インストール][インストール]

9.  インストールが完了したら、**[HPC クラスター マネージャーを開始する]** をオフにして、**\[完了]** をクリックします (ヘッド ノードの構成を完了するために HPC クラスター マネージャーを後の手順で開始します)。

    ![完了][完了]

## Azure サブスクリプションを準備する

[管理ポータル][管理ポータル]を使用して、Azure サブスクリプションで次の手順を使用します。これは、後で、オンプレミスのヘッド ノードから Azure ノードをデプロイするために必要です。

-   管理証明書のアップロード (ヘッド ノードと Azure サービス間の接続をセキュリティ保護するために必要です)

-   Azure ノード (worker ロール インスタンス) を実行する Azure クラウド サービスの作成

-   Azure のストレージ アカウントの作成

    > [WACOM.NOTE]Azure サブスクリプション ID も記録しておきます。これは後で必要になります。この ID を確認するには、Azure の[アカウント情報][アカウント情報]を調べてください。

### 既定の管理証明書のアップロード

HPC Pack ではヘッド ノードに自己署名証明書 (既定の Microsoft HPC Azure 管理証明書) がインストールされます。これを Azure 管理証明書としてアップロードできます。この証明書は、テスト目的および概念実証のデプロイ用に提供されます。

1.  ヘッド ノード コンピューターから、[管理ポータル][管理ポータル]にサインインします。

2.  **[設定]** をクリックし、**[管理証明書]** をクリックします。

3.  コマンド バーで、**[アップロード]** をクリックします。

    ![証明書の設定][証明書の設定]

4.  ヘッド ノードで C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer ファイルを参照し、**[チェック]** をクリックします。

    ![証明書のアップロード][証明書のアップロード]

管理証明書のリストに **[既定の Microsoft HPC Azure 管理]** が表示されます。

### Azure クラウド サービスの作成

> [WACOM.NOTE]パフォーマンスを最高にするには、クラウド サービスとストレージ アカウントを同じリージョンに作成します。

1.  管理ポータルで、コマンド バーの **[新規]** をクリックします。

2.  **[コンピューティング]**、**[クラウド サービス]**、**[簡易作成]** の順にクリックします。

3.  クラウド サービスの URL を入力し、**[クラウド サービスを作成する]** をクリックします。

    ![サービスの作成][サービスの作成]

### Azure のストレージ アカウントの作成

1.  管理ポータルで、コマンド バーの **[新規]** をクリックします。

2.  **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

3.  アカウントの URL を入力し、**[ストレージ アカウントの作成]** をクリックします。

    ![ストレージの作成][ストレージの作成]

## ヘッド ノードを構成する

HPC クラスター マネージャーを使用して Azure ノードをデプロイし、ジョブを送信するには、最初にクラスターの構成手順をいくつか実行する必要があります。

1.  ヘッド ノードで、HPC クラスター マネージャーを開始します。**[ヘッド ノードの選択]** ダイアログ ボックスが表示されたら、**[ローカル コンピューター]** をクリックします。**[展開作業一覧]** が表示されます。

2.  **[必須のデプロイ タスク]** で、**\[ネットワークの構成]** をクリックします。

    ![ネットワークの構成][ネットワークの構成]

3.  ネットワーク構成ウィザードで、**[企業ネットワークのみのすべてのノード]** (トポロジ 5) を選択します。

    ![トポロジ 5][トポロジ 5]

    > [WACOM.NOTE]これはデモンストレーションを目的とした最も単純な構成であり、ヘッド ノードには、Active Directory とインターネットに接続するための 1 つのネットワーク アダプターのみが必要です。このチュートリアルでは、追加のネットワークを必要とするクラスター シナリオは扱いません。

4.  ウィザードの残りのページは既定の設定のままにして **[次へ]** をクリックします。**[レビュー]** タブで、**[構成]** をクリックして、ネットワーク構成を完了します。

5.  **[デプロイ To-do リスト]** で、**[インストール資格情報の提供]** をクリックします。

6.  **\[インストール資格情報]** ダイアログ ボックスで、HPC Pack のインストールに使用するドメイン アカウントの資格情報を入力します。次に、 **[OK]** をクリックします

    ![インストール資格情報][インストール資格情報]

    > [WACOM.NOTE]HPC Pack サービスでは、インストール資格情報のみを使用して、オンプレミスの計算ノードをデプロイします。

7.  **[デプロイ To-do リスト]** で、**[新しいノードの名前付けの構成]** をクリックします。

8.  **[ノードの名前付け系列の指定]** ダイアログ ボックスで、既定の命名系のままにして、**[OK]** をクリックします。

    ![ノードの名前付け][ノードの名前付け]

    > [WACOM.NOTE]この命名系では、ドメインに参加している計算ノードの名前のみが生成されます。Azure ノードは自動的に命名されます。

9.  **[デプロイ To-do リスト]** で、**[ノード テンプレートの作成]** をクリックします。ノード テンプレートを使用して、Azure ノードをクラスターに追加します。

10. ノード テンプレートの作成ウィザードで、次の操作を行います。

    a. **[ノード テンプレートの種類の選択]** ページで、**[Windows Azure ノード テンプレート]** をクリックし、**[次へ]** をクリックします。

    ![ノード テンプレート][ノード テンプレート]

    b. **[次へ]** をクリックし、既定のテンプレート名をそのまま使用します。

    c. **[サブスクリプション情報の入力]** ページで、Azure サブスクリプション ID (Azure の[アカウント情報][アカウント情報]で参照できます) を入力します。次に、**[管理証明書]** で **[参照]** をクリックして、**[既定の Microsoft HPC Azure 管理]** を選択します。その後、**[次へ]** をクリックします。

    ![ノード テンプレート][1]

    d. **[サービス情報の入力]** ページで、前の手順で作成したクラウド サービスとストレージ アカウントを選択します。その後、**[次へ]** をクリックします。

    ![ノード テンプレート][2]

    e. ウィザードの残りのページは既定の設定のままにして **[次へ]** をクリックします。**[レビュー]** タブで、**[作成]** をクリックしてノード テンプレートを作成します。

    > [WACOM.NOTE]既定では、Azure ノード テンプレートに、ノードを手動で開始 (プロビジョニング) および停止するための設定が含まれています。Azure ノードを自動的に開始および停止するスケジュールを構成することもできます。

## クラスターに Azure ノードを追加する

ここでは、ノード テンプレートを使用して、Azure ノードをクラスターに追加します。ノードをクラスターに追加すると、ノードの構成情報が保存されるため、ノードをいつでもクラウド サービスのロール インスタンスとして開始 (プロビジョニング) することができます。Azure ノードについてサブスクリプションに課金されるのは、クラウド サービスでロール インスタンスを実行した後だけです。

このチュートリアルでは、2 つの小規模ノードを追加します。

1.  HPC クラスター マネージャーの **[ノード管理]** で、**[アクション]** ウィンドウの **\[ノードの追加]** をクリックします。

    ![ノードの追加][ノードの追加]

2.  ノードの追加ウィザードの **[デプロイ方法の選択]** ページで、**\[Azure ノードの追加]** をクリックして、**[次へ]** をクリックします。

    ![Azure ノードの追加][Azure ノードの追加]

3.  **[新しいのノードの指定]** ページで、前に作成した Azure ノード テンプレート (「**既定の AzureNode テンプレート**」という名前が付いています) を選択します。ノードの数に **[2]** を、ノードのサイズに **[S]** を選択し、**[次へ]** ボタンをクリックします。

    ![ノードの指定][ノードの指定]

    使用できる仮想マシンのサイズの詳細については、「[Windows Azure の仮想マシンおよびクラウド サービスのサイズ][Windows Azure の仮想マシンおよびクラウド サービスのサイズ]」を参照してください。

4.  **[ノード追加ウィザードの完了]** ページで、**\[完了]** をクリックします。

    HPC クラスター マネージャーに、**AzureCN-0001** と **AzureCN-0002** という名前の 2 つの Azure ノードが表示されます。どちらも **[未展開]** 状態です。

    ![追加されたノード][追加されたノード]

## Azure ノードを開始する

Azure のクラスター リソースを使用する場合、HPC クラスター マネージャーを使用して、Azure ノードを開始 (プロビジョニング) してオンラインにします。

1.  HPC クラスター マネージャーの **[ノード管理]** で、**[アクション]** ウィンドウに表示されているどちらか 1 つまたは両方のノードをクリックして、**[開始]** をクリックします。

    ![ノードの開始][ノードの開始]

2.  **[Azure ノードの開始]** ダイアログ ボックスで、**[開始]** をクリックします。

    ![ノードの開始][3]

    ノードが **[プロビジョニング]** 状態に移行します。プロビジョニング ログを表示して、プロビジョニング進捗状況を確認できます。

    ![ノードのプロビジョニング][ノードのプロビジョニング]

3.  数分後、Azure ノードのプロビジョニングが完了し、ノードが **[オフライン]** 状態になります。この状態では、ロール インスタンスは実行中ですが、クラスター ジョブは受け入れられません。

4.  ロール インスタンスが実行中であることを確認するために、[管理ポータル][管理ポータル]で、**[クラウド サービス]**、クラウド サービスの名前、**[インスタンス]** の順にクリックします。

    ![実行中のインスタンス][実行中のインスタンス]

    2 つの worker ロール インスタンスがサービスで実行中であることがわかります。HPC Pack では、ヘッド ノードと Azure 間の通信を処理するために、2 つの **HpcProxy** インスタンス (サイズは M) も自動的に展開されます。

5.  Azure ノードをオンラインにしてクラスター ジョブを実行するには、ノードを選択し右クリックして、**[オンラインにする]** をクリックします。

    ![オフラインのノード][オフラインのノード]

    ノードが **[オンライン]** 状態であることが表示されます。

## クラスター全体にコマンドを実行する

HPC Pack の **clusrun** コマンドを使用すると、1 つ以上のクラスター ノードでコマンドまたはアプリケーションを実行できます。簡単な例として、**clusrun** を使用して、Azure ノードの IP 構成を取得する手順を紹介します。

1.  ヘッド ノードで、コマンド プロンプトを開きます。

2.  次のコマンドを入力します。

    `clusrun /nodes:azurecn* ipconfig`

3.  次のような出力が表示されます。

    ![clusrun][clusrun]

## テスト ジョブを実行する

ハイブリッド クラスター上で実行するテスト ジョブを送信できます。この例は単純な "パラメーター スイープ" ジョブ (並列コンピューティングの一種) です。**set /a** コマンドを使用して、自身に整数を追加するサブタスクを実行します。クラスター内のすべてのノードが、1 ～ 100 の整数に関するサブタスクを完了します。

1.  HPC クラスター マネージャーの **[ジョブ管理]** で、**[アクション]** ウィンドウの **[新しいパラメーター スイープ ジョブ]** をクリックします。

    ![新しいジョブ][新しいジョブ]

2.  **[新しいパラメーター スイープ ジョブ]** ダイアログ ボックスの **[コマンド ライン]** で、「`set /a *+*`」と入力します (表示されている既定のコマンド ラインを上書きしてください)。残りの設定については既定値のままにして、**[送信]** をクリックしてジョブを送信します。

    ![パラメーター スイープ][パラメーター スイープ]

3.  ジョブが完了したら、**[マイ スイープ タスク]** ジョブをダブルクリックします。

4.  **[タスクの表示]** をクリックして、サブタスクの計算された出力を表示します。

    ![タスクの結果][タスクの結果]

5.  サブタスクの計算を実行したノードを表示するには、**[割り当て済みのノード]** をクリックします (クラスターによっては異なるノード名が表示されます)。

    ![タスクの結果][4]

## Azure ノードを停止する

クラスターを試した後は、HPC クラスター マネージャーを使用して Azure ノードを停止し、アカウントへの不必要な課金を避けることができます。ノードを停止するとクラウド サービスが停止し、Azure ロール インスタンスが削除されます。

1.  HPC クラスター マネージャーの **[ノード管理]** で、両方の Azure ノードを選択します。次に、**[操作]** ウィンドウで **[停止]** をクリックします。

    ![ノードの停止][ノードの停止]

2.  **[Azure ノードの停止]** ダイアログ ボックスで、**[停止]** をクリックします。

    ![ノードの停止][5]

3.  ノードが **[停止中]** 状態に移行します。数分後、HPC クラスター マネージャーに、ノードが **[未展開]** 状態であることが表示されます。

    ![未配置のノード][未配置のノード]

4.  ロール インスタンスが Azure で実行されていないことを確認するために、[管理ポータル][管理ポータル]で、**[クラウド サービス]**、クラウド サービスの名前、**[インスタンス]** を順にクリックします。運用環境にはインスタンスは展開されません。

    ![インスタンスなし][インスタンスなし]

    このチュートリアルはこれで終わりです。

## 関連リソース

-   [HPC Pack 2012 R2 と HPC Pack 2012][HPC Pack 2012 R2 と HPC Pack 2012]
-   [Microsoft HPC Pack を使用した Azure へのバースト][詳細なガイダンス]
-   [Azure ノードへのアプリケーションの展開][Azure ノードへのアプリケーションの展開]
-   [Azure VM の Microsoft HPC Pack][Azure VM の Microsoft HPC Pack]

  [ハイブリッド HPC クラスター]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
  [詳細なガイダンス]: http://go.microsoft.com/fwlink/p/?LinkID=200493
  [さまざまなサイズ]: http://go.microsoft.com/fwlink/p/?LinkId=389844
  [A8/A9 コンピューティング集中型インスタンスについて]: http://go.microsoft.com/fwlink/p/?Linkid=328042
  [前提条件]: #BKMK_Prereq
  [HPC Pack をヘッド ノードにインストールする]: #BKMK_DeployHN
  [Azure サブスクリプションを準備する]: #BKMK_Prpare
  [ヘッド ノードを構成する]: #BKMK_ConfigHN
  [クラスターに Azure ノードを追加する]: #BKMK_worker
  [Azure ノードを開始する]: #BKMK_start
  [クラスター全体にコマンドを実行する]: #BKMK_RunCommand
  [テスト ジョブを実行する]: #BKMK_RunJob
  [Azure ノードを停止する]: #BKMK_stop
  [Azure アカウントの作成に関するページ]: http://www.windowsazure.com/ja-jp/develop/php/tutorials/create-a-windows-azure-account/
  [評価版]: http://technet.microsoft.com/evalcenter/dn205286.aspx
  [ダウンロード]: http://go.microsoft.com/fwlink/p/?linkid=389557
  [HPC Pack 2012 セットアップ]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
  [インストールの種類の選択]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
  [インストール規則]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
  [DB 構成]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
  [インストール]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
  [完了]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
  [管理ポータル]: https://manage.windowsazure.com
  [アカウント情報]: [https://account.windowsazure.com/Subscriptions
  [証明書の設定]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
  [証明書のアップロード]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
  [サービスの作成]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
  [ストレージの作成]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
  [ネットワークの構成]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
  [トポロジ 5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
  [インストール資格情報]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
  [ノードの名前付け]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
  [ノード テンプレート]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
  [1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
  [2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
  [ノードの追加]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
  [Azure ノードの追加]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
  [ノードの指定]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
  [Windows Azure の仮想マシンおよびクラウド サービスのサイズ]: http://msdn.microsoft.com/library/windowsazure/dn197896.aspx
  [追加されたノード]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
  [ノードの開始]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
  [3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
  [ノードのプロビジョニング]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
  [実行中のインスタンス]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
  [オフラインのノード]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
  [clusrun]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
  [新しいジョブ]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
  [パラメーター スイープ]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
  [タスクの結果]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
  [4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
  [ノードの停止]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
  [5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
  [未配置のノード]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
  [インスタンスなし]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png
  [HPC Pack 2012 R2 と HPC Pack 2012]: http://go.microsoft.com/fwlink/p/?LinkID=263697
  [Azure ノードへのアプリケーションの展開]: http://go.microsoft.com/fwlink/p/?LinkId=325317
  [Azure VM の Microsoft HPC Pack]: http://go.microsoft.com/fwlink/p/?linkid=330375
