---
title: Power BI Desktop ファイルを Azure Analysis Services にインポートする | Microsoft Docs
description: Azure Portal を使って Power BI Desktop ファイル (pbix) をインポートする方法を説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aea6f3efcf3740527c43b75a30caadf6b2a8b623
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601073"
---
# <a name="import-a-power-bi-desktop-file"></a>Power BI Desktop ファイルをインポートする

Power BI Desktop ファイル (pbix) のデータモデルを Azure Analysis Services にインポートできます。 モデルのメタデータ、キャッシュ データ、およびデータ ソース接続がインポートされます。 レポートや視覚エフェクトはインポートされません。 Power BI Desktop からインポートされたモデルは、1400 互換性レベルです。

**制限事項**   
- pbix モデルは、**Azure SQL Database** と **Azure SQL Data Warehouse** のデータソースのみに接続できます。 
- pbix モデルでは、ライブ接続や DirectQuery 接続は使用できません。 
- Analysis Services でサポートされていないメタデータが pbix データ モデルに含まれている場合は、インポートが失敗する可能性があります。

## <a name="to-import-from-pbix"></a>pbix からインポートするには

1. サーバーの **[概要]** > **[Web デザイナー]** で、**[開く]** をクリックします。

    ![Azure ポータルでモデルを作成する](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. **[Web デザイナー]** > **[モデル]** を選択し、**[+ 追加]** をクリックします。

    ![Azure ポータルでモデルを作成する](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. **[新しいモデル]** で、モデルの名前を入力し、Power BI Desktop ファイルを選びます。

    ![Azure ポータルの [新しいモデル] ダイアログ](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. **[インポート]** で、ファイルを探して選びます。

     ![Azure ポータルの [接続] ダイアログ](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>関連項目

[Azure Portal でモデルを作成する](analysis-services-create-model-portal.md)   
[Azure Analysis Services に接続する](analysis-services-connect.md)  
