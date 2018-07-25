---
title: デバイス シミュレーション ソリューションで既存の IoT Hub を使用する - Azure | Microsoft Docs
description: この記事では、デバイス シミュレーション ソリューション アクセラレータを構成して既存の IoT Hub を使用する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967513"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>デバイス シミュレーション ソリューション アクセラレータで既存の IoT Hub を使用する

デバイス シミュレーション ソリューション アクセラレータをプロビジョニングするとき、シミュレーションで使用するソリューション アクセラレータのリソース グループに IoT Hub をデプロイすることもできます。

オプションの IoT Hub をデプロイしない場合、実行するすべてのシミュレーションのために独自のハブを使用する必要があります。 オプションの IoT Hub をデプロイする場合、このオプションのハブまたは独自のハブのいずれかを選択して使用できます。

IoT Hub がない場合は、いつでも [Azure Portal](https://portal.azure.com) で新しく作成できます。

既存の IoT Hub を使用するには、**iothubowner** 共有アクセス ポリシーに対する接続文字列が必要です。 この接続文字列は、[Azure Portal](https://portal.azure.com) から取得できます。

1. ポータルのハブの構成ページで、**[共有アクセス ポリシー]** をクリックします。
1. **iothubowner** をクリックします。
1. プライマリ キーまたはセカンダリの接続文字列をコピーします。

[![接続文字列を取得する](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

シミュレーションを構成するときは、コピーした接続文字列を使用します。

[![シミュレーションを構成する](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>次の手順

このハウツー ガイドでは、シミュレーションで既存の IoT Hub を使用する方法について学習しました。 次に、シミュレーション用の[カスタム デバイス モデルを構成する](iot-accelerators-device-simulation-custom-model.md)方法について学習します。
