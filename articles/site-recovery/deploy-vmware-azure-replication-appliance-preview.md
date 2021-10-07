---
title: Azure Site Recovery レプリケーション アプライアンスのデプロイ - プレビュー
description: この記事では、Azure Site Recovery - プレビューを使用して、VMware のディザスター リカバリー用レプリケーション アプライアンスを Azure にデプロイするときのサポートと要件について説明します
ms.service: site-recovery
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: 940cfb52985e956a283e8278c572569e4f350f55
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084191"
---
# <a name="deploy-azure-site-recovery-replication-appliance---preview"></a>Azure Site Recovery レプリケーション アプライアンスのデプロイ - プレビュー

>[!NOTE]
> この記事の情報は、Azure Site Recovery - プレビューに適用されます。 クラシック リリースでの構成サーバーの要件の詳細については、[こちらの記事をご覧ください](vmware-azure-configuration-server-requirements.md)。

>[!NOTE]
> プレビュー アプライアンスの設定用に、必ず新しい Recovery Services コンテナーを作成してください。 既存のコンテナーを使用しないでください。

>[!NOTE]
> 物理マシンのレプリケーションの有効化は、このプレビューではサポートされていません。 

Azure への VMware VM のディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスのレプリケーション アプライアンスを展開します。

- レプリケーション アプライアンスは、オンプレミスの VMware と Azure の間の通信を調整します。 データのレプリケーションも管理します。
- Azure Site Recovery レプリケーション アプライアンスのコンポーネントとプロセスの詳細については、[こちらをご覧ください](vmware-azure-architecture-preview.md)。

## <a name="hardware-requirements"></a>ハードウェア要件

**コンポーネント** | **要件**
--- | ---
CPU コア数 | 8
RAM | 32 GB
ディスクの数 | 3 (OS ディスク - 80 GB、データ ディスク 1 - 620 GB、データ ディスク 2 - 620 GB を含む)

## <a name="software-requirements"></a>ソフトウェア要件

**コンポーネント** | **要件**
--- | ---
オペレーティング システム | Windows Server 2016
オペレーティング システムのロケール | 英語 (en-*)
Windows Server の役割 | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V
グループ ポリシー | これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - 既存の Web サイトが存在しない <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない <br>- [匿名認証](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10))を有効にする <br> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 設定を有効にする
FIPS (連邦情報処理標準) | FIPS モードを有効にしない|

## <a name="network-requirements"></a>ネットワークの要件

|**コンポーネント** | **要件**|
|--- | ---|
|完全修飾ドメイン名 (FQDN) | 静的|
|Port | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送)|
|NIC の種類 | VMXNET3 (アプライアンスが VMware VM である場合)|


### <a name="allow-urls"></a>許可 URL

次の URL が許可され、Azure Site Recovery レプリケーション アプライアンスから継続的な接続のために到達可能であることを確認します。

  | **URL**                  | **詳細**                             |
  | ------------------------- | -------------------------------------------|
  | portal.azure.com          | Azure portal に移動します。              |
  | `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | Azure サブスクリプションへのサインイン用。  |
  |`*.microsoftonline.com `|アプライアンスが Azure Site Recovery と通信するための Azure Active Directory (AD) アプリを作成します。 |
  |management.azure.com |アプライアンスが Azure Site Recovery サービスと通信するための Azure AD アプリを作成します。 |
  |`*.services.visualstudio.com `|内部監視に使用するアプリ ログをアップロードします。 |
  |`*.vault.azure.net `|Azure Key Vault でシークレットを管理します。 注:レプリケートするマシンに、ここへのアクセス権があることを確認してください。 |
  |aka.ms |aka リンクへのアクセスを許可します。 Azure Site Recovery アプライアンスの更新に使用されます。 |
  |download.microsoft.com/download |Microsoft ダウンロードからのダウンロードを許可します。 |
  |`*.servicebus.windows.net `|アプライアンスと Azure Site Recovery サービスの間の通信。 |
  |`*.discoverysrv.windowsazure.com `<br><br>`*.hypervrecoverymanager.windowsazure.com `<br><br> `*.backup.windowsazure.com ` |Azure Site Recovery マイクロサービスの URL に接続します。
  |`*.blob.core.windows.net `|ターゲット ディスクの作成に使用される Azure Storage にデータをアップロードします。 |


> [!NOTE]
> プライベート リンクは、プレビュー リリースではサポートされていません。

## <a name="folder-exclusions-from-antivirus-program"></a>ウイルス対策プログラムからのフォルダーの除外

### <a name="if-antivirus-software-is-active-on-appliance"></a>ウイルス対策ソフトウェアがアプライアンスでアクティブな場合

スムーズなレプリケーションを実現し、接続の問題を回避するために、ウイルス対策ソフトウェアから次のフォルダーを除外します。

C:\ProgramData\Microsoft Azure <br>
C:\ProgramData\ASRLogs <br>
C:\Windows\Temp\MicrosoftAzure C:\Program Files\Microsoft Azure Appliance Auto Update <br>
C:\Program Files\Microsoft Azure Appliance Configuration Manager <br>
C:\Program Files\Microsoft Azure Push Install Agent <br>
C:\Program Files\Microsoft Azure RCM Proxy Agent <br>
C:\Program Files\Microsoft Azure Recovery Services Agent <br>
C:\Program Files\Microsoft Azure Server Discovery Service <br>
C:\Program Files\Microsoft Azure Site Recovery Process Server <br>
C:\Program Files\Microsoft Azure Site Recovery Provider <br>
C:\Program Files\Microsoft Azure to On-Premise Reprotect agent <br>
C:\Program Files\Microsoft Azure VMware Discovery Service <br>
C:\Program Files\Microsoft On-Premise to Azure Replication agent <br>
E:\ <br>

### <a name="if-antivirus-software-is-active-on-source-machine"></a>ウイルス対策ソフトウェアがソース マシンでアクティブな場合

ソース マシンにアクティブなウイルス対策ソフトウェアがある場合、インストール フォルダーを除外する必要があります。 そのため、スムーズなレプリケーションのために、フォルダー C:\ProgramData\ASR\agent を除外します。

## <a name="prepare-azure-account"></a>Azure アカウントの準備

Azure Site Recovery レプリケーション アプライアンスを作成して登録するには、以下を備えた Azure アカウントが必要です。

- Azure サブスクリプションに対する共同作成者または所有者のアクセス許可。
- Azure Active Directory (AAD) アプリを登録するためのアクセス許可。
- Azure での Azure Site Recovery レプリケーション アプライアンスの登録時に使用される Key Vault を作成するための、Azure サブスクリプションに対する所有者または共同作成者と、ユーザーアクセス管理者のアクセス許可。

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。 サブスクリプションの所有者でない場合は、必要なアクセス許可について所有者と連携します。

## <a name="prerequisites"></a>前提条件

**必要なキー コンテナーのアクセス許可は次のとおりです**。

- Microsoft.OffAzure/*
- Microsoft.KeyVault/register/action
- Microsoft.KeyVault/vaults/read
- Microsoft.KeyVault/vaults/keys/read
- Microsoft.KeyVault/vaults/secrets/read
- Microsoft.Recoveryservices/*

**必要なアクセス許可を割り当てるには、次の手順に従います**。

1. Azure portal で「**Subscriptions**」を検索し、 **[サービス]** で **[サブスクリプション]** 検索ボックスを選択して、Azure サブスクリプションを検索します。

2. **[サブスクリプション]** ページで、Recovery Services コンテナーを作成したサブスクリプションを選択します。

3. 選択したサブスクリプションで、 **[アクセス制御 (IAM)]** > **[アクセスの確認]** の順に選択します。 **[アクセスの確認]** で、適切なユーザー アカウントを検索します。

4. **[ロールの割り当てを追加する]** で、 **[追加]** を選択し、共同作成者または所有者のロールを選択してから、アカウントを選択します。 次に、 **[保存]** を選択します。

  アプライアンスを登録するには、お使いの Azure アカウントに AAD アプリを登録するためのアクセス許可が必要です。

  **必要なアクセス許可を割り当てるには、次の手順に従います**。

  - Azure portal で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** に移動します。 **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で *[はい]* に設定されています)。

  - **[アプリの登録]** 設定が *[いいえ]* に設定されている場合は、テナントまたはグローバル管理者に、必要なアクセス許可を割り当てるよう依頼してください。 テナントまたはグローバル管理者は、AAD アプリの登録を許可するために、アプリケーション開発者ロールをアカウントに割り当てることもできます。


## <a name="prepare-infrastructure"></a>インフラストラクチャの準備

オンプレミス マシンで復旧を有効にするために、オンプレミス環境で Azure Site Recovery レプリケーション アプライアンスを設定する必要があります。 アプライアンスによって実行される操作の詳細については、[このセクションをご覧ください](vmware-azure-architecture-preview.md)。

**[Recovery Services コンテナー]**  >  **[作業の開始]** の順に移動します。 [VMware machines to Azure]\(VMware マシンから Azure\) で、 **[インフラストラクチャの準備]** を選択し、以下のセクションに進みます。

![Recovery Services コンテナー プレビュー](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

新しいアプライアンスを設定するには、OVF テンプレート (推奨) または PowerShell を使用できます。 すべての[ハードウェア](#hardware-requirements)と[ソフトウェアの要件](#software-requirements)、および他の前提条件を満たしていることを確認します。

## <a name="create-azure-site-recovery-replication-appliance"></a>Azure Site Recovery レプリケーション アプライアンスを作成する

OVF テンプレートまたは PowerShell を使用して、Site Recovery レプリケーション アプライアンスを作成できます。

>[!NOTE]
> アプライアンスのセットアップは、順番に実行する必要があります。 複数のアプライアンスを並列で登録することはできません。


### <a name="create-replication-appliance-through-ovf-template"></a>OVF テンプレートを使用してレプリケーション アプライアンスを作成する

Azure Site Recovery で、すべての前提条件の構成がテンプレートで確実に処理されるため、この方法をお勧めします。
OVF テンプレートにより、必要な仕様のマシンが起動します。

![アプライアンス作成用のインフラストラクチャを準備する](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

**次の手順のようにします。**

1. OVF テンプレートをダウンロードして、オンプレミス環境にアプライアンスを設定します。
2. デプロイが完了したら、アプライアンス VM の電源をオンにし、Microsoft Evaluation ライセンスに同意します。
3. 次の画面で、管理者ユーザーのパスワードを指定します。
4. **[Finalize]\(確定\)** を選択すると、システムが再起動され、管理者ユーザー アカウントでログインできます。

### <a name="set-up-the-appliance-through-powershell"></a>PowerShell を使用してアプライアンスを設定する

>[!NOTE]
> 物理マシンのレプリケーションの有効化は、このプレビューではサポートされていません。 

組織の制限がある場合は、PowerShell を使用して Site Recovery レプリケーション アプライアンスを手動で設定できます。 次の手順のようにします。

1. [ここ](https://aka.ms/V2ARcmApplianceCreationPowershellZip)からインストーラーをダウンロードし、このフォルダーを Azure Site Recovery レプリケーション アプライアンス上に配置します。
2. zip フォルダーが正常にコピーされた後、ファイルを解凍してフォルダーのコンポーネントを抽出します。
3. フォルダーが抽出された先のパスに移動し、管理者として次の PowerShell スクリプトを実行します。

    **DRInstaller.ps1**  

## <a name="register-appliance"></a>アプライアンスの登録
  アプライアンスを作成すると、Microsoft Azure アプライアンス構成マネージャーが自動的に起動します。 インターネット接続、時刻同期、システム構成、グループ ポリシー (以下に記載) などの前提条件が検証されます。

  - CheckRegistryAccessPolicy - レジストリ編集ツールへのアクセスを防止します。
      - キー: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
      - DisableRegistryTools の値が 0 以外に設定されている必要があります。

  - CheckCommandPromptPolicy - コマンド プロンプトへのアクセスを防止します。

      - キー: HKLM\SOFTWARE\Policies\Microsoft\Windows\System
      - DisableCMD の値が 0 以外に設定されている必要があります。

  - CheckTrustLogicAttachmentsPolicy - 添付ファイルの信頼ロジックです。

      - キー: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Attachments
      - UseTrustedHandlers の値が 3 以外に設定されている必要があります。

  - CheckPowershellExecutionPolicy - スクリプト実行を有効にします。

      - PowerShell 実行ポリシーは AllSigned または Restricted 以外でなければなりません。
      - グループ ポリシー [Turn on Script Execution Attachment Manager]\(スクリプトの実行を有効にする 添付ファイル マネージャー\) が、[無効] または [署名済みスクリプトのみ許可する] 以外に設定されている必要があります。


  **次の手順を使用して、アプライアンスを登録します**。

1. **[use proxy to connect to internet]\(プロキシを使用してインターネットに接続する\)** オプションを切り替えて、プロキシ設定を構成します。

    すべての Azure Site Recovery サービスは、これらの設定を使用してインターネットに接続します。 サポートされるのは HTTP プロキシのみです。

2. [必要な URL](#allow-urls) が許可され、Azure Site Recovery レプリケーション アプライアンスから継続的な接続のために到達可能であることを確認します。

3. 前提条件を確認したら、次のステップではすべてのアプライアンス コンポーネントに関する情報がフェッチされます。 すべてのコンポーネントの状態を確認し、 **[続行]** をクリックします。 詳細を保存した後、アプライアンスの接続の選択に進みます。

4. 接続の詳細を保存した後、 **[続行]** を選択して Microsoft Azure での登録に進みます。

5. [前提条件](#prerequisites)が満たされていることを確認し、登録を進めます。

    ![アプライアンスの登録](./media/deploy-vmware-azure-replication-appliance-preview/app-setup-register.png)

  - **[Friendly name of appliance]\(アプライアンスのフレンドリ名\)** : Azure portal の Recovery Services コンテナー インフラストラクチャで、このアプライアンスを追跡する際に使用するフレンドリ名を指定します。

  - **[Azure Site Recovery replication appliance key]\(Azure Site Recovery レプリケーション アプライアンス キー\)** : ポータルで **[Recovery Services コンテナー]**  >  **[作業の開始]**  >  **[VMware to Azure Prepare Infrastructure]\(VMware から Azure へのインフラストラクチャの準備\)** に移動して、キーをコピーします。

  - キーを貼り付けて、 **[ログイン]** を選択します。
    新しい認証タブにリダイレクトされます。

      既定では、以下で強調されているように、認証マネージャー ページで認証コードが生成されます。 [認証] タブでこのコードを使用します。

  - Microsoft Azure 資格情報を入力し、登録を完了します。

      登録が成功したら、タブを閉じて構成マネージャーに移動し、セットアップを続行できます。

      ![認証コード](./media/deploy-vmware-azure-replication-appliance-preview/enter-code.png)

      > [!NOTE]
      > 認証コードは、生成から 5 分間で期限切れになります。 この期間を超えて非アクティブな場合は、Azure に再度ログインするように求めるメッセージが表示されます。


6. **[ログイン]** を選択してセッションに再接続します。 認証コードについては、構成マネージャーの「*概要*」または「*Azure Recovery Services コンテナーに登録する*」のセクションをご覧ください。

7. ログインが成功すると、サブスクリプション、リソース グループ、および Recovery Services コンテナーの詳細が表示されます。 コンテナーを変更する場合は、ログアウトできます。 そうでなければ、 **[続行]** をクリックして先に進みます。

    ![登録されたアプライアンス](./media/deploy-vmware-azure-replication-appliance-preview/app-setup.png)

    登録が成功したら、vCenter の詳細の構成に進みます。

    ![vCenter の構成](./media/deploy-vmware-azure-replication-appliance-preview/vcenter-information.png)

8. **[vCenter サーバーの追加]** を選択して、vCenter の情報を追加します。 vCenter のサーバー名または IP アドレスとポート情報を入力します。 その後、ユーザー名とパスワード、およびフレンドリ名を指定します。これは、[vCenter を介して管理されている仮想マシン](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)の詳細をフェッチするために使用されます。 ユーザー アカウントの詳細は暗号化され、マシンにローカルに格納されます。

>[!NOTE]
> 複数のアプライアンスに同じ vCenter Server を追加しようとしている場合は、両方のアプライアンスで同じフレンドリ名が使用されていることを確認します。

9. vCenter 情報が正常に保存された後、 **[仮想マシンの資格情報の追加]** を選択して、vCenter を通じて検出された VM のユーザーの詳細を指定します。

   >[!NOTE]
   > - Linux OS の場合は、ルート資格情報を指定してください。Windows OS の場合は、管理特権を持つユーザー アカウントを追加する必要があります。これらの資格情報は、レプリケーション操作の有効化中にモビリティ エージェントをソース VM にプッシュするために使用されます。 資格情報は、レプリケーションの有効化ワークフロー中に Azure portal で VM ごとに選択できます。
   > - アプライアンス コンフィギュレーターにアクセスして、マシンにアクセスするための資格情報を編集または追加します。

10. 詳細を正常に追加したら、 **[続行]** を選択して、すべての Azure Site Recovery レプリケーション アプライアンス コンポーネントをインストールし、Azure サービスに登録します。 このアクティビティには最大 30 分かかることがあります。

    構成の進行中にブラウザーを閉じないでください。

    >[!NOTE]
    > アプライアンスの複製は、このプレビューではサポートされていません。 複製しようとすると、復旧フローが中断されるおそれがあります。


## <a name="view-azure-site-recovery-replication-appliance-in-azure-portal"></a>Azure portal で Azure Site Recovery レプリケーション アプライアンスを表示する

Azure Site Recovery レプリケーション アプライアンスの構成が成功したら、Azure portal の **[Recovery Services コンテナー]** に移動します。

**[作業の開始]** の **[インフラストラクチャの準備 (プレビュー)]** を選択すると、Azure Site Recovery レプリケーション アプライアンスが既にこのコンテナーに登録されているのが確認できます。 以上で設定は完了です。 このレプリケーション アプライアンスを使用して、ソース マシンの保護を開始します。

*[Select 1 appliance(s)]\(1 アプライアンスを選択\)* をクリックすると、Azure Site Recovery レプリケーション アプライアンス ビューにリダイレクトされ、このコンテナーに登録されているアプライアンスの一覧が表示されます。

また、検出されたすべての vCenter Server/vSphere ホストの一覧を示す **[項目が検出されました]** タブも表示できます。

![レプリケーション アプライアンス プレビュー](./media/deploy-vmware-azure-replication-appliance-preview/discovered-items.png)

## <a name="sizing-and-capacity"></a>サイズ設定と容量
ワークロードを保護するために、組み込みのプロセス サーバーを使用するアプライアンスでは、次の構成に基づいて、最大 200 台まで仮想マシンを処理できます。

  |CPU |    メモリ |    キャッシュ ディスク サイズ |    データ変化率 |    保護対象コンピューター |
  |---|-------|--------|------|-------|
  |16 vCPU (2 ソケット * 8 コア @ 2.5 GHz)    | 32 GB |    1 TB (テラバイト) |    1 TB ～ 2 TB    | 151 ～ 200 台のマシンのレプリケートに使用する。|

- コンテナー内の任意のレプリケーション アプライアンスを使用して、vCenter サーバー内のすべてのマシンの検出を実行できます。

- 選択したアプライアンスが正常な場合は、同じコンテナー内の異なるアプライアンス間で[保護対象のマシンを切り替えます](switch-replication-appliance-preview.md)。

複数のアプライアンスを使用して、レプリケーション アプライアンスをフェールオーバーする方法の詳細については、[こちらの記事](switch-replication-appliance-preview.md)をご覧ください。

## <a name="next-steps"></a>次のステップ
Azure への [VMware VM](vmware-azure-set-up-replication-tutorial-preview.md) のディザスター リカバリーを設定する。
