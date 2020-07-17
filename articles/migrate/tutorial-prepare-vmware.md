---
title: Azure Migrate を使用した評価と移行に向けて VMware VM を準備する
description: Azure Migrate を使用した評価と移行に向けて VMware VM を準備する方法について説明します。
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677301"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>評価および Azure への移行のために VMware VM を準備する

この記事は、[Azure Migrate](migrate-services-overview.md) を使用して、オンプレミスの VMware VM の評価や Azure への移行を準備する場合に役立ちます。



これはシリーズの最初のチュートリアルであり、VMware VM を評価して移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Migrate と連携するように Azure を準備します。
> * Azure Migrate:Server Assessment ツールを使用した VM の評価に向けて VMware を準備します。
> * Azure Migrate:Server Migration ツールを使用した VM の移行に向けて VMware を準備します。 

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 これらは、デプロイを設定する方法を学習する場合に、また簡単な概念実証として役立ちます。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

VMware VM を評価または移行するにあたって Azure で行うタスクとそれに必要なアクセス許可を次に示します。

**タスク** | **詳細** 
--- | --- 
**Azure Migrate プロジェクトの作成** | Azure アカウントには、プロジェクトを作成するために共同作成者または所有者のアクセス許可が必要です。 
**リソースプロバイダーの登録** | Azure Migrate では、軽量な Azure Migrate アプライアンスを使用して VMware VM を検出および評価し、Azure Migrate Server Assessment によってそれらを Azure に移行します。<br/><br/> アプライアンスの登録中、リソースプロバイダーはアプライアンスで選択されたサブスクリプションに登録されます。 [詳細については、こちらを参照してください](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> リソースプロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。
**Azure AD アプリの作成** | アプライアンスの登録時に、Azure Migrate では Azure Active Directory (Azure AD) アプリが作成されます。 <br/><br/> - 最初のアプリは、アプライアンスで実行されているエージェントと Azure で実行されているそれぞれのサービスとの間の通信に使用されます。<br/><br/> - 2 番目のアプリは、VMware VM のエージェントレス移行の目的でユーザーのサブスクリプション内に作成されたキー コンテナーにアクセスするためにのみ使用されます。 [詳細については、こちらを参照してください](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> Azure AD アプリを作成するためのアクセス許可が必要です (アプリケーション開発者ロールで利用可能)。
**キー コンテナーの作成** | エージェントレス移行を使用して VMware VM を移行するために、ご利用のサブスクリプションのレプリケーション ストレージ アカウントへのアクセス キーを管理するためのキー コンテナーが Azure Migrate によって作成されます。<br/><br/> コンテナーを作成するには、Azure Migrate プロジェクトが存在しているリソース グループに対するロールの割り当てアクセス許可が必要です。




### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。

### <a name="assign-permissions-to-register-the-appliance"></a>アプライアンスを登録するためのアクセス許可を割り当てる

アプライアンスを登録するには、アプライアンスの登録時に Azure AD アプリを作成するためのアクセス許可を Azure Migrate に割り当てます。 次のいずれかの方法を使用して、アクセス許可を割り当てることができます。

- **アクセス許可を付与する**: テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
- **アプリケーション開発者ロールを割り当てる**: テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。

> [!NOTE]
> - アプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
> - 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。


#### <a name="grant-account-permissions"></a>アカウントへのアクセス許可の付与

テナント管理者またはグローバル管理者にアクセス許可を付与してもらうには、次の手順を実行します。

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。 これは、重要ではない既定の設定です。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることもできます。 ロールの割り当てについての[詳しい情報](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)をご覧ください。

### <a name="assign-permissions-to-create-a-key-vault"></a>キー コンテナーを作成するためのアクセス許可を割り当てる

Azure Migrate でキー コンテナーを作成できるようにするには、次のようにアクセス許可を割り当てます。

1. Azure portal で、リソース グループの **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。

    - サーバー評価を実行するには、**共同作成者**のアクセス許可で十分です。
    - エージェントレスのサーバー移行を実行するには、**所有者** (または**共同作成者**および**ユーザー アクセス管理者**) のアクセス許可が必要です。

3. 必要なアクセス許可がない場合は、リソース グループの所有者にそれらを依頼してください。



## <a name="prepare-for-vmware-vm-assessment"></a>VMware VM の評価の準備

VMware VM の評価を準備するには、以下が必要です。

- **VMware の設定を確認します**。 移行する vCenter Server と VM が要件を満たしていることを確認します。
- **評価のためのアカウントを設定します**。 Azure Migrate は、このアカウントを使用して vCenter Server にアクセスし、評価のための VM を検出します。
- **アプライアンスの要件を確認します**。 Azure Migrate アプライアンスをデプロイする前に、その要件を確認します。

### <a name="verify-vmware-settings"></a>VMware の設定の確認

1. 評価に関する VMware サーバーの要件を[確認](migrate-support-matrix-vmware.md#vmware-requirements)します。
2. 必要なポートが vCenter サーバー上で開かれていることを[確認](migrate-support-matrix-vmware.md#port-access)します。
3. vCenter Server で、OVA ファイルを使用して VM を作成するためのアクセス許可がアカウントにあることを確認します。 OVA ファイルを使用して、Azure Migrate アプライアンスを VMware VM としてデプロイします。


### <a name="set-up-an-account-for-assessment"></a>評価のためのアカウントの設定

評価およびエージェントレス移行のための VM を検出するには、Azure Migrate が vCenter Server にアクセスする必要があります。

- アプリケーションを検出したりエージェントレスで依存関係を視覚化したりする予定がある場合は、読み取り専用のアクセス権を持つ vCenter Server アカウントを作成し、 **[仮想マシン]**  >  **[ゲスト操作]** の権限を有効にします。

  ![vCenter Server アカウントの権限](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- アプリケーションの検出やエージェントレスでの依存関係の視覚化を行う予定がなければ、vCenter Server の読み取り専用アカウントを設定します。

### <a name="verify-appliance-settings-for-assessment"></a>評価用のアプライアンス設定の確認

次のチュートリアルで Azure Migrate アプライアンスを設定して評価を開始する前に、アプライアンスのデプロイの準備を行います。

1. Azure Migrate アプライアンスの要件を[確認](migrate-appliance.md#appliance---vmware)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある Azure の URL について確認します。
3. 検出および評価中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。
4. アプライアンスのポート アクセス要件に[注意](migrate-support-matrix-vmware.md#port-access)します。




## <a name="prepare-for-agentless-vmware-migration"></a>エージェントレスの VMware 移行の準備

VMware VM の[エージェントレス移行](server-migrate-overview.md)の要件を確認します。

1. VMware サーバーの要件を[確認](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)します。
2. vCenter Server にアクセスする Azure Migrate に必要な[アクセス許可を確認](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)します。
3. VMware VM の要件を[確認](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)します。
4. Azure Migrate アプライアンスの要件を[確認](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance)します。
5. [パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドに必要な URL アクセスをメモします。
6. [ポートのアクセス](migrate-support-matrix-vmware-migration.md#agentless-ports)要件を確認します。

## <a name="prepare-for-agent-based-vmware-migration"></a>エージェントベースの VMware 移行の準備

VMware VM の[エージェントベース移行](server-migrate-overview.md)の要件を確認します。

1. VMware サーバーの要件を[確認](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)します。
2. vCenter Server にアクセスする Azure Migrate に必要な[アクセス許可を確認](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)します。
2. VMware VM の要件を[確認](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms)します。これには、移行する各 VM への Mobility Service のインストールも含まれます。
3. エージェントベース移行では、レプリケーション アプライアンスを使用します。
    - レプリケーション アプライアンスのデプロイ要件を[確認](migrate-replication-appliance.md#appliance-requirements)します。
    - アプライアンスに MySQL をインストールするための[いくつかの方法を確認](migrate-replication-appliance.md#mysql-installation)します。
    - [パブリック](migrate-replication-appliance.md#url-access) クラウドと[政府機関向け](migrate-replication-appliance.md#azure-government-url-access)クラウドに必要な URL アクセスをメモします。
    - レプリケーション アプライアンスの[ポート アクセス](migrate-replication-appliance.md#port-access)要件を確認します。
    
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure アクセス許可を設定しました。
> * 評価と移行のために VMware を準備しました。


2 番目のチュートリアルに進み、Azure Migrate プロジェクトを設定し、Azure に移行するために VMware VM を評価します。

> [!div class="nextstepaction"]
> [VMware VM の評価](./tutorial-assess-vmware.md)
