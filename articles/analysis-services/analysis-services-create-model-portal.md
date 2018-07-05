---
title: Azure Analysis Services の Web デザイナーを使用して表形式モデルを作成する | Microsoft Docs
description: Azure ポータルで Web デザイナーを使用して Azure Analysis Services の表形式モデルを作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 17ff6ebed615971b4157831431d9e2395ca68b48
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441677"
---
# <a name="create-a-model-in-azure-portal"></a>Azure ポータルでモデルを作成する

Azure ポータルの Azure Analysis Services Web デザイナー (プレビュー) 機能は、表形式モデルの作成と編集、およびブラウザーでのモデル データのクエリを迅速かつ簡単に実行する方法を提供します。 

この Web デザイナーは、**プレビュー**段階であることに留意してください。 機能が制限されます。 高度なモデルの開発とテストを実行する場合は、Visual Studio (SSDT) と SQL Server Management Studio (SSMS) を使用することが最善の方法です。

## <a name="before-you-begin"></a>開始する前に

- Standard または Developer レベルの Azure Analysis Services サーバー。 Web デザイナーを使用して作成される新しいモデルは、これらのレベルでのみサポートされる DirectQuery です。
- データソースとしての Azure SQL Database、Azure SQL Data Warehouse、または Power BI Desktop (.pbix) ファイル。 Power BI Desktop ファイルから作成される新しいモデルは、Azure SQL Database および Azure SQL Data Warehouse をサポートします。
- Azure SQL Database または Azure SQL Data Warehouse データ ソースに接続するための SQL Server アカウントとパスワード。

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


