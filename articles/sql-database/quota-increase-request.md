---
title: クォータの増加を要求する
description: このページでは、Azure SQL Database の単一データベース、サーバー、およびマネージド インスタンスのクォータを増やすためのサポート リクエストを作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586157"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Azure SQL Database のクォータの増加を要求する

この記事では、Azure SQL Database の単一データベース、サーバー、およびマネージド インスタンスのクォータの増加を要求する方法について説明します。 また、リージョンへのサブスクリプション アクセスを有効にする方法についても説明します。

## <a name="create-a-new-support-request"></a><a id="newquota"></a>新しいサポート リクエストを作成する

SQL Database 用の Azure portal から新しいサポート リクエストを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) のメニューで、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート] リンク](./media/quota-increase-request/help-plus-support.png)

1. **[ヘルプとサポート]** で、 **[新しいサポート リクエスト]** を選択します

    ![新しいサポート リクエストを作成する](./media/quota-increase-request/new-support-request.png)

1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![問題の種類を選択する](./media/quota-increase-request/select-quota-issue-type.png)

1. **[サブスクリプション]** で、クォータを引き上げるサブスクリプションを選択します。

   ![クォータを引き上げるサブスクリプションを選択する](./media/quota-increase-request/select-subscription-support-request.png)

1. **[クォータの種類]** では、次のいずれかのクォータの種類を選択します。

   - 単一データベースおよびエラスティック プールのクォータの場合は **SQL Database**。
   - マネージド インスタンスの場合は **SQL Database Managed Instance**。

   次に、 **[次のステップ: ソリューション >>]** を選択します。

   ![クォータの種類を選択する](./media/quota-increase-request/select-quota-type.png)

1. **[詳細]** ウィンドウで、 **[詳細の指定]** を選択して追加情報を入力します。

   ![[詳細の指定] リンク](./media/quota-increase-request/provide-details-link.png)

**[詳細の指定]** をクリックすると、 **[クォータの詳細]** ウィンドウが表示され、追加情報を追加できるようになります。 次のセクションでは、**SQL Database** および **SQL Database Managed Instance** のクォータの種類に対するさまざまなオプションについて説明します。

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> SQL Database のクォータの種類

次のセクションでは、**SQL Database** のクォータの種類に対する 3 つのクォータ拡張オプションについて説明します。

- サーバーあたりのデータベース トランザクション ユニット (DTU)
- サブスクリプションあたりのサーバー
- リージョンへのサブスクリプション アクセスを有効にする

### <a name="database-transaction-units-dtus-per-server"></a>サーバーあたりのデータベース トランザクション ユニット (DTU)

サーバーあたりの DTU の増加を要求するには、次の手順に従います。

1. クォータの種類として **[サーバーあたりのデータベース トランザクション ユニット (DTU)]** を選択します。

1. **[リソース]** リストで、ターゲットとするリソースを選択します。

1. **[新しいクォータ]** フィールドに、要求する新しい DTU 制限を入力します。

   ![DTU のクォータの詳細](./media/quota-increase-request/quota-details-dtus.png)

詳細については、「[DTU 購入モデルを使用した単一データベースに対するリソース制限](sql-database-dtu-resource-limits-single-databases.md)」および「[DTU 購入モデルを使用したエラスティック プールのリソース制限](sql-database-dtu-resource-limits-elastic-pools.md)」を参照してください。

### <a name="servers-per-subscription"></a>サブスクリプションあたりのサーバー

サブスクリプションあたりのサーバー数の増加を要求するには、次の手順に従います。

1. クォータの種類として **[サブスクリプションあたりのサーバー]** を選択します。

1. **[場所]** リストで、使用する Azure リージョンを選択します。 クォータは、各リージョンのサブスクリプション単位となります。

1. **[新しいクォータ]** フィールドに、そのリージョン内で要求する最大サーバー数を入力します。

   ![サーバー クォータの詳細](./media/quota-increase-request/quota-details-servers.png)

詳細については、「[SQL Database のリソース制限およびリソース管理](sql-database-resource-limits-database-server.md)」を参照してください。

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a> リージョンへのサブスクリプション アクセスを有効にする

一部のオファーの種類は、すべてのリージョンで使用できるわけではありません。 次のようなエラーが表示される場合があります。

`This location is not available for subscription`

ご利用のサブスクリプションに特定のリージョンでのアクセス権が必要である場合は、 **[その他のクォータ要求]** オプションを使用してアクセス権を要求してください。 ご自分の要求内で、そのリージョンに対して有効にするオファリングと SKU の詳細を指定します。 オファリングと SKU のオプションを探索するには、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)」を参照してください。

![その他のクォータの詳細](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a> マネージド インスタンスのクォータの種類

クォータの種類 **SQL Server Managed Instance** については、次の手順に従います。

1. **[リージョン]** リストで、ターゲットにする Azure リージョンを選択します。

1. **[サブネット]** と **[仮想コア]** について要求する新しい制限を入力します。

   ![マネージド インスタンスのクォータの詳細](./media/quota-increase-request/quota-details-managed-instance.png)

詳細については、「[Azure SQL Database マネージド インスタンスのリソース制限の概要](sql-database-managed-instance-resource-limits.md)」を参照してください。

## <a name="submit-your-request"></a>要求を送信する

最後の手順では、SQL Database クォータ要求の残りの詳細を入力します。 次に、 **[次のステップ: 確認と作成 >>]** を選択します。要求の詳細を確認したら、 **[作成]** をクリックして要求を送信します。

## <a name="next-steps"></a>次のステップ

要求を送信すると、そのレビューが行われます。 フォームに指定した情報に基づいた回答を受け取ります。

Azure の他の制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。
