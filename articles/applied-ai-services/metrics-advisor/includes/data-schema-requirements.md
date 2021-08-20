---
title: データ スキーマの要件
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: f00c25396405678312e4c18a345840d628c15848
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114340859"
---
Metrics Advisor は、時系列の異常検出、診断、分析のためのサービスです。 AI を利用したサービスであるため、使用モデルをトレーニングするためのデータが用いられます。 このサービスは、次の列を含んだ集計データのテーブルを受け取ります。

* **Measure** (必須): メジャーは、単位固有の基本的な用語であり、メトリックの定量化可能な値です。 数値を格納する 1 つまたは複数の列を意味します。
* **Timestamp** (省略可): 0 個または 1 個の `DateTime` 型列または `String` 型列。 この列が設定されなかった場合は、それぞれのインジェスト期間の開始時刻がタイムスタンプとして設定されます。 タイムスタンプは `yyyy-MM-ddTHH:mm:ssZ` 形式で指定します。 
* **Dimension** (省略可): ディメンションは、1 つまたは複数のカテゴリの値です。 これらの値を組み合わせることによって、国、言語、テナントなど、特定の一変量時系列が識別されます。 ディメンション列のデータ型は任意です。 大量の列と値を扱う際は、処理の対象となるディメンションの数が多くなりすぎないように注意してください。

予期されるメトリック スキーマの例を次に示します。 

<!-- ![Screenshot of metrics schema example](../media/tutorial/metric-schema.png) -->

メトリック入力としてファイルが使用される ADLS や Azure BLOB などのデータ ソースを使用している場合は、予期されるメトリック スキーマに合わせてデータを事前に集計します。 ただし、Azure SQL サーバー、Azure Data Explorer などのデータ ソースや、メトリック データを取得するためのクエリの実行をサポートするその他のソースを使用している場合は、集計関数を使用して、予期されるスキーマにデータを集計できます。
