---
title: SQL 用に Microsoft Defender を設定する方法
description: クラウドのオプションの Microsoft Defender for SQL プランに対して Microsoft Defender を有効にする方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: c38ff365efb2be164304b67ce5ab083bb0f1b0fe
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057359"
---
# <a name="enable-microsoft-defender-for-sql-servers-on-machines"></a>コンピューターの SQL サーバーに対して Microsoft Defender を有効にする 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この Microsoft Defender プランでは、データベースへの不正なアクセスや、データベースの悪用を試みる可能性がある異常なアクティビティを検出します。

疑わしいデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセスやクエリのパターンがある場合に、アラートが表示されます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|**コンピューター上の SQL サーバー用の Microsoft Defender** は、料金に関する [ページ](https://azure.microsoft.com/pricing/details/security-center/)に記載されているように課金されます。|
|保護される SQL のバージョン:|SQL Server (現在 [Microsoft でサポートされている](/mem/configmgr/core/plan-design/configs/support-for-sql-server-versions)バージョン)|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet|
|||



## <a name="set-up-microsoft-defender-for-sql-servers-on-machines"></a>コンピューターの SQL サーバー用に Microsoft Defender をセットアップする

このプランを有効にするには:

[手順 1. エージェント拡張機能をインストールする](#step-1-install-the-agent-extension)

[手順 2. SQL サーバーのホストで Log Analytics エージェントをプロビジョニングする:](#step-2-provision-the-log-analytics-agent-on-your-sql-servers-host)

[手順 3.[クラウドの価格と設定] ページで、Defender でオプションのプランを有効にします。](#step-3-enable-the-optional-plan-in-defender-for-clouds-pricing-and-settings-page)


### <a name="step-1-install-the-agent-extension"></a>手順 1. エージェント拡張機能をインストールする

- **Azure VM 上の SQL Server** - 「[SQL Server VM を SQL IaaS Agent 拡張機能に登録する](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)」で説明されているように、SQL Server VM を SQL IaaS Agent 拡張機能に登録します。

- **Azure Arc 対応サーバー上の SQL Server** - [Azure Arc のドキュメント](../azure-arc/servers/manage-vm-extensions.md)で説明されているインストール方法に従って、Azure Arc エージェントをインストールします。

### <a name="step-2-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>手順 2. SQL サーバーのホストで Log Analytics エージェントをプロビジョニングする

- **Azure VM 上の SQL Server** - SQL マシンが Azure VM でホストされている場合は、[Log Analytics エージェントの自動プロビジョニングを有効にする <a name="auto-provision-mma"></a>](enable-data-collection.md#auto-provision-mma)ことができます。 または、手動の手順に従って [Azure Stack Hub VM をオンボードする](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms)こともできます。
- **Azure Arc 対応サーバー上の SQL Server** - SQL Server が [Azure Arc](../azure-arc/index.yml) 対応サーバーで管理されている場合、Defender for Cloud の推奨事項「Log Analytics エージェントは Windows ベースの Azure Arc マシンにインストールする (Preview)」に従って Log Analytics エージェントを導入することができます。

- **オンプレミスの SQL Server** - SQL Server が Azure Arc を使用しないオンプレミスの Windows マシンでホストされている場合は、Azure に接続するためのオプションが 2 つあります。
    
    - **Azure Arc をデプロイする**：任意の Windows端末を、クラウドの Defender に接続できます。 ただし、Azure Arc を使用すると、Azure 環境の *すべて* がより緊密に統合されます。 Azure Arc を設定すると、ポータルに **[SQL Server - Azure Arc]** ページが表示され、セキュリティ アラートはそのページの専用の **[セキュリティ]** タブに表示されます。 そのため、最初に推奨されるオプションは、[ホストで Azure Arc を設定](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows)し、前述の **Azure Arc 上の SQL Server** の手順に従うことです。
        
    - **Azure Arc を使用せずに Windows マシンを接続する** - Azure Arc を使用せずに Windows マシンで実行されている SQL Server を接続する場合は、「[Windows コンピューターを Azure Monitor に接続する](../azure-monitor/agents/agent-windows.md)」の手順に従ってください。


### <a name="step-3-enable-the-optional-plan-in-defender-for-clouds-pricing-and-settings-page"></a>手順 3. [クラウドの料金と設定] ページで、Defender でオプションのプランを有効にします。

1. Defender for Cloud メニューで、 **環境設定** ページを開きます。

    - **Microsoft Defender for Cloudのデフォルトのワークスペース** として使用している場合(defaultworkspace-[your subscription ID]-[region]」というファイル）は、該当する **サブスクリプション** を選択します。

    - **既定以外のワークスペース** を使用している場合は、該当する **ワークスペース** を選択します (必要に応じて、フィルターにワークスペースの名前を入力します)。

        ![既定以外のワークスペースをタイトルで検索する。](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. [**端末上の SQL サーバー** プラン] の [Microsoft Defender] オプションを **[オン**] に設定します。 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="オプションのプランを含む [Defender for Cloud] 料金ページ。":::

    選択したワークスペースに接続されているすべての SQL サーバーでプランが有効になります。 この保護は、SQL Server インスタンスの最初の再起動後に完全にアクティブになります。

    >[!TIP] 
    > 新しいワークスペースを作成するには、[Log Analytics ワークスペースの作成](../azure-monitor/logs/quick-create-workspace.md)に関する記事の手順に従います。


1. 必要に応じて、セキュリティ アラートのメール通知を構成します。 

    受信者の一覧を設定して、Defender for Cloud alerts が生成されたときに電子メール通知を受け取ることができます。 この電子メールには、Microsoft Defender for Cloud のアラートへの直リンクが含まれており、すべての関連情報が記載されています。 詳細については、[セキュリティ アラートの電子メール通知のセットアップ](configure-email-notifications.md)に関する記事を参照してください。


## <a name="microsoft-defender-for-sql-alerts"></a>SQL アラート用 Microsoft Defender
普段は見られない、潜在的に有害な SQL マシンへのアクセスが試行されると、あるいは SQL マシンの悪用が試行されると、アラートが生成されます。 これらのイベントにより、[アラートのリファレンス ページ](alerts-reference.md#alerts-sql-db-and-warehouse)に記載されているアラートがトリガーされる場合があります。

## <a name="explore-and-investigate-security-alerts"></a>セキュリティ通知について調査する

Microsoft defender for SQL アラートは、クラウドの [アラート] ページ、コンピューターの [セキュリティ] ページ、[ワークロード保護ダッシュボード](workload-protections-dashboard.md)、またはアラートメールの直リンクを使用して、defender で利用できます。

1. アラートを表示するには、[クラウド] メニューの [Defender からの **セキュリティアラート** ] を選択し、アラートを選択します。

1. アラートは自己完結型であり、それぞれに詳細な修復の手順と調査情報が含まれています。 さらに詳しいビューを表示するには、他の Microsoft Defender for Cloud と Microsoft Sentinel の機能を使用して、さらに調査することができます。

    * 詳しく調べるには SQL Server の監査機能を有効にします。 Microsoft sentinel ユーザーの場合、Windows セキュリティログイベントから sentinel に SQL 監査ログをアップロードして、豊富な調査エクスペリエンスを享受できます。 [SQL Server 監査の詳細についてはこちらを参照してください](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15)。
    * セキュリティ体制を向上させるには、各アラートに示されているホスト端末に対するクラウドの推奨事項に Defender を使用します。 これにより、将来の攻撃のリスクが軽減されます。 

    [アラートの管理と応答の詳細についてはこちらを参照してください](managing-and-responding-alerts.md)。


## <a name="faq---microsoft-defender-for-sql-servers-on-machines"></a>FAQ：端末上の SQL サーバー用 Microsoft Defender

### <a name="if-i-enable-this-microsoft-defender-plan-on-my-subscription-are-all-sql-servers-on-the-subscription-protected"></a>サブスクリプションでこの Microsoft Defender プランを有効にした場合、サブスクリプションのすべての SQL サーバーは保護されますか? 

いいえ。 Azure の仮想端末、または Azure の Arc 対応端末で実行されている SQL Server で SQL Server デプロイを防御するために、クラウドの Defender には次のものが必要です。

- マシン上の Log Analytics エージェント
- 関連するLog AnalyticsワークスペースでMicrosoft Defender for SQLソリューションが有効になっていること。

Azure portal の SQL Server ページに表示されるサブスクリプションの "*状態*" は、既定のワークスペースの状態を反映し、接続されているすべてのマシンに適用されます。 そのワークスペースにレポートするLog Analyticsエージェントがあるホスト上のSQLサーバーのみがDefender for Cloudで保護されます。




## <a name="next-steps"></a>次のステップ

関連資料については、次の記事をご覧ください。

- [SQL Database と Azure Synapse Analytics のセキュリティ アラート](alerts-reference.md#alerts-sql-db-and-warehouse)
- [セキュリティ アラートのメール通知を設定する](configure-email-notifications.md)
- [Microsoft Sentinel の詳細情報](../sentinel/index.yml)
