---
title: Azure Data Factory の Mapping Data Flow の列パターン
description: Azure Data Factory の Mapping Data Flows の列パターンを使用して一般化されたデータ変換パターンを作成する
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 076c3318a68a50e6bd1b4f9f2a4a4b9a034533c6
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68346575"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Azure Data Factory の Mapping Data Flow の列パターン

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory のデータ フロー変換によっては、"列パターン" の概念がサポートされる場合があります。この場合は、ハードコーディングされた列名ではなく、パターンに基づいて、テンプレートの列を作成できます。 式ビルダー内でこの機能を使用すると、変換する列に一致するパターンを定義できます。特定の正確なフィールド名は不要です。 パターンが便利なのは、受信ソース フィールドが頻繁に変更されるときです。特に、テキスト ファイルや NoSQL データベースの列が変更される場合に便利です。 この状態は、"スキーマの誤差" と呼ばれることがあります。

![列パターン](media/data-flow/columnpattern2.png "列パターン")

列パターンは、スキーマの誤差シナリオと一般的なシナリオの両方を扱う場合に便利です。 各列名が完全にわからない状況で役立ちます。 列名と列のデータ型に対してパターン マッチングを行い、`name`  &  `type` パターンに一致するデータ ストリーム内の任意のフィールドに対してその操作を実行する変換の式を作成できます。

パターンを受け入れる変換に式を追加するときは、[Add Column Pattern]\(列パターンの追加\) を選択します。 列パターンを使用すると、スキーマの誤差の列のパターンの一致が可能です。

テンプレートの列パターンを作成するときは、入力データ ストリームからの一致した各フィールドへの参照を表すために、式内で `$$` を使用します。

式ビルダーの正規表現のいずれかの関数を使用する場合は、その後に $1、$2、$3 ... を使用して、正規表現の式から一致したサブパターンを参照できます。

列パターンのシナリオの例では、一連の受信フィールドで SUM が使用されています。 集計の SUM 計算は、集計変換内にあります。 一致したすべてのフィールド型のうち、"integer" に一致するフィールド型に対して SUM を使用し、$$ を使用して式内の各一致を参照します。

## <a name="match-columns"></a>列の一致
![列パターンの種類](media/data-flow/pattern2.png "パターンの種類")

列に基づくパターンを作成するには、列の名前、型、ストリーム、または位置で一致させることができ、これらを式関数と正規表現と組み合わせて使用できます。

![列の位置](media/data-flow/position.png "列の位置")

## <a name="next-steps"></a>次の手順
ADF Mapping Data Flow の[式言語](https://aka.ms/dataflowexpressions)に関する記事で、データ変換の詳細を確認します。
