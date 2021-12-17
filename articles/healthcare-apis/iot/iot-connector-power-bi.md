---
title: IoT コネクタ Microsoft Power BI-Azure の医療 api
description: この記事では、IoT コネクタと Power BI の使用方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: b099a085041664ed00788720eb3245dd9518c020
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315408"
---
# <a name="iot-connector-and-microsoft-power-bi"></a>IoT コネクタと Microsoft Power BI

この記事では、IoT コネクタと Microsoft Power Business インテリジェンス (BI) の使用について説明します。

## <a name="iot-connector-and-power-bi-reference-architecture"></a>IoT コネクタと Power BI リファレンスアーキテクチャ

以下の参照アーキテクチャは、Microsoft クラウドサービスを使用して、IoMT (医療) のインターネット () および高速医療相互運用性リソース (fhir&#174;) のデータに基づいて Power BI できるようにするための基本的なコンポーネントを示しています。 

Microsoft Teams クライアント内に Power BI ダッシュボードを埋め込んで、ケアチームの連携を強化することもできます。 Teams に Power BI を埋め込む方法の詳細については、[こちら](/power-bi/collaborate-share/service-embed-report-microsoft-teams)を参照してください。

:::image type="content" source="media/iot-concepts/iot-connector-power-bi.png" alt-text="IoT コネクタと Power BI のスクリーンショット。" lightbox="media/iot-concepts/iot-connector-power-bi.png":::

IoT コネクタは、ほとんどの IoT デバイスまたはゲートウェイから、場所、データセンター、またはクラウドに関係なく、IoT データを取り込むことができます。 

デバイス/ゲートウェイ接続をサポートするために Azure IoT サービスを使用することをお勧めします。

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-power-bi.png" alt-text="IoT コネクタ、IoT Hub、Power BI のスクリーンショット。" lightbox="media/iot-concepts/iot-connector-iot-hub-power-bi.png":::

一部のソリューションでは、Azure IoT ハブの代わりに Azure IoT Central を使用できます。

Azure IoTエッジを IoT Hub と共に使用して、デバイスやデバイス内接続のためのオンプレミスエンドポイントを作成できます。

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-power-bi.png" alt-text="IoT コネクタ、IoT Hub、IoT Edge、および Power BI のスクリーンショット。" lightbox="media/iot-concepts/iot-connector-iot-edge-power-bi.png":::

## <a name="next-steps"></a>次のステップ

この記事では、IoT コネクタと Power BI 統合について学習しました。 IoT コネクタの概要については、「」を参照してください。

>[!div class="nextstepaction"]
>[IoT コネクタの概要](iot-connector-overview.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
