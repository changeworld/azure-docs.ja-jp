---
title: "Azure Data Factory を使ってデータを増分コピーする | Microsoft Docs"
description: "これらのチュートリアルでは、ソース データ ストアからターゲット データストアにデータを増分コピーする方法について説明します。 1 つ目は、単一のテーブルからデータをコピーするものです。 "
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: 2ae6cb42685dfb227bd75f83e73dfdf646ab909f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>ソース データ ストアからターゲット データ ストアにデータを増分読み込みする

データ統合ソリューションでは、初回のフル データ読み込みの後、増分 (または差分) データを読み込む手法が広く利用されています。 このセクションの各チュートリアルでは、Azure Data Factory バージョン 2 を使用して、データを増分読み込みするさまざまな方法を紹介しています。

## <a name="delta-data-loading-using-a-watermark"></a>基準値を使用した差分データ読み込み
このケースでは、ソース データベースにおける基準値を定義します。 基準値とは、最終更新タイムスタンプやインクリメントされるキーを格納する列のことです。 差分読み込みソリューションでは、古い基準値から新しい基準値までの間に生じた変更済みのデータが読み込まれます。 このアプローチのワークフローを表したのが次の図です。 

![基準値を使用するためのワークフロー](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

具体的な手順については、次のチュートリアルを参照してください。 

- [Azure SQL Database にある単一のテーブルから Azure Blob Storage にデータを増分コピーする](tutorial-incremental-copy-powershell.md)
- [オンプレミスの SQL Server にある複数のテーブルから Azure SQL Database にデータを増分コピーする](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-using-the-change-tracking-technology"></a>Change Tracking テクノロジを使用した差分データ読み込み
Change Tracking テクノロジは、SQL Server と Azure SQL Database において、アプリケーションのための効率的な変更追跡メカニズムとなる軽量ソリューションです。 挿入、更新、削除されたデータをアプリケーションから簡単に特定することができます。 

このアプローチのワークフローを表したのが次の図です。

![Change Tracking を使用するためのワークフロー](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

具体的な手順については、次のチュートリアルを参照してください。 <br/>
[Change Tracking テクノロジを使用して Azure SQL Database から Azure Blob Storage にデータを増分コピーする](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>次のステップ
次のチュートリアルに進みます。 

> [!div class="nextstepaction"]
>[Azure SQL Database にある単一のテーブルから Azure Blob Storage にデータを増分コピーする](tutorial-incremental-copy-powershell.md)
