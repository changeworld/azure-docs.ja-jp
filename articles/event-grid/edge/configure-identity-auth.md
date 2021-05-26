---
title: ID の構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid モジュールの ID を構成する
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: f6449866d47b20d22912e3803ac1737528aa1dbc
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370602"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Event Grid モジュールの ID を構成する

この記事では、Edge で Grid 用の ID を構成する方法について示します。 既定では、Event Grid モジュールによって、IoT セキュリティ デーモンによって構成されたように、その ID 証明書が提示されます。 Edge の Event Grid では、イベントを配信するときに、その発信呼び出しで ID 証明書を提示します。 その後、サブスクライバーは、それが受理前にイベントを送信した Event Grid モジュールであることを検証できます。

可能なすべての構成については、「[セキュリティと認証](security-authentication.md)」のガイドを参照してください。

## <a name="always-present-identity-certificate"></a>常に ID 証明書を提示する
発信呼び出しで ID 証明書を常に提示する構成の例を次に示します。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>ID 証明書を提示しない
発信呼び出しで ID 証明書を提示しない構成の例を次に示します。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
