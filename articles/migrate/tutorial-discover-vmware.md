---
title: Azure Migrate の検出および評価を使用して VMware 環境で実行されているサーバーを検出する
description: Azure Migrate の検出および評価ツールを使用して、VMware 環境のオンプレミス サーバー、アプリケーション、依存関係を検出する方法について説明します。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 11/12/2021
ms.custom: mvc
ms.openlocfilehash: 7eb6aea743176bb4cdaf313d392320378bc016d1
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397500"
---
# <a name="tutorial-discover-servers-running-in-a-vmware-environment-with-azure-migrate"></a>チュートリアル: Azure Migrate を使用して VMware 環境で実行されているサーバーを検出する

Azure への移行の一環として、オンプレミスのインベントリとワークロードを検出します。

このチュートリアルでは、Azure Migrate の検出および評価ツール、軽量の Azure Migrate アプライアンスを使用して、VMware 環境で実行されているサーバーを検出する方法について説明します。 このアプライアンスを vCenter Server インスタンスで実行されるサーバーとしてデプロイして、サーバーとそのパフォーマンス メタデータ、サーバー上で実行されているアプリケーション、サーバーの依存関係、ASP.NET Web アプリ、SQL Server インスタンスおよびデータベースを継続的に検出します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure アカウントを設定します。
> * 検出のために VMware 環境を準備します。
> * プロジェクトを作成します。
> * Azure Migrate アプライアンスを設定します。
> * 継続的な検出を開始します。

> [!NOTE]
> チュートリアルではシナリオを試すためのごく簡単な方法を紹介します。 可能な場合は既定のオプションが使用されます。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、これらの前提条件を満たしていることを確認してください。

要件 | 詳細
--- | ---
**vCenter Server/ESXi ホスト** | vCenter Server バージョン 6.7、6.5、6.0 または 5.5 を実行しているサーバーが必要です。<br /><br /> サーバーは、バージョン 5.5 以降が実行されている ESXi ホストでホストされている必要があります。<br /><br /> vCenter Server で、TCP ポート 443 での受信接続を許可して、アプライアンスが構成とパフォーマンスのメタデータを収集できるようにします。<br /><br /> 既定では、アプライアンスはポート 443 で vCenter Server に接続します。 vCenter Server を実行するサーバーが別のポートでリッスンする場合は、アプライアンス構成マネージャーで vCenter Server の詳細を指定するときにポートを変更できます。<br /><br /> ESXi ホストでは、インストールされているアプリケーションの検出とエージェントレスの依存関係分析をサーバーで実行するために、TCP ポート 443 で受信アクセスが許可されていることを確認します。
**Azure Migrate アプライアンス** | vCenter Server では、Azure Migrate アプライアンスをホストするサーバーにこれらのリソースを割り当てる必要があります。<br /><br /> - 32 GB の RAM、8 vCPU、約 80 GB のディスク ストレージ。<br /><br /> - 外部仮想スイッチと、アプライアンス サーバー上のインターネット アクセス (直接またはプロキシ経由)。
**サーバー** | 構成とパフォーマンス メタデータの検出では、Windows および Linux OS のすべてのバージョンがサポートされています。 <br /><br /> サーバー上でアプリケーションを検出するために、Windows および Linux OS のすべてのバージョンがサポートされています。 [エージェントレスの依存関係分析でサポートされている OS バージョン](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)をご確認ください。<br /><br /> インストールされているアプリケーションを検出してエージェントレスの依存関係を分析するには、VMware Tools (バージョン 10.2.1 以降) がサーバーにインストールされ、実行されている必要があります。 Windows サーバーには、PowerShell バージョン 2.0 以降がインストールされている必要があります。<br /><br /> SQL Server インスタンスおよびデータベースを検出するには、[サポート対象の SQL Server および Windows OS のバージョンとエディション](migrate-support-matrix-vmware.md#sql-server-instance-and-database-discovery-requirements)のほか、Windows の認証メカニズムを確認してください。<br /><br /> IIS Web サーバーで実行されている ASP.NET Web アプリを検出するには、[サポートされている Windows OS と IIS バージョン](migrate-support-matrix-vmware.md#aspnet-web-apps-discovery-requirements)を確認してください。

## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

プロジェクトを作成して Azure Migrate アプライアンスを登録するには、これらのアクセス許可を備えた Azure アカウントを用意する必要があります。

- Azure サブスクリプションにおける共同作成者または所有者のアクセス許可。
- Azure Active Directory (Azure AD) アプリを登録するためのアクセス許可。
- エージェントレスのサーバー移行時に使用される Azure Key Vault インスタンスを作成するための、Azure サブスクリプションにおける所有者または共同作成者とユーザー アクセス管理者のアクセス許可。

Azure の無料アカウントを作成した場合、既定では自分が Azure サブスクリプションの所有者になります。 サブスクリプションの所有者でない場合は、所有者と協力してアクセス許可を割り当てます。

Azure サブスクリプションにおける共同作成者または所有者のアクセス許可を設定するには:

1. Azure portal で「サブスクリプション」と検索します。 検索結果の **[サービス]** で **[サブスクリプション]** を選択します。

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="検索ボックスで Azure サブスクリプションを検索する方法を示すスクリーンショット。":::

1. **[サブスクリプション]** で、プロジェクトを作成するサブスクリプションを選択します。
1. 左側のメニューで **[アクセス制御 (IAM)]** を選択します。
1. **[アクセスの確認]** タブの **[アクセスの確認]** で、使用したいユーザー アカウントを検索します。
1. **[ロールの割り当てを追加する]** ペインで **[追加]** を選択します。

    :::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="ユーザー アカウントを検索してアクセスの確認とロールの割り当ての追加を行う方法を示すスクリーンショット。":::
    
1. **[ロールの割り当てを追加する]** で、共同作成者または所有者のロールを選択してから、アカウントを選択します。 **[保存]** を選択します。

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="アカウントにロールを割り当てるための [ロールの割り当ての追加] ページを示すスクリーンショット。":::

Azure AD アプリを登録するために必要なアクセス許可をアカウントに付与するには:

1. ポータルで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
1. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で **[はい]** に設定されています)。

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="アプリを登録するためのユーザー設定を確認する方法を示すスクリーンショット。":::

9. **[アプリの登録]** が **[いいえ]** に設定されている場合は、テナントまたはグローバル管理者に、必要なアクセス許可を割り当てるよう依頼します。 テナントまたはグローバル管理者は、ユーザーによる Azure AD アプリの登録を許可する目的でアプリケーション開発者ロールをアカウントに割り当てることもできます。 [詳細については、こちらを参照してください](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-vmware"></a>VMware を準備する

vCenter Server で、VMware Open Virtualization Appliance (OVA) 仮想マシン (VM) インストール ファイルを使用して VM を作成するためのアクセス許可が自分のアカウントにあることを確認します。 これらのアクセス許可は、OVA ファイルを使用して Azure Migrate アプライアンスを VMware VM としてデプロイするときに必要です。

Azure Migrate では、実際の VMware 環境で実行されているサーバーの検出および評価のために vCenter Server 読み取り専用アカウントが必要です。 インストールされているアプリケーションの検出とエージェントレスの依存関係分析も実行したい場合は、アカウントで VMware のアクセス許可が有効で、VM ゲスト操作が可能である必要があります。

### <a name="create-an-account-to-access-vcenter-server"></a>vCenter Server にアクセスするためのアカウントを作成する

VMware vSphere Web Client で、vCenter Server に使用する読み取り専用アカウントを設定します。

1. 管理特権があるアカウントを使用し、vSphere Web Client の **[Home]\(ホーム\)** メニューで **[Administration]\(管理\)** を選択します。
1. **[Single Sign-On]\(シングル サインオン\)** で **[Users and Groups]\(ユーザーとグループ\)** を選択します。
1. **[Users]\(ユーザー\)** で **[New User]\(新しいユーザー\)** を選択します。
1. アカウントの詳細情報を入力し、 **[OK]** を選択します。
1. **[Administration]\(管理\)** のメニューにある **[Access Control]\(アクセス制御\)** で、 **[Global Permissions]\(グローバル アクセス許可\)** を選択します。
1. ユーザー アカウントを選択してから **[Read-only]\(読み取り専用\)** を選択し、ロールをアカウントに割り当てます。 **[OK]** を選択します。
1. インストールされているアプリケーションの検出とエージェントレスの依存関係分析を開始できるようにするために、 **[Access Control]\(アクセス制御\)** のメニューで **[Roles]\(ロール\)** を選択します。 **[Roles]\(ロール\)** ペインの **[Roles]\(ロール\)** で **[Read-only]\(読み取り専用\)** を選択します。 **[Privileges]\(特権\)** で **[Guest operations]\(ゲスト操作\)** を選択します。 vCenter Server インスタンスのすべてのオブジェクトに特権が反映されるように、 **[Propagate to children]\(子に反映する\)** チェック ボックスをオンにします。

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="vSphere Web Client と、新しいアカウントを作成してユーザー ロールと特権を選択する方法を示すスクリーンショット。":::

> [!NOTE]
> vCenter Server アカウントのスコープを設定して、検出を特定の vCenter Server データセンター、クラスター、ホスト、クラスターまたはホストのフォルダー、個々のサーバーに制限できます。 [vCenter Server ユーザー アカウントのスコープを設定](set-discovery-scope.md)する方法をご確認ください。

> [!NOTE]
> 検出用に指定された vCenter サーバーにリンク モードを使用して接続されている vCenter 資産は、Azure Migrate によって検出されません。 検出する各 vCenter 環境に、Azure Migrate アプライアンスをデプロイする必要があります。

### <a name="create-an-account-to-access-servers"></a>サーバーにアクセスするためのアカウントを作成する

サーバー上の自分のユーザー アカウントには、インストールされているアプリケーションの検出、エージェントレスの依存関係分析、Web アプリと SQL Server インスタンスおよびデータベースの検出を開始するために必要なアクセス許可が付与されていなければなりません。 このユーザー アカウント情報は、アプライアンス構成マネージャーで指定できます。 アプライアンスは、サーバーにエージェントをインストールしません。

* Windows サーバーおよび Web アプリの検出の場合は、サーバーに対する管理者アクセス許可が付与されたアカウント (ローカルまたはドメイン) を作成します。 SQL Server インスタンスおよびデータベースを検出するには、Windows または SQL Server アカウントが sysadmin サーバー ロールのメンバーである必要があります。 [必要なロールをユーザー アカウントに割り当てる](/sql/relational-databases/security/authentication-access/server-level-roles)方法をご確認ください。
* Linux サーバーの場合は、ルート ユーザー アカウントの詳細を指定するか、/bin/netstat および /bin/ls ファイルに CAP_DAC_READ_SEARCH および CAP_SYS_PTRACE アクセス許可があるアカウントを作成します。

> [!NOTE]
> Azure Migrate アプライアンス構成マネージャーに複数のサーバー資格情報を追加して、インストールされているアプリケーションの検出、エージェントレスの依存関係分析、Web アプリと SQL Server インスタンスおよびデータベースの検出を実行できます。 複数のドメイン、Windows (ドメイン以外)、Linux (ドメイン以外)、または SQL Server の認証資格情報を追加できます。 [サーバーの資格情報を追加する](add-server-credentials.md)方法をご確認ください。

## <a name="set-up-a-project"></a>プロジェクトの設定

新しいプロジェクトを設定するには:

1. Azure portal で、 **[すべてのサービス]** を選択し、**Azure Migrate** を探します。
1. **[サービス]** で **[Azure Migrate]** を選択します。
1. **[概要]** で、移行の目標に応じて次のいずれかのオプションを選択します: **[Servers, databases and web apps]\(サーバー、データベース、および Web アプリ\)** 、 **[SQL Server (only)]\(SQL Server (のみ)\)** 、または **[その他のシナリオを探索する]** 。
1. **[プロジェクトの作成]** を選択します。
1. **[プロジェクトの作成]** で、Azure サブスクリプションとリソース グループを選択します。 リソース グループがない場合は作成します。
1. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成する地理的な場所を指定します。 [パブリック クラウドのサポート対象地域](migrate-support-matrix.md#supported-geographies-public-cloud)と[政府機関向けクラウドのサポート対象地域](migrate-support-matrix.md#supported-geographies-azure-government)をご確認ください。

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="新しい Azure Migrate プロジェクトのプロジェクト詳細を追加する方法を示すスクリーンショット。":::

1. **［作成］** を選択します
1. プロジェクトがデプロイされるまで数分待ちます。 **Azure Migrate の検出および評価** ツールは、新しいプロジェクトに既定で追加されます。

> [!NOTE]
> プロジェクトを既に作成してある場合は、そのプロジェクトを使用し、より多くのアプライアンスを登録してより多くのサーバーの検出と評価を行えます。 [プロジェクトを管理](create-manage-projects.md#find-a-project)する方法をご確認ください。

## <a name="set-up-the-appliance"></a>アプライアンスを設定する

Azure Migrate の検出および評価ツールでは、軽量の Azure Migrate アプライアンスを使用します。 このアプライアンスによってサーバー検出が実行され、サーバーの構成とパフォーマンス メタデータが Azure Migrate に送信されます。 アプライアンスは、プロジェクトからダウンロードできる OVA テンプレートをデプロイすることで設定します。

> [!NOTE]
> OVA テンプレートを使用してアプライアンスを設定できない場合は、Windows Server 2016 を実行している既存のサーバーで PowerShell スクリプトを実行して設定できます。 [PowerShell を使用して Azure Migrate アプライアンスを設定する](deploy-appliance-script.md#set-up-the-appliance-for-vmware)方法をご確認ください。 <br/>
> OVA テンプレートを使用してアプライアンスをデプロイする方法は、Azure Government クラウドではサポートされません。 Azure Government クラウド用のアプライアンスをデプロイする方法について[詳細をご覧ください](./deploy-appliance-script-government.md)。

### <a name="deploy-by-using-an-ova-template"></a>OVA テンプレートを使用してデプロイする

OVA テンプレートを使用してアプライアンスを設定するには、このセクションで詳しく説明されているこれらの手順を完了します。

1. ポータルで、アプライアンス名を指定してプロジェクト キーを生成します。
1. OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。 OVA が安全であることを確認します。
1. OVA ファイルからアプライアンスを作成します。 アプライアンスが Azure Migrate に接続できることを確認します。
1. アプライアンスを初めて構成します。 
1. プロジェクト キーを使用して、プロジェクトにアプライアンスを登録します。

#### <a name="generate-the-project-key"></a>プロジェクト キーを生成する

1. **[移行の目標]** で、 **[Servers, databases and web apps]\(サーバー、データベース、および Web アプリ\)**  >  **[Azure Migrate: Discovery and Assessment]**  >  **[検出]** の順に選択します。
1. **[Discover servers]\(サーバーの検出\)** で、 **[お使いのサーバーは仮想化されていますか?]** >  **[はい。VMware vSphere Hypervisor を使用します]** の順に選択します。
1. **[1: プロジェクト キーを生成します]** で、VMware 環境内のサーバーを検出するために設定する Azure Migrate アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
1. 必要な Azure リソースの作成を開始するには、 **[キーの生成]** を選択します。 リソースの作成中は、 **[検出]** ペインを閉じないようにしてください。
1. Azure リソースが正常に作成された後、"*プロジェクト キー*" が生成されます。
1. キーをコピーします。 キーは、アプライアンスの構成時にアプライアンスの登録を完了するために使用します。

#### <a name="download-the-ova-template"></a>OVA テンプレートをダウンロードする

**[2: Azure Migrate アプライアンスをダウンロードする]** で、OVA ファイルを選択してから **[ダウンロード]** を選択します。

##### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、ファイルが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、 **[管理者として実行]** オプションを使用してコマンド プロンプト ウィンドウを開きます。
1. OVA ファイルのハッシュを生成するには、次のコマンドを実行します。
  
    ```bash
    C:\>CertUtil -HashFile <file_location> <hashing_agorithm>
    ```
   
    例: `C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256`

1. 最新のアプライアンス バージョンとハッシュ値を確認します。

    - Azure パブリック クラウドの場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140333) | 08e4fa3aa827a88b68d3b56306926773f75c3a8a47ba4296871cfcb6c9830ea8

    - Azure Government の場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (85.8 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140337) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

#### <a name="create-the-appliance-server"></a>アプライアンス サーバーを作成する

ダウンロードしたファイルをインポートして、VMware 環境にサーバーを作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順に選択します。
1. OVF テンプレートのデプロイ ウィザードで、 **[ソース]** を選択して OVA ファイルの場所を入力します。
1. **[Name]\(名前\)** にサーバーの名前を入力します。 **[Location]\(場所\)** で、サーバーがホストされるインベントリ オブジェクトを選択します。
1. **[Host/Cluster]\(ホスト/クラスター\)** で、サーバーが実行されるホストまたはクラスターを選択します。
1. **[Storage]\(ストレージ\)** で、サーバーの保存先を指定します。
1. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを選択します。
1. **[Network Mapping]\(ネットワーク マッピング\)** で、サーバーの接続先となるネットワークを選択します。 ネットワークには、Azure Migrate にメタデータを送信するためのインターネット接続が必要です。
1. 設定を確認し、 **[Finish]\(完了\)** を選択します。

#### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック クラウド](migrate-appliance.md#public-cloud-urls)および[政府機関向けクラウド](migrate-appliance.md#government-cloud-urls)の Azure URL にアプライアンス サーバーから接続できることを確認します。

### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定するには:

> [!NOTE]
> ダウンロードした OVA テンプレートではなく [PowerShell スクリプト](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、最初の 2 つの手順はスキップしてかまいません。

1. vSphere Client で、サーバーを右クリックしてから **[Open Console]\(コンソールを開く\)** を選択します。
1. アプライアンスの言語、タイム ゾーン、パスワードを選択または入力します。
1. アプライアンスに接続できる任意のコンピューター上でブラウザーを開きます。 次に、アプライアンス構成マネージャーの URL (`https://appliance name or IP address: 44368`) を開きます。

     または、アプライアンス サーバーのデスクトップから構成マネージャーへのショートカットを選択して、構成マネージャーを開くこともできます。
1. ライセンス条項に同意し、サードパーティの情報に目を通します。
1. 構成マネージャーで、 **[前提条件のセットアップ]** を選択し、これらの手順を完了します。
    1. **接続**:サーバーがインターネットにアクセスできることが、アプライアンスによって確認されます。 サーバーでプロキシを使用する場合は、次の操作を行います。
        1. **[プロキシを設定する]** を選択して、プロキシのアドレス (`http://ProxyIPAddress` または `http://ProxyFQDN` の形式。*FQDN* は "*完全修飾ドメイン名*" を指す) とリスニング ポートを指定します。
        1.  プロキシで認証が必要な場合は資格情報を入力します。
        1. プロキシの詳細を追加した場合、あるいはプロキシまたは認証を無効にした場合は、 **[保存]** を選択し、接続をトリガーしてもう一度接続を確認します。

            サポートされるのは HTTP プロキシのみです。
    1. **時刻同期**: 検出が正常に機能するように、アプライアンス上の時刻がインターネットの時刻と同期していることを確認します。
    1. **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了すると、 **[View appliance services]\(アプライアンス サービスを表示\)** を選択して、アプライアンス サーバーで実行されているサービスの状態とバージョンを確認できます。
    1. **Install VDDK (VDDK のインストール)** : VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることがアプライアンスによって確認されます。 VDDK がインストールされていない場合は、VMware から VDDK 6.7 をダウンロードします。 "*インストール手順*" に記載されているとおりに、ダウンロードした zip ファイルの内容をアプライアンス上の指定された場所に抽出します。

        Azure Migrate Server Migration では、Azure への移行時に VDDK を使用してサーバーがレプリケートされます。 
1. アプライアンスの構成中ならいつでも "*前提条件を再実行*" して、アプライアンスがすべての前提条件を満たしているかどうかを確認できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="アプライアンス構成マネージャーでの前提条件の設定方法を示すスクリーンショット。":::

#### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーしたプロジェクト キーを貼り付けます。 キーがない場合は、 **[Azure Migrate: Discovery and Assessment]\(Azure Migrate: 検出および評価\)**  >  **[検出]**  >  **[既存のアプライアンスの管理]** の順に移動します。 プロジェクト キーを生成したときに指定したアプライアンスの名前を選択して、表示されたキーをコピーします。
1. Azure による認証にはデバイス コードが必要です。 **[ログイン]** を選択します。 **[Azure ログインの続行]** で、 **[Copy code & Login]\(コードのコピーとログイン\)** を選択してデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。プロンプトが表示されるように、ブラウザーでポップアップ ブロックを無効にしておいてください。

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="デバイス コードをコピーしてログインする場所を示すスクリーンショット。":::

1. ブラウザーの新しいタブで、デバイス コードを貼り付け、自分の Azure ユーザー名とパスワードを使用してサインインします。 PIN を使用したサインインはサポートされていません。

    ログインせずにログイン タブを誤って閉じた場合は、アプライアンス構成マネージャーのブラウザー タブを最新の情報に更新して、デバイス コードと **[Copy code & Login]\(コードのコピーとログイン\)** ボタンを表示します。
1. 正常にログインしたら、アプライアンス構成マネージャーが表示されているブラウザー タブに戻ります。 ログインに使用した Azure ユーザー アカウントに、キーの生成時に作成された Azure リソースに対する必要なアクセス許可がある場合は、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** を選択して、登録の詳細を確認できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="アプライアンスが正常に登録されたことを示す [Azure Migrate への登録] ペインのスクリーンショット。":::

## <a name="start-continuous-discovery"></a>継続的な検出を開始する

アプライアンス構成マネージャーでの設定手順を完了し、検出を準備して開始します。

### <a name="provide-vcenter-server-details"></a>vCenter Server の詳細を指定する

サーバーの構成およびパフォーマンス データを検出するには、アプライアンスから vCenter Server に接続する必要があります。

1. **[Step 1: Provide vCenter Server credentials]\(手順 1: vCenter Server の資格情報を指定する\)** で、 **[資格情報の追加]** を選択して資格情報の名前を入力します。 vCenter Server で実行中のサーバーを検出するためにアプライアンスで使用される vCenter Server アカウントのユーザー名とパスワードを追加します。
    - この記事で前に述べたように、必要なアクセス許可を備えたアカウントを設定する必要があります。
    - 検出のスコープを特定の VMware オブジェクト (vCenter Server データセンター、クラスター、ホスト、クラスターまたはホストのフォルダー、あるいは個々のサーバー) に設定する場合は、[検出のスコープを設定](set-discovery-scope.md)する手順を参照し、Azure Migrate によって使用されるアカウントを制限してください。
1. **[Step 2: Provide vCenter Server details]\(手順 2: vCenter Server の詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** を選択して、ドロップダウン リストから資格情報の名前を選択します。 vCenter Server の IP アドレスまたは FQDN を選択します。 ポートを既定値 (443) のままにしておくことも、vCenter Server でリッスンするカスタム ポートを指定することもできます。 **[保存]** を選択します。
1. アプライアンスでは、資格情報を使用して、vCenter Server を実行しているサーバーへの接続の検証を試行します。 資格情報テーブルに vCenter Server の IP アドレスまたは FQDN の検証状態が表示されます。
1. 検出を開始する前に、vCenter Server への接続はいつでも "*再検証*" できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="アプライアンス構成マネージャーで vCenter Server の資格情報と検出ソースを管理する方法を示すスクリーンショット。":::

### <a name="provide-server-credentials"></a>サーバーの資格情報を指定する

「**手順 3: ソフトウェア インベントリ、エージェントレスの依存関係の分析、SQL Server インスタンスとデータベースの検出、VMware 環境での ASP.NET Web アプリの検出を実行するためのサーバー資格情報を指定する**」で、複数のサーバー資格情報を指定できます。 これらのアプライアンス機能を使用しない場合は、この手順をスキップして、vCenter Server の検出に進むことができます。 このオプションはいつでも変更できます。

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="ソフトウェア インベントリ、依存関係分析、S Q L サーバー検出を行うための資格情報を指定する方法を示すスクリーンショット。":::

これらの機能を使用する場合、次の手順を実行して、サーバーの資格情報を指定します。 検出機能を実行するために、サーバーへの資格情報の自動マップがアプライアンスによって試行されます。

サーバーの資格情報を追加するには:

1. **[Add Credentials]\(資格情報の追加\)** を選択します。
1. ドロップダウン メニューで、 **[資格情報の種類]** を選択します。
    
    ドメイン、Windows (ドメイン以外)、Linux (ドメイン以外)、SQL Server の認証資格情報を指定できます。 [資格情報を指定する](add-server-credentials.md)方法とそれらを処理する方法を確認してください。
1. それぞれの種類の資格情報について、以下を入力します。
    * フレンドリ名。
    * "ユーザー名"。
    * パスワード。
    **[保存]** を選択します。

    ドメイン資格情報を使用することにした場合は、ドメインの FQDN の入力も必要です。 FQDN は、そのドメイン内の Active Directory インスタンスと共に資格情報の信頼性を検証するために必要です。
1. インストールされているアプリケーションの検出、エージェントレスの依存関係分析、Web アプリと SQL Server インスタンスおよびデータベースの検出のために、アカウントで[必要なアクセス許可](add-server-credentials.md#required-permissions)を確認します。
1. 一度に複数の資格情報を追加するには、 **[さらに追加]** を選択して資格情報を保存し、さらに資格情報を追加します。
    **[保存]** または **[さらに追加]** を選択すると、アプライアンスによって、認証のためにドメインの Active Directory インスタンスと共にドメイン資格情報が検証されます。 検証は、アカウントのロックアウトを避けるため、追加が行われるたびに実行されます。これは、各サーバーへの資格情報のマップがアプライアンスによって繰り返されるためです。

ドメイン資格情報の検証を確認するには:

構成マネージャーの資格情報テーブルで、ドメイン資格情報の **検証状態** を確認します。 ドメイン資格情報のみ検証されます。

検証が失敗した場合は、**失敗** の状態を選択して検証エラーを確認できます。 問題を修正し、 **[資格情報の再検証]** を選択して資格情報の検証を再試行します。

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="複数の資格情報の指定と検証を示すスクリーンショット。":::

### <a name="start-discovery"></a>検出を開始する

vCenter Server の検出を開始するには、 **[検出の開始]** を選択します。 検出が正常に開始された後、ソース テーブルで vCenter Server の IP アドレスまたは FQDN を見て検出状態を確認できます。

## <a name="how-discovery-works"></a>検出のしくみ

* 検出されたサーバーのインベントリが Azure portal に表示されるには、約 15 分かかります。
* サーバーの資格情報を指定してある場合、vCenter Server を実行しているサーバーの検出が完了したときに、ソフトウェア インベントリ (インストールされているアプリケーションの検出) は自動的に開始されます。 ソフトウェア インベントリは、12 時間ごとに実行されます。
* [ソフトウェア インベントリ](how-to-discover-applications.md)では、サーバー上で実行されている SQL Server インスタンスを特定します。 そこで収集された情報を使用し、アプライアンスで指定された Windows 認証資格情報または SQL Server 認証資格情報を介して、アプライアンスから SQL Server インスタンスへの接続が試行されます。 次に、SQL Server データベースとそのプロパティに関するデータが収集されます。 SQL Server の検出は 24 時間ごとに実行されます。
* アプライアンスは、ネットワークの見通しがある SQL Server インスタンスのみに接続できますが、ソフトウェア インベントリ単体ではネットワークの見通しが必要ない場合があります。
* インストールされているアプリケーションの検出には、15 分以上かかることがあります。 期間は検出されたサーバーの数によって異なります。 500 台のサーバーでは、検出されたインベントリがポータルの Azure Migrate プロジェクトに表示されるまで約 1 時間かかります。
* [ソフトウェア インベントリ](how-to-discover-applications.md)は、検出されたサーバーに存在する Web サーバーの役割を識別します。 サーバーで Web サーバーの役割が有効になっている場合、Azure Migrate により、サーバーで Web アプリの検出が実行されます。 Web アプリ構成データは 24 時間に 1 回更新されます。
* ソフトウェア インベントリの間、エージェントレスの依存関係分析のために、追加されたサーバー資格情報がサーバーに対して繰り返され、検証されます。 サーバーの検出が完了すると、ポータルでサーバーに対するエージェントレスの依存関係分析を有効にできます。 [エージェントレスの依存関係分析](how-to-create-group-machine-dependencies-agentless.md)を有効にするように選択できるのは、検証が成功したサーバーだけです。
* 検出を開始してから 24 時間以内に、ASP.NET Web アプリと、SQL Server インスタンスおよびデータベースのデータがポータルに表示され始めます。
* Azure Migrate の既定では、SQL インスタンスに接続する最も安全な方法が使用されます。つまり、Azure Migrate により、TrustServerCertificate プロパティが `true` に設定され、Azure Migrate アプライアンスとソース SQL Server インスタンス間の通信が暗号化されます。 さらに、トランスポート層で SSL を使用してチャネルが暗号化され、証明書チェーンによる信頼性の検証がバイパスされます。 そのため、証明書のルート証明機関を信頼するようにアプライアンス サーバーを設定する必要があります。 ただし、アプライアンス上で **[Edit SQL Server connection properties]\(SQL Server の接続プロパティの編集\)** を選択して、接続設定を変更することができます。 選択内容の詳細については、[こちら](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)を参照してください。

    :::image type="content" source="./media/tutorial-discover-vmware/sql-connection-properties.png" alt-text="SQL Server の接続プロパティを編集する方法を示すスクリーンショット。":::

vCenter Server の検出を開始するには、 **[検出の開始]** を選択します。 検出が正常に開始された後、ソース テーブルで vCenter Server の IP アドレスまたは FQDN を見て検出状態を確認できます。

### <a name="view-discovered-data"></a>検出されたデータを表示する

1. Azure portal で、Azure Migrate に戻ります。
1. **[更新]** を選択して、検出されたデータを表示します。

   :::image type="content" source="./media/tutorial-discover-vmware/discovery-assessment-tile.png" alt-text="検出および評価タイルでの更新方法を示すスクリーンショット。":::

## <a name="next-steps"></a>次の手順

- [Azure VM に移行するためにサーバーを評価](./tutorial-assess-vmware-azure-vm.md)する方法について学習します。
- [Azure SQL に移行するために、SQL Server を実行しているサーバーを評価](./tutorial-assess-sql.md)する方法について学習します。
- [Azure App Service に移行するために Web アプリを評価](./tutorial-assess-webapps.md)する方法について学習します。
- 検出中に [Azure Migrate アプライアンスによって収集されるデータ](migrate-appliance.md#collected-data---vmware)を確認します。
