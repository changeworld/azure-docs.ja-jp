---
title: Azure Monitor における Agent Health ソリューション | Microsoft Docs
description: Log Analytics または System Center Operations Manager に直接報告するエージェントの正常性を、このソリューションを使用して監視する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 33fd14f71e0ef441fc2b33765f7275fc33448e0b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663442"
---
# <a name="agent-health-solution-in-azure-monitor"></a>Azure Monitor での Agent Health ソリューション
Azure の Agent Health ソリューションは、応答していない、およびオペレーショナル データを送信している監視エージェントを把握するのに役立ちます。 それには、Azure Monitor の Log Analytics ワークスペース、または Azure Monitor に接続されている System Center Operations Manager 管理グループに直接報告する、すべてのエージェントが含まれます。

また、Agent Health ソリューションを使用すると次のことができます。

* 展開されているエージェントの数と、それらが分散配置されている地理的な場所を追跡します。
* 他のクエリを実行して、Azure 内、他のクラウド環境、またはオンプレミスに展開されているエージェントの分散の把握を維持します。    

## <a name="prerequisites"></a>前提条件
このソリューションを展開する前に、Log Analytics ワークスペースまたはワークスペースに統合されている [Operations Manager 管理グループ](../agents/om-agents.md)に報告を行っている、サポートされる [Windows エージェント](../agents/agent-windows.md)があることを確認します。

## <a name="management-packs"></a>管理パック
Operations Manager 管理グループが Log Analytics ワークスペースに接続されている場合は、以下の管理パックが Operations Manager にインストールされます。 これらの管理パックは、このソリューションを追加した後、直接接続された Windows コンピューターにもインストールされます。

* Microsoft System Center Advisor HealthAssessment Direct Channel インテリジェンス パック (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel インテリジェンス パック (Microsoft.IntelligencePacks.HealthAssessmentViaServer)  

これらの管理パックに関して構成や管理が必要なものはありません。 ソリューション管理パックの更新方法の詳細については、「 [Operations Manager を Log Analytics に接続する](../agents/om-agents.md)」を参照してください。

## <a name="configuration"></a>構成
[ソリューションの追加](solutions.md)に関するページの手順に従って、Agent Health ソリューションを Log Analytics ワークスペースに追加します。 それ以上の構成は必要ありません。

## <a name="supported-agents"></a>サポートされているエージェント
次の表では、このソリューションでサポートされている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| Windows エージェント | はい | ハートビート イベントは、直接の Windows エージェントから収集されます。|
| System Center Operations Manager 管理グループ | はい | ハートビート イベントは、管理グループに対して報告を行うエージェントから 60 秒ごとに収集されて、Azure Monitor に転送されます。 Operations Manager エージェントから Azure Monitor への直接接続は必要ありません。 ハートビート イベント データは管理グループから Log Analytics ワークスペースに転送されます。|

## <a name="using-the-solution"></a>ソリューションの使用
Log Analytics ワークスペースに Agent Health ソリューションを追加すると、ダッシュボードに **[Agent Health]** タイルが追加されます。 このタイルには、エージェントの総数と直近 24 時間応答していないエージェントの数が表示されます。

![ダッシュボードの [Agent Health] タイルを示すスクリーンショット。](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

**[Agent Health]** タイルを選択すると、 **[Agent Health]** ダッシュボードが開きます。  ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定の時間の範囲について、その列の基準に該当するイベント数の上位 10 件が表示されます。 各列の下にある **[すべて表示]** を選択するか、列見出しを選択することで、一覧全件を提供するログ検索を実行できます。

| 列 | 説明 |
|--------|-------------|
| Agent count over time (時間の経過に伴うエージェント数) | Linux と Windows 両方のエージェントについての、7 日間のエージェント数の傾向|
| Count of unresponsive agents (応答しないエージェントの数) | 過去 24 時間ハートビートを送信していないエージェントの一覧|
| OS の種類ごとの分布 | 対象の環境に存在する Windows エージェントと Linux エージェントの区分|
| エージェントのバージョンごとの分布 | 環境にインストールされているエージェント バージョンの区分とそれぞれの数|
| エージェントのカテゴリごとの分布 | ハートビート イベントを送信しているエージェントのカテゴリの区分 (ダイレクト エージェント、Operations Manager エージェント、または Operations Manager 管理サーバー)|
| 管理グループごとの分布 | 環境に存在する Operations Manager 管理グループの区分|
| エージェントの地理的な位置 | エージェントが存在する国や地域の区分と、それぞれの国や地域でインストールされているエージェントの総数|
| インストールされたゲートウェイの数 | Log Analytics ゲートウェイがインストールされているサーバーの数とその一覧|

![Agent Health ソリューション ダッシュボードの例を示すスクリーンショット。](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor のログ レコード
このソリューションによって Log Analytics ワークスペースに作成される 1 つのレコードの種類は、ハートビートです。 ハートビート レコードには、次の表に示すプロパティがあります。  

| プロパティ | 説明 |
| --- | --- |
| `Type` | `Heartbeat`|
| `Category` | `Direct Agent`、 `SCOM Agent`、または `SCOM Management Server`|
| `Computer` | コンピューター名|
| `OSType` | Windows または Linux オペレーティング システム|
| `OSMajorVersion` | オペレーティング システムのメジャー バージョン|
| `OSMinorVersion` | オペレーティング システムのマイナー バージョン|
| `Version` | Log Analytics エージェントまたは Operations Manager エージェントのバージョン|
| `SCAgentChannel` | `Direct` と `SCManagementServer` の一方または両方|
| `IsGatewayInstalled` | Log Analytics ゲートウェイがインストールされている場合は `true`、それ以外の場合 `false`|
| `ComputerIP` | Azure 仮想マシンの場合はパブリック IP アドレス (使用可能な場合)。プライベート IP を使用する仮想マシンの場合は Azure SNAT アドレス (プライベート IP アドレスではありません) |
| `ComputerPrivateIPs` | コンピューターのプライベート IP の一覧 |
| `RemoteIPCountry` | コンピューターがデプロイされている地理的位置|
| `ManagementGroupName` | Operations Manager 管理グループの名前|
| `SourceComputerId` | コンピューターの一意の ID|
| `RemoteIPLongitude` | コンピューターの地理的位置の経度|
| `RemoteIPLatitude` | コンピューターの地理的位置の緯度|

Operations Manager 管理サーバーに報告する各エージェントからは、2 つのハートビートが送信されます。 サブスクリプションで有効にしたデータ ソースと監視ソリューションに応じて、`SCAgentChannel` プロパティの値には `Direct` と `SCManagementServer` の両方が含まれます。 

前に説明したように、ソリューションからのデータの送信は次のようになります。

* Operations Manager 管理サーバーから Azure Monitor に直接
* エージェントで収集されたデータの量のため、エージェントから Azure Monitor に直接

値が `SCManagementServer` であるハートビート イベントの場合、実際にはデータがアップロードされるため、`ComputerIP` の値は管理サーバーの IP アドレスです。 `SCAgentChannel` が `Direct` に設定されているハートビートの場合は、エージェントのパブリック IP アドレスです。  

## <a name="sample-log-searches"></a>サンプル ログ検索
次の表は、ソリューションによって収集されるレコードのログ検索の例です。

| クエリ | 説明 |
|:---|:---|
| Heartbeat &#124; distinct Computer |エージェントの総数 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |直近 24 時間応答がなかったエージェントの数 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |直近 15 分間応答がなかったエージェントの数 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |過去 24 時間にオンラインになったコンピューターの数 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |直近 30 分間にオフラインになったエージェントの総数 (過去 24 時間) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |OS の種類ごとの、経時的なエージェント数の傾向|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |OS の種類ごとの分布 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |エージェントのバージョンごとの分布 |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |エージェントのカテゴリごとの分布 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | 管理グループごとの分布 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |エージェントの地理的な位置 |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |インストールされている Log Analytics ゲートウェイの数 |

## <a name="next-steps"></a>次のステップ

* [Azure Monitor でのログ クエリからのアラートの生成](../alerts/alerts-overview.md)について学習します。 

