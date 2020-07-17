---
title: Azure Migrate を使用したオンプレミス サーバーでのアプリ、ロール、機能の検出
description: Azure Migrate Server Assessment を使用して、オンプレミス サーバー上のアプリ、ロール、および機能を検出する方法について説明します。
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: ff9f5489b513cd1405e6b093d7537e4cbcead041
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744625"
---
# <a name="discover-machine-apps-roles-and-features"></a>マシンのアプリ、ロール、および機能を検出する

この記事では、Azure Migrate を使用して、オンプレミス サーバー上のアプリケーション、ロール、および機能を検出する方法について説明します。Server Assessment を使用して作成する方法について説明します。

オンプレミス マシンで実行されているアプリのインベントリとロール/機能を検出すると、ワークロードに合わせて調整された Azure への移行パスを特定し、計画することができます。

> [!NOTE]
> 現在、アプリ検出は VMware VM に対してプレビュー段階であり、検出のみに限定されています。 アプリベースの評価はまだ提供されていません。 オンプレミスの VMware VM、Hyper-V VM、物理サーバーに対するマシン ベースの評価です。

Azure Migrate を使用したアプリ検出:Server Assessment はエージェントレスです。 マシンと VM には何もインストールされません。 Server Assessment では、検出の実行に、マシンのゲスト資格情報と共に Azure Migrate アプライアンスが使用されます。 アプライアンスから VMware マシンへのリモート アクセスには、VMware API が使用されます。


## <a name="before-you-start"></a>開始する前に

1. Azure Migrate プロジェクトを[作成](how-to-add-tool-first-time.md)していることを確認します。
2. プロジェクトに Azure Migrate: Server Assessment ツールを[追加](how-to-assess.md)していることを確認します。
4. Azure Migrate アプライアンスを使用した VMware VM の検出と評価に関する [VMware の要件](migrate-support-matrix-vmware.md#vmware-requirements)を確認します。
5. Azure Migrate アプライアンスを展開するための[要件](migrate-appliance.md)を確認します。
6. アプリケーションの検出の[サポートと要件を検証](migrate-support-matrix-vmware.md#application-discovery)します。



## <a name="deploy-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスをデプロイする

1. Azure Migrate アプライアンスを展開するための要件を[確認](migrate-appliance.md#appliance---vmware)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government クラウド](migrate-appliance.md#government-cloud-urls)でアクセスする必要がある Azure の URL について確認します。
3. 検出および評価中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。
4. アプライアンスのポート アクセス要件に[注意](migrate-support-matrix-vmware.md#port-access)します。
5. [Azure Migrate アプライアンスを展開](how-to-set-up-appliance-vmware.md)して検出を開始します。 アプライアンスを展開するには、OVA テンプレートをダウンロードして VMware にインポートし、アプライアンスを VMware VM として作成します。 アプライアンスを構成し、Azure Migrate に登録します。
6. アプライアンスを展開するときに、継続的な検出を開始するには、以下を指定します。
    - 接続先の vCenter Server の名前。
    - アプライアンスから vCenter Server に接続するために作成した資格情報。
    - アプライアンスから Windows/Linux VM に接続するために作成したアカウントの資格情報。

アプライアンスを展開し、資格情報を指定すると、アプリ、機能、およびロールの検出と共に、VM メタデータとパフォーマンス データの継続的な検出がアプライアンスで開始されます。  アプリ検出の期間は、所有している VM の数によって変わります。 通常、500 台の VM のアプリ検出には 1 時間かかります。

## <a name="prepare-a-user-account"></a>ユーザー アカウントを準備する

検出用のアカウントを作成し、アプライアンスに追加します。

### <a name="create-a-user-account-for-discovery"></a>検出用のユーザー アカウントを作成する

サーバー評価で検出のために VM にアクセスできるように、ユーザー アカウントを設定します。 アカウントの要件を[確認](migrate-support-matrix-vmware.md#application-discovery)します。


### <a name="add-the-user-account-to-the-appliance"></a>アプライアンスにユーザー アカウントを追加する

アプライアンスにユーザー アカウントを追加します。

1. アプライアンス管理アプリを開きます。 
2. **[Provide vCenter details]\(vCenter の詳細\)** パネルに移動します。
3. **[VM でのアプリケーションと依存関係の検出]** で、 **[資格情報の追加]** をクリックします。
3. **[オペレーティング システム]** を選択し、アカウントのフレンドリ名と、 **[ユーザー名]** / **[パスワード]** を入力します
6. **[保存]** をクリックします。
7. **[保存して検出を開始]** をクリックします。

    ![VM ユーザー アカウントを追加する](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)





## <a name="review-and-export-the-inventory"></a>インベントリの確認とエクスポート

検出が終了すると、アプリ検出用の資格情報を指定した場合は、Azure portal でアプリ インベントリを確認してエクスポートできます。

1. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** で、表示されたカウントをクリックして、 **[検出済みサーバー]** ページを開きます。

    > [!NOTE]
    > この段階では、必要に応じて、検出されたマシンの依存関係を設定して、評価対象のマシン間の依存関係を視覚化することもできます。 [詳細については、こちらを参照してください](how-to-create-group-machine-dependencies.md)。

2. **[Applications discovered]\(検出されたアプリケーション\)** で、表示されたカウントをクリックします。
3. **[Application inventory]\(アプリケーション インベントリ\)** では、検出されたアプリ、ロール、および機能を確認できます。
4. インベントリをエクスポートするには、 **[検出済みサーバー]** の **[Export app inventory]\(アプリ インベントリのエクスポート\)** をクリックします。

アプリ インベントリは、Excel 形式でエクスポートおよびダウンロードされます。 **[Application Inventory]\(アプリケーション インベントリ\)** シートには、すべてのマシンで検出されたすべてのアプリが表示されます。

## <a name="next-steps"></a>次のステップ

- 検出済みサーバーのリフトアンドシフト移行のための[評価を作成](how-to-create-assessment.md)します。
- SQL Server データベースの評価に [Azure Migrate:Database Assessment](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017) を使用します。
