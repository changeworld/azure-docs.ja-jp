---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180886"
---
アンカーがサービス上で作成された後は、アンカーの位置を更新することはできません。新しい位置を追跡するには、新しいアンカーを作成し、古いアンカーを削除する必要があります。

プロパティを更新するためにアンカーを探知する必要がない場合は、`CloudSpatialAnchor` オブジェクトをプロパティと共に返す `GetAnchorPropertiesAsync()` メソッドを使用できます。
