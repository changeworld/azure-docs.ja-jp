---
title: "Azure Portal による新しいエラスティック プールの作成 | Microsoft Docs"
description: "SQL データベース構成にスケーラブルなエラスティック プールを追加して、多数のデータベースでの管理とリソース共有を簡単にする方法。"
keywords: "スケーラブルなデータベース、データベースの構成"
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: 
ms.assetid: bf12594b-d258-40e6-a9fc-d8a8710c2d65
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 16f4e287a955b787a08cc6949094bd0f5224421a
ms.openlocfilehash: 29488482f3e6c5f3dcf0eac04fe7e5e33fb832ce


---
# <a name="create-a-new-elastic-pool-with-the-azure-portal"></a>Azure Portal による新しいエラスティック プールの作成
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
>

この記事では、[Azure Portal](https://portal.azure.com/) でスケーラブルな[エラスティック プール](sql-database-elastic-pool.md)を作成する方法について説明します。 プールを作成する方法は 2 つあります。 目的のプール設定がわかっている場合は、一から作成できます。また、サービスによる推奨設定から始めることもできます。 SQL Database には、データベースの過去の使用状況テレメトリに基づいてコスト効率が高いプール設定を推奨するインテリジェンスが組み込まれています。

サーバーに複数のプールを追加することはできますが、同じプールに異なるサーバーからデータベースを追加することはできません。 プールを作成するには、V12 サーバーに 1 つ以上のデータベースが必要です。 データベースがない場合は、「 [SQL Database チュートリアル: サンプル データと Azure ポータルを使用して分単位で SQL Database を作成する](sql-database-get-started.md)」を参照してください。 データベースが 1 つだけのプールを作成できますが、プールのコスト効率が高いのは、データベースが複数ある場合のみです。 [エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)を参照してください。

> [!NOTE]
> エラスティック プールは、現在プレビュー段階にあるインド西部を除くすべての Azure リージョンで一般公開 (GA) されています。  プレビュー段階のリージョンでも、できるだけ早く一般公開される予定です。
>
>

## <a name="step-1-create-a-new-pool"></a>手順 1. 新しいプールを作成する

この記事では、Portal の既存の **[サーバー]** ブレードから新しいプールを作成する方法について説明します。これは、既存のデータベースをプールに移動する最も簡単な方法です。

> [!NOTE]
> 新しいプールは、**Marketplace** で "**SQL エラスティック プール**" を検索するか、**[SQL エラスティック プール]** 参照ブレードの **[+追加]** をクリックすることで作成することもできます。 このプールのプロビジョニング ワークフローでは、新しいサーバーまたは既存のサーバーを指定できます。
>
>

1. [Azure Portal](http://portal.azure.com/) の左側の一覧の下で、**[その他のサービス]** **>** **[SQL Server]** の順にクリックし、プールに追加するデータベースが含まれているサーバーをクリックします。
2. **[新しいプール]**をクリックします。

    ![サーバーへのプールの追加](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **- または -**

    そのサーバーに推奨されるエラスティック プールがあることを示すメッセージが表示される場合があります (V12 のみ)。 メッセージをクリックして、データベースの過去の使用状況テレメトリに基づいた推奨のプールを表示します。次に、レベルをクリックして詳細を確認し、プールをカスタマイズします。 推奨のしくみについては、このトピックで後述する「[プールの推奨事項について](#understand-pool-recommendations)」を参照してください。

    ![推奨されるプール](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. **[エラスティック プール]** ブレードが表示されます。これは、プールの設定を指定する場所です。 前の手順で **[新しいプール]** をクリックした場合、価格レベルは既定で **Standard** になり、まだ選択されているデータベースがありません。 空のプールを作成することも、そのサーバーからプールに移動する一連の既存のデータベースを指定することもできます。 推奨されるプールを作成している場合は、推奨の価格レベル、パフォーマンス設定、データベース一覧があらかじめ入力されていますが、それらは引き続き変更できます。

    ![エラスティック プールの構成](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. エラスティック プールの名前を指定するか、既定値を使用します。

## <a name="step-2-choose-a-pricing-tier"></a>手順 2. 価格レベルを選択する

プールの価格レベルによってプールのエラスティック データベースで使用できる機能が決まり、eDTU の最大数 (eDTU MAX) や各データベースで使用できるストレージ容量 (GB) が決まります。 詳細については、「サービス プラン」を参照してください。

プールの価格レベルを変更するには、**[価格レベル]** をクリックし、目的の価格レベルをクリックして、**[選択]** をクリックします。

> [!IMPORTANT]
> 価格レベルを選択し、最後の手順で **[OK]** をクリックして変更を確定すると、プールの価格レベルを変更できなくなります。 既存のエラスティック プールの価格レベルを変更するには、任意の価格レベルで新しいエラスティック プールを作成し、その新しいプールにエラスティック データベースを移行します。
>
>

![料金レベルを選択します](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## <a name="step-3-configure-the-pool"></a>手順 3. プールを構成する

価格レベルを設定した後は、データベースを追加するプールで [プールの構成] をクリックし、プールの eDTU とストレージ (プールの GB 値) を設定して、さらにプール内のエラスティック データベースの最小および最大 eDTU を設定します。

1.  **[プールの構成]**
2. プールに追加するデータベースを選択します。 プールを作成する場合、この手順は省略できます。 プールが作成されたら、データベースを追加できます。
    データベースを追加するには、**[データベースの追加]**、追加するデータベース、**[選択]** の順にクリックします。

    ![[データベースの追加]](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    使用するデータベースに過去の使用状況テレメトリが十分にある場合、構成の決定に役立つ **[Estimated eDTU and GB usage (推定される eDTU と GB の使用状況)]** グラフと **[Actual eDTU usage (実際の eDTU 使用量)]** 棒グラフが更新されます。 また、プールの適切なサイズ設定に役立つ推奨事項に関するメッセージが表示される場合もあります。 詳細については、「 [動的な推奨事項](#dynamic-recommendations)」を参照してください。

3. **[プールの構成]** ページにあるコントロールを使用して設定を確認し、プールを構成します。 各サービス レベルの上限の詳細については、[エラスティック プールの上限](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)に関するトピックを参照してください。プールの適切なサイズ設定に関する詳細なガイダンスについては、[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)に関する記事を参照してください。 プールの設定の詳細については、[エラスティック データベースのプロパティ](sql-database-elastic-pool.md#elastic-pool-and-elastic-database-properties)に関するページを参照してください。

    ![エラスティック プールの構成](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4.  **[選択]** in the **Configure Pool** をクリックします。
5. **[OK]** をクリックすると、プールが作成されます。


## <a name="understand-pool-recommendations"></a>プールの推奨事項について

SQL Database サービスでは使用状況の履歴を評価し、単一のデータベースを使用するよりコスト効率が高い場合は、1 つ以上のプールを推奨します。 それぞれの推奨事項は、サーバーのデータベースでプールに最適なデータベースを一意の小集合にしたもので構成されます。

![推奨されるプール](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

プールの推奨事項は、以下で構成されます。

- プールの価格レベル (Basic、Standard、Premium)
- 適切な **プールの eDTU** (プールあたりの最大 eDTU とも呼ばれます)
- データベースあたりの **eDTU の最大値**と **eDTU の最小値**
- プールの推奨データベースの一覧

プールを推奨するとき、このサービスでは過去 30 日間のテレメトリが考慮されます。 データベースがエラスティック プールの候補と見なされるには 7 日間以上存在している必要があります。 エラスティック プールに既に存在するデータベースはエラスティック プール推奨の候補と見なされません。

このサービスでは、各サービス レベルの単一のデータベースを同じレベルのプールに移動することの必要性と対費用効果が評価されます。 たとえば、サーバーのすべての Standard データベースの Standard エラスティック プールに対する適合性が評価されます。 つまり、このサービスでは、Standard データベースを Premium プールに移動することなど、レベル間の推奨は行われません。

### <a name="dynamic-recommendations"></a>動的な推奨事項

データベースをプールに追加した後は、選択したデータベースの使用状況の履歴に基づき、推奨事項が動的に生成されます。 この推奨事項は、 **[プールの構成]** ブレード上部の推奨事項バナーに加え、eDTU と GB の使用状況グラフにも表示されます。 このような推奨事項は、特定のデータベース用に最適化されたプールの作成をサポートすることを目的としています。

![動的な推奨事項](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="additional-resources"></a>その他のリソース

- [Azure ポータルを使用したエラスティック データベース プールの監視、管理、サイズ設定](sql-database-elastic-pool-manage-portal.md)
- [エラスティック データベース プールの監視と管理 (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
- [C# で SQL Database を作成し、管理する](sql-database-elastic-pool-manage-csharp.md)
- [Azure SQL Database によるスケールアウト](sql-database-elastic-scale-introduction.md)



<!--HONumber=Dec16_HO3-->


