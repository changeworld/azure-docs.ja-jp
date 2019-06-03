---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110689"
---
アクセス トークンの場合と同様、Azure AD トークンが設定されていない場合は、TokenRequired イベントを処理するか、デリゲート プロトコルに tokenRequired メソッドを実装する必要があります。

イベント引数のプロパティを設定することで、イベントを同期的に処理できます。
