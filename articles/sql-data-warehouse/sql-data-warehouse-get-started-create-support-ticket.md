---
title: "SQL Data Warehouse のサポート チケットを作成する方法 |Microsoft ドキュメント"
description: "Azure SQL Data Warehouse でサポート チケットを作成する方法です。"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: 058ff1229acee5d03db7c0305c5565ae95a85758
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>SQL Data Warehouse のサポート チケットを作成する方法
SQL Data Warehouse では、問題がある場合は、エンジニア リング チームが支援するようににしてください。 サポート チケットを作成します。

> [!NOTE] 
> 2016、12 月 20 日の時点では、Azure ポータルでリソース正常性チェックは正確なではありません。 この問題を解決に取り組んでおります。 


## <a name="create-a-support-ticket"></a>サポート チケットを作成する
1. 開く、 [Azure ポータル][Azure portal]です。
2. ホーム画面で、をクリックして、**ヘルプとサポート**を並べて表示します。
   
    ![ヘルプとサポート](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. ヘルプ + サポート ブレードで、をクリックして**作成サポート要求**です。
   
    ![新しいサポート要求](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. 選択、**要求の種類**です。
   
    ![要求の種類](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > 各 SQL server (例: myserver.database.windows.net) は、既定では、 **DTU クォータ**45,000 のです。 このクォータは、安全性に制限するだけです。 サポート チケットを作成して選択すると、、クォータを増やすことができます*クォータ*要求の種類として。 DTU ニーズを計算するには、合計、7.5 を掛けます[DWU] [ DWU]が必要です。 たとえば、1 つの SQL server 上の 2 つの DW6000s をホストするには、90,000 の DTU クォータを要求する必要があります。  ポータルでは、SQL サーバー ブレードから現在の DTU 使用量を表示できます。 一時停止および一時停止されていないデータベースの両方の DTU クォータに対してカウントします。 
   > 
   > 
5. 選択、**サブスクリプション**をレポートする問題のあるデータベースをホストします。
   
    ![Subscription](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. 選択**SQL Data Warehouse**リソースとします。
   
    ![リソース](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. 選択、 [Azure サポート プラン][Azure support plan]です。
   
   * **課金、クォータとサブスクリプションの管理**サポートは、すべてのサポート レベルで利用できます。
   * **解決**を介してサポートが提供される[開発者][Developer]、[標準][Standard]、 [Professional Direct] [ Professional Direct]または[プレミア][ Premier]をサポートします。 問題解決の問題は、お客様が Azure を使用しているときに発生する問題がある問題の原因を Microsoft 妥当な想定します。
   * **開発者指導**と**アドバイザリ サービス**はいただけます、 [Professional Direct] [ Professional Direct]と[プレミア][ Premier]サポートのレベルです。 
     
     プレミア サポート プランがあれば、報告することも SQL データ ウェアハウスに関連する問題、[マイクロソフト プレミア online ポータル][Microsoft Premier online portal]です。  参照してください[Azure サポート プラン][ Azure support plan]などの応答時間、料金は、スコープを含む、さまざまなサポート プランの詳細を表示します。Azure のサポートについてよく寄せられる質問を参照してください。 [Azure サポート Faq][Azure support FAQs]です。  
     
     ![サポート プラン](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. 選択、**問題の種類**と**カテゴリ**です。 この例では、問題の種類として"Tools"と「クライアント ツール」カテゴリとして選択しました。 
   
    ![問題の種類のカテゴリ](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. 問題を記述し、業務への影響のレベルを選択します。
   
    ![問題の説明](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. **連絡先情報**このサポート チケットが事前に入力されます。 必要な場合は、このオプションを更新します。
    
    ![連絡先情報](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. をクリックして**作成**サポート要求を送信します。

## <a name="monitor-a-support-ticket"></a>サポート チケットを監視します。
サポート リクエストを送信した後、Azure サポート チーム連絡を差し上げます。 要求の状態と詳細を確認する をクリックして**サポートの要求の管理**ダッシュ ボードにします。

![状態を確認します。](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>その他のリソース
SQL Data Warehouse コミュニティとつながることがさらに、[スタック オーバーフロー] [ Stack Overflow]または、 [Azure SQL データ ウェアハウスの MSDN フォーラム][Azure SQL Data Warehouse MSDN forum]です。

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

