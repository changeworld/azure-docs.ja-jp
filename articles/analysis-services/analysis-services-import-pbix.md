---
title: "Power BI Desktop ファイルを Azure Analysis Services にインポートする | Microsoft Docs"
description: "Azure Portal を使って Power BI Desktop ファイル (pbix) をインポートする方法を説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/26/2018
ms.author: owend
ms.openlocfilehash: e0be0c69b501d7e93c65bcf23d4dd1b6bfa89caf
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Power BI Desktop ファイルをインポートする

Power BI Desktop (pbix) ファイルをインポートすることにより、Azure AS で新しいモデルを作成することができます。 モデルのメタデータ、キャッシュ データ、およびデータ ソース接続がインポートされます。 レポートや視覚エフェクトはインポートされません。

**制限事項**   
- pbix モデルは、必ず [Analysis Services でサポートされたデータ ソース](analysis-services-datasource.md)に接続する必要があります。 
- pbix モデルでは、ライブ接続や DirectQuery 接続は使用できません。 
- pbix モデルがオンプレミスのデータ ソースに接続する場合は、[オンプレミスの データ ゲートウェイ](analysis-services-gateway.md)を Analysis Services サーバー用に構成する必要があります。
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
