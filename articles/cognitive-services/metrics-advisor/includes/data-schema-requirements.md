---
title: データ スキーマの要件
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 33bc52d3c334919a9e93d9666a24d85e3fe158b4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377312"
---
Metrics Monitor は、時系列の異常検出、診断、分析のためのサービスです。 AI を利用したサービスであるため、使用モデルをトレーニングするためのデータが用いられます。 このサービスは、次の列を含んだ集計データのテーブルを受け取ります。

* **Measure** (必須): 数値を格納する 1 つまたは複数の列。
* **Timestamp** (省略可): 0 個または 1 個の `DateTime` 型列または `String` 型列。 この列が設定されなかった場合は、それぞれのインジェスト期間の開始時刻がタイムスタンプとして設定されます。 タイムスタンプは `yyyy-MM-ddTHH:mm:ssZ` 形式で指定します。 
  * **タイムスタンプは、メトリックの細分性と合わせる必要があります。たとえば、日単位のメトリックであれば、時、分、秒を `00:00:00`** 形式でタイムスタンプに指定する必要があります。
* **Dimension** (省略可): 列のデータ型は任意です。 大量の列と値を扱う際は、処理の対象となるディメンションの数が多くなりすぎないように注意してください。

> [!Note]
> それぞれのメトリックについて、タイムスタンプは、1 つのディメンションの組み合わせに対してメジャーごとに 1 つのみとする必要があります。 オンボードの前にデータを集計するか、クエリを使用して取り込むデータを指定してください。