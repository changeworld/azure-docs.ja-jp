---
title: Azure Migrate を使用した評価と移行に向けて Hyper-V VM を準備する
description: Azure Migrate を使用した評価と移行に向けて Hyper-V VM を準備する方法について説明します。
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: 1d327f558806e0205540c183c56b92ba31e33cb7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "77031222"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Hyper-V VM の評価および Azure への移行を準備する

この記事では、[Azure Migrate](migrate-services-overview.md) を使用して、オンプレミスの Hyper-V VM の評価および Azure への移行を準備する方法について説明します。

[Azure Migrate](migrate-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。

これはシリーズの最初のチュートリアルであり、Hyper-V VM を評価して Azure に移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure を準備します。 Azure Migrate を操作するために、自分の Azure アカウントとリソースのアクセス許可を設定します。
> * サーバー評価用にオンプレミスの Hyper-V ホストと VM を準備します。 構成スクリプトを使用するか、手動で準備することができます。
> * Azure Migrate アプライアンスのデプロイを準備します。 アプライアンスは、オンプレミスの VM を検出および評価するために使用されます。
> * サーバー移行用にオンプレミスの Hyper-V ホストと VM を準備します。


> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、Hyper-V の評価と移行に関する手引きを参照してください。


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

### <a name="azure-permissions"></a>Azure のアクセス許可

Azure Migrate のデプロイに対するアクセス許可を設定する必要があります。

**タスク** | **アクセス許可**
--- | ---
**Azure Migrate プロジェクトの作成** | Azure アカウントには、プロジェクトを作成するためのアクセス許可が必要です。
**Azure Migrate アプライアンスを登録する** | Azure Migrate は、軽量な Azure Migrate アプライアンスを使用して、Azure Migrate Server Assessment によって Hyper-V VM を検出しおよび評価します。 このアプライアンスでは VM が検出され、VM のメタデータとパフォーマンス データが Azure Migrate に送信されます。<br/><br/>アプライアンスの登録時に、リソースプロバイダー (Microsoft.OffAzure、Microsoft.Migrate、および Microsoft.KeyVault) が、アプライアンスで選択したサブスクリプションに登録されます。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 リソースプロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。<br/><br/> オンボードの一環として、Azure Migrate によって Azure Active Directory (Azure AD) アプリが作成されます。<br/> AAD アプリは、アプライアンスで実行されているエージェントと Azure で実行されているそれぞれのサービスとの間の通信 (認証と承認) に使用されます。 このアプリには、任意のリソースに対して ARM 呼び出しや RBAC アクセスを行うための特権はありません。



### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

Azure Migrate プロジェクトを作成するためのアクセス許可があることを確認します。

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。


### <a name="assign-permissions-to-register-the-appliance"></a>アプライアンスを登録するためのアクセス許可を割り当てる

次のいずれかの方法を使用して、アプライアンスの登録時に Azure AD アプリを作成するためのアクセス許可を Azure Migrate に割り当てることができます。

- テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
- テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。

> [!NOTE]
> - このアプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
> - 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。


#### <a name="grant-account-permissions"></a>アカウントへのアクセス許可の付与

テナントおよびグローバル管理者は、次のようにアクセス許可を付与できます。

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> これは、重要ではない既定の設定です。 詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)をご覧ください。



#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることができます。 詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)をご覧ください。


## <a name="prepare-hyper-v-for-assessment"></a>Hyper-V の評価の準備

Hyper-V の VM 評価を、手動で、または構成スクリプトを使用して、準備することができます。 スクリプトでも[手動](#prepare-hyper-v-manually)でも、以下の準備が必要です。

- Hyper-V のホスト設定を[検証](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)し、Hyper-V ホスト上で[必要なポート](migrate-support-matrix-hyper-v.md#port-access)が開かれていることを確認します。
- 各ホスト上で PowerShell リモート処理を設定して、Azure Migrate アプライアンスが WinRM 接続を介してホスト上で PowerShell コマンドを実行できるようにします。
- VM ディスクがリモート SMB 共有に配置されている場合は、資格情報を委任します。
- Hyper-V ホスト上で VM を検出するためにアプライアンスによって使用されるアカウントを設定します。
- 検出と評価を行いたい各 VM で Hyper-V 統合サービスを設定します。



## <a name="prepare-with-a-script"></a>スクリプトの準備

スクリプトでは次を実行します。

- サポートされている PowerShell バージョンでスクリプトが実行されていることを確認します。
- 自分 (スクリプトを実行しているユーザー) が Hyper-V ホストの管理特権を持っていることを確認します。
- Hyper-V ホストと通信するために Azure Migrate サービスによって使用されるローカル ユーザー アカウント (管理者ではありません) を作成できるようにします。 このユーザー アカウントは、ホスト上のこれらのグループに追加されます。
    - リモート管理ユーザー
    - Hyper-V 管理者
    - パフォーマンス モニター ユーザー
- サポートされているバージョンの Hyper-V がホストで実行されていることを確認し、Hyper-V ロールを確認します。
- WinRM サービスを有効にし、ホスト上のポート 5985 (HTTP) と 5986 (HTTPS) を開きます (メタデータの収集に必要)。
- ホストでの PowerShell リモート処理を有効にします。
- ホストによって管理されているすべての VM で Hyper-V 統合サービスが有効になっていることを確認します。
- 必要に応じて、ホスト上で CredSSP を有効にします。

次のようにスクリプトを実行します。

1. Hyper-V ホストに PowerShell バージョン 4.0 以降がインストールされていることを確認します。
2. [Microsoft ダウンロード センター](https://aka.ms/migrate/script/hyperv)からスクリプトをダウンロードします。 このスクリプトは、Microsoft によって暗号的に署名されています。
3. MD5 または SHA256 ハッシュ ファイルを使用して、スクリプトの整合性を検証します。 ハッシュタグの値は以下に記載されています。 このコマンドを実行して、スクリプトのハッシュを生成します。
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    使用例:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  スクリプトの整合性を検証した後、この PowerShell コマンドを使用して、各 Hyper-V ホストでスクリプトを実行します。
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>ハッシュタグの値

ハッシュ値は次のとおりです。

| **ハッシュ** | **Value** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-hyper-v-manually"></a>手動での Hyper-V の準備

スクリプトを使用するのではなく、手動で Hyper-V を準備する場合は、このセクションの手順に従います。

### <a name="verify-powershell-version"></a>PowerShell のバージョンを確認する

Hyper-V ホストに PowerShell バージョン 4.0 以降がインストールされていることを確認します。



### <a name="set-up-an-account-for-vm-discovery"></a>VM 検出用のアカウントの設定

Azure Migrate には、オンプレミスの VM を検出するためのアクセス許可が必要です。

- Hyper-V ホストおよびクラスターに対する管理者アクセス許可を備えた、ドメインまたはローカルのユーザー アカウントを設定します。

    - 検出に含めるすべてのホストとクラスターに対して 1 つのアカウントが必要です。
    - アカウントは、ローカル アカウントでもドメイン アカウントでもかまいません。 Hyper-V ホストまたはクラスターに対する管理者アクセス許可を用意することをお勧めします。
    - または、管理者アクセス許可を割り当てたくない場合、以下のアクセス許可が必要です。
        - リモート管理ユーザー
        - Hyper-V 管理者
        - パフォーマンス モニター ユーザー

### <a name="verify-hyper-v-host-settings"></a>Hyper-V ホストの設定の確認

1. サーバー評価のための [Hyper-V ホストの要件](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)を確認します。
2. Hyper-V ホスト上で[必要なポート](migrate-support-matrix-hyper-v.md#port-access)が開かれていることを確認します。

### <a name="enable-powershell-remoting-on-hosts"></a>ホスト上での PowerShell リモート処理の有効化

次のように、各ホスト上で PowerShell リモート処理を設定します。

1. 各ホスト上で、管理者として PowerShell コンソールを開きます。
2. 次のコマンドを実行します。

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>VM での統合サービスの有効化

Azure Migrate が VM 上のオペレーティング システム情報をキャプチャできるように、各 VM で統合サービスを有効にする必要があります。

検出と評価を行う VM については、各 VM で [Hyper-V 統合サービス](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)を有効にします。


### <a name="enable-credssp-on-hosts"></a>ホスト上での CredSSP の有効化

ホストに VM があり、ディスクが SMB 共有上にある場合は、ホスト上でこの手順を実行します。

- このコマンドは、すべての Hyper-V ホスト上でリモートで実行できます。
- クラスターに新しいホスト ノードを追加すると、自動的に検出用に追加されますが、必要に応じて新しいノード上で CredSSP を手動で有効にする必要があります。

次のように有効にします。

1. SMB 共有上にディスクがある Hyper-V VM を実行している Hyper-V ホストを特定します。
2. 特定された各 Hyper-V ホスト上で、次のコマンドを実行します。

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

アプライアンスを設定したら、[アプライアンス上で CredSSP を有効にして](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds)、その設定を終了します。 これについては、このシリーズの次のチュートリアルで説明します。


## <a name="prepare-for-appliance-deployment"></a>アプライアンスのデプロイの準備

次のチュートリアルで Azure Migrate アプライアンスを設定して評価を開始する前に、アプライアンスのデプロイの準備を行います。

1. アプライアンスの要件を[確認](migrate-appliance.md#appliance---hyper-v)します。
2. アプライアンスがアクセスする必要がある Azure URL を[確認](migrate-appliance.md#url-access)します。
3. 検出および評価中にアプライアンスによって収集されるデータを確認します。
4. アプライアンスのポート アクセス要件に[注意](migrate-appliance.md#collected-data---hyper-v)します。


## <a name="prepare-for-hyper-v-migration"></a>Hyper-V の移行の準備

1. Hyper-V ホストの移行の要件と、VM 移行のために Hyper-V ホストおよびクラスターがアクセスする必要がある Azure URL を[確認](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts)します。
2. Azure に移行する Hyper-V VM の要件を[確認](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)します。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure アカウントのアクセス許可を設定しました。
> * 評価および移行のために Hyper-V ホストおよび VM を準備しました。
> * Azure Migrate アプライアンスのデプロイを準備しました。

次のチュートリアルに進み、Azure Migrate プロジェクトを作成し、アプライアンスをデプロイして、Azure に移行する Hyper-V VM を検出および評価します。

> [!div class="nextstepaction"]
> [Hyper-V VM を評価する](./tutorial-assess-hyper-v.md)
