---
title: Azure に移行するために Azure Migrate Server Assessment を使用して物理サーバーを評価する
description: Azure Migrate Server Assessment を使用して、Azure に移行するためにオンプレミスの物理サーバーを評価する方法について説明します。
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 10cd13831fe6cb6b843f0a79a5d5ba56814bcde0
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028992"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Azure Migrate を使用した物理サーバーの評価: Server Assessment

この記事では、Azure Migrate: Server Assessment ツールを使用して、オンプレミスの物理サーバーを評価する方法について説明します。Server Assessment ツールを追加済みであることを確認してください。

[Azure Migrate](migrate-services-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。

これは、物理サーバーを評価して Azure に移行する方法を示すシリーズの 2 番目のチュートリアルです。 このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Migrate プロジェクトを設定する。
> * 物理サーバーを評価するためにオンプレミスで実行される Azure Migrate アプライアンスを設定する。
> * オンプレミスの物理サーバーの継続的な検出を開始する。 アプライアンスにより、検出されたサーバーの構成データとパフォーマンス データが Azure に送信されます。
> * 検出されたサーバーをグループ化し、サーバー グループを評価する。
> * 評価を確認する。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、ハウツー記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

- このシリーズの最初のチュートリアルを[完了](tutorial-prepare-physical.md)します。 そうしないと、このチュートリアルの手順はうまくいきません。
- 最初のチュートリアルでは、以下のことを行ったはずです。
    - Azure Migrate の [Azure アクセス許可を設定](tutorial-prepare-physical.md#prepare-azure)する。
    - 評価のために[物理サーバーを準備する](tutorial-prepare-physical.md#prepare-for-physical-server-assessment)。 アプライアンスの要件を確認する必要があります。 また、物理サーバーの検出用にアカウントが設定されている必要があります。 必要なポートが使用可能であり、Azure にアクセスするための URL がわかっている必要があります。


## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate プロジェクトを設定する

次のように、新しい Azure Migrate プロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** の **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** をクリックします。

    ![サーバーの検出と評価](./media/tutorial-assess-physical/assess-migrate.png)

4. **[作業の開始]** で、 **[ツールの追加]** をクリックします。
5. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。     
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 アジア、ヨーロッパ、英国、および米国がサポートされています。

    - プロジェクトの地理的な場所は、オンプレミスのサーバーから収集されたメタデータを格納するためにのみ使用されます。
    - 移行を実行するときは、任意のターゲット リージョンを選択できます。

    ![Azure Migrate プロジェクトの作成](./media/tutorial-assess-physical/migrate-project.png)


7. **[次へ]** をクリックします。
8. **[評価ツールの選択]** で、次を選択します: **[Azure Migrate: Server Assessment]**  >  **[次へ]** 。

    ![Azure Migrate プロジェクトの作成](./media/tutorial-assess-physical/assessment-tool.png)

9. **[移行ツールの選択]** で、 **[今は移行ツールの追加をスキップします]**  >  **[次へ]** の順に選択します。
10. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** をクリックします。
11. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトのページが表示されます。 プロジェクトが表示されない場合は、Azure Migrate ダッシュボードの **[サーバー]** からアクセスできます。


## <a name="set-up-the-appliance"></a>アプライアンスを設定する

Azure Migrate: Server Assessment では、軽量のアプライアンスが実行されます。

- このアプライアンスにより、物理サーバーの検出が実行され、サーバーのメタデータとパフォーマンス データが Azure Migrate Server Assessment に送信されます。
- アプライアンスを設定するには、次のようにします。
    - Azure portal から、Azure Migrate インストーラー スクリプトが含まれた ZIP ファイルをダウンロードします。
    - ZIP ファイルの内容を抽出します。 管理特権で PowerShell コンソールを起動します。
    - PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。
    - アプライアンスを初めて構成し、Azure Migrate プロジェクトに登録します。
- 1 つの Azure Migrate プロジェクトに対して、複数のアプライアンスを設定できます。 すべてのアプライアンスで多数の物理サーバーを検出できます。 アプライアンスごとに最大 250 台のサーバーを検出できます。

### <a name="download-the-installer-script"></a>インストーラー スクリプトをダウンロードする

アプライアンスの ZIP ファイルをダウンロードします。

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** をクリックします。
3. **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。

    ![インストーラーのダウンロード](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller\AzureMigrateInstaller.ps1 SHA256```

3.  アプライアンスの最新のバージョンでは、生成されたハッシュがこれらの設定と一致する必要があります。

  **アルゴリズム** | **ハッシュ値**
  --- | ---
  MD5 | 96fd99581072c400aa605ab036a0a7c0
  SHA256 | f5454beef510c0aa38ac1c6be6346207c351d5361afa0c9cea4772d566fcdc36

### <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate インストーラー スクリプトを実行する

インストーラー スクリプトでは以下が実行されます。

- エージェントと、物理サーバーの検出と評価のための Web アプリケーションをインストールする。
- Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
- IIS 書き込み可能モジュールをダウンロードしてインストールする。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
- パスに次のファイルを作成する。
    - **構成ファイル**: %ProgramData%\Microsoft Azure\Config
    - **ログ ファイル**: %ProgramData%\Microsoft Azure\Logs

次のようにスクリプトを実行します。

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。
2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。
4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
スクリプトが正常に終了すると、アプライアンス Web アプリケーションが起動します。

問題が発生した場合は、トラブルシューティングのために、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log のスクリプト ログにアクセスできます。

> [!NOTE]
> 既存の Azure Migrate アプライアンスで Azure Migrate インストーラー スクリプトを実行しないでください。

### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

アプライアンスが [Azure URL](migrate-appliance.md#url-access) に接続できることを確認します。


### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

1. アプライアンスに接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリのショートカットをクリックして、デスクトップからアプリを開くこともできます。
2. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **ライセンス**:ライセンス条項に同意し、サード パーティの情報を確認します。
    - **接続**:サーバーがインターネットにアクセスできることが、アプリによって確認されます。 サーバーでプロキシを使用する場合は、次の操作を行います。
        - **[プロキシの設定]** をクリックし、 http://ProxyIPAddress または http://ProxyFQDN の形式で、プロキシ アドレスとリスニング ポートを指定します。
        - プロキシで認証が必要な場合は、資格情報を指定します。
        - サポートされるのは HTTP プロキシのみです。
    - **時刻同期**:時刻が確認されます。 サーバーの検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**:Azure Migrate Server Assessment によって、アプライアンスに最新の更新プログラムがインストールされていることが確認されます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. **[ログイン]** をクリックします。 表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
2. 新しいタブで、自分の Azure 資格情報を使用してサインインします。
    - 自分のユーザー名とパスワードを使用してサインインします。
    - PIN を使用したサインインはサポートされていません。
3. 正常にサインインした後、Web アプリに戻ります。
4. Azure Migrate プロジェクトが作成されたサブスクリプションを選択します。 その後、プロジェクトを選択します。
5. アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
6. **[登録]** をクリックします。


## <a name="start-continuous-discovery"></a>継続的な検出を開始する

次に、アプライアンスから検出対象の物理サーバーに接続し、検出を開始します。

1. **[資格情報の追加]** をクリックして、アプライアンスがサーバーの検出に使用するアカウント資格情報を指定します。  
2. **オペレーティング システム**、資格情報のフレンドリ名、**ユーザー名**、**パスワード**を指定し、 **[追加]** をクリックします。
Windows および Linux サーバーごとに 1 セットの資格情報を追加できます。
4. **[サーバーの追加]** をクリックし、サーバーの詳細 (FQDN/IP アドレスと資格情報のフレンドリ名 (行ごとに 1 つのエントリ)) を指定してサーバーに接続します。
3. **[検証]** をクリックします。 検証後、検出可能なサーバーの一覧が表示されます。
    - サーバーの検証が失敗した場合は、 **[状態]** 列のアイコンをポイントしてエラーを確認します。 問題を修正し、もう一度検証します。
    - サーバーを削除するには、 **[削除]** を選択します。
4. 検証後、 **[保存して検出を開始]** をクリックして、検出プロセスを開始します。

これで検出が開始されます。 検出されたサーバーのメタデータが Azure portal に表示されるまでにサーバーあたり約 1.5 分かかります。

### <a name="verify-servers-in-the-portal"></a>ポータルでサーバーを確認する

検出後、サーバーが Azure portal に表示されていることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。

## <a name="set-up-an-assessment"></a>評価を設定する

Azure Migrate:Server Assessment を使用して作成する方法について説明します。

**評価** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集されたパフォーマンス データに基づく評価 | **推奨される VM サイズ**: CPU とメモリの使用率データに基づきます。<br/><br/> **推奨されるディスクの種類 (Standard または Premium マネージド ディスク)** : オンプレミス ディスクの IOPS とスループットに基づきます。
**オンプレミス** | オンプレミスのサイズ設定に基づく評価。 | **推奨される VM サイズ**: オンプレミスのサーバーのサイズに基づきます。<br/><br> **推奨されるディスクの種類**: 評価用に選択するストレージの種類の設定に基づきます。


### <a name="run-an-assessment"></a>評価を実行する

評価を実行するには次のようにします。

1. 評価を作成するための[ベスト プラクティス](best-practices-assessment.md)を確認します。
2. **[サーバー]** タブの **[Azure Migrate:Server Assessment]** タイルで、 **[評価]** をクリックします。

    ![アクセス](./media/tutorial-assess-physical/assess.png)

2. **[サーバーの評価]** で、評価の名前を指定します。
3. **[すべて表示]** をクリックして、評価のプロパティを確認します。

    ![評価のプロパティ](./media/tutorial-assess-physical/view-all.png)

3. **[グループを選択または作成します]** で **[新規作成]** を選択し、グループ名を指定します。 評価のために 1 つ以上のサーバーをグループにまとめます。
4. **[グループにマシンを追加します]** で、グループに追加するサーバーを選択します。
5. **[評価を作成します]** をクリックして、グループを作成し、評価を実行します。

    ![評価を作成する](./media/tutorial-assess-physical/assessment-create.png)

6. 評価が作成されたら、それを表示します ( **[サーバー]**  >  **[Azure Migrate: Server Assessment]**  >  **[評価]** )。
7. **[評価のエクスポート]** をクリックし、Excel ファイルとしてダウンロードします。



## <a name="review-an-assessment"></a>評価を確認する

評価には以下が記述されています。

- **Azure 対応性**: サーバーが Azure への移行に適しているかどうか。
- **月間コスト見積もり**: Azure でサーバーを実行するためのコンピューティングとストレージの月間推定コスト。
- **月間ストレージ コスト見積もり**: 移行後のディスク ストレージの推定コスト。

### <a name="view-an-assessment"></a>評価を表示する

1. **[移行の目標]**  >   **[サーバー]** で、 **[評価]** を **[Azure Migrate: Server Assessment]** でクリックします。
2. **[評価]** で、評価をクリックして開きます。

    ![評価の概要](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 対応性の確認

1. **[Azure 対応性]** で、VM が Azure への移行の準備ができているかどうかを確認します。
2. 状態を確認します。
    - **[Azure に対応]** : Azure Migrate によって、評価内の VM の VM サイズとコスト見積もりが推奨されます。
    - **[条件付きで対応]** : 問題と推奨される修復方法が示されます。
    - **[Azure に未対応]** : 問題と推奨される修復方法が示されます。
    - **[Readiness 不明]** : データの可用性の問題により、Azure Migrate が対応性を評価できない場合に使用されます。

2. **[Azure 対応性]** の状態をクリックします。 サーバー対応性の詳細を表示し、ドリルダウンしてサーバーの詳細 (コンピューティング、ストレージ、ネットワークの設定など) を表示できます。



### <a name="review-cost-details"></a>コスト詳細の確認

このビューには、Azure で VM を実行する際のコンピューティングおよびストレージの推定コストが表示されます。

1. 月間のコンピューティングおよびストレージのコストを確認します。 評価対象のグループ内のすべてのサーバーのコストが集計されます。

    - コストの見積もりは、マシンの推奨サイズ、およびそのディスクとプロパティに基づいています。
    - コンピューティングとストレージの月間推定コストが表示されます。
    - コストの見積もりは、IaaS VM としてのオンプレミス サーバーの実行に対するものです。 Azure Migrate Server Assessment では、PaaS または SaaS のコストは考慮されません。

2. 月間ストレージ コストの見積もりを確認できます。 このビューには、評価されたグループの集計されたストレージ コストが、ストレージ ディスクの種類ごとに分けて表示されます。
3. ドリルダウンして、特定のサーバーの詳細を確認できます。


### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

パフォーマンスベースの評価を実行すると、評価に信頼度レーティングが割り当てられます。

![信頼度レーティング](./media/tutorial-assess-physical/confidence-rating.png)

- 1 つ星 (最低) から 5 つ星 (最高) までの評価が与えられます。
- 信頼度レーティングは、評価による推奨サイズの信頼性を判断する目安となります。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいています。

評価の信頼度レーティングは、次のとおりです。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星

評価レーティングのベスト プラクティスについて、[さらに学習](best-practices-assessment.md#best-practices-for-confidence-ratings)してください。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure Migrate アプライアンスを設定する
> * 評価を作成して確認する

このシリーズの 3 番目のチュートリアルに進み、Azure Migrate: Server Migration を使用して物理サーバーを Azure に移行する方法について学習します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。

> [!div class="nextstepaction"]
> [物理サーバーを移行する](./tutorial-migrate-physical-virtual-machines.md)
