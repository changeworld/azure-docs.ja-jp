---
title: Azure Migrate Server Assessment を使用して VMware VM を検出する
description: Azure Migrate Server Assessment ツールを使用して、オンプレミスの VMware VM を検出する方法について説明します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 0e06d82c30743a4084cfc5ff856b4a9c8d548146
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98566943"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>チュートリアル:Server Assessment を使用して VMware VM を検出する

Azure への移行の一環として、オンプレミスのインベントリとワークロードを検出します。

このチュートリアルでは、軽量の Azure Migrate アプライアンスを使用して、Azure Migrate: Server Assessment ツールでオンプレミスの VMware 仮想マシン (VM) を検出する方法について説明します。 アプライアンスを VMware VM としてデプロイして、VM とそのパフォーマンス メタデータ、VM で実行されているアプリケーション、VM の依存関係を継続的に検出します。

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
**vCenter Server/ESXi ホスト** | バージョン 5.5、6.0、6.5、または 6.7 を実行している vCenter Server が必要です。<br/><br/> VM は、バージョン 5.5 以降が実行されている ESXi ホストでホストされている必要があります。<br/><br/> vCenter Server で、TCP ポート 443 での受信接続を許可して、アプライアンスが構成とパフォーマンスのメタデータを収集できるようにします。<br/><br/> 既定では、アプライアンスはポート 443 で vCenter に接続します。 vCenter Server が別のポートでリッスンする場合は、アプライアンス構成マネージャーで vCenter Server の詳細を指定するときにポートを変更できます。<br/><br/> VM をホストする ESXi サーバーで、VM にインストールされているアプリケーションと VM の依存関係を検出するために、TCP ポート 443 で受信アクセスが許可されていることを確認します。
**アプライアンス** | vCenter Server には、Azure Migrate アプライアンスに VM を割り当てるためのリソースが必要です。<br/><br/> - 32 GB の RAM、8 個の vCPU、約 80 GB のディスク記憶域。<br/><br/> - 外部仮想スイッチと、アプライアンス VM のインターネット アクセス (直接またはプロキシ経由)。
**VM** | 構成とパフォーマンスのメタデータの検出、および VM にインストールされているアプリケーションの検出では、Windows および Linux OS のすべてのバージョンがサポートされています。 <br/><br/> エージェントレスの依存関係分析でサポートされている OS バージョンについては、[こちら](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)を確認してください。<br/><br/> インストールされているアプリケーションと VM の依存関係を検出するには、VMware Tools (10.2.0 より後) が VM にインストールされ、実行されている必要があります。また、Windows VM には、PowerShell バージョン 2.0 以降がインストールされている必要があります。


## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

Azure Migrate プロジェクトを作成し、Azure Migrate アプライアンスを登録するには、以下を備えたアカウントが必要です。
- Azure サブスクリプションに対する共同作成者または所有者のアクセス許可
- Azure Active Directory (AAD) アプリを登録するためのアクセス許可
- エージェントレスの VMware 移行時に使用されるキー コンテナーを作成するための、Azure サブスクリプションに対する所有者または共同作成者とユーザー アクセス管理者のアクセス許可

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

1. アプライアンスを登録するには、お使いの Azure アカウントに **AAD アプリを登録するためのアクセス許可** が必要です。
1. Azure portal で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** に移動します。
1. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で **[はい]** に設定されています)。

    ![[ユーザー設定] で、ユーザーが Active Directory アプリを登録できることを確認する](./media/tutorial-discover-vmware/register-apps.png)

9. [アプリの登録] 設定が [いいえ] に設定されている場合は、テナントまたはグローバル管理者に、必要なアクセス許可を割り当てるよう依頼してください。 テナントまたはグローバル管理者は、AAD アプリの登録を許可するために、**アプリケーション開発者** ロールをアカウントに割り当てることもできます。 [詳細については、こちらを参照してください](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-vmware"></a>VMware を準備する

vCenter Server で、OVA ファイルを使用して VM を作成するためのアクセス許可がお使いのアカウントにあることを確認します。 これは、OVA ファイルを使用して Azure Migrate アプライアンスを VMware VM としてデプロイする場合に必要です。

Server Assessment には、VMware VM の検出と評価用に vCenter Server の読み取り専用アカウントが必要です。 インストールされているアプリケーションと VM の依存関係も検出する必要がある場合は、 **[Virtual Machines]\(仮想マシン\) > [Guest Operations]\(ゲスト操作\)** でそのアカウントの特権が有効になっている必要があります。

### <a name="create-an-account-to-access-vcenter"></a>vCenter にアクセスするためのアカウントを作成する

vSphere Web Client で、次のようにアカウントを設定します。

1. 管理者特権を備えたアカウントを使用して、vSphere Web Client で **[Administration]\(管理\)** を選択します。
2. **[Access]\(アクセス\)** 、 **[SSO Users and Groups]\(SSO ユーザーとグループ\)** の順に選択します。
3. **[Users]\(ユーザー\)** で、新しいユーザーを追加します。
4. **[New User]\(新しいユーザー\)** で、アカウントの詳細を入力します。 次に、 **[OK]** をクリックします
5. **[Global Permissions]\(グローバル アクセス許可\)** でユーザー アカウントを選択し、アカウントに **[Read-only]\(読み取り専用\)** ロールを割り当てます。 次に、 **[OK]** をクリックします
6. インストールされているアプリケーションと VM の依存関係も検出する必要がある場合は、 **[Roles]\(ロール\)** に移動して、 **[Read-only]\(読み取り専用\)** ロールを選択し、 **[Privileges]\(特権\)** で **[Guest Operations]\(ゲスト操作\)** を選択します。 [Propagate to children]\(子に反映する\) チェック ボックスをオンにすると、vCenter Server のすべてのオブジェクトに特権を反映できます。
 
    ![読み取り専用ロールでのゲスト操作を許可するチェック ボックス](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>VM にアクセスするためのアカウントを作成する

インストールされているアプリケーションと VM の依存関係を検出するには、VM に対して必要な権限を備えたユーザー アカウントが必要です。 このユーザー アカウントは、アプライアンス構成マネージャーで指定できます。 アプライアンスによって、VM にエージェントがインストールされることはありません。

1. Windows VM の場合は、VM に対する管理アクセス許可が付与されたアカウント (ローカルまたはドメイン) を作成します。
2. Linux VM の場合は、root 権限が付与されたアカウントを作成します。 または、/bin/netstat および /bin/ls のファイルに対する次のアクセス許可が付与されたアカウントを作成することもできます: CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。

> [!NOTE]
> 現在、Azure Migrate では、インストールされているアプリケーションと VM の依存関係を検出するためにアプライアンスで提供できる、Windows VM 用のユーザー アカウントと Linux VM 用のユーザー アカウントがそれぞれ 1 つサポートされています。


## <a name="set-up-a-project"></a>プロジェクトの設定

新しい Azure Migrate プロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** で、 **[プロジェクトの作成]** を選択します。
5. **[プロジェクトの作成]** で、Azure サブスクリプションとリソース グループを選択します。 リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

   ![プロジェクト名とリージョンのボックス](./media/tutorial-discover-vmware/new-project.png)

7. **［作成］** を選択します
8. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。**Azure Migrate: Server Assessment** ツールは、新しいプロジェクトに既定で追加されます。

![既定で追加された Server Assessment ツールを示すページ](./media/tutorial-discover-vmware/added-tool.png)

> [!NOTE]
> プロジェクトを既に作成している場合は、その同じプロジェクトを使用して追加のアプライアンスを登録することで、より多くの VM を検出して評価できます。[詳細情報](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>アプライアンスを設定する

Azure Migrate: Server Assessment では、軽量の Azure Migrate アプライアンスが使用されます。 このアプライアンスによって、VM 検出が実行され、VM の構成とパフォーマンスのメタデータが Azure Migrate に送信されます。 アプライアンスは、Azure Migrate プロジェクトからダウンロードできる OVA テンプレートをデプロイすることで設定できます。

> [!NOTE]
> なんらかの理由で、テンプレートを使用してアプライアンスを設定できない場合は、既存の Windows Server 2016 サーバー上で PowerShell スクリプトを使用して設定できます。 [詳細については、こちらを参照してください](deploy-appliance-script.md#set-up-the-appliance-for-vmware)。


### <a name="deploy-with-ova"></a>OVA を使用してデプロイする

OVA テンプレートを使用してアプライアンスを設定するには:
1. アプライアンス名を指定し、ポータルで Azure Migrate プロジェクト キーを生成します
1. OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。 OVA がセキュリティで保護されていることを確認します。
1. アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。
1. アプライアンスを初めて構成し、Azure Migrate プロジェクト キーを使用して Azure Migrate プロジェクトに登録します。

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Azure Migrate プロジェクト キーを生成する

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3. **[1:Azure Migrate プロジェクト キーを生成します]** で、VMware VM の検出用に設定する Azure Migrate アプライアンスの名前を指定します。名前は 14 文字以内の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [マシンの検出] ページを閉じないでください。
1. Azure リソースが正常に作成されると、**Azure Migrate プロジェクト キー** が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

### <a name="2-download-the-ova-template"></a>2. OVA テンプレートをダウンロードする

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

### <a name="3-create-the-appliance-vm"></a>3. アプライアンス VM を作成する

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


### <a name="4-configure-the-appliance"></a>4. アプライアンスを構成する

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

    - [アプリケーションの検出](how-to-discover-applications.md)または[エージェントレスの依存関係分析](how-to-create-group-machine-dependencies-agentless.md)に使用するアカウントを作成済みの場合は、必要に応じて、ここで資格情報を追加します。
    - これらの機能を使用しない場合は、スライダーをクリックして手順をスキップできます。 後からいつでもインテントを元に戻すことができます。
    - [アプリケーションの検出](migrate-support-matrix-vmware.md#application-discovery-requirements)または[エージェントレスの依存関係分析](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)のためにアカウントで必要なアクセス許可を確認します。

5. VM の検出を開始するには、 **[検出の開始]** をクリックします。 検出が正常に開始されたら、vCenter Server の IP アドレスまたは FQDN に対する検出状態を表で確認できます。

検出は次のように行われます。
- 検出された VM のメタデータがポータルに表示されるまでに、約 15 分かかります。
- インストールされているアプリケーション、ロール、および機能の検出には時間がかかります。 検出対象の VM 数によって期間は変わります。 500 台の VM がある場合、アプリケーション インベントリが Azure Migrate ポータルに表示されるまでに約 1 時間かかります。
- VM の検出が完了したら、ポータルから、目的の VM でエージェントレスの依存関係分析を有効にすることができます。


## <a name="next-steps"></a>次のステップ

- Azure VM への移行のために [VMware VM を評価](./tutorial-assess-vmware-azure-vm.md)します。
- 検出中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。