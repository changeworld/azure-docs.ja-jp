---
title: Azure Migrate Server Assessment を使用して VMware VM を検出する
description: Azure Migrate Server Assessment ツールを使用して、オンプレミスの VMware VM を検出する方法について説明します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: e11c3277ffa07fe0a8d5fc7495e2c09152ce585f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704293"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>チュートリアル:Server Assessment を使用して VMware VM を検出する

Azure への移行の一環として、オンプレミスのインベントリとワークロードを検出します。 

このチュートリアルでは、軽量の Azure Migrate アプライアンスを使用して、Azure Migrate: Server Assessment ツールでオンプレミスの VMware 仮想マシン (VM) を検出する方法について説明します。 アプライアンスを VMware VM としてデプロイして、VM とパフォーマンスのメタデータ、VM で実行されているアプリ、VM の依存関係を継続的に検出します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure アカウントを設定します。
> * 検出のために VMware 環境を準備します。
> * Azure Migrate プロジェクトを作成します。
> * Azure Migrate アプライアンスを設定します。
> * 継続的な検出を開始します。

> [!NOTE]
> 各チュートリアルでは、シナリオを試すための最も簡単な方法を紹介し、可能であれば既定のオプションを使用します。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の前提条件を満たしていることを確認してください。


**要件** | **詳細**
--- | ---
**vCenter Server/ESXi ホスト** | バージョン 5.5、6.0、6.5、または 6.7 を実行している vCenter Server が必要です。<br/><br/> VM は、バージョン 5.5 以降が実行されている ESXi ホストでホストされている必要があります。<br/><br/> vCenter Server で、TCP ポート 443 での受信接続を許可して、アプライアンスが評価データを収集できるようにします。<br/><br/> 既定では、アプライアンスはポート 443 で vCenter に接続します。 vCenter Server が別のポートでリッスンする場合は、アプライアンスからサーバーに接続して検出を開始するときのポートを変更できます。<br/><br/> VM をホストする EXSi サーバーで、アプリ検出用に、TCP ポート 443 で受信アクセスが許可されていることを確認します。
**アプライアンス** | vCenter Server には、Azure Migrate アプライアンスに VM を割り当てるためのリソースが必要です。<br/><br/> - Windows Server 2016<br/><br/> - 32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域。<br/><br/> - 外部仮想スイッチと、VM のインターネット アクセス (直接またはプロキシ経由)。
**VM** | このチュートリアルを使用するには、Windows VM で、Windows Server 2016、2012 R2、2012、または 2008 R2 が実行されている必要があります。<br/><br/> Linux VM では、Red Hat Enterprise Linux 7/6/5、Ubuntu Linux 14.04/16.04、Debian 7/8、Oracle Linux 6/7、または CentOS 5/6/7 が実行されている必要があります。<br/><br/> VM には、VMware ツール (10.2.0 以降のバージョン) がインストールされ、実行されている必要があります。<br/><br/> Windows VM では、Windows PowerShell 2.0 以降をインストールする必要があります。


## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

Azure Migrate プロジェクトを作成し、Azure Migrate アプライアンスを登録するには、以下を備えたアカウントが必要です。
- Azure サブスクリプションに対する共同作成者または所有者のアクセス許可。
- Azure Active Directory アプリを登録するためのアクセス許可。

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。 サブスクリプションの所有者でない場合は、所有者と協力して、次のようにアクセス許可を割り当てます。

1. Azure portal で "サブスクリプション" を検索し、 **[サービス]** で **[サブスクリプション]** を選択します。

    ![Azure サブスクリプションを検索するための検索ボックス](./media/tutorial-discover-vmware/search-subscription.png)

2. **[サブスクリプション]** ページで、Azure Migrate プロジェクトを作成するサブスクリプションを選択します。 
3. サブスクリプションで、 **[アクセス制御 (IAM)]**  >  **[アクセスの確認]** の順に選択します。
4. **[アクセスの確認]** で、適切なユーザー アカウントを検索します。
5. **[ロールの割り当てを追加する]** で、 **[追加]** をクリックします。

    ![ユーザー アカウントを検索してアクセスを確認し、ロールを割り当てる](./media/tutorial-discover-vmware/azure-account-access.png)

6. **[ロールの割り当ての追加]** で、共同作成者または所有者ロールを選択し、アカウント (この例では azmigrateuser) を選択します。 **[保存]** をクリックします。

    ![[ロールの割り当ての追加] ページを開いて、アカウントにロールを割り当てる](./media/tutorial-discover-vmware/assign-role.png)

7. ポータルでユーザーを検索し、 **[サービス]** で **[ユーザー]** を選択します。
8. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で **[はい]** に設定されています)。

    ![[ユーザー設定] で、ユーザーが Active Directory アプリを登録できることを確認する](./media/tutorial-discover-vmware/register-apps.png)

9. テナントおよびグローバル管理者は、AAD アプリの登録を許可する目的で **アプリケーション開発者** ロールをアカウントに割り当てることもできます。 [詳細については、こちらを参照してください](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-vmware"></a>VMware を準備する

vCenter Server で、アプライアンスが vCenter Server へのアクセスに使用できるアカウントを作成し、必要なポートが開いていることを確認します。 また、アプライアンスが VM へのアクセスに使用できるアカウントも必要です。 

### <a name="create-an-account-to-access-vcenter"></a>vCenter にアクセスするためのアカウントを作成する

vSphere Web Client で、次のようにアカウントを設定します。

1. 管理者特権を備えたアカウントを使用して、vSphere Web Client で **[Administration]\(管理\)** を選択します。
2. **[Access]\(アクセス\)** 、 **[SSO Users and Groups]\(SSO ユーザーとグループ\)** の順に選択します。
3. **[Users]\(ユーザー\)** で、新しいユーザーを追加します。
4. **[New User]\(新しいユーザー\)** で、アカウントの詳細を入力します。 次に、 **[OK]** をクリックします
5. **[Global Permissions]\(グローバル アクセス許可\)** でユーザー アカウントを選択し、アカウントに **[Read-only]\(読み取り専用\)** ロールを割り当てます。 次に、 **[OK]** をクリックします
6. **[Roles]\(ロール\)** で **[Read-only]\(読み取り専用\)** ロールを選択し、 **[Privileges]\(特権\)** で **[Guest Operations]\(ゲスト操作\)** を選択します。 これらの特権は、VM で実行されているアプリを検出し、VM の依存関係を分析するために必要です。
 
    ![読み取り専用ロールでのゲスト操作を許可するチェック ボックス](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>VM にアクセスするためのアカウントを作成する

アプライアンスは、VM にアクセスしてアプリを検出し、VM の依存関係を分析します。 アプライアンスは、VM にエージェントをインストールしません。

1. アプライアンスが Windows VM 上のアプリと依存関係を検出する際に使用できるローカル管理者アカウントを作成します。
2. Linux マシンの場合、root 権限を備えたユーザー アカウントを作成するか、/bin/netstat ファイルと /bin/ls ファイルに対して次のアクセス許可を備えたユーザー アカウントを作成します: CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。

> [!NOTE]
> Azure Migrate では、すべての Windows サーバー上でのアプリ検出用の資格情報を 1 つと、すべての Linux マシン上でのアプリ検出用の資格情報を 1 つサポートします。


## <a name="set-up-a-project"></a>プロジェクトの設定

新しい Azure Migrate プロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** で、 **[プロジェクトの作成]** を選択します。
5. **[プロジェクトの作成]** で、Azure サブスクリプションとリソース グループを選択します。 リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

   ![プロジェクト名とリージョンのボックス](./media/tutorial-discover-vmware/new-project.png)

7. **［作成］** を選択します
8. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。

**Azure Migrate: Server Assessment** ツールは、新しいプロジェクトに既定で追加されます。

![既定で追加された Server Assessment ツールを示すページ](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>アプライアンスを設定する

OVA テンプレートを使用してアプライアンスを設定するには:
- アプライアンス名を指定し、ポータルで Azure Migrate プロジェクト キーを生成します
- OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクト キーを使用して Azure Migrate プロジェクトに登録します。

> [!NOTE]
> なんらかの理由で、テンプレートを使用してアプライアンスを設定できない場合は、PowerShell スクリプトを使用して設定できます。 [詳細については、こちらを参照してください](deploy-appliance-script.md#set-up-the-appliance-for-vmware)。


### <a name="deploy-with-ova"></a>OVA を使用してデプロイする

OVA テンプレートを使用してアプライアンスを設定するには:
- アプライアンス名を指定し、ポータルで Azure Migrate プロジェクト キーを生成します
- OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクト キーを使用して Azure Migrate プロジェクトに登録します。

### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate プロジェクト キーを生成する

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3. **[1:Azure Migrate プロジェクト キーを生成します]** で、VMware VM の検出用に設定する Azure Migrate アプライアンスの名前を指定します。名前は 14 文字以内の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [マシンの検出] ページを閉じないでください。
1. Azure リソースが正常に作成されると、**Azure Migrate プロジェクト キー** が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

### <a name="download-the-ova-template"></a>OVA テンプレートをダウンロードする

**[2:Azure Migrate アプライアンスをダウンロードする]** で、.OVA ファイルを選択し、 **[ダウンロード]** をクリックします。 


### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. OVA ファイルのハッシュを生成するには、次のコマンドを実行します。
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   使用例: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    - Azure パブリック クラウドの場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Azure Government の場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (85.8 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca




### <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。
2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、OVA ファイルの場所を指定します。
3. **[名前]** と **[場所]** で、VM のフレンドリ名を指定します。 VM がホストされるインベントリ オブジェクトを選択します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、VM が実行されるホストまたはクラスターを指定します。
6. **[ストレージ]** で、VM の保存先を指定します。
7. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
8. **[ネットワーク マッピング]** で、VM の接続先となるネットワークを指定します。 そのネットワークには、Azure Migrate Server Assessment にメタデータを送信するためのインターネット接続が必要です。
9. 設定を再確認したら、 **[Finish]\(完了\)** をクリックします。


### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス VM から接続できることを確認します。


### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

> [!NOTE]
> ダウンロードした OVA でなく、[PowerShell スクリプト](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、この手順の最初の 2 つの操作は必要ありません。

1. vSphere クライアント コンソールで、VM を右クリックし、 **[コンソールを開く]** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリ ショートカットを選択して、アプライアンス デスクトップからアプリを開くこともできます。
1. **ライセンス条項** に同意し、サード パーティの情報を確認します。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
   - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
     - **[プロキシの設定]** をクリックし、プロキシ アドレス (http://ProxyIPAddress または http://ProxyFQDN) の形式) とリッスン ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。
     - サポートされるのは HTTP プロキシのみです。
     - プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンスで実行されているコンポーネントの状態とバージョンを確認できます。
   - **Install VDDK\(VDDK のインストール\)** :アプライアンスでは、VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが確認されます。 インストールされていない場合は、**インストール手順** に記載されているとおりに、VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip コンテンツをアプライアンス上の指定された場所に抽出します。

     Azure Migrate Server Migration では、Azure への移行の間に VDDK を使用してマシンがレプリケートされます。 
1. 必要に応じて、アプライアンス構成中ならいつでも、**前提条件の再実行** を行って、アプライアンスがすべての前提条件を満たしているかどうかを確認できます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **Azure Migrate プロジェクト キー** を貼り付けます。 このキーがない場合は、 **[Server Assessment] > [検出] > [既存のアプライアンスの管理]** に移動して、キーの生成時に指定したアプライアンス名を選択して、対応するキーをコピーします。
1. Azure で認証するには、デバイス コードが必要です。 **[ログイン]** をクリックすると、次に示すように、デバイス コードを含むモーダルが開きます。

    ![デバイス コードを示すモーダル](./media/tutorial-discover-vmware/device-code.png)

1. **[Copy code & Login]\(コードのコピーとログイン\)** をクリックしてデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、デバイス コードを貼り付け、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. ログインせずにログイン タブを誤って閉じた場合は、アプライアンス構成マネージャーのブラウザー タブを最新の情報に更新して、[ログイン] ボタンを再度有効にする必要があります。
1. 正常にログインしたら、アプライアンス構成マネージャーで前のタブに戻ります。
1. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しいアクセス許可が付与されている場合、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** をクリックすることで登録の詳細を確認できるようになります。



## <a name="start-continuous-discovery"></a>継続的な検出を開始する

VM の構成データとパフォーマンス データを検出するには、アプライアンスを vCenter Server に接続する必要があります。

1. **[ステップ 1:Provide vCenter Server credentials]\(ステップ 1: vCenter Server の資格情報を指定する\)** で、 **[資格情報の追加]** をクリックして資格情報のフレンドリ名を指定し、vCenter Server インスタンスでの VM の検出にアプライアンスで使用される vCenter Server またはクラスターの **[ユーザー名]** と **[パスワード]** を追加します。
    - 前のチュートリアルで、必要なアクセス許可を持つアカウントを設定してある必要があります。
    - 検出を特定の VMware オブジェクト (vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々の VM) にスコーピングする場合、Azure Migrate によって使用されるアカウントを制限するには、[この記事](set-discovery-scope.md)の手順を参照してください。
1. **[ステップ 2:Provide vCenter Server details]\(ステップ 2: VCenter Server の詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、ドロップダウンから資格情報のフレンドリ名を選択し、vCenter Server インスタンスの **IP アドレスまたは FQDN** を指定します。 **[ポート]** を既定 (443) のまま使用することも、vCenter Server でリッスンするカスタム ポートを指定して **[保存]** をクリックしることもできます。
1. [保存] をクリックすると、アプライアンスは、指定された資格情報を使用して vCenter Server への接続の検証を試み、vCenter Server の IP アドレスまたは FQDN に対して **検証状態** を表に表示します。
1. 検出を開始する前に、vCenter Server への接続はいつでも **再検証** できます。
1. **[ステップ 3:Provide VM credentials to discover installed applications and to perform agentless dependency mapping]\(ステップ 3: インストールされているアプリケーションを検出するための VM 資格情報を指定し、エージェントレスの依存関係マッピングを実行する\)** で、 **[資格情報の追加]** をクリックし、資格情報を提供するオペレーティング システム、資格情報のフレンドリ名、 **[ユーザー名]** と **[パスワード]** を指定します。 その後、 **[保存]** をクリックします。

    - [アプリケーション検出機能](how-to-discover-applications.md)または[エージェントレスの依存関係の分析機能](how-to-create-group-machine-dependencies-agentless.md)のために使用するアカウントを作成した場合は、必要に応じて、ここで資格情報を追加します。
    - これらの機能を使用しない場合は、スライダーをクリックして手順をスキップできます。 後からいつでもインテントを元に戻すことができます。
    - [アプリケーション検出](migrate-support-matrix-vmware.md#application-discovery-requirements)または[エージェントレス分析](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)に必要な資格情報を確認してください。

5. VM の検出を開始するには、 **[検出の開始]** をクリックします。 検出が正常に開始されたら、vCenter Server の IP アドレスまたは FQDN に対する検出状態を表で確認できます。

検出は次のように行われます。
- 検出された VM のメタデータがポータルに表示されるまでに、約 15 分かかります。
- インストールされているアプリケーション、ロール、および機能の検出には時間がかかります。 検出対象の VM 数によって期間は変わります。 500 台の VM がある場合、アプリケーション インベントリが Azure Migrate ポータルに表示されるまでに約 1 時間かかります。


## <a name="next-steps"></a>次のステップ

- Azure VM への移行のために [VMware VM を評価](./tutorial-assess-vmware-azure-vm.md)します。
- 検出中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。