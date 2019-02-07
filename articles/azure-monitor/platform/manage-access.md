---
title: Azure Log Analytics と OMS ポータルでワークスペースを管理する |Microsoft ドキュメント
description: ユーザー、アカウント、ワークスペース、Azure アカウントにさまざまな管理タスクを実行して、Azure Log Analytics と OMS ポータルでワークスペースを管理できます。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 32a31a87bacbb13cd3b2cb4561ac04e54d51ba46
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656755"
---
# <a name="manage-workspaces"></a>ワークスペースを管理する

Log Analytics へのアクセスを管理するには、ワークスペースに関するさまざまな管理タスクを実行する必要があります。 この記事では、ワークスペースを管理するためのアドバイスと手順について説明します。 ワークスペースは基本的にはアカウント情報とアカウントの単純な構成情報が含まれるコンテナーです。 組織のメンバーは、複数のワークスペースを使用して、IT インフラストラクチャの一部またはすべてから収集されるデータのさまざまなセットを管理する場合があります。

ワークスペースを作成するには、次のことを実行する必要があります。

1. Azure サブスクリプションを取得する。
2. ワークスペース名を選択する。
3. ワークスペースをサブスクリプションおよびリソース グループのいずれかに関連付ける。
4. 地理的な場所を選択する。

## <a name="determine-the-number-of-workspaces-you-need"></a>必要なワークスペースの数を決定する
ワークスペースは、Azure のリソースであり、収集、集計、分析され、Azure Portal に表示されるデータのコンテナーです。

Azure サブスクリプションごとに複数のワークスペースを用意できるほか、複数のワークスペースにアクセスして、容易にクエリを実行できます。 このセクションでは、複数のワークスペースを作成すると便利な状況について説明します。

現時点では、ワークスペースに次の情報が示されます。

* データ ストレージの地理的な場所
* 異なるユーザー アクセス権を定義するためのデータの分離
* [価格レベル](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)、[リテンション期間](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)、[データ キャッピング](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)などの設定の構成のスコープ 

消費の観点から、作成するワークスペースの数はできるだけ少なくすることをお勧めします。 これにより、管理とクエリのエクスペリエンスがより簡単かつ迅速になります。 ただし、上記の特性に基づき、次の条件に当てはまる場合は複数のワークスペースを作成してみてください。

* 世界規模の企業が、データ主権またはコンプライアンス上の理由から特定のリージョンにデータを格納する必要がある。
* Azure を使用しているときに、管理対象の Azure リソースと同じリージョンにワークスペースを配置することによって送信データ転送の料金が生じるのを回避したい。
* 独自の Azure サブスクリプション内の部門またはビジネス グループごとにワークスペースを作成することで、請求金額を使用量に基づいて異なる部門またはビジネス グループに割り当てたい。
* マネージド サービス プロバイダーが、管理する各顧客の Log Analytics データを他の顧客のデータから切り離しておく必要がある。
* 管理している複数の顧客、部門、ビジネス グループが (他の顧客、部門、ビジネス グループではなく) 各自のデータを確認できるようにしたい。

Windows エージェントを使用してデータを収集する場合は、[1 つ以上のワークスペースにレポートを生成するように各エージェントを構成](../../azure-monitor/platform/agent-windows.md)できます。

System Center Operations Manager を使用している場合、各 Operations Manager 管理グループを 1 つのワークスペースのみに接続できます。 Operations Manager を使って管理するコンピューターに Microsoft Monitoring Agent をインストールし、Operations Manager と別の Log Analytics ワークスペースの両方にレポートを生成するようにエージェントを構成できます。

## <a name="workspace-information"></a>ワークスペース情報

ワークスペースに関する詳細情報は Azure Portal で表示できます。 

1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com)にサインインします。

2. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。  

    ![Azure ポータル](media/manage-access/azure-portal-01.png)  

3. Log Analytics サブスクリプション ウィンドウで、ワークスペースを選択します。

4. ワークスペースのページに、作業開始と構成に関する詳細と、その他の情報へのリンクが表示されます。  

    ![ワークスペースの詳細](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>アカウントとユーザーの管理
各ワークスペースには、複数のアカウントを関連付けることができます。また、各アカウントは、複数のワークスペースにアクセスできます。 アクセスは、[Azure のロールベースのアクセス](../../role-based-access-control/role-assignments-portal.md)によって管理されます。 これらのアクセス権は、Azure portal と API アクセスの両方に適用されます。


次のアクティビティにも、Azure のアクセス許可が必要です。

| Action                                                          | 必要とされる Azure のアクセス許可 | メモ |
|-----------------------------------------------------------------|--------------------------|-------|
| 管理ソリューションの追加と削除                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | これらのアクセス許可は、リソース グループまたはサブスクリプション レベルで付与する必要があります。 |
| 価格レベルの変更                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| *Backup* ソリューション タイルと *Site Recovery* ソリューション タイルのデータの表示 | 管理者/共同管理者 | クラシック デプロイ モデルを使用してデプロイされたリソースにアクセスします |
| Azure Portal でのワークスペースの作成                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Azure のアクセス許可を使用した Log Analytics へのアクセスの管理
Azure のアクセス許可を使用して Log Analytics ワークスペースへのアクセス権を付与するには、「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../../role-based-access-control/role-assignments-portal.md)」の手順に従ってください。

Azure には、Log Analytics 用に、次の 2 つの組み込みユーザー ロールがあります。
- Log Analytics 閲覧者
- Log Analytics 共同作成者

"*Log Analytics 閲覧者*" ロールのメンバーは、以下の操作を行うことができます。
- すべての監視データを表示および検索する 
- 監視設定を表示する。これには、すべての Azure リソースに対する Azure 診断の構成の表示などが含まれます。

Log Analytics 閲覧者ロールには、次の Azure アクションが含まれています。

| type    | アクセス許可 | 説明 |
| ------- | ---------- | ----------- |
| Action | `*/read`   | すべての Azure リソースとリソース構成を表示する機能。 次のものを表示できます。 <br> 仮想マシン拡張機能の状態 <br> リソースに対する Azure 診断の構成 <br> すべてのリソースのすべてのプロパティと設定 |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | ログ検索 v2 クエリを実行する機能 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | ログ検索 v1 クエリを実行する機能 |
| Action | `Microsoft.Support/*` | サポート ケースを開く機能 |
|非アクション | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | データ コレクション API の使用とエージェントのインストールに必要なワークスペース キーの読み取りを防ぎます。 これにより、ユーザーは新しいリソースをワークスペースに追加できなくなります |


"*Log Analytics 共同作成者*" ロールのメンバーは、以下の操作を行うことができます。
- すべての監視データの読み取り (Log Analytics 閲覧者と同様)  
- Automation アカウントの作成と構成  
- 管理ソリューションの追加と削除    
    > [!NOTE] 
    > 最後の 2 つのアクションを正常に実行するには、このアクセス許可をリソース グループまたはサブスクリプション レベルで付与する必要があります。  

- ストレージ アカウント キーの読み取り   
- Azure Storage からのログの収集の構成  
- 次のような、Azure リソースの監視設定の編集
  - VM への VM 拡張機能の追加
  - すべての Azure リソースに対する Azure 診断の構成

> [!NOTE] 
> 仮想マシンに仮想マシン拡張機能を追加する機能を使用して、仮想マシンに対するフル コントロールを取得することができます。

Log Analytics 共同作成者ロールには、次の Azure アクションが含まれています。

| アクセス許可 | 説明 |
| ---------- | ----------- |
| `*/read`     | すべてのリソースとリソース構成を表示する機能。 次のものを表示できます。 <br> 仮想マシン拡張機能の状態 <br> リソースに対する Azure 診断の構成 <br> すべてのリソースのすべてのプロパティと設定 |
| `Microsoft.Automation/automationAccounts/*` | Runbook の追加と編集など、Azure Automation アカウントを作成および構成する機能 |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Microsoft Monitoring Agent 拡張機能、OMS Agent for Linux 拡張機能など、仮想マシン拡張機能を追加、更新、および削除する |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | ストレージ アカウント キーを表示する。 Azure Storage アカウントからログを読み取るように Log Analytics を構成するために必要です |
| `Microsoft.Insights/alertRules/*` | アラート ルールを追加、更新、および削除する |
| `Microsoft.Insights/diagnosticSettings/*` | Azure リソースに対する診断設定を追加、更新、および削除する |
| `Microsoft.OperationalInsights/*` | Log Analytics ワークスペースの構成を追加、更新、および削除する |
| `Microsoft.OperationsManagement/*` | 管理ソリューションを追加および削除する |
| `Microsoft.Resources/deployments/*` | デプロイを作成および削除する。 ソリューション、ワークスペース、および Automation アカウントを追加および削除するために必要です |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | デプロイを作成および削除する。 ソリューション、ワークスペース、および Automation アカウントを追加および削除するために必要です |

ユーザー ロールに対してユーザーの追加と削除を行うには、`Microsoft.Authorization/*/Delete` および `Microsoft.Authorization/*/Write` アクセス許可が必要です。

これらのロールを使用すると、次のようなさまざまなスコープでユーザーにアクセスを許可することができます。
- サブスクリプション - サブスクリプション内のすべてのワークスペースへのアクセス
- リソース グループ - リソース グループ内のすべてのワークスペースへのアクセス
- リソース - 指定されたワークスペースのみへのアクセス

正確なアクセス制御を行うために、割り当てをリソース レベル (ワークスペース) で実行することをお勧めします。  必要な特定のアクセス許可を持つロールを作成するには、[カスタム ロール](../../role-based-access-control/custom-roles.md)を使用します。

## <a name="next-steps"></a>次の手順
* [Log Analytics エージェントの概要](../../azure-monitor/platform/log-analytics-agent.md)に関するページを参照して、データセンターや他のクラウド環境内のコンピューターからデータを収集します。
* 「[Azure Virtual Machines に関するデータの収集](../../azure-monitor/learn/quick-collect-azurevm.md)」を参照して、Azure VM からのデータ コレクションを構成します。  
* [Add Log Analytics solutions from the Solutions Gallery](../../azure-monitor/insights/solutions.md) 」 (ソリューションギャラリーから Log Analytics ソリューションを追加する) を参照してください。

