---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044688"
---
### <a name="set-up-the-windows-device-portal"></a>Windows デバイス ポータルを設定する

WiFi 経由で HoloLens に接続するには、次の手順を実行します。

1. まず [HoloLens を WiFi に接続します](https://docs.microsoft.com/hololens/hololens-network)。

2. 次に、 **[設定] > [ネットワークとインターネット] > [Wi-Fi] > [詳細オプション]** でデバイスの IP アドレスを取得します。

3. PC 上の Web ブラウザーから、`https://<YOUR_HOLOLENS_IP_ADDRESS>` にアクセスします。 ブラウザーに次のメッセージが表示されます。"この Web サイトのセキュリティ証明書には問題があります"。 このメッセージが表示されるのは、デバイス ポータルに発行された証明書が自己署名証明書であるためです。 この証明書エラーを無視して続行することができます。

Windows デバイス ポータルの設定の詳細については、[こちら](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)を参照してください。
