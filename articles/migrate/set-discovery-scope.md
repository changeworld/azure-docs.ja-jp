---
title: Azure Migrate を使用して VMware VM 検出範囲を設定する
description: Azure Migrate を使って VMware VM の評価と移行の検出範囲を設定する方法について説明します。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337353"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>VMware VM の検出範囲の設定

この記事では、[Azure Migrate アプライアンス](migrate-appliance-architecture.md)によって検出される VMware VM の検出範囲を制限する方法について説明します。

Azure Migrate アプライアンスは、次の場合にオンプレミスの VMware VM を検出します。 

- [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用して Azure への移行について VMware VM を評価する
- [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用して VMware VM を Azure に[エージェントレス移行](server-migrate-overview.md)する

## <a name="set-discovery-scope"></a>検出スコープの設定:


Azure Migrate アプライアンスを VMware VＭ の評価または移行のために設定したら、アプライアンスは VM の検出を開始し、VM のメタデータを Azure に送信します。 検出のためにアプライアンスを vCenter Server に接続する前に、検出範囲を設定すると、検出を vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々の VＭ に制限することができます。

範囲を設定するには、Azure Migrate が使用しているアカウントに vCenter Server へのアクセス許可を割り当てます。

## <a name="create-a-vcenter-user-account"></a>vCenter ユーザー アカウントを作成する

Azure Migrate アプライアンスが VMware VM の検出、評価、移行に使用する vCenter ユーザー アカウントをまだ設定していない場合は、まずそれを行います。

1.    vCenter Server 管理者として vSphere Web クライアントにログインします。
2.    **[管理]**  >  **[SSO users and Groups]** (SSO ユーザーとグループ) を選択し、 **[ユーザー]** タブをクリックします。
3.    **[新しいユーザー]** アイコンを選択します。
4.    新しいユーザー情報を入力し、 **[OK]** をクリックします。

アカウントのアクセス許可は展開する内容によって異なります。

**機能** | **アカウントのアクセス許可**
--- | ---
[評価](tutorial-assess-vmware.md)| アカウントは読み取り専用アクセス権が必要です。
[アプリ/ロール/機能の検出](how-to-discover-applications.md) | アカウントは読み取り専用アクセス権があり、 [仮想マシン] > [ゲスト操作] の権限が有効になっている必要があります。
[依存関係を分析する (エージェントレス)](how-to-create-group-machine-dependencies-agentless.md) | アカウントは読み取り専用アクセス権があり、 [仮想マシン] > [ゲスト操作] の権限が有効になっている必要があります。
[移行 (エージェントレス)](tutorial-migrate-vmware.md) | 適切なアクセス許可が割り当てられてたロールが必要です。<br/><br/> ロールを作成するには、vCenter Server 管理者として vSphere Web クライアントにログインします。 vCenter Server インスタンス > **ロールの作成** を選択します。 <em>Azure_Migrate</em> など、ロール名を指定し、[必要なアクセス許可](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)をロールに割り当てます。


## <a name="assign-permissions-on-vcenter-objects"></a>vCenter オブジェクトに対するアクセス許可を割り当てる

次の 2 つの方法のいずれかを使用して、インベントリ オブジェクトに対するアクセス許可を割り当てることができます。

- 検出または移行する VM をホストするすべての親オブジェクトについて、必要なアクセス許可を持つロールを使用しているアカウントに割り当てます。
- もう 1 つの方法として、ユーザー アカウントとロールをデータ センター レベルで割り当て、それらを子オブジェクトに伝達することもできます。 そのうえで、検出も移行もしたくないすべてのオブジェクトについては、**アクセスなし**ロールをアカウントに与えます。 すべての新しい子オブジェクトにも、親から継承されたアクセス権が自動的に付与されるので、この構成は煩雑であると同時に、アクセス制御が行われる場合があるため、この方法は推奨されません。

関連するすべてのオブジェクトに使用しているアカウントにロールを割り当てるには、次の手順を実行します。

- **評価の場合**:VM 評価の場合は、**読み取り専用**のロールを、検出する VM をホストしているすべての親オブジェクトの vCenter ユーザー アカウントに適用します。 データセンター以下の階層内にあるすべての親オブジェクト (ホスト、ホストのフォルダー、クラスター、フォルダー) が含まれます。 階層内の子オブジェクトにこれらのアクセス許可を反映します。

    ![アクセス許可の割り当て](./media/tutorial-assess-vmware/assign-perms.png)

- **エージェントレス型移行の場合**:エージェントレス型移行の場合は、検出する VM をホストしているすべての親オブジェクトについて、[必要なアクセス許可](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)を持つユーザー定義のロールをユーザー アカウントに適用します。 ロール名として <em>Azure_Migrate</em> を使用できます。

### <a name="scope-support"></a>範囲のサポート

現時点では、vCenter アカウントが vCenter VM フォルダー レベルでアクセス権を付与されている場合、Server Assessment ツールで VM を検出することはできません。 ホストとクラスターのフォルダーはサポートされています。

VM フォルダーで検出の範囲を指定する場合は、次の手順を実行して、vCenter アカウントに VM レベルで読み取り専用アクセス権を割り当てることにより実現できます。

1. 検出の範囲を設定する VM フォルダー内のすべての VM に対して、読み取り専用アクセス許可を割り当てます。
2. VM をホストしているすべての親オブジェクトに読み取り専用アクセス権を付与します。
    - データセンター以下の階層内にあるすべての親オブジェクト (ホスト、ホストのフォルダー、クラスター、クラスターのフォルダー) が含まれます。
    - すべての子オブジェクトに権限を広める必要はありません。
3. 検出に資格情報を使用します。**コレクション スコープ**にはデータセンターを選択します。 ロール ベースのアクセス制御を設定すると、対応する vCenter ユーザー アカウントがテナント固有の VM のみにアクセスできるようになります。


## <a name="next-steps"></a>次のステップ

[アプライアンスを設定し](how-to-set-up-appliance-vmware.md)、[継続的検出を開始します](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)。
