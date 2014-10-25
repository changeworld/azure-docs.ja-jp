<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Azure Site Recovery の概要:オンプレミス間の保護


Azure Site Recovery を使用して、VMM クラウド内の 内部設置型 Hyper-V ホスト サーバー上の仮想マシンを保護します。利用できる構成は、次のとおりです。

-   **オンプレミス間の保護** - 内部設置型仮想マシンを別の内部設置型サイトに複製します。Azure Site Recovery コンテナーの保護設定を構成して有効にしてください。仮想マシンのデータが、内部設置型 Hyper-V サーバーから別の内部設置型 Hyper-V サーバーに複製されます。 このシナリオについての詳細は、「[Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection (Azure Site Recovery の概要: オンプレミス間の保護)」を参照してください。][Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection (Azure Site Recovery の概要: オンプレミス間の保護)」を参照してください。].
-   **オンプレミスと Azure 間の保護** - 内部設置型仮想マシンを Azure に複製します。Azure Site Recovery コンテナーの保護設定を構成して有効にしてください。仮想マシンのデータが、内部設置型 Hyper-V サーバーから Azure ストレージに複製されます。このシナリオについての詳細は、「[Getting Started with Azure Site Recovery: On-Premises to Azure Protection (Azure Site Recovery の概要: オンプレミスと Azure 間の保護)」を参照してください。][Getting Started with Azure Site Recovery: On-Premises to Azure Protection (Azure Site Recovery の概要: オンプレミスと Azure 間の保護)」を参照してください。]

## <span id="about"></span></a>このチュートリアルについて

このチュートリアルを利用すると、オンプレミスと Azure 間のデプロイの Azure Site Recovery の概念実証をすばやく行うことができます。このチュートリアルでは、可能な場合は、最も短時間ですむ方法と既定の設定を使用しています。このチュートリアルでは、Azure Site Recovery コンテナーの作成、ソース VMM サーバーへの Azure Site Recovery プロバイダーのインストール、クラウドの保護設定の構成、仮想マシンの保護の有効化、展開のテストを行います。

フル デプロイについての情報は、下記を参照してください。

-   [Plan for Azure Site Recovery Deployment (Azure Site Recovery のデプロイメントのプランニング)][Plan for Azure Site Recovery Deployment (Azure Site Recovery のデプロイメントのプランニング)] - フル デプロイを開始する前に完了する必要のあるプランニングの手順について説明しています。
-   [Azure Site Recovery のデプロイ: オンプレミス間の保護][Azure Site Recovery のデプロイ: オンプレミス間の保護] - フル デプロイを行うための手順を説明しています。

このチュートリアルの使用中に問題が発生した場合は、wiki の記事「[Azure Site Recovery: Common Error Scenarios and Resolutions (Azure Site Recovery: 一般的なエラー シナリオと解決方法)][Azure Site Recovery: Common Error Scenarios and Resolutions (Azure Site Recovery: 一般的なエラー シナリオと解決方法)]」を参照するか、[Azure 復旧サービス フォーラム][Azure 復旧サービス フォーラム] に質問を投稿してください。

</div>

## <span id="before"></span></a>開始する前に


このチュートリアルを開始する前に、前提条件を確認してください。

### <span id="HVRMPrereq"></span></a>前提条件

-   **Azure アカウント** — Azure アカウントが必要です。お持ちでない場合は、[Azure の無料評価版][Azure の無料評価版]を参照してください。料金については、「[Azure Site Recovery Manager Pricing Details (Azure Site Recovery Manager の料金詳細)][Azure Site Recovery Manager Pricing Details (Azure Site Recovery Manager の料金詳細)]」を参照してください。
-   **VMM server** — System Center 2012 SP1 または System Center 2012 R2 で実行されている、少なくとも 1 つの VMM サーバーが必要です。
-   **VMM クラウド** — 保護するソース VMM サーバー上で少なくとも 1 つのクラウドが必要であり、ターゲット VMM サーバー上でも 1 つのクラウドが必要です。1 つの VMM サーバーを実行している場合は、2 つのクラウドが必要です。保護するプライマリ クラウドには、次のものが含まれている必要があります。
    -   1 つ以上の VMM ホスト グループ
    -   各ホスト グループ内に 1 つ以上の Hyper-V ホスト サーバーまたはクラスター。
    -   クラウド内のソース Hyper-V サーバー上に配置された 1 つ以上の仮想マシン。

## <span id="tutorial"></span></a>チュートリアルの手順

前提条件を確認した後、以下の手順を実行します。

-   [手順 1.コンテナーの作成][手順 1.コンテナーの作成]— - Azure Site Recovery コンテナーを作成します。
-   [手順 2.プロバイダー アプリケーションのインストール][手順 2.プロバイダー アプリケーションのインストール] — 登録キーを生成し、その後、VMM サーバーで Microsoft Azure Site Recovery プロバイダー アプリケーションを実行します。プロバイダーがインストールされ、VMM サーバーがコンテナーに登録されます。
-   [手順 3.クラウドの保護の構成][手順 3.クラウドの保護の構成] - VMM クラウドの保護設定を構成します。
-   <a href="#networkmapping">手順 5.ネットワーク マッピングの構成 — 必要に応じて、ネットワーク マッピングを構成して、ソース VM ネットワークをターゲット VM ネットワークにマッピングできます。
-   [手順 6.ストレージのマッピングの構成][手順 6.ストレージのマッピングの構成] — 必要に応じて、ストレージのマッピングを構成して、ソース VMM サーバー上の記憶域の分類をターゲット サーバー上の記憶域の分類にマッピングできます。
-   [手順 7.仮想マシンの保護を有効にする][手順 7.仮想マシンの保護を有効にする] - 保護対象の VMM クラウドに配置された仮想マシンの保護を有効にします。
-   [手順 8.復旧計画の構成と実行][手順 8.復旧計画の構成と実行] - 復旧計画を作成し、その計画のテスト フェールオーバーを実行します。

<a name="vault"></a>

## 手順 1.コンテナーの作成

</p>
1.  [管理ポータル][管理ポータル]にサインインします。

2.  **[データ サービス]** をクリックし、**[復旧サービス]** を展開し、**[Site Recovery コンテナー]**クリックします。

3.  **[新規作成]**、**[簡易作成]** の順にクリックします。

4.  **[名前]** ボックスに、コンテナーを識別する表示名を入力します。

5.  **[リージョン]** ボックスで、コンテナーのリージョンを選択します。サポートされているリージョンを確認するには、「[Azure Site Recovery Pricing Details (Azure Site Recovery の料金の詳細)][Azure Site Recovery Pricing Details (Azure Site Recovery の料金の詳細)]」で利用可能地域を参照してください。

6.  **[コンテナーの作成]** をクリックします。

    ![新しいコンテナー][新しいコンテナー]

ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。メイン [復旧サービス] ページで、コンテナーは **[アクティブ]** と表示されています。

<a name="upload"></a>

## 手順 2.コンテナーの構成

</p>
1.  **[復旧サービス]** ページで、コンテナーをクリックして [クイック スタート] ページを開きます。[クイック スタート] は、アイコンを使っていつでも開くことができます。

    ![クイック スタート アイコン][]

2.  ドロップダウン リストで、**[2 つの内部設置型 Hyper-V サイト間]** を選択します。
3.  **[VMM サーバーの準備]** で、**[登録キーの生成]** ファイルをクリックします。キーは生成後 5 日間有効です。VMM サーバーにファイルをコピーします。このファイルは、プロバイダーのセットアップ時に必要になります。

    ![登録キー][登録キー]

<a name="download"></a>

## 手順 3.Azure Site Recovery プロバイダーのインストール

</p>
1.  **[クイック スタート]** ページの **[VMM サーバーの準備]** で、**[VMM サーバーへのインストール用の Microsoft Azure Site Recovery プロバイダーのダウンロード]** をクリックして、最新バージョンのプロバイダー インストール ファイルを取得します。

2.  ソース VMM サーバーとターゲット VMM サーバーでこのファイルを実行します。

3.  **[前提条件の確認]**で、VMM サービスを停止して、プロバイダーのセットアップを開始します。VMM サービスは停止し、セットアップの終了時に自動的に再起動します。

    ![前提条件][前提条件]

4.  **[Microsoft Update]** で、アップデートの内容を設定できます。この設定を行うことで、設定した Microsoft Update のポリシーに従って、プロバイダーの有効な更新がインストールされます。

    ![Microsoft 更新プログラム][Microsoft 更新プログラム]

プロバイダーのインストール後は、セットアップを続行し、サーバーをコンテナーに登録します。

1.  [インターネット接続] ページで、VMM サーバーで実行中のプロバイダーがインターネットに接続する方法を指定します。**[既定のシステム プロキシ設定を使用]** を選択して、サーバー上に構成されている既定のインターネット接続設定を使用します。

    ![インターネット設定][インターネット設定]

2.  **[登録キー]** で、Azure Site Recovery からダウンロードして VMM サーバーにコピーした登録キーを選択します。
3.  **[コンテナー名]** で、サーバーが登録されるコンテナーの名前を確認します。
4.  **[サーバー名]** に、コンテナーで VMM サーバーを識別する表示名を入力します。

    ![サーバー登録][サーバー登録]

5.  **[初期クラウド メタデータ]** 同期で、VMM サーバー上のすべてのクラウドのメタデータをコンテナーと同期するかどうか選択します。この操作は、各サーバーで 1 回のみ実行する必要があります。すべてのクラウドを同期したくない場合は、この設定をオフのままにして、VMM コンソールのクラウドのプロパティで各クラウドを個別に同期できます。

6.  **[データ暗号化]** で、Azure で保護されているデータを暗号化するために使用する証明書を生成します。
    このオプションは、内部設置型サイトから別の内部設置型サイトへ複製する場合は使用する必要がありません。

    ![サーバー登録][1]

7.  **[登録]** をクリックしてプロセスを完了します。登録後に、VMM サーバーからのメタデータが、Azure Site Recovery によって取得されます。サーバーは、コンテナーの **[サーバー]** ページの **[リソース]** タブに表示されます。

## <span id="clouds"></span></a>手順 4.クラウドの保護設定の構成

VMM サーバーを登録した後、クラウドの保護設定を構成することができます。プロバイダーのインストール時にオプション **[コンテナーとのクラウド データの同期]** を有効にしたので、VMM サーバー上のすべてのクラウドが、コンテナーの **[保護された項目]** タブに表示されます。

![発行済みのクラウド][発行済みのクラウド]

1.  [クイック スタート] ページで、**[VMM クラウドの保護の設定]** をクリックします。
2.  **[保護された項目]** タブで、構成するクラウドを選択し、**[構成]** タブに移動します。以下の点に注意してください。
3.  **[Target]** で、**[VMM]** を選択します。
4.  **[ターゲットの場所]** で、復旧時に使用するクラウドを管理するオンサイト VMM サーバーを選択します。
5.  **[ターゲット クラウド]** で、ソース クラウド内の仮想マシンのフェールオーバー時に使用するターゲット クラウドを選択します。以下の点に注意してください。

    -   保護する仮想マシンの復旧要件を満たしたターゲット クラウドを選択することをお勧めします。
    -   クラウドは、プライマリ クラウドまたはターゲット クラウドとして、1 つのクラウド ペアに属することしかできません。

6.  **[コピーの頻度]** では、既定の設定をそのまま使用します。この値は、ソースとターゲットの場所の間でデータが同期される頻度を指定します。この設定は、Hyper-V ホストが Windows Server 2012 R2 を実行している場合にのみ該当します。他のサーバーでは、5 分間という既定の設定が使用されます。
7.  **[追加の復旧ポイント]** では、既定の設定をそのまま使用します。この値は、追加の復旧ポイントを作成するかどうかを指定します。既定値である 0 を使用する場合は、プライマリ仮想マシンに対応する最新の復旧ポイントのみが、レプリカのホスト サーバーに格納されます。
8.  **[アプリケーションの整合性スナップショットの頻度]** では、既定の設定をそのまま使用します。この値は、スナップショットを作成する頻度を指定します。スナップショットは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。チュートリアルでこの値を設定する場合は、構成する追加の復旧ポイントの値より小さくするように注意してください。
    ![保護設定の構成][保護設定の構成]
9.  **[データ転送の圧縮]** で、転送されるレプリケート済みデータを圧縮するかどうかを指定します。
10. **[認証]** で、プライマリおよび復旧用の Hyper-V ホスト サーバー間でトラフィックを認証する方法を指定します。動作している Kerberos 環境が既に構成済みの場合を除き、このチュートリアルでは、[HTTPS] を選択します。Azure Site Recovery は、HTTPS 認証に使用する証明書を自動的に構成します手動で構成する必要はありません。この設定は、Windows Server 2012 R2 で実行されている Hyper-V ホスト サーバーだけに関連することに注意してください。
11. **[ポート]** で、既定の設定をそのまま使用します。この値は、ソースとターゲットの Hyper-V ホスト コンピューターがレプリケーション トラフィックをリッスンするポートの番号を設定します。
12. **[レプリケーション方法]** で、通常のレプリケーションを開始する前に実行する初期レプリケーションで、ソースからターゲットにデータを複製する際の処理方法を指定します。

    -   **ネットワーク経由**: ネットワーク経由でデータをコピーすると、時間がかかり大量のリソースを消費します。クラウドの仮想マシンで使用する仮想ハード ディスクの容量が比較的小さい場合、そして、プライマリ VMM サーバーがセカンダリ VMM サーバーに高速回線で接続されている場合に、このオプションを使用することをお勧めします。コピーを直ちに開始することも、実行時刻を選択することもできます。ネットワーク レプリケーションを使用する場合は、ピーク時間以外に実行することをお勧めします。
    -   **オフライン**: この方法は、外部メディアを使用して初期レプリケーションを実行することを指定します。これは、ネットワーク性能の低下を避ける場合、または、場所が地理的に離れている場合に便利です。この方法を使用するには、ソース クラウド上のエクスポート場所と、ターゲット クラウド上のインポート場所を指定します。仮想マシンの保護を有効にすると、指定されたエクスポート場所に仮想ハード ディスクがコピーされます。それをターゲット サイトに送信して、インポート場所にコピーします。インポートされた情報はレプリカ仮想マシンに自動的にコピーされます。オフライン レプリケーションの前提条件の一覧については、展開ガイドの「[ステップ 3. VMM クラウドの保護設定の構成][ステップ 3. VMM クラウドの保護設定の構成]」を参照してください。

13. クラウドのプロパティの [仮想マシン] タブの **[仮想マシンの保護を削除する]** オプションを選択して仮想マシンの保護を停止する場合に削除するレプリカ仮想マシンを、**[レプリカ仮想マシンの削除]** を選択して指定します。この設定を有効にすると、保護を無効にしたときに、仮想マシンの Azure Site Recovery からの削除、VMM コンソールでの仮想マシンの Site Recovery 設定の削除、レプリカの削除が行われます。
    ![保護設定の構成][2]

この設定を保存すると、ジョブが作成され、これを **[ジョブ]** タブで監視できます。VMM ソース クラウド内のすべての Hyper-V ホスト サーバーは、レプリケーション用に構成されます。クラウド設定は **[構成]** タブで変更できます。ターゲットの場所やターゲット クラウドを変更する場合は、クラウド構成を削除して、クラウドを再構成する必要があります。

## <span id="networkmapping"></span></a>手順 5.ネットワーク マッピングの構成

このチュートリアルでは、テスト環境で Azure Site Recovery を展開する最も簡単な方法について説明します。このチュートリアルの一環としてネットワーク マッピングを構成することを希望する場合は、計画ガイドの[ネットワーク マッピングの準備に関するページ][ネットワーク マッピングの準備に関するページ]を参照してください。マッピングを構成するには、デプロイ ガイドの「[ネットワーク マッピングの構成][ネットワーク マッピングの構成]」の手順に従ってください。

## <span id="storagemapping"></span></a>手順 6.ストレージ マッピングの構成

このチュートリアルでは、テスト環境で Azure Site Recovery を展開する最も簡単な方法について説明します。このチュートリアルの一環としてストレージ マッピングを構成する場合は、展開ガイドの「[ストレージ マッピングの構成][ストレージ マッピングの構成]」の手順に従ってください。

## <span id="enablevirtual"></span></a>手順 7.仮想マシンの保護の有効化

サーバー、クラウド、およびネットワークを正しく構成した後で、クラウド内の仮想マシンの保護を有効にすることができます。

1.  仮想マシンが配置されているクラウドの **[仮想マシン]** タブで、**[保護の有効化]** をクリックし、**[仮想マシンの追加]** を選択します。
2.  クラウド内の仮想マシンのリストから、保護する仮想マシンを選択します。

![仮想マシンの保護の有効化][仮想マシンの保護の有効化]

[\*\*ジョブ\*\*] タブで、初期レプリケーションを含む [保護の有効化] アクションの進捗状況を確認します。保護の最終処理のジョブが実行されると、仮想マシンは、フェールオーバーを実行できる状態になります。保護が有効され仮想マシンが複製されると、Azure でそれらの状態を表示できます。

![仮想マシン保護ジョブ][仮想マシン保護ジョブ]

## <span id="recoveryplans"></span></a>手順 8.展開をテスト

展開をテストするために、1 台の仮想マシンに対するテスト フェールオーバーを実行することや、複数の仮想マシンで構成される復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。

-   復旧計画作成の手順については、「[復旧計画の作成およびカスタマイズ: オンプレミスと Azure 間][復旧計画の作成およびカスタマイズ: オンプレミスと Azure 間]」を参照してください。
-   テスト フェールオーバー実行の手順については、「[オンプレミス間のデプロイのテスト][オンプレミス間のデプロイのテスト]」を参照してください。

### <span id="runtest"></span></a>アクティビティの監視

**[ジョブ]** タブと **[ダッシュボード]** を使用して、Azure Site Recovery コンテナーで実行されるメイン ジョブを表示して監視できます。これには、クラウドに対する保護の構成、仮想マシンに対する保護の有効化と無効化、フェールオーバー (計画されたフェールオーバー、計画されていないフェールオーバー、またはテスト フェールオーバー) の実行、計画されていないフェールオーバーのコミットが含まれます。

**[ジョブ]** タブからは、ジョブの表示、ジョブの詳細とエラーの表示、特定の条件に一致するジョブを取得するジョブ クエリの実行、ジョブの Excel へのエクスポート、失敗したジョブの再開を行うことができます。

**[ダッシュボード]** からは、最新バージョンのプロバイダーとエージェントのインストール ファイルのダウンロード、コンテナーの構成情報の取得、コンテナーによって管理されている保護対象の仮想マシンの数の確認、最近のジョブの確認、コンテナー証明書の管理、仮想マシンの再同期を行うことができます。

ジョブの操作とダッシュボードの詳細については、「[Operations and Monitoring Guide (運用と監視ガイド)][Operations and Monitoring Guide (運用と監視ガイド)]」を参照してください。

## <span id="next"></span></a>次のステップ

-   完全な運用環境で Azure Site Recovery の計画と展開を実行するには、「[Planning Guide for Azure Site Recovery (Azure Site Recovery 計画ガイド)][Plan for Azure Site Recovery Deployment (Azure Site Recovery のデプロイメントのプランニング)]」と「[Deployment Guide for Azure Site Recovery (Azure Site Recovery デプロイ ガイド)][Azure Site Recovery のデプロイ: オンプレミス間の保護]」を参照してください。
-   ご不明な点やご質問などがありましたら、[Azure 復旧サービス フォーラム][Azure 復旧サービス フォーラム]にアクセスしてください。

</div>

  [Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection (Azure Site Recovery の概要: オンプレミス間の保護)」を参照してください。]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Getting Started with Azure Site Recovery: On-Premises to Azure Protection (Azure Site Recovery の概要: オンプレミスと Azure 間の保護)」を参照してください。]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Plan for Azure Site Recovery Deployment (Azure Site Recovery のデプロイメントのプランニング)]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Azure Site Recovery のデプロイ: オンプレミス間の保護]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery: Common Error Scenarios and Resolutions (Azure Site Recovery: 一般的なエラー シナリオと解決方法)]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure 復旧サービス フォーラム]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Azure の無料評価版]: http://aka.ms/try-azure
  [Azure Site Recovery Manager Pricing Details (Azure Site Recovery Manager の料金詳細)]: http://go.microsoft.com/fwlink/?LinkId=378268
  [手順 1.コンテナーの作成]: #vault
  [手順 2.プロバイダー アプリケーションのインストール]: #download
  [手順 3.クラウドの保護の構成]: #clouds
  [手順 6.ストレージのマッピングの構成]: #storagemapping
  [手順 7.仮想マシンの保護を有効にする]: #enablevirtual
  [手順 8.復旧計画の構成と実行]: #recovery%20plans
  [管理ポータル]: https://manage.windowsazure.com
  [Azure Site Recovery Pricing Details (Azure Site Recovery の料金の詳細)]: http://go.microsoft.com/fwlink/?LinkId=389880
  [新しいコンテナー]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [クイック スタート アイコン]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [登録キー]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [前提条件]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft 更新プログラム]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [インターネット設定]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [サーバー登録]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [発行済みのクラウド]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [保護設定の構成]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [ステップ 3. VMM クラウドの保護設定の構成]: http://go.microsoft.com/fwlink/?LinkId=323469
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [ネットワーク マッピングの準備に関するページ]: http://go.microsoft.com/fwlink/?LinkId=324817
  [ネットワーク マッピングの構成]: http://go.microsoft.com/fwlink/?LinkId=402534
  [ストレージ マッピングの構成]: http://go.microsoft.com/fwlink/?LinkId=402535
  [仮想マシンの保護の有効化]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [仮想マシン保護ジョブ]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [復旧計画の作成およびカスタマイズ: オンプレミスと Azure 間]: http://go.microsoft.com/fwlink/?LinkId=511492
  [オンプレミス間のデプロイのテスト]: http://go.microsoft.com/fwlink/?LinkId=511493
  [Operations and Monitoring Guide (運用と監視ガイド)]: http://go.microsoft.com/fwlink/?LinkId=398534
