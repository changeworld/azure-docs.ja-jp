---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "67180877"
---
アクセス トークンの場合と同様、Azure AD トークンが設定されていない場合は、TokenRequired イベントを処理するか、デリゲート プロトコルに tokenRequired メソッドを実装する必要があります。

イベント引数のプロパティを設定することで、イベントを同期的に処理できます。
