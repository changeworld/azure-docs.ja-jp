---
title: IoT コネクタとTeams通知 - Azure Healthcare API
description: この記事では、IoT コネクタを使用して通知を送信する方法Teamsします
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 5f2cb9c9a4e4ef20e489c36a92285a6f96f74282
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894814"
---
# <a name="iot-connector-and-microsoft-teams-notifications"></a>IoT コネクタとMicrosoft Teams通知

この記事では、IoT コネクタの使用と通知のMicrosoft Teamsについて確認します。

## <a name="iot-connector-and-teams-notifications-reference-architecture"></a>IoT コネクタとTeams通知の参照アーキテクチャ

IoT コネクタ、高速ヘルスケア相互運用性リソース (FHIR&#174;) サービス、Teamsを組み合わせると、複数の医療ソリューションを有効にできます。 

以下に示すのは、IoT コネクタ、FHIR、Teams Patient App を有効にするための通知の概念アーキテクチャをTeamsする IoT コネクタです。 

また、ダッシュボードをPower BIクライアント内に埋め込Microsoft Teamsすることもできます。 Microsoft Team でのデータの埋め込みのPower BIについては、こちらを参照[してください](/power-bi/collaborate-share/service-embed-report-microsoft-teams)。

:::image type="content" source="media/iot-concepts/iot-connector-teams.png" alt-text="IoT コネクタと ioT コネクタのTeams。" lightbox="media/iot-concepts/iot-connector-teams.png":::

の IoT コネクタは、場所、データ センター、クラウドに関係なく、ほとんどの IoT デバイスまたはゲートウェイから IoT データを取り込む可能性があります。 

デバイスまたはゲートウェイの接続を支援Azure IoTサービスを使用してください。

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-teams.png" alt-text="IoT コネクタと IoT コネクタのIoT Hub。" lightbox="media/iot-concepts/iot-connector-iot-hub-teams.png":::

一部のソリューションでは、Azure IoT Hub の代Azure IoT Central を使用できます。

Azure IoTEdge を IoT Hub と一緒に使用して、デバイスやデバイス内接続用のオンプレミス のエンド ポイントを作成できます。

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-teams.png" alt-text="IoT コネクタと ioT コネクタのIoT Edge。" lightbox="media/iot-concepts/iot-connector-iot-edge-teams.png":::

## <a name="next-steps"></a>次のステップ

この記事では、IoT コネクタと通知の統合Teamsについて学習しました。 IoT コネクタの概要については、以下を参照してください。

>[!div class="nextstepaction"]
>[IoT コネクタの概要](iot-connector-overview.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
