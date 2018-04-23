---
title: 'Azure Analysis Services チュートリアルのレッスン 8: パースペクティブを作成する | Microsoft Docs'
description: この Azure Analysis Services チュートリアル プロジェクトでは、パースペクティブを作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bb6599fefaa03e2ca0fec2788dca27240dbad8c6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="create-perspectives"></a>パースペクティブを作成する

このレッスンでは、Internet Sales パースペクティブを作成します。 パースペクティブでは、表示可能なモデルのサブセットを定義して、集中的、ビジネス固有またはアプリケーション固有のビューポイントを作成できます。 ユーザーがパースペクティブを使用してモデルに接続すると、そのパースペクティブで定義されているフィールドと同じモデル オブジェクト (テーブル、列、メジャー、階層、KPI) のみ表示されます。 詳細については、「[パースペクティブ](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular)」を参照してください。
  
このレッスンで作成する Internet Sales パースペクティブは、DimCustomer テーブル オブジェクトを除外します。 特定のオブジェクトをビューから除外するパースペクティブを作成しても、そのオブジェクトはまだモデル内に存在します。 ただし、レポート クライアントのフィールド リストには表示されません。 計算列やメジャーは、パースペクティブに含まれていてもいなくても、除外されているオブジェクト データから計算可能です。  
  
このレッスンの目的は、パースペクティブの作成方法について理解し、表形式モデルの作成ツールに慣れることです。 後でこのモデルを展開して追加のテーブルを含める場合は、追加のパースペクティブを作成して、在庫や販売など、モデルの別のビューポイントを定義することができます。  
  
このレッスンの推定所要時間: **5 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、順番に従って実行する必要があります。 このレッスンの作業を実行する前に、前のレッスン「[レッスン 7: 主要業績評価指標を作成する](../tutorials/aas-lesson-7-create-key-performance-indicators.md)」を完了している必要があります。  
  
## <a name="create-perspectives"></a>パースペクティブを作成する  
  
#### <a name="to-create-an-internet-sales-perspective"></a>Internet Sales パースペクティブを作成するには  
  
1.  **[モデル]** メニュー > **[パースペクティブ]** > **[作成と管理]** をクリックします。  
  
2.  **[パースペクティブ]** ダイアログ ボックスで、**[新しいパースペクティブ]** をクリックします。  
  
3.  **[新しいパースペクティブ]** の列見出しをダブルクリックし、名前を **Internet Sales** に変更します。  
  
4.  **DimCustomer** を "*除く*" すべてのテーブルを選択します。  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    後のレッスンでは、[Excel で分析] 機能を使用して、このパースペクティブをテストすることができます。 Excel の [ピボットテーブルのフィールド リスト] には、DimCustomer テーブルを除く各テーブルが含まれます。  

## <a name="whats-next"></a>次の手順
[レッスン 9: 階層を作成する](../tutorials/aas-lesson-9-create-hierarchies.md)
  
  
  
  
