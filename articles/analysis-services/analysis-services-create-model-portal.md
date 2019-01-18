---
title: Azure Analysis Services の Web デザイナーを使用して表形式モデルを作成する | Microsoft Docs
description: Azure ポータルで Web デザイナーを使用して Azure Analysis Services の表形式モデルを作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0b613a978c15715519ecc0f130ff6ff3ee84e306
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186967"
---
# <a name="create-a-model-in-azure-portal"></a>Azure ポータルでモデルを作成する

Azure ポータルの Azure Analysis Services Web デザイナー (プレビュー) 機能は、表形式モデルの作成と編集、およびブラウザーでのモデル データのクエリを迅速かつ簡単に実行する方法を提供します。 

> [!IMPORTANT]
> このフィーチャーは非推奨です。 今後の更新プログラムで削除されるか、大幅に変更される可能性があります。 今後の更新プログラムとの互換性を維持するために、新規および既存のプロジェクトでこの機能の使用を中止することをお勧めします。 高度なモデルの開発とテストを実行する場合は、Visual Studio (SSDT) と SQL Server Management Studio (SSMS) を使用することが最善の方法です。


## <a name="before-you-begin"></a>開始する前に

- Standard または Developer レベルの Azure Analysis Services サーバーを使用する必要があります。 Web デザイナーを使用して作成される新しいモデルは、これらのレベルでのみサポートされる DirectQuery です。
- データソースとしての Azure SQL Database、Azure SQL Data Warehouse、または Power BI Desktop (.pbix) ファイル。 Power BI Desktop ファイルから作成される新しいモデルは、Azure SQL Database および Azure SQL Data Warehouse をサポートします。
- Azure SQL Database または Azure SQL Data Warehouse データ ソースに接続するための SQL Server アカウントとパスワード。
- 新しいモデルを作成するには、サーバー管理特権が必要です。 デザイナーを使用してモデルの編集およびクエリを実行するには、データベース管理特権が必要です。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="to-create-a-new-tabular-model"></a>新しい表形式モデルを作成するには

1. サーバーの **[概要]** > **[Web デザイナー]** で、**[開く]** をクリックします。

    ![Azure ポータルでモデルを作成する](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. **[Web デザイナー]** > **[モデル]** を選択し、**[+ 追加]** をクリックします。

    ![Azure ポータルでモデルを作成する](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. **[新しいモデル]** にモデルの名前を入力し、データ ソースを選択します。

    ![Azure ポータルの [新しいモデル] ダイアログ](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. **[接続]** で、接続プロパティを入力します。 SQL Server アカウントのユーザー名とパスワードを使用する必要があります。

     ![Azure ポータルの [接続] ダイアログ](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. **[テーブルとビュー]** で、モデルに含めるテーブルを選択し、**[作成]** をクリックします。 キー ペアによるテーブル間のリレーションシップが自動的に作成されます。

     ![テーブルとビューを選択する](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

新しいモデルがブラウザーに表示されます。 ここから、以下を実行できます。   

- クエリ デザイナーにフィールドをドラッグし、フィルターを追加することで、モデル データをクエリします。
- テーブルに新しいメジャーを作成します。
- JSON エディターを使用してモデル メタデータを編集します。
- Visual Studio (SSDT)、Power BI Desktop、または Excel でモデルを開きます。

![テーブルとビューを選択する](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> ブラウザーでモデルのメタデータを編集するか新しいメジャーを作成するとに、これらの変更が Azure 内のモデルに保存されます。 SSDT、Power BI Desktop、または Excel でもモデルを操作している場合、モデルが同期から外れる可能性があります。


## <a name="next-steps"></a>次の手順 
[データベース ロールとユーザーの管理](analysis-services-database-users.md)  
[Excel を使用した接続](analysis-services-connect-excel.md)  


