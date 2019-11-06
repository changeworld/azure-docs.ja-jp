---
title: Azure Data Factory マッピング データ フローの条件分割変換
description: Azure Data Factory Data Flow の条件分割変換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d06b5b86737d0940930a3ccea3b6d65be0a802f9
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387903"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>データ フローの条件分割変換のマッピング



![条件分割ツールボックス](media/data-flow/conditionalsplit2.png "条件分割ツールボックス")

条件分割変換では、データ行をデータの内容に応じた別のストリームにルーティングできます。 条件分割変換の実装は、プログラミング言語の CASE 決定構造と同様です。 この変換では式を評価し、その結果に基づいて、データ行を指定されたストリームに送ります。 この変換には既定の出力も用意されているので、行が式に一致しない場合は既定の出力に送られます。

![条件分割](media/data-flow/conditionalsplit1.png "条件分割のオプション")

## <a name="multiple-paths"></a>複数のパス

条件をさらに追加するには、下部の構成ウィンドウで [ストリームの追加] を選択し、式ビルダーのテキスト ボックス内をクリックして式を作成します。

![条件分割マルチ](media/data-flow/conditionalsplit3.png "条件分割マルチ")

## <a name="next-steps"></a>次の手順

条件分割で使用される一般的なデータ フロー変換:[結合変換](data-flow-join.md)、[参照変換](data-flow-lookup.md)、[選択変換](data-flow-select.md)
