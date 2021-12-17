---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 2586f1a25cb5ed72ce8753efe8f10009ac85165d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571048"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>クラウド空間アンカー セッションを構成する

次に、クラウド空間アンカー セッションの構成について記述してゆきます。 最初の行では、セッションに対してセンサー プロバイダーを設定します。 この時点からは、セッション中に作成したアンカーはすべて、センサーの一連の読み取り値に関連付けられます。 次に、近接デバイスの検索条件をインスタンス化し、アプリケーションの要件に合うようにそれを初期化します。 最後に、近接デバイスの条件からウォッチャーを作成することで、アンカー検索時にセンサー データを使用するようセッションに指示します。
