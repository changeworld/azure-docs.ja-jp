---
title: "Logic Apps B2B の統合アカウントのディザスター リカバリー - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps B2B のディザスター リカバリー"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 0ebaa553f360cc7deb52139fe9adaaca39a2ee23
ms.lasthandoff: 04/13/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Logic Apps B2B のリージョン間ディザスター リカバリー
B2B のワークロードには、発注や請求などの金銭が関係するトランザクションが含まれます。  ビジネスでは、障害イベント時に迅速に復旧して、パートナーと合意したビジネス レベルの SLA を満たすことが重要です。  このトピックでは、B2B ワークロード向けのビジネス継続性計画の構築について説明します。  次の内容が含まれます。

* セカンダリ リージョンの統合アカウントの作成
* プライマリ リージョンからセカンダリ リージョンへの接続の確立 
* 障害イベント時のセカンダリ リージョンへのフェールオーバー
* 障害イベント後のプライマリ リージョンへの復帰

## <a name="create-an-integration-account-in-secondary-region"></a>セカンダリ リージョンの統合アカウントの作成
1. セカンダリ リージョンを選択し、[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を作成します。  

2. パートナー、スキーマ、および実行状態をセカンダリ リージョンの統合アカウントにレプリケートする必要がある必須のメッセージ フローの契約を追加します。

    > [!Tip]
    > リージョン間で統合アカウントのアーティファクトの命名規則に整合性があることを確認します。 
    > 
    > 

3. プライマリ リージョンのすべてのリソース (SQL Azure または DocumentDB データベースや、メッセージングに使用する Service Bus / Event Hubs、APIM、Logic Apps など) をセカンダリ リージョンにもデプロイします。  

## <a name="establish-a-connection-from-primary-to-secondary"></a>プライマリからセカンダリへの接続の確立 
プライマリ リージョンから実行状態を取得するには、セカンダリ リージョンにロジック アプリを作成します。  ロジック アプリには、**トリガー**と**アクション**が必要です。  トリガーはプライマリ リージョンの統合アカウントに接続し、アクションはセカンダリ リージョンの統合アカウントに接続する必要があります。  時間間隔に基づいて、トリガーはプライマリ リージョンのポーリングを実行し、実行状態テーブルは新しいレコードが存在すればそれを取得し、アクションはそのレコードでセカンダリ リージョンの統合アカウントを更新します。 これは、プライマリ リージョンからセカンダリ リージョンに増分ランタイム状態を取得するのに役立ちます。

Logic Apps 統合アカウントのビジネス継続性は、X12、AS2、EDIFACT の各 B2B プロトコルに基づいてサポートするために設計されています。  詳細な手順については、それぞれのリンクを選択してください。

* [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)
* [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)
* EDIFACT (近日対応予定)

## <a name="fail-over-to-secondary-region-during-a-disaster-event"></a>障害イベント時のセカンダリ リージョンへのフェールオーバー
障害イベント時に、プライマリ リージョンでビジネス継続性を確保できない場合は、セカンダリ リージョンにトラフィックをダイレクトします。 セカンダリ リージョンは、ビジネス機能を迅速に復旧して、パートナーと合意した復旧時間目標/復旧ポイント目標 (RPO/RTO) を達成するのに役立ちます。  また、リージョン間でフェールオーバーするための労力を最小限に抑えます。 

プライマリ リージョンからセカンダリ リージョンに制御番号をコピーする際、待ち時間が予想されます。  障害イベント時に、重複して生成された制御番号をパートナーに送信することを回避するには、[PowerShell コマンドレット](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)を使用して**セカンダリ リージョン契約**で制御番号を増やすことをお勧めします。

## <a name="fall-back-to-primary-region-post-disaster-event"></a>障害イベント後のプライマリ リージョンへの復帰
プライマリ リージョンが使用可能な場合、プライマリ リージョンに復帰するには次の手順に従います     
* **セカンダリ リージョン**でパートナーからのメッセージの受け入れを停止します   
* [PowerShell コマンドレット](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)を使用して、すべての**プライマリ リージョン契約**について、生成される制御番号を増やします   
* セカンダリ リージョンからプライマリ リージョンにトラフィックをダイレクトします   
* プライマリ リージョンから実行状態を取得するためにセカンダリ リージョンで作成したロジック アプリが有効になっていることを確認します    

## <a name="x12"></a>X12 
EDI X12 ドキュメントのビジネス継続性は、制御番号に基づいて設計されています   
* パートナーから受信した制御番号 (受信メッセージ)  
* 生成されてパートナーに送信された制御番号 (送信メッセージ)  
    
    > [!Tip]
    > [X12 クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/)を使用して Logic Apps を作成することもできます。  プライマリ統合アカウントとセカンダリ統合アカウントの作成は、テンプレートを使用するための前提条件です。  テンプレートは 2 つのロジック アプリを作成するのに役立ちます。1 つは受信した制御番号用、もう 1 つは生成された制御番号用です。  Logic Apps でそれぞれのトリガーとアクションが作成されます。トリガーはプライマリ統合アカウントに、アクションはセカンダリ統合アカウントに接続します。
    > 
    >

### <a name="control-numbers-received-from-the-partners"></a>パートナーから受信した制御番号
1. セカンダリ リージョンで[ロジック アプリ](../logic-apps/logic-apps-create-a-logic-app.md)を作成します   

2. **X12** を検索し、**[X12 - When a received control number is modified]**(X12 - 受信した制御番号を変更する場合) を選択します    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

3. トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 トリガーすると、プライマリ リージョンの統合アカウントに接続されます。  接続に名前を付け、リストから**プライマリ リージョンの統合アカウント**を選択して、[create](作成) をクリックします。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

4. 制御番号の同期を開始する DateTime は省略できます。  頻度は、**[Day]**(日)、**[Hour]**(時間)、**[Minute]**(分)、または **[Second]**(秒) の間隔に設定できます。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

5. **[New step]**(新しいステップ) と **[Add an action]**(アクションの追加) をクリックします    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

6. **X12** を検索し、**[X12 - Add or update a received control number]**(X12 - 受信した制御番号を追加または更新する) を選択します   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

7. アクションを実行すると、セカンダリ統合アカウントに接続されます。  **[Change connection]**(接続の変更) と **[Add new connection]**(新しい接続の追加) を選択すると、使用可能な統合アカウントが一覧表示されます。  接続に名前を付け、リストから**セカンダリ リージョンの統合アカウント**を選択して、[create](作成) をクリックします。     
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

8. 動的コンテンツを選択して、ロジック アプリを保存します   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

9. 時間間隔に基づいて、トリガーはプライマリ リージョンの受信済み制御番号テーブルのポーリングを実行して新しいレコードを取得し、アクションはそのレコードでセカンダリ リージョンの統合アカウントを更新します。  更新がない場合、トリガーの状態はスキップされて表示されます。
    
    > [!Tip]
    > 契約受信設定の重複チェックを有効にすると、受信済み制御番号のランタイム状態が保持され、一定の間隔でトリガーを起動するのに役立ちます
    > 
    >

![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)


### <a name="control-numbers-generated-and-send-to-the-partners"></a>生成されてパートナーに送信された制御番号
1. セカンダリ リージョンで[ロジック アプリ](../logic-apps/logic-apps-create-a-logic-app.md)を作成します  

2. **X12** を検索し、**[X12 - When a generated control number is modified]**(X12 - 生成された制御番号を変更する場合) を選択します   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 トリガーすると、プライマリ リージョンの統合アカウントに接続されます。  接続に名前を付け、リストから**プライマリ リージョンの統合アカウント**を選択して、[create](作成) をクリックします。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. 制御番号の同期を開始する DateTime は省略できます。  頻度は、**[Day]**(日)、**[Hour]**(時間)、**[Minute]**(分)、または **[Second]**(秒) の間隔に設定できます。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. **[New step]**(新しいステップ) と **[Add an action]**(アクションの追加) をクリックします   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. **X12** を検索し、**[X12 - Add or update a generated control number]**(X12 - 生成された制御番号を追加または更新する) を選択します   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. アクションを実行すると、セカンダリ統合アカウントに接続されます。  **[Change connection]**(接続の変更) と **[Add new connection]**(新しい接続の追加) を選択すると、使用可能な統合アカウントが一覧表示されます。  接続に名前を付け、リストから**セカンダリ リージョンの統合アカウント**を選択して、[create](作成) をクリックします。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. 動的コンテンツを選択して、ロジック アプリを保存します   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. 時間間隔に基づいて、トリガーはプライマリ リージョンの受信済み制御番号テーブルのポーリングを実行して新しいレコードを取得し、アクションはそのレコードでセカンダリ リージョンの統合アカウントを更新します。  更新がない場合、トリガーの状態はスキップされて表示されます。  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

時間間隔に基づいて、増分ランタイム状態がプライマリ リージョンからセカンダリ リージョンにレプリケートされます。  障害イベント時に、プライマリ リージョンが使用できない場合は、セカンダリ リージョンにトラフィックをダイレクトしてビジネス継続性を確保します。 

## <a name="as2"></a>AS2 
AS2 プロトコルを使用するドキュメントのビジネス継続性は、メッセージ ID と MIC 値に基づいて設計されています

> [!Tip]
    > [AS2 クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/pull/3302)を使用してロジック アプリを作成することもできます。  プライマリ統合アカウントとセカンダリ統合アカウントの作成は、テンプレートを使用するための前提条件です。  テンプレートは、ロジック アプリを作成してトリガーとアクションを設定するのに役立ちます。  トリガーからプライマリ統合アカウントへの接続と、セカンダリ統合アカウントへのアクションを作成します。
    > 
    >

1. セカンダリ リージョンで[ロジック アプリ](../logic-apps/logic-apps-create-a-logic-app.md)を作成します  

2. **AS2** を検索し、**[AS2 - When a MIC value is created]**(AS2 - MIC 値を作成する場合) を選択します   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 トリガーすると、プライマリ リージョンの統合アカウントに接続されます。  接続に名前を付け、リストから**プライマリ リージョンの統合アカウント**を選択して、[create](作成) をクリックします。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. MIC 値の同期を開始する DateTime は省略できます。  頻度は、**[Day]**(日)、**[Hour]**(時間)、**[Minute]**(分)、または **[Second]**(秒) の間隔に設定できます。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. **[New step]**(新しいステップ) と **[Add an action]**(アクションの追加) をクリックします   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. **AS2** を検索し、**[AS2 - Add or update a MIC]**(AS2 - MIC を追加または更新する) を選択します   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. アクションを実行すると、セカンダリ統合アカウントに接続されます。  **[Change connection]**(接続の変更) と **[Add new connection]**(新しい接続の追加) を選択すると、使用可能な統合アカウントが一覧表示されます。  接続に名前を付け、リストから**セカンダリ リージョンの統合アカウント**を選択して、[create](作成) をクリックします。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. 動的コンテンツを選択して、ロジック アプリを保存します   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. 時間間隔に基づいて、トリガーはプライマリ リージョン テーブルのポーリングを実行して新しいレコードを取得し、アクションはそのレコードでセカンダリ リージョンの統合アカウントを更新します。  更新がない場合、トリガーの状態はスキップされて表示されます。  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

時間間隔に基づいて、増分ランタイム状態がプライマリ リージョンからセカンダリ リージョンにレプリケートされます。  障害イベント時に、プライマリ リージョンが使用できない場合は、セカンダリ リージョンにトラフィックをダイレクトしてビジネス継続性を確保します。 

## <a name="next-steps"></a>次のステップ
* [B2B メッセージの監視方法](logic-apps-monitor-b2b-message.md)についての詳細情報。   
