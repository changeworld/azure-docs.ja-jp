---
title: Azure Data Factory マッピング データ フロー参照ノード
description: Data Factory データ フローによって、結合、検索、和集合用の参照ノードが追加されます
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 2e318210d96822b13f65eadeef79798b1b4595d1
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56325897"
---
# <a name="mapping-data-flow-reference-node"></a>マッピング データ フロー参照ノード

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![参照ノード](media/data-flow/referencenode.png "参照ノード")

参照ノードは、キャンバス上の別の既存ノードを参照するためにアタッチされているノードを示し、キャンバスに自動的に追加されます。 参照ノードは、別のデータ フロー変換へのポインターまたは参照として考えてください。

例: 複数のデータ ストリームの結合または和集合を行うと、データ フロー キャンバスによって、プライマリ以外の受信ストリームの名前と設定を反映する参照ノードが追加されることがあります。

参照ノードは、移動または削除することはできません。 ただし、ノードをクリックして、元の変換設定を変更することができます。

データ フローが参照ノードを追加するタイミングを制御する UI ルールは、使用可能な領域と行間の上下の間隔に基づきます。
