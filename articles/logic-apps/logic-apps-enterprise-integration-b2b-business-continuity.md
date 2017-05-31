---
title: "Logic Apps B2B の統合アカウントのディザスター リカバリー: Azure App Service | Microsoft Docs"
description: "Logic Apps B2B のディザスター リカバリー"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 197df490690754730425231f358fde31d17dcfad
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Logic Apps B2B のリージョン間ディザスター リカバリー
B2B のワークロードには、発注や請求などの金銭が関係するトランザクションが含まれます。 ビジネスでは、障害イベント時に迅速に復旧して、パートナーと合意したビジネス レベルの SLA を満たすことが重要です。 この記事では、B2B ワークロード向けのビジネス継続性計画の構築方法について説明します。 

* ディザスター リカバリーの準備 
* 障害イベント時のセカンダリ リージョンへのフェールオーバー 
* 障害イベント後のプライマリ リージョンへの復帰

## <a name="disaster-recovery-readiness"></a>ディザスター リカバリーの準備  

1. セカンダリ リージョンを指定し、セカンダリ リージョンに[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を作成します。 

2. パートナー、スキーマ、および実行状態をセカンダリ リージョンの統合アカウントにレプリケートする必要がある必須のメッセージ フローの契約を追加します。

    > [!Tip]
    > リージョン間で統合アカウントのアーティファクトの命名規則に整合性があることを確認します。 
    > 
    > 

3. プライマリ リージョンから実行状態を取得するには、セカンダリ リージョンにロジック アプリを作成します。  ロジック アプリには、**トリガー**と**アクション**が必要です。  トリガーはプライマリ リージョンの統合アカウントに接続し、アクションはセカンダリ リージョンの統合アカウントに接続する必要があります。  時間間隔に基づいて、トリガーはプライマリ リージョンのポーリングを実行し、実行状態テーブルは新しいレコードが存在すればそれを取得し、アクションはそのレコードでセカンダリ リージョンの統合アカウントを更新します。 これは、プライマリ リージョンからセカンダリ リージョンに増分ランタイム状態を取得するのに役立ちます。

4. Logic Apps 統合アカウントのビジネス継続性は、X12、AS2、EDIFACT の各 B2B プロトコルに基づいてサポートするために設計されています。  詳細な手順については、それぞれのリンクを選択してください。

5. プライマリ リージョンのすべてのリソースを、セカンダリ リージョンにもデプロイすることをお勧めします。 プライマリ リージョンのリソースには、Azure SQL Database や Azure Cosmos DB、メッセージングに使用する Azure Service Bus/Azure Event Hubs、Azure API Management、Azure App Service の Logic Apps 機能などが含まれています。   

6. プライマリ リージョンからセカンダリ リージョンへの接続を確立します。 プライマリ リージョンから実行状態を取得するには、セカンダリ リージョンにロジック アプリを作成します。 ロジック アプリには、トリガーとアクションが必要です。 トリガーは、プライマリ リージョンの統合アカウントに接続される必要があります。 アクションは、セカンダリ リージョンの統合アカウントに接続される必要があります。 時間間隔に基づいて、トリガーはプライマリ リージョン実行状態テーブルのポーリングを実行し、新しいレコードが存在すればそれを取得します。 アクションはセカンダリ リージョンの統合アカウントのテーブルを更新します。 このプロセスは、プライマリ リージョンからセカンダリ リージョンに増分ランタイム状態を取得するのに役立ちます。

Logic Apps 統合アカウントのビジネス継続性は、X12、AS2、EDIFACT の各 B2B プロトコルに基づいてサポートを提供します。 X12 と AS2 を使用する手順について詳しくは、この記事の「[X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)」と「[AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)」をご覧ください。

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>障害イベント時のセカンダリ リージョンへのフェールオーバー
障害イベント時に、プライマリ リージョンでビジネス継続性を確保できない場合は、セカンダリ リージョンにトラフィックをダイレクトします。 セカンダリ リージョンは、パートナーと合意した RPO/RTO を満たすため、迅速にビジネスに必要な機能を復旧するのに役立ちます。 また、リージョン間でフェールオーバーするための労力を最小限に抑えます。 

プライマリ リージョンからセカンダリ リージョンに制御番号をコピーする際に、待ち時間が予想されます。 障害イベント時に、重複して生成された制御番号をパートナーに送信することを回避するには、[PowerShell コマンドレット](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)を使用してセカンダリ リージョン契約で制御番号を増やすことをお勧めします。

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>障害イベント後のプライマリ リージョンへの復帰
使用可能な場合にプライマリ リージョンへ復帰するには、次の手順に従います。

1. セカンダリ リージョンでパートナーからのメッセージの受け入れを停止します。  

2. [PowerShell コマンドレット](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)を使用して、すべてのプライマリ リージョン契約について、生成される制御番号を増やします。  

3. セカンダリ リージョンからプライマリ リージョンにトラフィックをダイレクトします。

4. プライマリ リージョンから実行状態を取得するためにセカンダリ リージョンで作成したロジック アプリが有効になっていることを確認します。

## <a name="x12"></a>X12 
EDI X12 ドキュメントのビジネス継続性は、制御番号に基づいています。
* パートナーから受信した制御番号 (受信メッセージ)  
* 生成されてパートナーに送信された制御番号 (送信メッセージ) 
    
    > [!Tip]
    > [X12 クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/)を使用してロジック アプリを作成することもできます。 プライマリ統合アカウントとセカンダリ統合アカウントの作成は、テンプレートを使用するための前提条件です。 テンプレートは 2 つのロジック アプリを作成するのに役立ちます。1 つは受信した制御番号用、もう 1 つは生成された制御番号用です。 ロジック アプリでそれぞれのトリガーとアクションが作成されます。トリガーはプライマリ統合アカウントに、アクションはセカンダリ統合アカウントに接続します。
    > 
    >

### <a name="control-numbers-received-from-partners"></a>パートナーから受信した制御番号

1. 契約受信設定の重複チェックを有効にします。   
![X12 を検索](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

2. セカンダリ リージョンに[ロジック アプリ](../logic-apps/logic-apps-create-a-logic-app.md)を作成します。 

3. **X12** を検索し、**[X12 - 受信した制御番号が変更されたとき]** を選択します。   
![X12 を検索](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

4. トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 トリガーは、プライマリ リージョンの統合アカウントに接続される必要があります。 接続名を入力し、リストから**プライマリ リージョンの統合アカウント**を選択して、**[作成]** をクリックします。  
![プライマリ リージョンの統合アカウント名](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

5. **制御番号の同期を開始する DateTime** 設定は省略できます。 **頻度**は、**[日]**、**[時間]**、**[分]**、または **[秒]** の間隔に設定できます。  
![DateTime と頻度](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

6. **[新しいステップ]** > **[アクションの追加]** の順に選択します。    
![[アクションの追加]](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

7. **X12** を検索し、**[X12 - 受信した制御番号を追加または更新します]** を選択します。   
![受信した制御番号の変更](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

8. アクションをセカンダリ リージョン統合アカウントに接続するには、**[接続の変更]** > **[新しい接続の追加]** の順に選択し、使用可能な統合アカウントのリストを表示します。 接続名を入力し、リストから**セカンダリ リージョンの統合アカウント**を選択して、**[作成]** をクリックします。   
![セカンダリ リージョンの統合アカウント名](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

9. 動的コンテンツを選択して、ロジック アプリを保存します。 
![動的コンテンツ](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

10. 時間間隔に基づいて、トリガーはプライマリ リージョンで受信した制御番号テーブルのポーリングを実行し、新しいレコードを取得します。 アクションはセカンダリ リージョンの統合アカウントのレコードを更新します。 更新がない場合、トリガーの状態は **[スキップ済み]** と表示されます。
![制御番号テーブル](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)

### <a name="control-numbers-generated-and-sent-to-partners"></a>生成されてパートナーに送信された制御番号
1. セカンダリ リージョンに[ロジック アプリ](../logic-apps/logic-apps-create-a-logic-app.md)を作成します。

2. **X12** を検索し、**[X12 - 生成された制御番号が変更されたとき]** を選択します。  
![生成された制御番号の変更](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 トリガーは、プライマリ リージョンの統合アカウントに接続される必要があります。 接続名を入力し、リストから**プライマリ リージョンの統合アカウント**を選択して、**[作成]** をクリックします。   
![プライマリ リージョンの統合アカウント名](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. **制御番号の同期を開始する DateTime** 設定は省略できます。 **頻度**は、**[日]**、**[時間]**、**[分]**、または **[秒]** の間隔に設定できます。  
![DateTime と頻度](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. **[新しいステップ]** > **[アクションの追加]** の順に選択します。  
![[アクションの追加]](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. **X12** を検索し、**[X12 - 生成された制御番号を追加または更新します]** を選択します。   
![生成された制御番号の追加または変更](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. アクションをセカンダリ リージョン統合アカウントに接続するには、**[接続の変更]** > **[新しい接続の追加]** の順に選択し、使用可能な統合アカウントのリストを表示します。 接続名を入力し、リストから**セカンダリ リージョンの統合アカウント**を選択して、**[作成]** をクリックします。   
![セカンダリ リージョンの統合アカウント名](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. 動的コンテンツを選択して、ロジック アプリを保存します。 
![動的コンテンツ](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. 時間間隔に基づいて、トリガーはプライマリ リージョンで受信した制御番号テーブルのポーリングを実行し、新しいレコードを取得します。 アクションはセカンダリ リージョンの統合アカウントのレコードを更新します。 更新がない場合、トリガーの状態は **[スキップ済み]** と表示されます。  
![制御番号テーブル](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

時間間隔に基づいて、増分ランタイム状態がプライマリ リージョンからセカンダリ リージョンにレプリケートされます。 障害イベント時に、プライマリ リージョンが使用できない場合は、セカンダリ リージョンにトラフィックをダイレクトしてビジネス継続性を確保します。 

## <a name="as2"></a>AS2 
AS2 プロトコルを使用するドキュメントのビジネス継続性は、メッセージ ID と MIC 値に基づいています。

> [!Tip]
    > [AS2 クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/pull/3302)を使用してロジック アプリを作成することもできます。 プライマリ統合アカウントとセカンダリ統合アカウントの作成は、テンプレートを使用するための前提条件です。 テンプレートは、トリガーとアクションをもつロジック アプリを作成するのに役立ちます。 このロジック アプリは、トリガーからプライマリ統合アカウントへの接続と、アクションからセカンダリ統合アカウントへの接続を作成します。
    > 
    >

1. セカンダリ リージョンで[ロジック アプリ](../logic-apps/logic-apps-create-a-logic-app.md)を作成します。  

2. **AS2** を検索し、**[AS2 - When a MIC value is created] (AS2 - MIC 値を作成する場合)** を選択します。   
![AS2 を検索](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 トリガーは、プライマリ リージョンの統合アカウントに接続される必要があります。 接続名を入力し、リストから**プライマリ リージョンの統合アカウント**を選択して、**[作成]** をクリックします。   
![プライマリ リージョンの統合アカウント名](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. **MIC 値の同期を開始する DateTime** 設定は省略できます。 **頻度**は、**[日]**、**[時間]**、**[分]**、または **[秒]** の間隔に設定できます。   
![DateTime と頻度](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. **[新しいステップ]** > **[アクションの追加]** の順に選択します。  
![[アクションの追加]](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. **AS2** を検索し、**[AS2 - Add or update a MIC] (AS2 - MIC を追加または更新する)** を選択します。  
![MIC の追加または変更](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. アクションをセカンダリ リージョン統合アカウントに接続するには、**[接続の変更]** > **[新しい接続の追加]** の順に選択し、使用可能な統合アカウントのリストを表示します。 接続名を入力し、リストから**セカンダリ リージョンの統合アカウント**を選択して、**[作成]** をクリックします。    
![セカンダリ リージョンの統合アカウント名](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. 動的コンテンツを選択して、ロジック アプリを保存します。   
![動的コンテンツ](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. 時間間隔に基づいて、トリガーはプライマリ リージョン テーブルのポーリングを実行し、新しいレコードを取得します。 アクションはセカンダリ リージョンの統合アカウントのレコードを更新します。 更新がない場合、トリガーの状態は **[スキップ済み]** と表示されます。  
![プライマリ リージョン テーブル](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

時間間隔に基づいて、増分ランタイム状態がプライマリ リージョンからセカンダリ リージョンにレプリケートされます。 障害イベント時に、プライマリ リージョンが使用できない場合は、セカンダリ リージョンにトラフィックをダイレクトしてビジネス継続性を確保します。 

## <a name="next-steps"></a>次のステップ
[B2B メッセージの監視方法](logic-apps-monitor-b2b-message.md)についての詳細情報。   


