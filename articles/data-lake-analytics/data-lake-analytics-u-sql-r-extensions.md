---
title: "Azure Data Lake Analytics で R を使用して U-SQL スクリプトを拡張する | Microsoft Docs"
description: "U-SQL スクリプトで R コードを実行する方法"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>チュートリアル: R を使用して U-SQL の拡張を始める

R コードをデプロイする基本的な手順を以下の例で示します。
* REFERENCE ASSEMBLY ステートメントを使用して U-SQL スクリプトの R 拡張機能を有効にします。
* REDUCE 操作を使用してキーの入力データをパーティション化します。
* U-SQL 用の R 拡張機能には、レジューサ (Extension.Python.Reducer) が組み込まれており、レジューサに割り当てられている各頂点で R コードを実行します。 
* それぞれ inputFromUSQL と outputToUSQL という名前の専用の名前付きデータ フレームを使用して、USQL と R の間でデータをやり取りします。入出力データ フレーム識別子の名前は固定されています (つまり、ユーザーがこれらの入出力データ フレーム識別子の定義済みの名前を変更することはできません)。


--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-r-integrates-with-u-sql"></a>R と U-SQL を統合する方法

### <a name="datatypes"></a>データ型
* R データ フレームと U-SQL の間では、U-SQL の文字列と数値列は現状のまま変換されます (サポートされる型: double、string、bool、integer、byte)。
* 因子データ型は U-SQL ではサポートされていません。
* byte[] は、base64 でエンコードされた文字列としてシリアル化する必要があります。
* U-SQL で R の入力データ フレームを作成するか、レジューサ パラメーター stringsAsFactors を true に設定することによって、U-SQL の文字列を R コードの因子に変換できます。

### <a name="schemas"></a>スキーマ
* U-SQL データセットでは、重複する列名を持つことはできません。
* U-SQL データセットの列名は文字列でなければなりません。
* 列名は、U-SQL と R スクリプトで同じである必要があります。
* 読み取り専用の列を出力データ フレームに含めることはできません。 読み取り専用の列は、UDO の出力スキーマに含まれている場合、U-SQL テーブルに自動的に戻されるためです。

## <a name="next-steps"></a>次のステップ
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する](data-lake-analytics-use-window-functions.md)

