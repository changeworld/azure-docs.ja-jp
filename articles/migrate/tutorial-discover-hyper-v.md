---
title: Azure Migrate Server Assessment を使用して Hyper-V VM を検出する
description: Azure Migrate Server Assessment ツールを使用して、オンプレミスの Hyper-V VM を検出する方法について説明します。
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 0643a13a07572dc24ef895062593e00188a0752f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317166"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>チュートリアル:Server Assessment を使用して Hyper-V VM を検出する

Azure への移行の一環として、オンプレミスのインベントリとワークロードを検出します。 

このチュートリアルでは、オンプレミスの Hyper-V 仮想マシン (VM) を軽量の Azure Migrate アプライアンスを使用して、Azure Migrate: Server Assessment ツールで検出する方法について説明します。 アプライアンスを Hyper-V VM としてデプロイし、マシンとパフォーマンスのメタデータを継続的に検出します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure アカウントを設定する
> * 検出のために Hyper-V 環境を準備します。
> * Azure Migrate プロジェクトを作成します。
> * Azure Migrate アプライアンスを設定します。
> * 継続的な検出を開始します。

> [!NOTE]
> 各チュートリアルでは、シナリオを試すための最も簡単な方法を紹介し、既定のオプションを使用します。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の前提条件を満たしていることを確認してください。


**要件** | **詳細**
--- | ---
**Hyper-V ホスト** | VM が配置されている Hyper-V ホストは、スタンドアロンでも、クラスターに含まれていてもかまいません。<br/><br/> ホストでは、Windows Server 2019、Windows Server 2016、または Windows Server 2012 R2 が実行されている必要があります。<br/><br/> Common Information Model (CIM) セッションを使用して、アプライアンスが VM メタデータとパフォーマンス データをプルするために接続できるように、WinRM ポート 5985 (HTTP) で受信接続が許可されていることを確認します。
**アプライアンスのデプロイ** | Hyper-v には、アプライアンスに VM を割り当てるためのリソースが必要です。<br/><br/> - Windows Server 2016<br/><br/> \- 16 GB の RAM<br/><br/> - 8 つの vCPU<br/><br/> - 約 80 GB のディスク記憶域<br/><br/> - 外部仮想スイッチ<br/><br/> - VM のインターネット アクセス (直接またはプロキシ経由)
**VM** | VM では、任意の Windows または Linux オペレーティング システムを実行できます。 

開始する前に、検出中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---hyper-v)できます。

## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

Azure Migrate プロジェクトを作成し、Azure Migrate アプライアンスを登録するには、以下を備えたアカウントが必要です。
- Azure サブスクリプションに対する共同作成者または所有者のアクセス許可。
- Azure Active Directory アプリを登録するためのアクセス許可。

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。 サブスクリプションの所有者でない場合は、所有者と協力して、次のようにアクセス許可を割り当てます。


1. Azure portal で "サブスクリプション" を検索し、 **[サービス]** で **[サブスクリプション]** を選択します。

    ![Azure サブスクリプションを検索するための検索ボックス](./media/tutorial-discover-hyper-v/search-subscription.png)

2. **[サブスクリプション]** ページで、Azure Migrate プロジェクトを作成するサブスクリプションを選択します。 
3. サブスクリプションで、 **[アクセス制御 (IAM)]**  >  **[アクセスの確認]** の順に選択します。
4. **[アクセスの確認]** で、適切なユーザー アカウントを検索します。
5. **[ロールの割り当てを追加する]** で、 **[追加]** をクリックします。

    ![ユーザー アカウントを検索してアクセスを確認し、ロールを割り当てる](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. **[ロールの割り当ての追加]** で、共同作成者または所有者ロールを選択し、アカウント (この例では azmigrateuser) を選択します。 **[保存]** をクリックします。

    ![[ロールの割り当ての追加] ページを開いて、アカウントにロールを割り当てる](./media/tutorial-discover-hyper-v/assign-role.png)

7. ポータルでユーザーを検索し、 **[サービス]** で **[ユーザー]** を選択します。
8. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で **[はい]** に設定されています)。

    ![[ユーザー設定] で、ユーザーが Active Directory アプリを登録できることを確認する](./media/tutorial-discover-hyper-v/register-apps.png)

9. テナントおよびグローバル管理者は、AAD アプリの登録を許可する目的で**アプリケーション開発者**ロールをアカウントに割り当てることもできます。 [詳細については、こちらを参照してください](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-hyper-v-hosts"></a>Hyper-V ホストを準備する

Hyper-V ホストに対する管理者アクセス権があるアカウントを設定します。 アプライアンスでは、このアカウントを検出に使用します。

- オプション 1: Hyper-V ホスト マシンに対する管理者アクセス権があるアカウントを準備します。
- オプション 2:ローカル管理者アカウントまたはドメイン管理者アカウントを準備し、アカウントを次のグループに追加します: Remote Management Users、Hyper-V Administrators、Performance Monitor Users。


## <a name="set-up-a-project"></a>プロジェクトの設定

新しい Azure Migrate プロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** で、 **[プロジェクトの作成]** を選択します。
5. **[プロジェクトの作成]** で、Azure サブスクリプションとリソース グループを選択します。 リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

   ![プロジェクト名とリージョンのボックス](./media/tutorial-discover-hyper-v/new-project.png)

7. **［作成］** を選択します
8. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。

**Azure Migrate: Server Assessment** ツールは、新しいプロジェクトに既定で追加されます。

![既定で追加された Server Assessment ツールを示すページ](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>アプライアンスを設定する

このチュートリアルでは、次のように、Hyper-V VM でアプライアンスを設定します。

- アプライアンス名を指定し、ポータルで Azure Migrate プロジェクト キーを生成します。
- Azure portal から圧縮された Hyper-V VHD をダウンロードします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクト キーを使用して Azure Migrate プロジェクトに登録します。
> [!NOTE]
> なんらかの理由で、テンプレートを使用してアプライアンスを設定できない場合は、PowerShell スクリプトを使用して設定できます。 [詳細については、こちらを参照してください](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)。


### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate プロジェクト キーを生成する

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
3. **[1:Azure Migrate プロジェクト キーを生成します]** で、Hyper-V VM の検出用に設定する Azure Migrate アプライアンスの名前を指定します。名前は 14 文字以内の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [マシンの検出] ページを閉じないでください。
1. Azure リソースが正常に作成されると、**Azure Migrate プロジェクト キー**が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

### <a name="download-the-vhd"></a>VHD をダウンロードする

**[2:Azure Migrate アプライアンスをダウンロードする]** で、.VHD ファイルを選択し、 **[ダウンロード]** をクリックします。 


### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。

2. 次の PowerShell コマンドを実行して、ZIP ファイルのハッシュを生成します
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - 使用例: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    - Azure パブリック クラウドの場合:

        **シナリオ** | **ダウンロード** | **SHA256**
        --- | --- | ---
        Hyper-V (8.91 GB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Azure Government の場合:

        **シナリオ*** | **ダウンロード** | **SHA256**
        --- | --- | ---
        Hyper-V (85.8 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. アプライアンス VM をホストする Hyper-V ホスト上のフォルダーに、圧縮された VHD ファイルを抽出します。 3 つのフォルダーが抽出されます。
2. Hyper-V マネージャーを開きます。 **[アクション]** で **[仮想マシンのインポート]** をクリックします。
2. 仮想マシンのインポート ウィザードの **[開始する前に]** で、 **[次へ]** をクリックします。
3. **[フォルダーの検索]** で、抽出された VHD が含まれているフォルダーを指定します。 続けて、 **[次へ]** をクリックします。
1. **[仮想マシンを選択します]** で、 **[次へ]** をクリックします。
2. **[インポートの種類の選択]** で、 **[仮想マシンをコピーする (新しい一意な ID を作成する)]** をクリックします。 続けて、 **[次へ]** をクリックします。
3. **[移動先の選択]** は、既定の設定のままにします。 **[次へ]** をクリックします。
4. **[保存フォルダー]** は、既定の設定のままにします。 **[次へ]** をクリックします。
5. **[ネットワークの選択]** で、VM によって使用される仮想スイッチを指定します。 このスイッチには、Azure にデータを送信するためのインターネット接続が必要です。
6. **[概要]** で、設定を確認します。 **[完了]** をクリックします。
7. Hyper-V マネージャーの **[仮想マシン]** で、VM を起動します。


### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス VM から接続できることを確認します。

### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

> [!NOTE]
> ダウンロードした VHD でなく、[PowerShell スクリプト](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、この手順の最初の 2 つの操作は必要ありません。

1. Hyper-V マネージャーの **[仮想マシン]** で、VM を右クリックして **[接続]** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリ ショートカットをクリックして、アプライアンス デスクトップからアプリを開くこともできます。
1. **ライセンス条項**に同意し、サード パーティの情報を確認します。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
      - **[プロキシの設定]** をクリックし、プロキシ アドレス (http://ProxyIPAddress または http://ProxyFQDN) の形式) とリッスン ポートを指定します。
      - プロキシで認証が必要な場合は、資格情報を指定します。
      - サポートされるのは HTTP プロキシのみです。
      - プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
    - **時刻同期**:時刻が確認されます。 VM の検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**:Azure Migrate Server Assessment では、アプライアンスに最新の更新プログラムがインストールされているかどうかの確認が行われます。チェックが完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンスで実行されているコンポーネントの状態とバージョンを確認できます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **Azure Migrate プロジェクト キー**を貼り付けます。 このキーがない場合は、 **[Server Assessment] > [検出] > [Manage existing appliances]\(既存のアプライアンスの管理\)** に移動して、キーの生成時に指定したアプライアンス名を選択して、対応するキーをコピーします。
1. **[ログイン]** をクリックします。 新しいブラウザー タブで Azure ログイン プロンプトが開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. 正常にログインしたら、Web アプリに戻ります。 
4. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しいアクセス許可が付与されている場合、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** をクリックすることで登録の詳細を確認できるようになります。



### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD の資格情報を委任する

SMB 上で VHD を実行している場合は、アプライアンスから Hyper-V ホストへの資格情報の委任を有効にする必要があります。 アプライアンスからこれを行うには、次のようにします。

1. アプライアンス VM 上で、このコマンドを実行します。 HyperVHost1/HyperVHost2 は、ホスト名の例です。

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. または、アプライアンス上のローカル グループ ポリシー エディターでこれを行います。
    - **[ローカル コンピューター ポリシー]**  >  **[コンピューターの構成]** で、 **[管理用テンプレート]**  >  **[システム]**  >  **[資格情報の委任]** の順にクリックします。
    - **[新しい資格情報の委任を許可する]** をダブルクリックし、 **[有効]** を選択します。
    - **[オプション]** で **[表示]** をクリックし、検出したい各 Hyper-V ホストを、プレフィックスとして **wsman/** を付けて、一覧に追加します。
    - **[資格情報の委任]** で **[NTLM のみのサーバー認証で新しい資格情報の委任を許可する]** をダブルクリックします。 もう一度、検出したい各 Hyper-V ホストを、プレフィックスとして **wsman/** を付けて、一覧に追加します。

## <a name="start-continuous-discovery"></a>継続的な検出を開始する

アプライアンスから Hyper-V ホストまたはクラスターに接続し、VM の検出を開始します。

1. **[ステップ 1:Provide Hyper-V host credentials]\(ステップ 1: Hyper-V ホストの資格情報を指定する\)** で、 **[Add credentials]\(資格情報の追加\)** をクリックして資格情報のフレンドリ名を指定し、アプライアンスで VM の検出に使用される Hyper-V ホストまたはクラスターの **[ユーザー名]** と **[パスワード]** を追加します。 **[Save]** をクリックします。
1. 複数の資格情報を一度に追加するには、 **[Add more]\(さらに追加\)** をクリックして資格情報を保存して追加します。 Hyper-V VM の検出では、複数の資格情報がサポートされています。
1. **[ステップ 2:Provide Hyper-V host/cluster details]\(ステップ 2: Hyper-V ホストまたはクラスターの詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、Hyper-V ホストまたはクラスターの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** と、ホストまたはクラスターに接続するための資格情報のフレンドリ名を指定します。
1. 一度に **1 つの項目を追加**するか、一括で**複数の項目を追加**することができます。 また、 **[Import CSV]\(CSV のインポート\)** を使用して、Hyper-V ホストまたはクラスターの詳細を指定することもできます。


    - **[Add single item]\(1 つの項目を追加\)** を選択した場合は、資格情報のフレンドリ名と Hyper-V ホストまたはクラスターの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** を指定して **[保存]** をクリックします。
    - **[Add multiple items]\(複数の項目を追加\)** _(既定で選択)_ を選択した場合は、テキスト ボックスで Hyper-V ホストまたはクラスターの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** を資格情報のフレンドリ名と共に指定することで、一度に複数のレコードを追加できます。追加したレコードを **[Verify]\(検証\)** し、 **[保存]** をクリックします。
    - **[Import CSV]\(CSV のインポート\)** を選択した場合は、CSV テンプレート ファイルをダウンロードし、そのファイルに Hyper-V ホストまたはクラスターの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** および資格情報のフレンドリ名を入力できます。 その後、ファイルをアプライアンスにインポートし、ファイル内のレコードを **[Verify]\(検証\)** して、 **[保存]** をクリックします。

1. [保存] をクリックすると、アプライアンスによって追加された Hyper-V ホストまたはクラスターへの接続が検証され、各ホストまたはクラスターの **[Validation status]\(検証状態\)** が表に表示されます。
    - 正常に検証されたホストまたはクラスターについては、その IP アドレスまたは FQDN をクリックすることで詳細を表示できます。
    - ホストの検証に失敗した場合は、表の状態列にある **[Validation failed]\(検証に失敗しました\)** をクリックしてエラーを確認します。 問題を修正し、もう一度検証してください。
    - ホストまたはクラスターを削除するには、 **[Delete]\(削除\)** をクリックします。
    - クラスターから特定のホストを削除することはできません。 削除できるのは、クラスター全体のみです。
    - クラスター内の特定のホストに問題がある場合でも、クラスターを追加できます。
1. 検出を開始する前に、ホストまたはクラスターへの接続はいつでも**再検証**できます。
1. 正常に検証されたホストまたはクラスターから VM の検出を開始するには **[Start discovery]\(検出の開始\)** をクリックします。 検出が正常に開始されたら、各ホストまたはクラスターに対する検出の状態を表で確認できます。

これで検出が開始されます。 検出されたサーバーのメタデータが Azure portal に表示されるまでにホストあたり約 2 分かかります。

## <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

検出の完了後、VM がポータルに表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。

## <a name="next-steps"></a>次のステップ

- Azure VM への移行のために [Hyper-V VM を評価](tutorial-assess-hyper-v.md)します。
- 検出中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---hyper-v)します。