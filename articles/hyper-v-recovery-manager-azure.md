<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Azure Site Recovery の概要:内部設置型と Azure 間の保護" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery は、内部設置型 VMM クラウドに配置された Hyper-V 仮想マシンの Azure へのレプリケーション、フェイルオーバー、および回復を調整します。" metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery の概要:内部設置型と Azure 間の保護" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Azure Site Recovery の概要:内部設置型と Azure 間の保護

<div class="dev-callout">

Azure Site Recovery を使用して、VMM クラウド内の 内部設置型 Hyper-V ホスト サーバー上の仮想マシンを保護します。利用できる構成は、次のとおりです。

-   **オンプレミスと Azure 間の保護** - 内部設置型仮想マシンを Azure に複製します。Azure Site Recovery コンテナーに保護設定を構成し、有効にします。仮想マシンのデータが、内部設置型 Hyper-V サーバーから Azure ストレージに複製されます。
-   **オンプレミス間の保護** - 内部設置型仮想マシンを別の内部設置型サイトに複製します。Azure Site Recovery コンテナーに保護設定を構成し、有効にします。仮想マシンのデータが、内部設置型 Hyper-V サーバーから別の内部設置型 Hyper-V サーバーに複製されます。 このシナリオの詳細については、「[Azure Site Recovery の概要: オンプレミス間の保護」を参照してください。][Azure Site Recovery の概要: オンプレミス間の保護」を参照してください。].

## <span id="about"></span></a>このチュートリアルについて

このチュートリアルを利用すると、オンプレミスと Azure 間のデプロイの Azure Site Recovery の概念実証をすばやく行うことができます。このチュートリアルでは、可能な場合は、最も短時間ですむ方法と既定の設定を使用しています。このチュートリアルでは、Azure Site Recovery 資格情報コンテナーの作成、ソース VMM サーバーへの Azure Site Recovery プロバイダーのインストール、VMM クラウド内の Hyper-V ホストへの Azure Recovery Services エージェントのインストール、クラウドの保護設定の構成、仮想マシンの保護の有効化、デプロイメントのテストを行います。

フル デプロイについての情報は、下記を参照してください。

-   「[Azure Site Recovery のデプロイ計画][Azure Site Recovery のデプロイ計画]」- フル デプロイを開始する前に必要なプランニングの手順について説明しています。
-   「[Azure Site Recovery のデプロイ : オンプレミスと Azure 間の保護][Azure Site Recovery のデプロイ : オンプレミスと Azure 間の保護]」— 完全なデプロイの手順をステップ バイ ステップで説明しています。

このチュートリアルの使用中に問題が発生した場合は、wiki の記事「[Azure Site Recovery: Common Error Scenarios and Resolutions (Azure Site Recovery: 一般的なエラー シナリオと解決方法)][Azure Site Recovery: Common Error Scenarios and Resolutions (Azure Site Recovery: 一般的なエラー シナリオと解決方法)]」を参照するか、[Azure 復旧サービス フォーラム][Azure 復旧サービス フォーラム] に質問を投稿してください。

</div>

## <span id="before"></span></a>開始する前に

<div class="dev-callout">

このチュートリアルを開始する前に、前提条件を確認してください。

### <span id="HVRMPrereq"></span></a>前提条件

-   **Azure アカウント** — Azure アカウントが必要です。お持ちでない場合は、[Azure の無料評価版][Azure の無料評価版]を参照してください。料金については、「[Azure Site Recovery Manager Pricing Details (Azure Site Recovery Manager の料金詳細)][Azure Site Recovery Manager Pricing Details (Azure Site Recovery Manager の料金詳細)]」を参照してください。
-   **Azure ストレージのアカウント** — Azure にレプリケートしたデータを格納するために Azure ストレージのアカウントが必要になります。アカウントではジオ (主要地域) レプリケーションを有効にする必要があります。アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。Azure ストレージのセットアップの詳細については、「[Microsoft Azure ストレージの概要][Microsoft Azure ストレージの概要]」を参照してください。
-   **VMM サーバー** — System Center 2012 R2 上で実行される VMM サーバー。
-   **VMM クラウド** — VMM サーバー上の少なくとも 1 つのクラウド。このクラウドには以下のものが含まれている必要があります。
    -   1 つ以上の VMM ホスト グループ
    -   各ホスト グループ内に 1 つ以上の Hyper-V ホスト サーバーまたはクラスター。
    -   クラウド内のソース Hyper-V サーバー上に配置された 1 つ以上の仮想マシン。仮想マシンは世代 1 にする必要があります。
-   **仮想マシン** — Azure 要件に準じた仮想マシンが必要です。計画ガイドの「[前提条件とサポート][前提条件とサポート]」を参照してください。
-   Azure にフェイルオーバーするための仮想マシンのサポート要件の詳細については、を参照してください。

## <span id="tutorial"></span></a>チュートリアルの手順

前提条件を確認した後、以下の手順を実行します。

-   [手順 1.コンテナーの作成][手順 1.コンテナーの作成] - Azure Site Recovery コンテナーを作成します。
-   [手順 2.プロバイダー アプリケーションのインストール][手順 2.プロバイダー アプリケーションのインストール] — 登録キーを生成し、その後、VMM サーバーで Microsoft Azure Site Recovery プロバイダー アプリケーションを実行します。プロバイダーがインストールされ、VMM サーバーがコンテナーに登録されます。
-   [手順 3.Azure ストレージ アカウントの追加][手順 3.Azure ストレージ アカウントの追加] — アカウントがない場合、アカウントを作成します。
-   [手順 4.Agent アプリケーションのインストール][手順 4.Agent アプリケーションのインストール] — Microsoft Azure Recovery Services Agent を Hyper-V ホストごとにインストールします。
-   [手順 5.クラウドの保護の構成][手順 5.クラウドの保護の構成] - VMM クラウドの保護設定を構成します。
-   [手順 6.ネットワーク マッピングの構成][手順 6.ネットワーク マッピングの構成] — ソース VM ネットワークをターゲット Azure ネットワークにマッピングするネットワーク マッピングをオプションで構成できます。
-   [手順 7.仮想マシンの保護の有効化][手順 7.仮想マシンの保護の有効化] - 保護する VMM クラウドに配置された仮想マシンの保護を有効にします。
-   [手順 8.デプロイのテスト][手順 8.デプロイのテスト] — 1 台の仮想マシンに対するテスト フェールオーバーを実行することや、復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。

<a name="vault"></a>

## 手順 1.コンテナーの作成

</p>
1.  [管理ポータル][管理ポータル]にサインインします。

2.  **[データ サービス]** をクリックし、**[復旧サービス]** を展開し、**[Site Recovery コンテナー]**クリックします。

3.  **[新規作成]**、**[簡易作成]** の順にクリックします。

4.  **[名前]** ボックスに、コンテナーを識別する表示名を入力します。

5.  **[リージョン]** ボックスで、コンテナーのリージョンを選択します。利用可能なリージョンには、東アジア、西ヨーロッパ、米国西部、米国東部、北ヨーロッパ、東南アジアが含まれています。
6.  **[コンテナーの作成]** をクリックします。

    ![新しいコンテナー][新しいコンテナー]

ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。メイン [復旧サービス] ページで、コンテナーは **[アクティブ]** と表示されています。

<a name="download"></a>

## 手順 2.登録キーの生成と Azure Site Recovery プロバイダーのインストール

</p>
1.  **[復旧サービス]** ページで、コンテナーをクリックして [クイック スタート] ページを開きます。[クイック スタート] は、アイコンを使っていつでも開くことができます。

    ![[クイック スタート] アイコン][]

2.  ドロップダウン リストで、**[内部設置型 Hyper-V サイトと Microsoft Azure 間]** を選択します。
3.  **[VMM サーバーの準備]** で、**[登録キーの生成]** ファイルをクリックします。キーは生成後 5 日間有効です。VMM サーバーにファイルをコピーします。このファイルは、プロバイダーのセットアップ時に必要になります。

    ![登録キー][登録キー]

4.  **[クイック スタート]** ページの **[VMM サーバーの準備]** で、**[VMM サーバーへのインストール用の Microsoft Azure Site Recovery プロバイダーのダウンロード]** をクリックして、最新バージョンのプロバイダー インストール ファイルを取得します。

5.  ソース VMM サーバーでこのファイルを実行します。

6.  **[前提条件の確認]**で、VMM サービスを停止して、プロバイダーのセットアップを開始します。VMM サービスは停止し、セットアップの終了時に自動的に再起動します。

    ![前提条件][前提条件]

7.  **[Microsoft Update]** で、アップデートの内容を設定できます。この設定を行うことで、設定した Microsoft Update のポリシーに従って、プロバイダーの有効な更新がインストールされます。

    ![Microsoft 更新プログラム][Microsoft 更新プログラム]

プロバイダーのインストール後は、セットアップを続行し、サーバーをコンテナーに登録します。

1.  **[インターネット接続]** で、VMM サーバーで実行中のプロバイダーがインターネットに接続する方法を指定します。**[既定のシステム プロキシ設定を使用]** を選択して、サーバー上に構成されている既定のインターネット接続設定を使用します。

    ![インターネット設定][インターネット設定]

2.  **[登録キー]** で、Azure Site Recovery からダウンロードして VMM サーバーにコピーした登録キーを選択します。
3.  **[コンテナー名]** で、サーバーが登録されるコンテナーの名前を確認します。
4.  **[サーバー名]** に、コンテナーで VMM サーバーを識別する表示名を入力します。

    ![サーバー登録][サーバー登録]

5.  **[初期クラウド メタデータ]** 同期で、VMM サーバー上のすべてのクラウドのメタデータをコンテナーと同期するかどうか選択します。この操作は、各サーバーで 1 回のみ実行する必要があります。すべてのクラウドを同期したくない場合は、この設定をオフのままにして、VMM コンソールのクラウドのプロパティで各クラウドを個別に同期できます。

6.  **[データ暗号化]** で、データの暗号化用に自動的に生成された SSL 証明書を保存する場所を指定します。この証明書は、Azure Site Recovery ポータル内で Azure によって保護されているクラウドのデータ暗号化が有効な場合に使用されます。この証明書を安全な場所に保管します。Azure へのフェイルオーバーの実行時に、暗号化されたデータを復号化するために、この証明書を選択します。
    このオプションは、内部設置型サイト間でレプリケーションを実行する場合には関係ありません。

    ![サーバー登録][1]

7.  **[登録]** をクリックしてプロセスを完了します。登録後に、VMM サーバーからのメタデータが、Azure Site Recovery によって取得されます。サーバーは、コンテナーの **[サーバー]** ページの **[リソース]** タブに表示されます。

## <span id="storage"></span></a>手順 3.Azure のストレージ アカウントの作成

Azure ストレージ アカウントがない場合は、**[Azure ストレージ アカウントの追加]** をクリックします。アカウントでは geo レプリケーションを有効にする必要があります。アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。

このチュートリアルを利用すると、オンプレミスと Azure 間のデプロイの Azure Site Recovery の概念実証をすばやく行うことができます。このチュートリアルでは、可能な場合は、最も短時間ですむ方法と既定の設定を使用しています。このチュートリアルでは、Azure Site Recovery 資格情報コンテナーの作成、ソース VMM サーバーへの Azure Site Recovery プロバイダーのインストール、VMM クラウド内の Hyper-V ホストへの Azure Recovery Services エージェントのインストール、クラウドの保護設定の構成、仮想マシンの保護の有効化、デプロイメントのテストを行います。

![ストレージ アカウント][ストレージ アカウント]

## <span id="agent"></span></a>手順 4.Azure Recovery Services エージェントの Hyper-V ホストへのインストール

保護する VMM クラウドに配置されている Hyper-V ホスト サーバーごとに、Azure Recovery Services Agent をインストールします。

1.  [クイック スタート] ページで、**[Azure Site Recovery Services Agent をダウンロードしてホストにインストールする]** をクリックして、最新バージョンの Agent のインストール ファイルを取得します。

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  保護する VMMM クラウドに配置されている Hyper-V ホスト サーバーごとにインストール ファイルを実行します。
3.  **[前提条件のチェック]** ページで **[次へ]** をクリックします。不足している前提条件があると自動的にインストールされます。

    ![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

4.  **[インストール設定]** ページでエージェントのインストール先を指定し、バックアップのメタデータがインストールされるキャッシュの場所を選択します。その後、**[インストール]** をクリックします。

## <span id="clouds"></span></a>手順 5.クラウドの保護設定の構成

VMM サーバーを登録した後、クラウドの保護設定を構成することができます。プロバイダーのインストール時にオプション **[コンテナーとのクラウド データの同期]** を有効にしたので、VMM サーバー上のすべてのクラウドが、コンテナーの **[保護された項目]** タブに表示されます。

![発行済みのクラウド][発行済みのクラウド]

1.  [クイック スタート] ページで、**[VMM クラウドの保護の設定]** をクリックします。

2.  **[保護された項目]** タブで、構成するクラウドをクリックし、**[構成]** タブに移動します。
3.  **[ターゲット]** で、**[Microsoft Azure]** を選択します。
4.  **[ストレージ アカウント]** で、仮想マシンを格納する Azure ストレージを選択します。
5.  **[格納データの暗号化]** を **[オフ]** に設定します。この設定は、内部設置型サイトと Azure 間のレプリケートでデータを暗号化する必要があることを指定します。
6.  **[コピーの頻度]** では、既定の設定をそのまま使用します。この値は、ソースとターゲットの場所の間でデータが同期される頻度を指定します。
7.  **[保持する復旧ポイント]** で、既定の設定をそのまま使用します。既定値である 0 を使用する場合は、プライマリ仮想マシンに対応する最新の復旧ポイントのみが、レプリカのホスト サーバーに格納されます。
8.  **[アプリケーションの整合性スナップショットの頻度]** では、既定の設定をそのまま使用します。この値は、スナップショットを作成する頻度を指定します。スナップショットは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。値を設定する場合は、構成する追加の復旧ポイント数よりも少ない値にしてください。
9.  **[レプリケーションの開始時刻]** で、Azure へのデータの初期レプリケーションを開始する時刻を指定します。Hyper-V ホスト サーバーのタイムゾーンが使用されます。初期レプリケーションはピーク時以外にスケジュールすることをお勧めします。

    ![Cloud replication settings][Cloud replication settings]

この設定を保存すると、ジョブが作成され、これを **[ジョブ]** タブで監視できます。VMM ソース クラウド内のすべての Hyper-V ホスト サーバーは、レプリケーション用に構成されます。 保存後は、クラウド設定は **[構成]** タブで変更できます。ターゲットの場所またはターゲットのストレージを変更するには、クラウド構成を削除してから、クラウドを再構成する必要があります。ストレージ アカウントを変更する場合は、ストレージ アカウントの修正後に保護を有効にした仮想マシンにのみ、その変更が適用されることに注意してください。既存の仮想マシンは新しいストレージ アカウントに移行されません。

## <span id="networkmapping"></span></a>手順 6.ネットワーク マッピングの構成

このチュートリアルでは、テスト環境で Azure Site Recovery を展開する最も簡単な方法について説明します。このチュートリアルの一環としてネットワーク マッピングを構成することを希望する場合は、計画ガイドの[ネットワーク マッピングの準備に関するページ][ネットワーク マッピングの準備に関するページ]を参照してください。マッピングを構成するには、デプロイ ガイドの「[ネットワーク マッピングの構成][ネットワーク マッピングの構成]」の手順に従ってください。

## <span id="virtualmachines"></span></a>手順 7.仮想マシンの保護の有効化

サーバー、クラウド、およびネットワークを正しく構成した後で、クラウド内の仮想マシンの保護を有効にすることができます。以下の点に注意してください。

-   仮想マシンは Azure 要件を満たしている必要があります。計画ガイドの「[前提条件とサポート][前提条件とサポート]」で確認してください。
-   オペレーティング システムとオペレーティング システム ディスクの保護を有効にするには、仮想マシンにプロパティを設定する必要があります。仮想マシン テンプレートを使用して VMM 内で仮想マシンを作成する際に、プロパティを設定できます。また、仮想マシンのプロパティの [全般] タブと [ハードウェア構成] タブで既存の仮想マシンに対してこれらのプロパティを設定することもできます。VMM でこれらのプロパティを設定していない場合は、Azure Site Recovery ポータルで構成できます。

![Create virtual machine][Create virtual machine]

![Modify virtual machine properties][Modify virtual machine properties]

1.  保護を有効にするために、仮想マシンが配置されているクラウドの **[仮想マシン]** タブで、**[保護を有効にする]** をクリックしてから **[仮想マシンを追加する]** を選択します。
2.  クラウド内の仮想マシンのリストから、保護する仮想マシンを選択します。

    ![仮想マシンの保護の有効化][仮想マシンの保護の有効化]

3.  仮想マシンのプロパティを確認し、必要に応じて変更します。

    ![Verify virtual machines][Verify virtual machines]

[\*\*ジョブ\*\*] タブで、初期レプリケーションを含む [保護の有効化] アクションの進捗状況を確認します。保護の最終処理のジョブが実行されると、仮想マシンは、フェールオーバーを実行できる状態になります。保護が有効され仮想マシンが複製されると、Azure でそれらの状態を表示できます。

![仮想マシン保護ジョブ][仮想マシン保護ジョブ]

## <span id="test"></span></a>手順 8.展開をテスト

展開をテストするために、1 台の仮想マシンに対するテスト フェールオーバーを実行することや、複数の仮想マシンで構成される復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。以下の点に注意してください。

-   フェールオーバー後に Azure でリモート デスクトップを使用して仮想マシンに接続する場合は、テストのフェールオーバーを実行する前に、仮想マシンのリモート デスクトップ接続を有効にします。
-   フェールオーバー後、パブリック IP アドレスを使用して、Azure の仮想マシンにリモート デスクトップで接続します。この接続では、パブリック アドレスを使用する仮想マシンの接続を妨げるドメイン ポリシーを使用していないことを確認してください。

-   復旧計画作成の手順については、「[復旧計画の作成およびカスタマイズ: オンプレミスと Azure 間][復旧計画の作成およびカスタマイズ: オンプレミスと Azure 間]」を参照してください。
-   テスト フェールオーバー実行の手順については、「[オンプレミスと Azure 間のデプロイメントのテスト][オンプレミスと Azure 間のデプロイメントのテスト]」を参照してください。

</p>
### <span id="runtest"></span></a>アクティビティの監視

**[ジョブ]** タブと **[ダッシュボード]** を使用して、Azure Site Recovery コンテナーで実行されるメイン ジョブを表示して監視できます。これには、クラウドに対する保護の構成、仮想マシンに対する保護の有効化と無効化、フェールオーバー (計画されたフェールオーバー、計画されていないフェールオーバー、またはテスト フェールオーバー) の実行、計画されていないフェールオーバーのコミットが含まれます。

**[ジョブ]** タブからは、ジョブの表示、ジョブの詳細とエラーの表示、特定の条件に一致するジョブを取得するジョブ クエリの実行、ジョブの Excel へのエクスポート、失敗したジョブの再開を行うことができます。

**[ダッシュボード]** からは、最新バージョンのプロバイダーとエージェントのインストール ファイルのダウンロード、コンテナーの構成情報の取得、コンテナーによって管理されている保護対象の仮想マシンの数の確認、最近のジョブの確認、コンテナー証明書の管理、仮想マシンの再同期を行うことができます。

ジョブの操作とダッシュボードの詳細については、「[Operations and Monitoring Guide (運用と監視ガイド)][Operations and Monitoring Guide (運用と監視ガイド)]」を参照してください。

## <span id="next"></span></a>次のステップ

-   完全な運用環境で Azure Site Recovery の計画と展開を実行するには、「[Planning Guide for Azure Site Recovery (Azure Site Recovery 計画ガイド)][Azure Site Recovery のデプロイ計画]」と「[Deployment Guide for Azure Site Recovery (Azure Site Recovery デプロイ ガイド)][Deployment Guide for Azure Site Recovery (Azure Site Recovery デプロイ ガイド)]」を参照してください。
-   疑問がある場合は、「[Azure Recovery Services Forum (Azure 復旧サービス フォーラム)][Azure 復旧サービス フォーラム]」にアクセスしてください。

</div>

  [Azure Site Recovery の概要: オンプレミス間の保護」を参照してください。]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Azure Site Recovery のデプロイ計画]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Azure Site Recovery のデプロイ : オンプレミスと Azure 間の保護]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Azure Site Recovery: Common Error Scenarios and Resolutions (Azure Site Recovery: 一般的なエラー シナリオと解決方法)]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure 復旧サービス フォーラム]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Azure の無料評価版]: http://aka.ms/try-azure
  [Azure Site Recovery Manager Pricing Details (Azure Site Recovery Manager の料金詳細)]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Microsoft Azure ストレージの概要]: http://go.microsoft.com/fwlink/?LinkId=398704
  [前提条件とサポート]: http://go.microsoft.com/fwlink/?LinkId=402602
  [手順 1.コンテナーの作成]: #vault
  [手順 2.プロバイダー アプリケーションのインストール]: #download
  [手順 3.Azure ストレージ アカウントの追加]: #storage
  [手順 4.Agent アプリケーションのインストール]: #agent
  [手順 5.クラウドの保護の構成]: #clouds
  [手順 6.ネットワーク マッピングの構成]: #NetworkMapping
  [手順 7.仮想マシンの保護の有効化]: #virtualmachines
  [手順 8.デプロイのテスト]: #test
  [管理ポータル]: https://manage.windowsazure.com
  [新しいコンテナー]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [[クイック スタート] アイコン]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [登録キー]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png
  [前提条件]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft 更新プログラム]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [インターネット設定]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [サーバー登録]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [ストレージ アカウント]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [発行済みのクラウド]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png
  [ネットワーク マッピングの準備に関するページ]: http://go.microsoft.com/fwlink/?LinkId=324817
  [ネットワーク マッピングの構成]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Create virtual machine]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png
  [仮想マシンの保護の有効化]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png
  [Verify virtual machines]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png
  [仮想マシン保護ジョブ]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [復旧計画の作成およびカスタマイズ: オンプレミスと Azure 間]: http://go.microsoft.com/fwlink/?LinkId=511492
  [オンプレミスと Azure 間のデプロイメントのテスト]: http://go.microsoft.com/fwlink/?LinkId=511494
  [Operations and Monitoring Guide (運用と監視ガイド)]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Deployment Guide for Azure Site Recovery (Azure Site Recovery デプロイ ガイド)]: http://go.microsoft.com/fwlink/?LinkId=321295
