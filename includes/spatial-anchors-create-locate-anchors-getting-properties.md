---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180886"
---
アンカーがサービス上で作成された後は、アンカーの位置を更新することはできません。新しい位置を追跡するには、新しいアンカーを作成し、古いアンカーを削除する必要があります。

プロパティを更新するためにアンカーを探知する必要がない場合は、`CloudSpatialAnchor` オブジェクトをプロパティと共に返す `GetAnchorPropertiesAsync()` メソッドを使用できます。
