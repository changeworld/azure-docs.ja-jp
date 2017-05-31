---
title: "Azure Analysis Services Adventure Works チュートリアル | Microsoft Docs"
description: "Azure Analysis Services の Adventure Works チュートリアルを紹介します｡"
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
ms.openlocfilehash: b20adfa6c69488b848d4ca5bee2cb4baeca806b8
ms.contentlocale: ja-jp
ms.lasthandoff: 05/05/2017

---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services - Adventure Works チュートリアル

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

このチュートリアルは､[SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) を使用して 1400 互換性レベルで表形式モデルを作成､配備する方法に関するレッスンで構成されます｡  

Analysis Services および表形式モデルが初めてという方には､このチュートリアルを最後まで終えることが基本的な表形式モデルを作成し､実際の Analysis Server サーバーにそのモデルをデプロイする方法を最短で学ぶ方法です｡ 前提条件となるものをすべて整えた後､このチュートリアルを終えるのに要する時間は 2 ないし 3 時間です｡  
  
## <a name="what-youll-learn"></a>学習内容   
  
-   SSDT において **1400 互換性レベル**で新しい表形式モデルのプロジェクトを作成する方法
  
-   リレーショナルデータベースから表形式モデルのプロジェクトにデータをインポートする方法  
  
-   モデル内の表間のリレーションシップを作成､管理する方法  
  
-   計算列､メジャー､重要な業務指標の分析に有用な主要業績評価指標を作成する方法  
  
-   業務およびアプリケーションに固有のビューポイントを提供することによってより容易にモデルデータを参照するのに役立つパースペクティブと階層を作成､管理する方法  
  
-   テーブルのデータをより小さな論理部分に分割し､それぞれを独立して処理することを可能にするパーティションを作成する方法  
  
-   ユーザー メンバーとともにロールを作成することによってモデル オブジェクトとモデル データのセキュリティを保護する方法  
  
-   **Azure Analysis Services** サーバーまたはオンプレミスの SQL Server 2017 Analysis Services サーバーに表形式モデルをデプロイする方法  
  
## <a name="prerequisites"></a>前提条件  
このチュートリアルを完了するには、以下が必要になります。  
  
-   モデルのデプロイ先となる Azure Analysis Services または SQL Server 2017 Analysis Services インスタンス。 無料の [試用版 Azure Analysis Services ](https://azure.microsoft.com/services/analysis-services/) へのサインアップと [サーバーの作成](../analysis-services-create-server.md) [SQL Server 2017 Community Technology Preview](https://www.microsoft.com/evalcenter/evaluate-sql-server-vnext-ctp) へのサインアップとダウンロード 

-   [AdventureWorksDW2014 サンプル データベース](http://go.microsoft.com/fwlink/?LinkID=335807) がインストールされた SQL Server または Azure SQL Database｡ このサンプル データベースに､このチュートリアルを学ぶのに必要なデータが含まれています｡ [無料版 SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) のダウンロード または､無料の[試用版 Azure SQL Database](https://azure.microsoft.com/services/sql-database/) へのサインアップ 

    **重要:** オンプレミスの SQL Server にサンプル データベースをインストールして､Azure Analysis Services サーバーにモデルをデプロイする場合は､[オンプレミスのデータ ゲートウェイ](../analysis-services-gateway.md)が必要です｡

-   最新バージョンの [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)

-   最新バージョンの [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)    

-   [Power BI Desktop](https://powerbi.microsoft.com/desktop/) または Excel などのクライアント アプリケーション 

## <a name="scenario"></a>シナリオ  
このチュートリアルは､Adventure Works Cycles という架空の会社を題材にしています｡ Adventure Works は､金属製および複合材料製の自転車を製造し､北米､ヨーロッパ､およびアジアに流通市場を持つ大きな多国籍の製造企業です｡ ワシントン州ボセルに本社があり､従業員数は 500 人です｡ また､Adventure Works はその市場拠点で複数の地域担当販売チームも有しています｡ このチュートリアルでは､営業ユーザーのために AdventureWorksDW サンプルデータベースのインターネット販売データを分析するための表形式モデルを作成します｡  
  
チュートリアル全体は､いくつかのレッスンから構成されています｡ 1 つのレッスンはいくつかのタスクから構成され､レッスンを終えるには､各タスクを順に終了する必要があります｡ 1 つのレッスンには､似たような結果になるタスクがいくつ含まれることがありますが､各タスクの実行手順は少しずつ異なります｡ これは､しばしば､同じタスクであってもその実行方法が複数あることを意味し､前のレッスンやタスクで学習したスキルを活用すること挑戦になります｡  
  
レッスンの目的は､SSDT に含まれる機能の多くを利用することによって動作する基本的な表形式モデルをオーサリングする手順を手引きすることにあります｡ レッスンはそれぞれその前のレッスンに基づいているため､全レッスンを順序通りに行ってください｡ すべてのレッスンを終了すると､Adventure Works のインターネット販売に関する表形式モデル例をオーサリングし､Analysis Services サーバーにデプロイしたことになります｡  
  
このチュートリアルには､Azure Portal における Azure Analysis Services サーバーの管理や､ SQL Server Management Studio (SSMS) を使用することによりサーバーやデプロイされたデータベースの管理､デプロイされたモデルに接続してモデルデータを閲覧するための報告用クライアント アプリケーションに関するレッスンや情報は含まれていません｡  


## <a name="lessons"></a>レッスン  
このチュートリアルには､次のレッスンが含まれていません｡  
  
|レッスン|推定所要時間|  
|----------|------------------------------|  
|[レッスン 1: 新しい表形式モデル プロジェクトを作成する](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 分|  
|[レッスン 2: データを取得する](../tutorials/aas-lesson-2-get-data.md)|10 分|  
|[レッスン 3: 日付テーブルとしてマークする](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 分|  
|[レッスン 4: リレーションシップを作成する](../tutorials/aas-lesson-4-create-relationships.md)|10 分|  
|[レッスン 5: 計算列を作成する](../tutorials/aas-lesson-5-create-calculated-columns.md)|約 15 分|
|[レッスン 6: メジャーを作成する](../tutorials/aas-lesson-6-create-measures.md)|30 分|  
|[レッスン 7: 主要業績評価指標を作成する](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|約 15 分|  
|[レッスン 8: パースペクティブを作成する](../tutorials/aas-lesson-8-create-perspectives.md)|5 分|  
|[レッスン 9: 階層を作成する](../tutorials/aas-lesson-9-create-hierarchies.md)|20 分|  
|[レッスン 10: パーティションを作成する](../tutorials/aas-lesson-10-create-partitions.md)|約 15 分|  
|[レッスン 11: ロールを作成する](../tutorials/aas-lesson-11-create-roles.md)|約 15 分|  
|[レッスン 12: Excel で分析する](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 分| 
|[レッスン 13: デプロイする](../tutorials/aas-lesson-13-deploy.md)|5 分|  
  
## <a name="supplemental-lessons"></a>補助レッスン  
これらのレッスンは必ずしもチュートリアルに必須のレッスンではありませんが､表形式モデルに対する高度なオーサリング機能の理解を深めるうえで有用です｡  
  
|レッスン|推定所要時間|  
|----------|------------------------------|  
|[詳細列](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 分|
|[動的セキュリティ](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 分|
|[不規則な階層](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 分| 

  
## <a name="next-steps"></a>次のステップ  
先ずは､[レッスン 1: 新しい表形式モデルを作成する](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md) を参照してください｡  
  
  
  


