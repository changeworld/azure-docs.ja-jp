---
title: Azure Data Factory マッピング データ フローの新しい分岐変換
description: Azure Data Factory マッピング データ フローの新しい分岐変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 4832cd2036f615d1e90d5e7a21c1a9832c2fa837
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387129"
---
# <a name="mapping-data-flow-union-transformation"></a>Mapping Data Flow の和集合変換



和集合では、複数のデータ ストリームの SQL Union を和集合変換からの新しい出力として使用し、これらのストリームを 1 つに結合します。 各入力ストリームのすべてのスキーマは、結合キーを必要とすることなく、データ フロー内で結合されます。

構成された各行の横の「+」アイコンを選択することにより、データ フロー内の既存の変換からのストリームとソース データの両方を含む、設定テーブルの n 個のストリームを結合できます。

![和集合変換](media/data-flow/union.png "和集合")

ここでは、複数のソース (この例では、3 つの異なるソース ファイル) の異種のメタデータを結合して、1 つのストリームにまとめることができます。

![和集合変換の概要](media/data-flow/union111.png "和集合 1")

これを行うには、追加するすべてのソースを含めて、[Union Settings]\(和集合設定\) で行を追加します。 共通の参照や結合キーは必要ありません。

![和集合変換の設定](media/data-flow/unionsettings.png "和集合の設定")

[Select transformation after your Union]\(和集合の後に変換を選択\) を設定すると、重複するフィールド、またはヘッダーのないソースからの名前のないフィールドを名前変更できます。 次の例では、[検査] をクリックして、 3 つの異なるソースからの合計 132 個の列を含む結合されたメタデータを表示します。

![和集合変換の最終設定](media/data-flow/union333.png "和集合 3")

## <a name="name-and-position"></a>名前と位置

"名前による和集合" を選択すると、各列の値は、新しい連結メタデータ スキーマで各ソースの対応する列にドロップされます。

"位置による和集合" を選択すると、各列の値は対応するそれぞれのソースの元の位置にドロップされ、以下に示すように、各ソースのデータが同じストリームに追加されたデータの新しい結合ストリームが生成されます。

![和集合の出力](media/data-flow/unionoutput.png "和集合の出力")

## <a name="next-steps"></a>次の手順

[結合](data-flow-join.md)変換や[存在](data-flow-exists.md)変換など、同様の変換について調べます。
