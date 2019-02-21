---
title: Azure Data Factory Mapping Data Flow の検査とデータのプレビュー
description: Azure Data Factory Mapping Data Flow には、データ フローのメタデータ ([Inspect](検査)) とデバッグ モード時のデータのプレビュー ([Data Preview](データのプレビュー)) が表示されるウィンドウがあります。
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: e7d03ecd8ea4aecf1e0cfdd02d3b9bc5300abe6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212540"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Azure Data Factory Mapping Data Flow 変換の [Inspect]\(検査\) タブ

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![[Inspect]\(検査\) ウィンドウ](media/data-flow/agg3.png "[Inspect]\(検査\) ウィンドウ")

[Inspect]\(検査\) ウィンドウには、変換するデータ ストリームのメタデータのビューが表示されます。 列数、変更された列、追加された列、データ型、列の順序、および列の参照を確認できます。 [Inspect]\(検査\) は、メタデータの読み取り専用ビューです。 [Inspect]\(検査\) ウィンドウでメタデータを表示するためにデバッグ モードを有効にする必要はありません。

変換を使ってデータの形状を変更すると、メタデータの変更が [Inspect]\(検査\) ウィンドウに反映されます。 ソースの変換に定義済みのスキーマがない場合、メタデータは [Inspect]\(検査\) ウィンドウに表示されません。 スキーマの誤差シナリオでは、メタデータがないことは一般的です。

![[Data Preview]\(データのプレビュー\)](media/data-flow/datapreview.png "[Data Preview]\(データのプレビュー\)")

[Data Preview]\(データのプレビュー\) は、変換中のデータを読み取り専用で表示するウィンドウです。 変換と式の出力を表示して、データ フローを確認することができます。 データのプレビューを表示するには、デバッグ モードをオンにする必要があります。 [Data Preview]\(データのプレビュー\) グリッドの列をクリックすると、右側に次のパネルが表示されます。 ポップアウト パネルには、選択した各列に関するプロファイルの統計情報が表示されます。

![[Data Preview]\(データのプレビュー\) のグラフ](media/data-flow/chart.png "[Data Preview]\(データのプレビュー\) のグラフ")

