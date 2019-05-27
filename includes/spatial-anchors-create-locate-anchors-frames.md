---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110732"
---
## <a name="provide-frames-to-the-session"></a>セッションにフレームを提供する

空間アンカー セッションは、ユーザーの周りに空白をマップすることによって機能します。 そうすることで、アンカーの場所を決定しやすくなります。 モバイル プラットフォーム (iOS と Android) には、プラットフォームの AR ライブラリからフレームを取得するためにカメラ フィードへのネイティブ呼び出しが必要です。 これに対し、Hololens は環境を継続的にスキャンするため、Mobile の場合と異なり特定の呼び出しは必要ありません。