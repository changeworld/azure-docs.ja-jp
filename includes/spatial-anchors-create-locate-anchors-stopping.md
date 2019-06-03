---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110492"
---
## <a name="pause-reset-or-stop-the-session"></a>セッションの一時停止、リセット、停止

セッションを一時的に停止するために、`Stop()` を呼び出すことができます。 そうすることで、ProcessFrame() を呼び出した場合でも、あらゆるウォッチャーや環境処理を停止できます。 次に `Start()` を呼び出して、処理を再開できます。 再開するとき、既にセッションでキャプチャされた環境データは保持されます。
