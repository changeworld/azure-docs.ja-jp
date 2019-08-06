---
title: Azure Data Factory の Mapping Data Flow の選択変換
description: Azure Data Factory の Mapping Data Flow の選択変換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314219"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory の Mapping Data Flow の選択変換
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

この変換は、列を選択する (行数を減らす) ため、列とストリームの名前をエイリアス化するため、および列の並べ替えを行うために使用します。

## <a name="how-to-use-select-transformation"></a>選択変換の使用方法
選択変換では、ストリーム全体またはそのストリーム内の列をエイリアス化して別の名前 (エイリアス) を割り当てておき、それらの新しい名前をデータ フロー内で参照できるようにします。 この変換は、自己結合シナリオで役に立ちます。 ADF の Data Flow 内で自己結合を実装する方法は、ストリームを指定し、"新しいブランチ" を分岐させ、その直後に "選択" 変換を追加することです。 これで、そのストリームには、元のストリームに結合するために使用できる新しい名前が付けられます (自己結合が作成されます)。

![自己結合](media/data-flow/selfjoin.png "自己結合")

上の図では、選択変換が一番上にあります。 これは、元のストリームを "OrigSourceBatting" にエイリアス化しています。 下部の強調表示されている結合変換で、この選択エイリアス ストリームを右側の結合として使用して、内部結合の右側と左側の両方で同じキーを参照できるようになっていることがわかります。

選択変換は、データ フローから列を選択解除する方法としても使用できます。 たとえば、シンクに 6 つの列が定義されているが、特定の 3 つの列のみを選択して変換した後、シンクにフローする場合は、選択変換を使用してこれら 3 つの列のみを選択できます。

> [!NOTE]
> 特定の列のみを選択するには、[すべて選択] をオフに切り替える必要があります。

![選択変換](media/data-flow/select001.png "選択のエイリアス")

## <a name="options"></a>オプション
* "選択" 変換の既定の設定は、受信するすべての列を処理し、それらの元の名前を保持することです。 選択変換の名前を設定することで、ストリームをエイリアス化できます。
* 個々の列をエイリアス化する場合は、[すべて選択] をオフにし、下部にある列マッピングを使用します。
* 入力または出力メタデータから重複している列を排除するには、[Skip Duplicates]\(重複をスキップ\) を選択します。

![重複をスキップ](media/data-flow/select-skip-dup.png "重複をスキップ")

## <a name="next-steps"></a>次の手順
* 選択を使用して列の名前変更、並べ替え、およびエイリアス化を実行した後、[シンク変換](data-flow-sink.md)を使用してデータをデータストアに格納します。
