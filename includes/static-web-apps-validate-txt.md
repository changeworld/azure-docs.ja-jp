---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/10/2021
ms.author: buhollan
ms.openlocfilehash: 89e61c1cb472f7b6bb62f1f2bd84d083e103d650
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110066118"
---
#### <a name="validate-txt-record"></a>TXT レコードの検証

1. Azure portal の _[検証と構成]_ 画面に戻ります。

この手順では、Azure によって、TXT レコードが DNS プロバイダーを使用して自動的に検証されます。 検証プロセスが完了すると、追加したドメインの横に緑色のインジケーターが表示されます。

:::image type="content" source="../articles/static-web-apps/media/custom-domain/txt-record-ready.png" alt-text="TXT レコードが検証されたことを示す緑色のインジケーター":::

_[カスタム ドメイン]_ 画面でドメインの横に緑色のインジケーターが表示されたら、2 番目の手順を完了します。これは、ルート ドメインを追加する場合には ALIAS レコードを、サブドメインを追加する場合には CNAME レコードを追加します。
