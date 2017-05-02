---
title: "Excel で Azure Analysis Services に接続する | Microsoft Docs"
description: "Azure Analysis Services サーバーに Excel を使って接続する方法を説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/12/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a83e313413d87179b89e78d2b90a08a9bcc92fa1
ms.lasthandoff: 04/18/2017


---
# <a name="connect-with-excel"></a>Excel を使用した接続

Azure でサーバーを作成して、表形式モデルをデプロイすると、すぐに接続してデータの分析を開始できるようになります。 


## <a name="connect-in-excel"></a>Excel での接続

Excel でのサーバーへの接続は、Excel 2016 の Get Data またはそれより前のバージョンの Power Query を使用してサポートされます。 Power Pivot での [テーブルのインポート] ウィザードを使用した接続はサポートされません。 

**Excel 2016 で接続するには**

1. Excel 2016 では、**[データ]** リボンで **[外部データの取り込み]** > **[その他のデータ ソース]** > **[From Analysis Services (Analysis Services)]** をクリックします。

2. データ接続ウィザードで、**[サーバー名]** にクリップボードのサーバー名を貼り付けます。 そして **[ログオン資格情報]** で **[以下のユーザー名とパスワードを使用する]** を選択し、組織でのユーザー名 (たとえば nancy@adventureworks.com) とパスワードを入力します。

    ![Excel ログオンから接続する](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. **[データベースとテーブルの選択]** で、データベースとモデルまたはパースペクティブを選択し、**[完了]** をクリックします。
   
    ![Excel 選択モデルから接続する](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>関連項目
[クライアント ライブラリ](analysis-services-data-providers.md)   
[サーバーの管理](analysis-services-manage.md)     



