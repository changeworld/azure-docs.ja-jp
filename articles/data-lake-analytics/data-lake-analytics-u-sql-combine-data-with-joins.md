---
title: "Azure Data Lake Analytics で U-SQL の JOIN を使用して行セットを結合する | Microsoft Docs"
description: "U-SQL の JOIN 演算子を使用して Azure Data Lake Analytics で行セットを結合する方法について説明します。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5ffc7116812e9f18f972eac2daff7935b00891f1
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---
# <a name="combine-rowsets-with-u-sql-joins"></a>U-SQL の JOIN を使用して行セットを結合する

U-SQL ではテーブルだけでなく、すべての行セット (ファイルから生成されたものでも) を結合するために、INNER JOIN、LEFT/RIGHT/FULL OUTER JOIN、SEMI JOIN などの一般的な結合演算子が提供されます。

次のスクリプトでは、``@searchlog`` と広告インプレッション ログを結合し、指定日のクエリ文字列の広告を示します。

```
@adlog =
    EXTRACT UserId int,
            Ad string,
            Clicked int
    FROM "/Samples/Data/AdsLog.tsv"
    USING Extractors.Tsv();

@join =
    SELECT a.Ad, s.Query, s.Start AS Date
    FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                    ON a.UserId == s.UserId
    WHERE a.Clicked == 1;

OUTPUT @join   
    TO "/output/Searchlog-join.csv"
    USING Outputters.Csv();
```

U-SQL でサポートされるのは、ANSI 準拠の結合構文である Rowset1 JOIN Rowset2 ON 述語のみです。 FROM Rowset1, Rowset2 WHERE 述語の古い構文はサポートされて_いません_。
JOIN の述語は、式のない等価結合である必要があります。 式を使用する場合は、前述の行セットの select 句に追加します。 別の比較を実行する場合は、WHERE 句に移動できます。

### <a name="next-steps"></a>次のステップ
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する](data-lake-analytics-use-window-functions.md)


