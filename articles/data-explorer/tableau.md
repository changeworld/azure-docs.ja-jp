---
title: Azure Data Explorer ODBC コネクタを使用して Tableau データを視覚化する
description: この記事では、Azure Data Explorer 接続への Open Database Connectivity (ODBC) 接続を使用して、Tableau でデータを視覚化する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562447"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Azure Data Explorer からのデータを Tableau で視覚化する

 [Tableau](https://www.tableau.com/) は、ビジネス インテリジェンス向けのビジュアル分析プラットフォームです。 Tableau から Azure Data Explorer に接続して、サンプル クラスターからデータを取り込むには、SQL Server Open Database Connectivity (ODBC) ドライバーを使用します。 

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* SQL Server ODBC ドライバーを使用して、[ODBC を使って Azure Data Explorer に接続すること](connect-odbc.md) (Tableau から Azure Data Explorer に接続するため)。 

* Tableau Desktop (完全または[試用](https://www.tableau.com/products/desktop/download)版)。

* StormEvents サンプル データを含むクラスター。 詳細については、[Azure Data Explorer クラスターとデータベースの作成](create-cluster-database-portal.md)に関するページと「[Azure データ エクスプローラーにサンプル データを取り込む](ingest-sample-data.md)」を参照してください。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Tableau でデータを視覚化する 

ODBC の構成が完了したら、サンプル データを Tableau に取り込むことができます。

1. Tableau Desktop の左側のメニューで、 **[Other Databases (ODBC)]\(その他のデータベース (ODBC)\)** を選択します。

    ![ODBC に接続する](media/tableau/connect-odbc.png)

1. **[DSN]** では、ODBC に対して作成したデータ ソースを選び、 **[サインイン]** を選択します。

    ![ODBC のサインイン](media/tableau/odbc-sign-in.png)

1. **[データベース]** では、*TestDatabase* など、サンプル クラスターのデータベースを選択します。 **[スキーマ]** では、 *[dbo]* 、 **[テーブル]** では、 *[StormEvents]* サンプル テーブルを選びます。

    ![データベースとテーブルを選択する](media/tableau/select-database-table.png)

1. これで、Tableau にサンプル データのスキーマが表示されるようになりました。 **[今すぐ更新]**  を選択して、Tableau にデータを取り込みます。

    ![データの更新](media/tableau/update-data.png)

    データがインポートされると、Tableau に次の図のようなデータの行が表示されます。

    ![結果セット](media/tableau/result-set.png)

1. これで、Azure Data Explorer から取り込んだデータに基づいて、Tableau で視覚化することができました。 詳細については、[Tableau の学習](https://www.tableau.com/learn)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer のクエリを記述する](write-queries.md)