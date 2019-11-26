---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092998"
---
## <a name="configure-the-cloud-anchor-session"></a>クラウドのアンカー セッションを構成する

次に、クラウドのアンカー セッションの構成について記述してゆきます。 最初の行では、セッションに対してセンサー プロバイダーを設定します。 この時点からは、セッション中に作成したアンカーはすべて、センサーの一連の読み取り値に関連付けられます。 次に、近接デバイスの検索条件をインスタンス化し、アプリケーションの要件に合うようにそれを初期化します。 最後に、近接デバイスの条件からウォッチャーを作成することで、アンカー検索時にセンサー データを使用するようセッションに指示します。