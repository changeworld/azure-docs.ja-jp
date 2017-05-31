---
title: "Azure Analysis Services チュートリアル - レッスン 6: メジャーを作成する | Microsoft Docs"
description: "この Azure Analysis Services チュートリアル プロジェクトでメジャーを作成する方法を説明します。"
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
ms.date: 05/02/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: c4977758997c91f0191e0367fb57923f43080b56
ms.contentlocale: ja-jp
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-6-create-measures"></a>レッスン 6: メジャーを作成する
このレッスンでは､モデルに取り込むメジャーを作成します｡ 前のレッスンで作成した計算列同様､メジャーは DAX 数式を使用して作成される計算値です｡ ただし､計算列と異なり､メジャーはユーザーが選択した*フィルター*に基づいて評価されます｡ピボットテーブル内の [Row Labels] フィールドに追加された列あるいはスライサーなどです｡ フィルター内の各セルの値は､適用されたメジャーによって求められます｡ メジャーは､ほぼあらゆる表形式モデルに含めることが可能で､数値データに対して動的な計算を行うことができる強力で柔軟な計算手段です｡ 詳細は､[Measures](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular) を参照してください｡
  
メジャーを作成するには､*Measure Grid* を使用します｡ 既定では､各テーブルにはメジャー グリッドが 1 つあります｡しかしながら､通常､必ずしもすべてのテーブルにメジャーを作成することはありません｡ メジャー グリッドは､モデル デザイナーでデータ ビューを表示しているときにテーブルの下に表示されます｡ テーブルのメジャー グリッドを非表示または表示するには､**[テーブル]** メニューをクリックし､**[Show Measure Grid]** をクリックします｡  
  
メジャーは､メジャー グリッドで空のセルをクリックし､数式バーで DAX 式を入力することによって作成できます｡ ENTER を入力することで式の入力を終えると､そのセルにメジャーが表示されます｡ またメジャーは､標準の集計関数を使用して作成することもできます｡このためには､列をクリックし､ツールバー上の [AutoSum] ボタン (**∑**) をクリックします｡ AutoSum 機能で作成されたメジャーは､その列の真下のメジャー グリッド セルに表示され､移動することができます｡  
  
このレッスンでは､数式バーで DAX 式を入力する方法と AutoSum 機能を使用する方法の両方の方法でメジャーを作成します｡  
  
このレッスンの推定所要時間: **30 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンの作業を実行する前に、前のレッスン「[レッスン 5: 計算列を作成する](../tutorials/aas-lesson-5-create-calculated-columns.md)」を終えている必要があります。  
  
## <a name="create-measures"></a>メジャーを作成する  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>DimData テーブルに DaysCurrentQuarterToDate メジャーを作成する  
  
1.  モデル デザイナーで **DimDate** テーブルをクリックします｡  
  
2.  メジャー グリッドの左上の空のセルをクリックします｡  
  
3.  数式バーに次の式を入力します｡  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    左上のセルにメジャー名､**DaysCurrentQuarterToDate** ､その後に結果 **92** が表示されています｡
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    計算列と異なり､メジャー式では､メジャー名に続けてコンマ､数式を入力できます｡

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>DimData テーブルに DaysCurrentQuarter メジャーを作成する  
  
1.  モデル デザイナーで **DimDate** テーブルがアクティブな状態のまま､メジャー グリッドの作成したばかりのメジャーの下にある空のセルをクリックします｡  
  
2.  数式バーに次の式を入力します｡  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    現在進行中の期間と前期間の比較比率を作成する場合､数式は経過した期間の割合を考慮し､その割合と前期間の同じ割合とを比較する必要があります｡ この場合は､ [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] が現期間で経過した割合を示します｡  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>FactInternetSales テーブルにInternetDistinctCountSalesOrder メジャーを作成する  
  
1.  **FactInternetSales** テーブルをクリックします｡   
  
2.  **SalesOrderNumber** 列の見出しをクリックします｡  
  
3.  ツールバーで AutoSum (**∑**) ボタンの下向き矢印をクリックし､**DistinctCount** を選択します｡  
  
    AutoSum 機能により､DistinctCount 標準集計式を使用して選択された列のメジャーが自動的に作成されます｡  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  メジャー グリッドで新しいメジャーをクリックし､**[プロパティ]** ウィンドウの **[Measure Name]** のメジャー名を **InternetDistinctCountSalesOrder** に変更します｡ 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>FactInternetSales テーブルに追加のメジャーを作成する  
  
1.  AutoSum 機能を使用して次のメジャーを作成し､名前を指定します｡  

    |分割|メジャー名|AutoSum (∑)|数式|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|カウント|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|合計|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|合計|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|合計|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|合計|=SUM([SalesAmount])|  
    |利幅|InternetTotalMargin|合計|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|合計|=SUM([TaxAmt])|  
    |運送料|InternetTotalFreight|合計|=SUM([Freight])|  
  
2.  メジャー グリッド内の空のセルをクリックし､数式バーを使用することで､次のメジャーを順に作成し､名前を指定します｡  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
FactInternetSales テーブルに作成したメジャーは､ユーザーの選択したフィルターで定義された品目の売上高や経費､利幅などの重要な財務データの分析に使用できます｡  
  
## <a name="whats-next"></a>次の手順
[レッスン 7: 主要業績評価指標を作成する](../tutorials/aas-lesson-7-create-key-performance-indicators.md)  

  

