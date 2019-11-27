---
title: ID の構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid モジュールの ID を構成する
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991820"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Event Grid モジュールの ID を構成する

この記事では、Event Grid モジュールで可能な ID 構成の例を示します。 既定では、Event Grid モジュールは、IoT セキュリティ デーモンによって構成された ID 証明書を提示します。 ID 証明書は、Event Grid モジュールによって、イベントを配信するときの発信呼び出しで提示されます。 その後、Event Grid イベントのサブスクライバーは、イベントを受け取る前に、そのモジュールが本当にイベントを送信した Event Grid モジュールであるかどうかを検証できます。

可能なすべての構成については、「[セキュリティと認証](security-authentication.md)」のガイドを参照してください。

## <a name="always-present-identity-certificate"></a>常に ID 証明書を提示する
発信呼び出しで ID 証明書を常に提示する構成の例を次に示します。 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>ID 証明書を提示しない
発信呼び出しで ID 証明書を提示しない構成の例を次に示します。 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
