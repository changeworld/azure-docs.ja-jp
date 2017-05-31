---
title: "Azure Analysis Services チュートリアルのレッスン 12: Excel で分析する |Microsoft Docs"
description: "Azure Analysis Services チュートリアル プロジェクトで Excel の Analyze を使用する方法を説明します｡"
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
ms.openlocfilehash: d61ad247d795c210350c6681087b811c1fa39a70
ms.contentlocale: ja-jp
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-12-analyze-in-excel"></a>レッスン 12: Excel で分析する

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

このレッスンでは､SSDT の Analyze in Excel 機能を使用して､Microsoft Excel を開き､モデル ワークスペースへのデータ ソース接続を自動的に作成して､ワークスペースに自動的にピボットテーブルを追加します｡ Analyze in Excel 機能は､モデルをデプロイする前にモデル デザインの有効性を迅速かつ簡単にテストする手段を提供することを意図しています｡ このレッスンでは､データ分析は行いません｡ このレッスンの目的はモデル作成者が､モデル デザインのテストに利用できるツールを慣れてもらうことにあります｡ モデル作成者向けの Analyze in Excel 機能と異なり､エンドユーザーは Excel や Power BI などのレポート用クライアント アプリケーションを使用して､デプロイされたモデル データに接続し､参照することができます｡  
  
このレッスンを行うには､SSDT と同じコンピューターに Excel がインストールされている必要があります｡
  
このレッスンの推定所要時間: **5 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンの作業を実行する前に、前のレッスン「[レッスン 11: ロールを作成する](../tutorials/aas-lesson-11-create-roles.md)」を終えている必要があります。  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>Default および Internet Sales パースペクティブを使用して参照します｡  
これらのタスクを初めて行う場合は､Default パースペクティブ(すべてのモデル オブジェクトが含まれる) と Internet Sales パースペクティブの両方を使ってモデルを参照します｡ Internet Sales パースペクティブでは､Customer テーブル オブジェクトが除外されています｡  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>Default パースペクティブを使って参照する  
  
1.  **[Model]** メニュー > **[Analyze in Excel]** をクリックします｡  
  
2.  **[Analyze in Excel]** ダイアログ ボックスで **[OK]** をクリックします｡  
  
    新しいノートブックで Excel が開きます｡ 現在のユーザー アカウントを使ってデータ ソース接続が作成され､Default パースペクティブを使って表示可能なフィールドが定義されます｡ ワークシートに自動的にピボットテーブルが追加されます｡  
  
3.  Excel の **PivotTable Field List** に **DimDate** および **FactInternetSales** メジャー グループが表示され､かつ､**DimCustomer**､**DimDate**､ **DimGeography**､**DimProduct**､ **DimProductCategory**､ **DimProductSubcategory**､および **FactInternetSales** テーブルが表示されます｡それぞれのテーブルには､列がすべて含まれています｡  
  
4.  ワークブックを保存しないで Excel を終了します｡  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>Internet Sales パースペクティブを使って参照する  
  
1.  **[モデル]** メニューをクリックし、**[Analyze in Excel]** をクリックします。  
  
2.  **[Analyze in Excel]** ダイアログ ボックスで､**Current Windows User** を選択状態のままにし､**[Perspective]** ドロップダウン リストボックスで**Internet Sales** を選択して､**[OK]** をクリックします｡ 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  Excel の **PivotTable Fields** のフィールド一覧からDimCustomer テーブルが除外されています｡  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  ワークブックを保存しないで Excel を終了します｡  
  
## <a name="browse-by-using-roles"></a>ロールを使って参照する  
ロールは､あらゆる表形式モデルに不可欠の要素です｡ メンバーとしてユーザーが登録されているロールがない場合､ユーザーはモデルを使ってデータにアクセスすることも分析することもできません｡ Analyze in Excel 機能は､定義されているロールをテストする手段を提供します｡  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Sales Manager ユーザー ロールを使って参照する  
  
1.  SSDT で、**[モデル]** メニューをクリックし、**[Analyze in Excel]** をクリックします。  
  
2.  **[Analyze in Excel]** ダイアログ ボックスの **[Specify the user name or role to use to connect to the model]** で **[Role]** を選択し､ドロップダウン リストボックスで**Sales Manager** を選択して､**[OK]** をクリックします｡  
  
    新しいノートブックで Excel が開きます｡ ピボットテーブルが自動的に作成されます。 [ピボットテーブルのフィールド] リストに、新しいモデルで使用できるすべてのデータ フィールドが表示されます。  
      
3.  ワークブックを保存しないで Excel を終了します｡  
  
## <a name="whats-next"></a>次の手順
次の｢[レッスン 13: デプロイする](../tutorials/aas-lesson-13-deploy.md)｣に進みます｡

  
  
  

