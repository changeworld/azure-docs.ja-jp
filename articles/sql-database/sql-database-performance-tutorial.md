---
title: "パフォーマンスの問題のトラブルシューティングとデータベースの最適化 | Microsoft Docs"
description: "パフォーマンスに関する推奨事項を SQL Database に適用し、データベースに対して実行されているクエリのパフォーマンスに関する洞察を得る方法を学習します"
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: performance
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0bdb111257053e4a57510a72ab683bc9c890d2f7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="troubleshoot-performance-issues-and-optimize-your-database"></a>パフォーマンスの問題のトラブルシューティングとデータベースの最適化

データベースのパフォーマンスが悪い一般的な理由は、インデックスの欠落や最適化が不十分なクエリです。 このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * パフォーマンスの向上に関する推奨事項を確認、適用、および元に戻す
> * リソースの使用率が高いクエリを見つける
> * 実行時間が長いクエリを見つける

> 推奨事項を受け取るには、インデックスの欠如のようなパフォーマンスの問題があるデータベースに対する継続的なワークロードが必要です。
>

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure ポータル](https://portal.azure.com/)にログインします。

## <a name="review-and-apply-a-recommendation"></a>推奨事項を確認して適用する

システムによるデータベースに関する推奨事項を適用するには、次の手順に従います。

1. データベース ブレードの **[パフォーマンスの推奨事項]** メニューをクリックします。

    ![パフォーマンスの推奨事項](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. 推奨事項の一覧で、アクティブな推奨事項を選択します。 この例では、[インデックスの作成] を選択します。

    ![推奨事項の選択](./media/sql-database-performance-tutorial/create_index.png)

3. **[適用]** ボタンをクリックして、推奨事項を適用します。 必要に応じて推奨事項の詳細を確認し、**[スクリプトの表示]** ボタンをクリックして、実行される T-SQL スクリプトを表示します。

    ![推奨事項の適用](./media/sql-database-performance-tutorial/apply.png)

4. [省略可能] 推奨事項が自動的に適用されるように、自動チューニングを有効にします。

    ![自動チューニング](./media/sql-database-performance-tutorial/auto_tuning.png)

## <a name="revert-a-recommendation"></a>推奨事項を元に戻す

Database Advisor は、実装されたすべての推奨事項を監視します。 推奨事項によってワークロードが改善されない場合は、自動的に元に戻されます。 推奨事項を手動で元に戻すこともできますが、ほとんどの場合、そうする必要はありません。 推奨事項を元に戻すには、次の手順に従います。

1. [パフォーマンスの推奨事項] メニューに移動し、適用されている推奨事項の 1 つを選択します。

    ![推奨事項の選択](./media/sql-database-performance-tutorial/select.png)

2. 詳細ビューで、**[元に戻す]** をクリックします。

    ![推奨事項を元に戻す](./media/sql-database-performance-tutorial/revert.png)

## <a name="find-the-query-that-consumes-the-most-resources"></a>リソースを最も消費しているクエリを見つける

リソースを最も消費しているクエリを見つけるには、次の手順に従います。

1. データベース ブレードの **[Query Performance Insight]** メニューをクリックします。

    ![クエリの洞察](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. リソースの種類を選択します。

    ![クエリの洞察](./media/sql-database-performance-tutorial/select_resource_type.png)

3. テーブル内の最初のクエリを選択します。

    ![クエリの洞察](./media/sql-database-performance-tutorial/select_query.png)

4. クエリの詳細を確認します。

    ![クエリの洞察](./media/sql-database-performance-tutorial/query_details.png)

## <a name="find-the-longest-running-query"></a>実行時間が最長のクエリを見つける

1. Query Performance Insight に移動し、**[実行時間が長いクエリ]** タブを選択します。

    ![クエリの洞察](./media/sql-database-performance-tutorial/long_running.png)

3. テーブル内の最初のクエリを選択します。

    ![クエリの洞察](./media/sql-database-performance-tutorial/select_first_query.png)

4. クエリの詳細を確認します。

    ![クエリの洞察](./media/sql-database-performance-tutorial/review_query_details.png)



## <a name="next-steps"></a>次のステップ 
データベースのパフォーマンスが悪い一般的な理由は、インデックスの欠落や最適化が不十分なクエリです。 このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * パフォーマンスの向上に関する推奨事項を確認、適用、および元に戻す
> * リソースの使用率が高いクエリを見つける
> * 実行時間が長いクエリを見つける

[SQL Database のパフォーマンスのチューニングのヒント](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

