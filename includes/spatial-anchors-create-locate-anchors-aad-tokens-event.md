---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180877"
---
アクセス トークンの場合と同様、Azure AD トークンが設定されていない場合は、TokenRequired イベントを処理するか、デリゲート プロトコルに tokenRequired メソッドを実装する必要があります。

イベント引数のプロパティを設定することで、イベントを同期的に処理できます。
