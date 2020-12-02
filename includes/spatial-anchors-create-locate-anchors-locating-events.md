---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993082"
---
監視が作成された後、要求されたすべてのアンカーに対して `AnchorLocated` イベントが発生します。 このイベントは、アンカーが探知されたとき、またはアンカーを探知できなかった場合に発生します。 このような状況が発生した場合は、その理由が状態に示されます。 監視のすべてのアンカーが (探知されたかどうかを問わず) 処理された後、`LocateAnchorsCompleted` イベントが発生します。 識別子の上限は、Watcher 1 つあたり 35 個です。 
