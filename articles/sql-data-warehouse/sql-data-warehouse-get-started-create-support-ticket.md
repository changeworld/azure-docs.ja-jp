---
title: サポート チケットを作成する方法
description: Azure Synapse Analytics でサポート チケットを作成する方法。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195693"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Azure Synapse Analytics 用のサポート チケットを作成する方法
Azure Synapse Analytics で問題が発生した場合は、サポート チケットを作成してください。エンジニアリング チームがサポートいたします。

## <a name="create-a-support-ticket"></a>サポート チケットの作成
1. [Azure Portal](https://portal.azure.com/)を開きます。
1. ホーム画面で **[ヘルプとサポート]** タブをクリックします。
   
    ![[ヘルプとサポート]](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. お使いの [Azure サポート プラン](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)を確認します。
   
   * **課金、クォータ、サブスクリプション管理**のサポートは、すべてのサポート レベルでご利用いただけます。
   * **障害対応**のサポートは、[Developer](https://azure.microsoft.com/support/plans/developer/)、[Standard](https://azure.microsoft.com/support/plans/standard/)、[Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)、または [Premier](https://azure.microsoft.com/support/plans/premier/) サポートを通して提供されます。 障害対応のサポートとは、Azure のご利用に際して発生した、マイクロソフトが原因と想定される問題への対応です。
   * **開発者支援**および**アドバイザリ サービス**は、[Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) および [Premier](https://azure.microsoft.com/support/plans/premier/) サポート レベルでご利用いただけます。 
     
     Premier サポート プランを使用している場合は、[Microsoft Premier オンライン ポータル](https://premier.microsoft.com/) で SQL Data Warehouse 関連の問題を報告することもできます。 各種のサポート プランの詳細 (範囲、応答時間、価格などを含む) については、[Azure サポート プラン](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)に関するページを参照してください。Azure サポートに関するよく寄せられる質問については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
1. **[ヘルプとサポート]** ページで、 **[新しいサポート リクエスト]** を選択します。 ドロップダウン メニューから問題の種類を選択します。 次に、 **[基本]** タブへの情報の入力を続けます。問題の **[概要]** を入力し、メニューから **[問題の種類]** を選択し、保存を選択します。

    ![[ヘルプとサポート]](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > 既定では、各 SQL Server (myserver.database.windows.net など) の **DTU クォータ** は 45,000 に設定されています。 このクォータは単に安全上の制限です。 サポート チケットを作成し、要求の種類として *[クォータ]* を選択すれば、クォータを引き上げることができます。 DTU のニーズを計算するには、必要な [DWU](sql-data-warehouse-overview-what-is.md) の合計に 7.5 を掛けます。 たとえば、2 つの DW6000 を 1 つの SQL Server でホストする場合、90,000 の DTU クォータを要求する必要があります。  現在の DTU 消費量は、ポータルの [SQL Server] ブレードから確認できます。 DTU クォータには、一時停止しているデータベースと一時停止していないデータベースの両方が考慮されます。 
   > 

1. 問題の解決に役立つ解決策が表示される場合があります。 提示された解決策で問題が解決しない場合は、 **[次へ: 詳細]** を選択します。 問題の詳細と連絡先情報を提示します。 **[次へ: レビューと作成]** 
![[詳細]](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. 情報を確認し、フォームの下部にある **[作成]** を選択してサポート リクエストを送信します。

## <a name="monitor-a-support-ticket"></a>サポート チケットの状態の確認
サポート要求を送信すると、Azure サポート チームから連絡があります。 要求の状態と詳細を確認するには、ダッシュボードで **[すべてのサポート要求]** をクリックします。

![状態の確認](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>その他のリソース
[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) または [Azure SQL Data Warehouse MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)で SQL Data Warehouse のコミュニティとつながることもできます。

 
