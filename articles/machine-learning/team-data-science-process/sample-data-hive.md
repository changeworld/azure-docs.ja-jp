---
title: "Azure HDInsight Hive テーブル内のデータのサンプリング | Microsoft Docs"
description: "Azure HDInsight (Hadopop) Hive テーブルのデータをダウンサンプリングする"
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: d765c2adc8a3aa77d903490875c7f8ad622ef4d2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2017
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Azure HDInsight Hive テーブル内のデータのサンプリング
この記事では、Hive クエリを使用して Azure HDInsight Hive テーブルに格納されているデータをダウンサンプリングして、分析で管理しやすいサイズに削減する方法について説明します。 一般的に使用されている 3 つのサンプリング方法について説明します。

* 一様ランダム サンプリング
* グループごとのランダム サンプリング
* 階層サンプリング

次の**メニュー**は、さまざまなストレージ環境のデータをサンプリングする方法を説明するトピックにリンクしています。

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**データをサンプリングする理由**
分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 ダウンサンプリングすると、データの理解、探索、および特徴エンジニアリングが容易になります。 Team Data Science Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)の 1 ステップです。

## <a name="how-to-submit-hive-queries"></a>Hive クエリを送信する方法
Hive クエリは、Hadoop クラスターのヘッド ノード上の Hadoop コマンド ライン コンソールから送信できます。 これを行うには、Hadoop クラスターのヘッド ノードにログインし、Hadoop コマンド ライン コンソールを開き、そこから Hive クエリを送信します。 Hadoop コマンド ライン コンソールで Hive クエリを送信する手順については、「[Hive クエリを送信する方法](move-hive-tables.md#submit)」をご覧ください。

## <a name="uniform"></a> 一様ランダム サンプリング
一様ランダム サンプリングとは、データ セットの各行にサンプリングされる機会が均等にあるという意味です。 これは、追加のフィールド rand() を、内部の "select" クエリのデータセットに追加し、そのランダム フィールドについての条件を外部の "select" クエリに追加することで実装できます。

クエリの使用例を次に示します。

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

ここで、 `<sample rate, 0-1>` は、ユーザーがサンプリングするレコードの割合を指定しています。

## <a name="group"></a> グループごとのランダム サンプリング
カテゴリ別のデータをサンプリングする場合、カテゴリ変数の一部が何らかの値であるすべてのインスタンスを含めるか除外することができます。 この種のサンプリングは、"グループごとのサンプリング" と呼ばれます。 たとえば、NY、MA、CA、NJ、PA などの値を持つカテゴリ変数 "*State*" がある場合に、サンプリングされているかどうかにかかわらず、各州のレコードをまとめることができます。

グループごとのサンプリングを実行するサンプルのクエリは次のとおりです。

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>階層サンプリング
取得したサンプルに、母集団と同じ比率のカテゴリ別のサンプル値がある場合、ランダム サンプリングは、カテゴリ変数に関して階層化されます。 上記と同じ例で、州ごとに次の件数のデータがあるとします。NJ には 100 件のデータ、NY には 60 件のデータ、WA には 300 件のデータ。 階層のサンプリングの割合を 0.5 に指定すると、取得されるサンプルは NJ、NY、WA それぞれで 50 件、30 件、150 件となります。

クエリの使用例を次に示します。

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Hive で使用できるより高度なサンプリング方法については、「 [LanguageManual Sampling (LanguageManual のサンプリング)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)」をご覧ください。

