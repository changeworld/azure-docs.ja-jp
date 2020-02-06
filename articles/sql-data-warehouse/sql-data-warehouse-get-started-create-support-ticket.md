---
title: サポート チケットを作成する方法
description: Azure SQL Data Warehouse でサポート チケットを作成する方法。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717829"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>SQL Data Warehouse のサポート チケットを作成する方法
SQL Data Warehouse で問題が発生した場合は、サポート チケットを作成してください。エンジニアリング チームがサポートいたします。

## <a name="create-a-support-ticket"></a>サポート チケットの作成
1. [Azure Portal](https://portal.azure.com/)を開きます。
2. ホーム画面で **[ヘルプとサポート]** タブをクリックします。
   
    ![[ヘルプとサポート]](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. [ヘルプとサポート] ブレードで **[新しいサポート要求]** をクリックし、 **[基本]** ブレードに入力します。

   [[Azure サポート プラン]](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)を選択します。
   
   * **課金、クォータ、サブスクリプション管理**のサポートは、すべてのサポート レベルでご利用いただけます。
   * **障害対応**のサポートは、[Developer](https://azure.microsoft.com/support/plans/developer/)、[Standard](https://azure.microsoft.com/support/plans/standard/)、[Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)、または [Premier](https://azure.microsoft.com/support/plans/premier/) サポートを通して提供されます。 障害対応のサポートとは、Azure のご利用に際して発生した、マイクロソフトが原因と想定される問題への対応です。
   * **開発者支援**および**アドバイザリ サービス**は、[Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) および [Premier](https://azure.microsoft.com/support/plans/premier/) サポート レベルでご利用いただけます。 
     
     Premier サポート プランを使用している場合は、[Microsoft Premier オンライン ポータル](https://premier.microsoft.com/) で SQL Data Warehouse 関連の問題を報告することもできます。 各種のサポート プランの詳細 (範囲、応答時間、価格などを含む) については、[Azure サポート プラン](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)に関するページを参照してください。Azure サポートに関するよく寄せられる質問については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。  
        
     ![[基本] ブレード](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![[基本] ブレード 1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. **[問題]** ブレードに入力します。

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > 既定では、各 SQL Server (myserver.database.windows.net など) の **DTU クォータ** は 45,000 に設定されています。 このクォータは単に安全上の制限です。 サポート チケットを作成し、要求の種類として *[クォータ]* を選択すれば、クォータを引き上げることができます。 DTU のニーズを計算するには、必要な [DWU](sql-data-warehouse-overview-what-is.md) の合計に 7.5 を掛けます。 たとえば、2 つの DW6000 を 1 つの SQL Server でホストする場合、90,000 の DTU クォータを要求する必要があります。  現在の DTU 消費量は、ポータルの [SQL Server] ブレードから確認できます。 DTU クォータには、一時停止しているデータベースと一時停止していないデータベースの両方が考慮されます。 
   > 
   > 
   
5. **連絡先情報**を入力します。

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. **[作成]** をクリックしてサポート要求を送信します。

## <a name="monitor-a-support-ticket"></a>サポート チケットの状態の確認
サポート要求を送信すると、Azure サポート チームから連絡があります。 要求の状態と詳細を確認するには、ダッシュボードで **[すべてのサポート要求]** をクリックします。

![状態の確認](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>その他のリソース
[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) または [Azure SQL Data Warehouse MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)で SQL Data Warehouse のコミュニティとつながることもできます。

 
