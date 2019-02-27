---
title: Azure Data Factory の Mapping Data Flow の参照変換
description: Azure Data Factory の Mapping Data Flow の参照変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 28c8f2b0005641934f7fcd9549f1cf004b206d80
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271406"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory の Mapping Data Flow の参照変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

参照を使用して、別のソースの参照データをデータ フローに追加します。 参照変換では、参照テーブルをポイントし、キー フィールドが一致する定義済みのソースが必要です。

![参照変換](media/data-flow/lookup1.png "参照")

受信するストリームのフィールドと参照元のフィールドの間で一致させるキー フィールドを選択します。 データ フロー デザイン キャンバスで、参照の右側として使用する新しいソースを先に作成しておく必要があります。

一致が見つかると、参照元から結果の行と列がデータ フローに追加されます。 データ フローの最後でシンクに書き込む、関心のあるフィールドを選択できます。
