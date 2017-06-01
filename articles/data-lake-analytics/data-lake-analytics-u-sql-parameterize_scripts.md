---
title: "Azure Data Lake Analytics で U-SQL スクリプトをパラメーター化する | Microsoft Docs"
description: "Azure Data Lake Analytics で U-SQL スクリプトをパラメーター化する方法について説明します。"
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
ms.date: 05/10/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6318fa8b14d8bb7d650522e2d96a5b1417afe3df
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="parameterizing-u-sql-scripts"></a>U-SQL スクリプトのパラメーター化

U-SQL スクリプトを使用する場合、スクリプトをパラメーター化すると便利です。 パラメーターを使用すると、本文を変更せずに、個別のパラメーター値を渡してスクリプトの実行を制御できます。 一般的なシナリオでは、スクリプトによって一定期間に渡ってデータを処理する必要があるので、開発者は開始日と終了日でスクリプトをパラメーター化できます。 

スクリプトは次の 2 つの方法でパラメーター化されます。
* スクリプトの先頭部分でパラメーターの安全性を高めます。 U-SQL では、この方法をサポートしています。
* ジョブ送信 API を使用して、呼び出し元がパラメーターを送信できるようにします。 U-SQL では、この方法はサポートしていません。

## <a name="example-scripts"></a>スクリプトの例

単純な初期スクリプトを次に示します。

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    
   
このシナリオでは、`DECLARE EXTERNAL` ステートメントを追加して、``Region`` をパラメーター化します。

```
DECLARE EXTERNAL @TargetRegion = "en-us";

@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    

このスクリプトを実行すると、`en-us` リージョンの行だけが既定でフィルター処理されます。 ここまで、スクリプトでパラメーターを定義しました。 `EXTERNAL` キーワードは、`@TargetRegion` パラメーターを定義する DECLARE ステートメントを先頭に追加することで、`@TargetRegion` 値を再定義できることを示しています。

次のスクリプトは、このパラメーターの値が指定されたときに、パラメーター化されたスクリプトがどのようになるかを示しています。 このスクリプトにより、リージョンが `en-gb` である行がフィルター処理されるようになります。

```
DECLARE @TargetRegion = "en-gb";

DECLARE EXTERNAL @TargetRegion = "en-us";

@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    


## <a name="next-steps"></a>次のステップ
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure ポータルを使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

