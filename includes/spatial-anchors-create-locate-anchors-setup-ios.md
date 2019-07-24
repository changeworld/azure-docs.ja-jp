---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180876"
---
## <a name="setting-up-the-library"></a>ライブラリの設定

セッションで環境データを処理できるようにするには、Start() を呼び出します。

セッションによって発生したイベントを処理するには、セッションの `delegate` プロパティをビューのようなオブジェクトに設定します。 このオブジェクトでは SSCCloudSpatialAnchorSessionDelegate プロトコルを実装する必要があります。
