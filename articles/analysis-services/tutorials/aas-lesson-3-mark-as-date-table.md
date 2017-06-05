---
title: "Azure Analysis Services チュートリアル - レッスン 3: 日付テーブルとしてマークする | Microsoft Docs"
description: "Azure Analysis Services チュートリアル プロジェクトで日付テーブルをマークする方法を説明します｡"
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
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 80c15b2115afca7342771d3f53d1742fc347ae1c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-3-mark-as-date-table"></a>レッスン 3: 日付テーブルとしてマークする

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

｢レッスン 2: データを取得する｣では､DimDate というディメンション テーブルをインポートしました｡ モデルでは､このテーブルは DimDate と命名されていますが､このテーブルには日付と時刻データが含まれているため *日付テーブル*とも呼ばれます｡  
  
少し後でメジャーを作成するときに行うように､計算で DAX タイムインテリジェンス関数を使用する都度､日付テーブルのプロパティ (*日付テーブル* とそのテーブルの一意の識別子の *日付列*)を指定する必要あります｡
  
このレッスンでは､ DimDateテーブルを*日付テーブル*､その日付テーブル内の日付列 を*日付列* (一意の識別子) としてマークします｡  

日付テーブルと日付列をマークする前に､モデルをより容易に理解できるよう､少しハウスキーピングを行っておきましょう｡ DimDate テーブルに **FullDateAlternateKey** という列があります｡この列の各行は､テーブルに含まれる各暦年度の 1 日にあたります｡ この列は､メジャー数式やレポートで頻繁に使用することになります｡ しかしながら､実際には FullDateAlternateKey はこの列の優れた識別子ではありません｡ この列名を **日付**に変更することで､簡単に識別し､数式に含められるようにします｡ SSDT､さらには Power BI や Excel などのクライアント側のレポートアプリケーションで簡単に識別できるように必要に応じてテーブルや列などのオブジェクトの名前を変更することは良いアイデアです｡ 
  
このレッスンの推定所要時間: **3 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンのタスクを実行する前に、前のレッスン「[レッスン 2: 日付テーブルとしてマークする](../tutorials/aas-lesson-2-get-data.md)」を終えている必要があります。 

### <a name="to-rename-the-fulldatealternatekey-column"></a>FullDateAlternateKey 列の名前を変更する

1.  モデル デザイナーで **DimDate** テーブルをクリックします｡

2.  **FullDateAlternateKey** 列のヘッダーをダブルクリックして､名前を**日付**に変更します｡

  
### <a name="to-set-mark-as-date-table"></a>Mark as Date Table を設定する  
  
1.  **Date** 列を選択し､ **[プロパティ]** ウィンドウの **Data Type** で **Date** が選択されていることを確認します｡  
  
2.  **[Table]** メニューをクリックし､**[Date]** をクリックして､**[Mark as Date Table]** をクリックします｡  
  
3.  **[Mark as Date Table]** ダイアログ ボックスの**[Date]** リストボックスで､一意の識別子として**[Date]** 列を選択します｡ この列が既定で選択されます｡ **[OK]**をクリックします。 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>次の手順
[レッスン 4: リレーションシップを作成する](../tutorials/aas-lesson-4-create-relationships.md)
  

