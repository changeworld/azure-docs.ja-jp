---
title: Azure Data Factory Mapping Data Flow の新しい分岐変換
description: Azure Data Factory Mapping Data Flow の新しい分岐変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: c7c6904d4cbfc6513f37c40803dbd79875954825
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271550"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure Data Factory Mapping Data Flow の和集合変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

和集合では、複数のデータ ストリームの SQL Union を和集合変換からの新しい出力として使用し、これらのストリームを 1 つに結合します。

![和集合変換](media/data-flow/union.png "和集合")

