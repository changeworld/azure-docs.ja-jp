---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "67180896"
---
## <a name="pause-reset-or-stop-the-session"></a>セッションの一時停止、リセット、停止

セッションを一時的に停止するために、`Stop()` を呼び出すことができます。 そうすることで、ProcessFrame() を呼び出した場合でも、あらゆるウォッチャーや環境処理を停止できます。 次に `Start()` を呼び出して、処理を再開できます。 再開するとき、既にセッションでキャプチャされた環境データは保持されます。
