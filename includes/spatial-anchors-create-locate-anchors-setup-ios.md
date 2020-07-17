---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "67180876"
---
## <a name="setting-up-the-library"></a>ライブラリの設定

セッションで環境データを処理できるようにするには、Start() を呼び出します。

セッションによって発生したイベントを処理するには、セッションの `delegate` プロパティをビューのようなオブジェクトに設定します。 このオブジェクトでは SSCCloudSpatialAnchorSessionDelegate プロトコルを実装する必要があります。
