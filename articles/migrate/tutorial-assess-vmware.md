---
title: Azure への移行のために VMware VM を評価する
description: Azure Migrate Server Assessment を使用して Azure に移行するためにオンプレミスの VMware VM を評価する方法について説明します。
ms.topic: tutorial
ms.date: 03/23/2019
ms.openlocfilehash: 944b7c12a353a29a172576974261eece63ebf668
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548740"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Azure Migrate Server Assessment を使用して VMware VM を評価する

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用して、オンプレミスの VMware 仮想マシン (VM) を評価する方法について説明します。


これはシリーズの 2 番目のチュートリアルであり、VMware VM を評価して Azure に移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Migrate プロジェクトを設定する。
> * VM を評価するためにオンプレミスで実行される Azure Migrate アプライアンスを設定する。
> * オンプレミス VM の継続的な検出を開始する。 アプライアンスでは、検出された VM の構成データとパフォーマンス データが Azure に送信されます。
> * 検出された VM をグループ化し、VM グループを評価する。
> * 評価を確認する。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、ハウツー記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

- このシリーズの[最初のチュートリアルを完了します](tutorial-prepare-vmware.md)。 そうしないと、このチュートリアルの手順はうまくいきません。
- 最初のチュートリアルでは、以下のことを行ったはずです。
    - Azure Migrate と連携するように [Azure を準備](tutorial-prepare-vmware.md#prepare-azure)します。
    - [評価のために VMware を準備](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment)します。 これには、VMware の設定の確認、Azure Migrate が vCenter Server にアクセスするために使用できるアカウントの設定などが含まれています。
    - VMware の評価用の Azure Migrate アプライアンスをデプロイするために必要なものを[確認](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment)します。

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate プロジェクトを設定する

次のように、新しい Azure Migrate プロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
1. **[サービス]** で **[Azure Migrate]** を選択します。
1. **[概要]** の **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** を選択します。

   ![サーバーを評価して移行するボタン](./media/tutorial-assess-vmware/assess-migrate.png)

1. **[作業の開始]** で、 **[ツールの追加]** を選択します。
1. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。     
1. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 アジア、ヨーロッパ、英国、および米国がサポートされています。

   プロジェクトの地理的な場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。 移行を実行するときは、任意のターゲット リージョンを選択できます。

   ![プロジェクト名とリージョンのボックス](./media/tutorial-assess-vmware/migrate-project.png)

1. **[次へ]** を選択します。
1. **[評価ツールの選択]** で、次を選択します: **[Azure Migrate: Server Assessment]**  >  **[次へ]** 。

   ![Server Assessment ツールの選択](./media/tutorial-assess-vmware/assessment-tool.png)

1. **[移行ツールの選択]** で、 **[今は移行ツールの追加をスキップします]**  >  **[次へ]** の順に選択します。
1. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** を選択します。
1. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトのページが表示されます。 プロジェクトが表示されない場合は、Azure Migrate ダッシュボードの **[サーバー]** からアクセスできます。

## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する

Azure Migrate:Server Assessment では、軽量の Azure Migrate アプライアンスが使用されます。 このアプライアンスは VM の検出を実行し、VM のメタデータとパフォーマンス データを Azure Migrate に送信します。
- アプライアンスは、ダウンロードした OVA テンプレートを使用して VMware VM 上に設定できます。 または、PowerShell インストーラー スクリプトを使用して、VM 上または物理マシン上にアプライアンスを設定することもできます。
- このチュートリアルでは、OVA テンプレートを使用します。 スクリプトを使用してアプライアンスを設定する場合は、[この記事](deploy-appliance-script.md)を参照してください。

アプライアンスの作成後、Azure Migrate:Server Assessment に接続できることを確認し、最初の構成を行い、Azure Migrate プロジェクトに登録します。



### <a name="download-the-ova-template"></a>OVA テンプレートをダウンロードする

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
1. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
1. **[ダウンロード]** を選択して、OVA テンプレート ファイルをダウンロードします。

   ![OVA ファイルをダウンロードするための選択](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
1. OVA ファイルのハッシュを生成するには、次のコマンドを実行します。
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

バージョン 2.19.07.30 の場合、生成されたハッシュは次の値と一致する必要があります。

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順に選択します。

   ![OVF テンプレートをデプロイするためのメニュー コマンド](./media/tutorial-assess-vmware/deploy-ovf.png)

1. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、OVA ファイルの場所を指定します。
1. **[名前]** と **[場所]** で、VM のフレンドリ名を指定します。 VM がホストされるインベントリ オブジェクトを選択します。
1. **[Host/Cluster]\(ホスト/クラスター\)** で、VM が実行されるホストまたはクラスターを指定します。
1. **[ストレージ]** で、VM の保存先を指定します。
1. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
1. **[ネットワーク マッピング]** で、VM の接続先となるネットワークを指定します。 そのネットワークには、Azure Migrate Server Assessment にメタデータを送信するためのインターネット接続が必要です。
1. 設定を確認し、 **[Finish]\(完了\)** を選択します。

### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

アプライアンス VM が [Azure URL](migrate-appliance.md#url-access) に接続できることを確認します。

### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

> [!NOTE]
> ダウンロードした OVA でなく、[PowerShell スクリプト](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、この手順の最初の 2 つの操作は必要ありません。

1. vSphere クライアント コンソールで、VM を右クリックし、 **[コンソールを開く]** を選択します。
1. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
1. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリ ショートカットを選択して、アプライアンス デスクトップからアプリを開くこともできます。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
   - **ライセンス**:ライセンス条項に同意し、サード パーティの情報を確認します。
   - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
     - **[プロキシの設定]** を選択し、 http://ProxyIPAddress または http://ProxyFQDN の形式で、プロキシ アドレスとリスニング ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。
     - HTTP プロキシのみがサポートされていることに注意してください。
   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。
   - **Install VDDK\(VDDK のインストール\)** :アプライアンスでは、VMWare vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが確認されます。 インストールされていない場合は、VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip コンテンツをアプライアンス上の指定された場所に抽出します。

     Azure Migrate Server Migration では、Azure への移行の間に VDDK を使用してマシンがレプリケートされます。       

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. **[ログイン]** を選択します。 表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
1. 正常にサインインしたら、Web アプリに戻ります。
1. Azure Migrate プロジェクトが作成されたサブスクリプションを選択した後、プロジェクトを選択します。
1. アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
1. **[登録]** を選択します。


## <a name="start-continuous-discovery"></a>継続的な検出を開始する

VM の構成データとパフォーマンス データを検出するには、アプライアンスを vCenter Server に接続する必要があります。

### <a name="specify-vcenter-server-details"></a>vCenter Server の詳細を指定する
1. **[vCenter Server の詳細を指定する]** で、vCenter Server インスタンスの名前 (FQDN) または IP アドレスを指定します。 既定のポートをそのまま使用することも、vCenter Server でリッスンするカスタム ポートを指定することもできます。
2. vCenter Server インスタンス上の VM を検出するためにアプライアンスが使用する vCenter Server アカウントの資格情報を **[ユーザー名]** と **[パスワード]** に指定します。 

    - [前のチュートリアル](tutorial-prepare-vmware.md#set-up-an-account-for-assessment)で、必要なアクセス許可を持つアカウントを設定してある必要があります。
    - 検出を特定の VMware オブジェクト (vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々の VM) にスコーピングする場合、Azure Migrate によって使用されるアカウントを制限するには、[この記事](set-discovery-scope.md)の手順を参照してください。

3. **[接続の検証]** を選択し、アプライアンスが vCenter Server に接続できることを確認します。
4. **[VM でのアプリケーションと依存関係の検出]** で、必要に応じて **[資格情報の追加]** をクリックし、資格情報が関連するオペレーティング システムと、資格情報のユーザー名およびパスワードを指定します。 **[追加]** をクリックします。

    - [アプリケーション検出機能](how-to-discover-applications.md)または[エージェントレスの依存関係の分析機能](how-to-create-group-machine-dependencies-agentless.md)のために使用するアカウントを作成した場合は、必要に応じて、ここで資格情報を追加します。
    - これらの機能を使用していない場合は、この設定を省略できます。
    - [アプリ検出](migrate-support-matrix-vmware.md#application-discovery)または[エージェントレス分析](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)に必要な資格情報を確認してください。

5. VM の検出を開始するには、 **[保存して検出を開始]** を選択します。

検出は次のように行われます。
- 検出された VM のメタデータがポータルに表示されるまでに、約 15 分かかります。
- インストールされているアプリケーション、ロール、および機能の検出には時間がかかります。 検出対象の VM 数によって期間は変わります。 500 台の VM がある場合、アプリケーション インベントリが Azure Migrate ポータルに表示されるまでに約 1 時間かかります。

### <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

検出の後で、VM が Azure portal に表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
1. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出済みサーバー]** の数を表示するアイコンを選択します。

## <a name="set-up-an-assessment"></a>評価を設定する

Azure Migrate Server Assessment を使用して、次の 2 種類の評価を作成できます。

**評価** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集されたパフォーマンス データに基づく評価 | **推奨される VM サイズ**: CPU とメモリの使用率データに基づきます。<br/><br/> **推奨されるディスクの種類 (Standard または Premium マネージド ディスク)** : オンプレミス ディスクの IOPS とスループットに基づきます。
**オンプレミス** | オンプレミスのサイズ設定に基づく評価 | **推奨される VM サイズ**: オンプレミスの VM サイズに基づきます。<br/><br> **推奨されるディスクの種類**: 評価用に選択するストレージの種類の設定に基づきます。

## <a name="run-an-assessment"></a>評価を実行する

評価を実行するには次のようにします。

1. 評価を作成するための[ベスト プラクティス](best-practices-assessment.md)を確認します。
1. **[サーバー]** タブの **[Azure Migrate: Server Assessment]** タイルで、 **[評価]** を選択します。

   ![[評価] ボタンの場所](./media/tutorial-assess-vmware/assess.png)

1. **[サーバーの評価]** で、評価の名前を指定します。
1. **[すべて表示]** を選択し、評価のプロパティを確認します。

   ![評価のプロパティ](./media/tutorial-assess-vmware/view-all.png)

1. **[グループを選択または作成します]** で **[新規作成]** を選択し、グループ名を指定します。 グループで、評価のために 1 つ以上の VM をまとめます。
1. **[グループにマシンを追加します]** で、グループに追加する VM を選択します。
1. **[評価を作成します]** を選択して、グループを作成し、評価を実行します。

   ![サーバーの評価](./media/tutorial-assess-vmware/assessment-create.png)

1. 評価が作成されたら、それを表示します ( **[サーバー]**  >  **[Azure Migrate: Server Assessment]**  >  **[評価]** )。
1. **[評価のエクスポート]** を選択し、Excel ファイルとしてダウンロードします。

## <a name="review-an-assessment"></a>評価を確認する

評価には以下が記述されています。

- **Azure 対応性**: VM が Azure への移行に適しているかどうか。
- **月間コスト見積もり**: Azure で VM を実行するためのコンピューティングとストレージの月間推定コスト。
- **月間ストレージ コスト見積もり**: 移行後のディスク ストレージの推定コスト。

評価を表示するには:

1. **[移行の目標]**  >  **[サーバー]** で、 **[評価]** を **[Azure Migrate: Server Assessment]** でクリックします。
1. **[評価]** で、評価を選択して開きます。

   ![評価の概要](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 対応性の確認

1. **[Azure 対応性]** で、VM が Azure への移行の準備ができているかどうかを確認します。
1. VM の状態を確認します。
    - **[Azure に対応]** : 評価内の VM の VM サイズとコスト見積もりが Azure Migrate から推奨される場合に使用します。
    - **[条件付きで対応]** : 問題と推奨される修復方法が示されます。
    - **[Azure に未対応]** : 問題と推奨される修復方法が示されます。
    - **[Readiness 不明]** : データの可用性の問題により、Azure Migrate が対応性を評価できない場合に使用されます。

1. **[Azure 対応性]** の状態を選択します。 VM の対応性の詳細を確認できます。 また、ドリルダウンして、コンピューティング、ストレージ、ネットワークの設定など、VM の詳細を確認することもできます。

### <a name="review-cost-details"></a>コスト詳細の確認

評価の概要には、Azure で実行されている VM のコンピューティングとストレージのコストの見積りが表示されます。 コストは、評価されるグループ内のすべての VM について集計されます。 ドリルダウンして、特定の VM のコストの詳細を見ることができます。

> [!NOTE]
> コストの見積もりは、マシンの推奨サイズ、およびそのディスクとプロパティに基づいています。 見積りは、オンプレミスの VM を IaaS VM として実行するためのものです。 Azure Migrate Server Assessment では、PaaS または SaaS のコストは考慮されません。

評価されたグループの集計されたストレージ コストは、ストレージ ディスクの種類ごとに分けて表示されます。 

### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

Azure Migrate Server Assessment によって、1 つ星 (最低) から 5 つ星 (最高) までの信頼度レーティングがパフォーマンスベースの評価に割り当てられます。

![信頼度レーティング](./media/tutorial-assess-vmware/confidence-rating.png)

信頼度レーティングを使うと、評価の推奨サイズの信頼性を見積もることができます。 このレーティングは、評価の計算に必要なデータ ポイントの可用性に基づいています。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星

信頼度レーティングの[ベスト プラクティスの詳細については、こちら](best-practices-assessment.md#best-practices-for-confidence-ratings)を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Migrate アプライアンスを設定しました。 また、評価を作成して確認しました。

Azure Migrate Server Migration を使用して VMware VM を Azure に移行する方法について学習するには、このシリーズの 3 番目のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [VMware VM を移行する](./tutorial-migrate-vmware.md)
