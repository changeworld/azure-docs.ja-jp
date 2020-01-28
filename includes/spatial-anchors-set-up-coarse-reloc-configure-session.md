---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545214"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>クラウド空間アンカー セッションを構成する

次に、クラウド空間アンカー セッションの構成について記述してゆきます。 最初の行では、セッションに対してセンサー プロバイダーを設定します。 この時点からは、セッション中に作成したアンカーはすべて、センサーの一連の読み取り値に関連付けられます。 次に、近接デバイスの検索条件をインスタンス化し、アプリケーションの要件に合うようにそれを初期化します。 最後に、近接デバイスの条件からウォッチャーを作成することで、アンカー検索時にセンサー データを使用するようセッションに指示します。