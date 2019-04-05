---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57907703"
---
## <a name="pause-reset-or-stop-the-session"></a>セッションの一時停止、リセット、停止

セッションを一時的に停止するために、Stop() を呼び出せます。 そうすることで、ProcessFrame() を呼び出した場合でも、あらゆるウォッチャーや環境処理を停止できます。 次に Start() を呼び出して、処理を再開できます。 再開するとき、既にセッションでキャプチャされた環境データは保持されます。
