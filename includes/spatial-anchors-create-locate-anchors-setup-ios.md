---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110713"
---
## <a name="setting-up-the-library"></a>ライブラリの設定

セッションで環境データを処理できるようにするには、Start() を呼び出します。

セッションによって発生したイベントを処理するには、セッションの `delegate` プロパティをビューのようなオブジェクトに設定します。 このオブジェクトでは SSCCloudSpatialAnchorSessionDelegate プロトコルを実装する必要があります。
