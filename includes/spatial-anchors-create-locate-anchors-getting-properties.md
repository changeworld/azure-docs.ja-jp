---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110809"
---
アンカーがサービス上で作成された後は、アンカーの位置を更新することはできません。新しい位置を追跡するには、新しいアンカーを作成し、古いアンカーを削除する必要があります。

プロパティを更新するためにアンカーを探知する必要がない場合は、`CloudSpatialAnchor` オブジェクトをプロパティと共に返す `GetAnchorPropertiesAsync()` メソッドを使用できます。
