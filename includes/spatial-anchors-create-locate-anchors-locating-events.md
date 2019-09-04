---
ms.openlocfilehash: f478956c3a220ea4435c4f6e8b1096a1e9fdcadf
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014786"
---
監視が作成された後、要求されたすべてのアンカーに対して `AnchorLocated` イベントが発生します。 このイベントは、アンカーが探知されたとき、またはアンカーを探知できなかった場合に発生します。 このような状況が発生した場合は、その理由が状態に示されます。 監視のすべてのアンカーが (探知されたかどうかを問わず) 処理された後、`LocateAnchorsCompleted` イベントが発生します。 識別子の上限は、Watcher 1 つあたり 35 個です。 
