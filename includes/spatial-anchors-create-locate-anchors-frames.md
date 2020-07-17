---
ms.openlocfilehash: 505670e719e86086dbf7721b4298ec913220f928
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "76694471"
---
## <a name="provide-frames-to-the-session"></a>セッションにフレームを提供する

空間アンカー セッションは、ユーザーの周りに空白をマップすることによって機能します。 そうすることで、アンカーの場所を決定しやすくなります。 モバイル プラットフォーム (iOS と Android) には、プラットフォームの AR ライブラリからフレームを取得するためにカメラ フィードへのネイティブ呼び出しが必要です。 これに対し、HoloLens は環境を継続的にスキャンするため、モバイルの場合と異なり特定の呼び出しは必要ありません。