---
title: "OMS の Agent Health ソリューション | Microsoft Docs"
description: "この記事の目的は、OMS または System Center Operations Manager に対して直接報告を行うエージェントの正常性を Agent Health ソリューションで監視する方法についてわかりやすく説明することです。"
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/22/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: ca2316010f9508b6dc71ceff434988695063db0f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---
<a id="agent-health-solution-in-oms" class="xliff"></a>

#  OMS の Agent Health ソリューション
OMS の Agent Health ソリューションを使用すると、OMS ワークスペースに対して、または OMS に接続された System Center Operations Manager 管理グループに対して直接報告を行うすべてのエージェントの中で、運用データを送信しているエージェントと応答していないエージェントを効率的に把握できます。  また、デプロイされているエージェントの数や地理的な分布を追跡できるほか、Azure を初めとする各種クラウド環境やオンプレミスにデプロイされているエージェントの分布を把握するためのその他のクエリを実行することができます。    

<a id="prerequisites" class="xliff"></a>

## 前提条件
このソリューションをデプロイする前に、現在サポートされている [Windows エージェント](../log-analytics/log-analytics-windows-agents.md)が、OMS ワークスペースに対して、または OMS ワークスペースに統合されている [Operations Manager 管理グループ](../log-analytics/log-analytics-om-agents.md)に対して報告を行っていることを確認してください。    

<a id="solution-components" class="xliff"></a>

## ソリューションのコンポーネント
このソリューションは次のリソースで構成されています。これらのリソースは、ワークスペースに追加され、エージェントに直接接続されるか、Operations Manager に接続された管理グループに接続されます。 

<a id="management-packs" class="xliff"></a>

### 管理パック
System Center Operations Manager 管理グループが OMS ワークスペースに接続されている場合は、以下の管理パックが Operations Manager にインストールされます。  これらの管理パックは、このソリューションを追加した後、直接接続された Windows コンピューターにもインストールされます。 管理パックに伴って構成や管理が必要となるものはありません。 

* Microsoft System Center Advisor HealthAssessment Direct Channel インテリジェンス パック (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel インテリジェンス パック (Microsoft.IntelligencePacks.HealthAssessmentViaServer)  

ソリューション管理パックの更新方法の詳細については、「 [Operations Manager を Log Analytics に接続する](../log-analytics/log-analytics-om-agents.md)」を参照してください。

<a id="configuration" class="xliff"></a>

## 構成
[ソリューションの追加](../log-analytics/log-analytics-add-solutions.md)に関するページの手順に従って、Agent Health ソリューションを OMS ワークスペースに追加します。 さらに手動で構成する必要はありません。


<a id="data-collection" class="xliff"></a>

## データ収集
<a id="supported-agents" class="xliff"></a>

### サポートされているエージェント
次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続されているソース | サポートの有無 | 説明 |
| --- | --- | --- |
| Windows エージェント | あり | ハートビート イベントは、直接の Windows エージェントから収集されます。|
| System Center Operations Manager 管理グループ | あり | ハートビート イベントは、管理グループに対して報告を行うエージェントから 60 秒ごとに収集されて Log Analytics に転送されます。 Operations Manager エージェントから Log Analytics への直接接続は必要ありません。 ハートビート イベント データは管理グループから Log Analytics リポジトリに転送されます。|

<a id="using-the-solution" class="xliff"></a>

## ソリューションの使用
OMS ワークスペースに Agent Health ソリューションを追加すると、OMS ダッシュボードに **[Agent Health]** タイルが追加されます。 このタイルには、エージェントの総数と直近 24 時間応答していないエージェントの数が表示されます。<br><br> ![ダッシュボードの Agent Health ソリューション タイル](./media/oms-solution-agenthealth/agenthealth-solution-tile-homepage.png)

**[Agent Health]** タイルをクリックすると、**[Agent Health]** ダッシュボードが開きます。  ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定の時間の範囲について、その列の基準に該当するイベント数の上位 10 件が表示されます。 ログ検索を実行してイベント全件を取得するには、各列の右下にある **[すべて表示]** を選択するか、列ヘッダーをクリックします。

| 分割 | Description | 
|--------|-------------|
| Agent count over time (時間の経過に伴うエージェント数) | 7 日間にわたるエージェント数の傾向。Linux エージェントと Windows エージェントの両方が対象となります。| 
| Count of unresponsive agents (応答しないエージェントの数) | 過去 24 時間ハートビートを送信していないエージェントの一覧。| 
| Distribution by OS Type (OS の種類ごとの分布) | 対象の環境に存在する Windows エージェントと Linux エージェントの区分。| 
| Distribution by Agent Version (エージェントのバージョンごとの分布) | 対象の環境にインストールされている各種エージェント バージョンの区分と各バージョンの数。| 
| Distribution by Agent Category (エージェントのカテゴリごとの分布) | ハートビート イベントを送信するエージェントの各カテゴリの区分 (ダイレクト エージェント、OpsMgr エージェント、または OpsMgr 管理サーバー)。| 
| Distribution by Management Group (管理グループごとの分布) | 対象の環境に存在する各種 SCOM 管理グループの区分。| 
| Geo-location of Agents (エージェントの地理的な位置) | エージェントが存在する国の区分と、それぞれの国でインストールされているエージェントの総数。| 
| Count of Gateways Installed (インストールされたゲートウェイの数) | OMS ゲートウェイがインストールされているサーバーの数とその一覧。|

![Agent Health ソリューション ダッシュボードの例](./media/oms-solution-agenthealth/agenthealth-solution-dashboard.png)  

<a id="log-analytics-records" class="xliff"></a>

## Log Analytics のレコード
このソリューションによって OMS リポジトリに作成されるレコードの種類は 1 つです。  

<a id="heartbeat-records" class="xliff"></a>

### ハートビート レコード
**Heartbeat** という種類のレコードが作成されます。  これらのレコードは、次の表に示したプロパティを持ちます。  

| プロパティ | 説明 |
| --- | --- |
| 型 | *Heartbeat*|
| カテゴリ | *Direct Agent*、*SCOM Agent*、*SCOM Management Server* のいずれかの値になります。| 
| コンピューター | コンピューター名。| 
| OSType | Windows または Linux オペレーティング システム。| 
| OSMajorVersion | オペレーティング システムのメジャー バージョン。| 
| OSMinorVersion | オペレーティング システムのマイナー バージョン。| 
| バージョン | OMS エージェントまたは Operations Manager エージェントのバージョン。| 
| SCAgentChannel | *Direct* と *SCManagementServer* のいずれかの値になります。| 
| IsGatewayInstalled | OMS ゲートウェイがインストールされている場合、値は *true* です。それ以外の場合は *false* になります。| 
| ComputerIP | コンピューターの IP アドレス。| 
| RemoteIPCountry | コンピューターがデプロイされている地理的位置。| 
| ManagementGroupName | Operations Manager 管理グループの名前。| 
| SourceComputerId | コンピューターの一意の ID。| 
| RemoteIPLongitude | コンピューターの地理的位置の経度。| 
| RemoteIPLatitude | コンピューターの地理的位置の緯度。| 

Operations Manager 管理サーバーに対して報告を行う各エージェントからは 2 つのハートビートが送信され、ご利用の OMS サブスクリプションで有効にした Log Analytics のデータ ソースとソリューションによっては、SCAgentChannel プロパティが **Direct** と **SCManagementServer** の両方の値を取ります。 ソリューションからのデータは、Operations Manager 管理サーバーから OMS Web サービスに直接送信される場合と、エージェントで収集されるデータのボリューム上、エージェントから OMS Web サービスに直接送信される場合とがあることを思い出してください。 この値が **SCManagementServer** であるハートビート イベントの場合、データは実質的に管理サーバーによってアップロードされるため、ComputerIP の値は、管理サーバーの IP アドレスになります。  SCAgentChannel が **Direct** に設定されているハートビートの場合は、エージェントのパブリック IP アドレスになります。  

<a id="sample-log-searches" class="xliff"></a>

## サンプル ログ検索
次の表は、このソリューションによって収集されたレコードを探すログ検索の例です。 

| クエリ | Description |
| --- | --- |
| Type=Heartbeat &#124; distinct Computer |エージェントの総数 | 
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-24HOURS |直近 24 時間応答がなかったエージェントの数 | 
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-15MINUTES |直近 15 分間応答がなかったエージェントの数 | 
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer IN {Type=Heartbeat TimeGenerated>NOW-24HOURS &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |(直近 24 時間) オンライン状態のコンピューター | 
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer NOT IN {Type=Heartbeat TimeGenerated>NOW-30MINUTES &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |(直近 24 時間のうち) 直近 30 分間オフライン状態であったエージェントの総数 | 
| Type=Heartbeat &#124; measure countdistinct(Computer) by OSType |時間の経過に伴うエージェント数の傾向を OSType ごとに取得| 
| Type=Heartbeat&#124;measure countdistinct(Computer) by OSType |OS の種類ごとの分布 | 
| Type=Heartbeat&#124;measure countdistinct(Computer) by Version |エージェントのバージョンごとの分布 | 
| Type=Heartbeat&#124;measure count() by Category |エージェントのカテゴリごとの分布 | 
| Type=Heartbeat&#124;measure countdistinct(Computer) by ManagementGroupName | 管理グループごとの分布 | 
| Type=Heartbeat&#124;measure countdistinct(Computer) by RemoteIPCountry |エージェントの地理的な位置 |
| Type=Heartbeat IsGatewayInstalled=true&#124;Distinct Computer |インストールされている OMS ゲートウェイの数 | 

  
<a id="next-steps" class="xliff"></a>

## 次のステップ

* Log Analytics におけるアラートの生成について詳しくは、 [Log Analytics のアラート](../log-analytics/log-analytics-alerts.md) に関するページを参照してください。
