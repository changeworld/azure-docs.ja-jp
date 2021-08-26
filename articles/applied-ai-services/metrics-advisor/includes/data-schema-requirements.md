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
ms.openlocfilehash: 32fc672bf41c35881baf082b5694d1800084889d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745959"
---
Azure Metrics Advisor は、時系列の異常検出、診断、分析のためのサービスです。 AI を利用したサービスとして、ユーザーのデータを使用して、使用するモデルをトレーニングします。 このサービスは、次の列を含んだ集計データのテーブルを受け取ります。

* **Measure** (必須): メジャーは、単位固有の基本的な用語であり、メトリックの定量化可能な値です。 数値を格納する 1 つまたは複数の列を意味します。
* **Timestamp** (省略可): 型が `DateTime` または `String` である、0 個または 1 個の列。 この列が設定されなかった場合は、タイムスタンプが各インジェスト期間の開始時刻として設定されます。 タイムスタンプを `yyyy-MM-ddTHH:mm:ssZ` の形式で指定します。 
* **Dimension** (省略可): ディメンションは、1 つまたは複数のカテゴリの値です。 これらの値を組み合わせることによって、特定の一変量時系列 (国、言語、テナントなど) が識別されます。 ディメンション列のデータ型は任意です。 大量の列と値を扱う際は、処理の対象となるディメンションの数が多くなりすぎないように注意してください。

Azure Data Lake Storage や Azure Blob Storage などのデータ ソースを使用している場合は、予期されるメトリック スキーマに合わせてデータを集計できます。 これは、これらのデータ ソースがメトリック入力としてファイルを使用するためです。

Azure SQL や Azure Data Explorer などのデータ ソースを使用している場合は、集計関数を使用して、予期されるスキーマにデータを集計できます。 これは、これらのデータ ソースで、ソースからメトリック データを取得するためのクエリの実行がサポートされているためです。

