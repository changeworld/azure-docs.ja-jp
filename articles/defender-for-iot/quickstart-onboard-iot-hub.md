---
title: Defender for IoT エージェントベースのソリューションにオンボードする
description: Azure IoT Hub で Defender for IoT セキュリティ サービスをオンボードし、有効にする方法について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809135"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Defender for IoT エージェントベースのソリューションにオンボードする

この記事では、既存の IoT Hub 上で Defender for IoT サービスを有効にする方法について説明します。 現在 IoT Hub がない場合、作業を開始するには、「[Azure portal を使用して IoT Hub を作成する](../iot-hub/iot-hub-create-through-portal.md)」を参照してください。

Defender for IoT の IoT Hub を使用して、IoT セキュリティを管理できます。 IoT Hub にある管理ポータルでは、以下の操作を実行できます。 

- IoT Hub のセキュリティを管理します。

- IoT Hub テレメトリに基づいて、エージェントをインストールすることなく、IoT デバイスのセキュリティの基本的な管理を行う。 

- マイクロ エージェントに基づいて、IoT デバイスのセキュリティの高度な管理を行う。

> [!NOTE]
> Defender for IoT では、現在、Standard レベルの IoT Hub だけがサポートされています。

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>IoT Hub に Defender for IoT をオンボードする

すべての新しい IoT hub に対して、Defender for IoT は既定で **[有効]** に設定されます。 IoT Hub の作成プロセス中に Defender for IoT が **[有効]** に切り替えられることを確認できます。

トグルが **[有効]** に設定されていることを確認するには、以下の操作を行います。

1. Azure Portal に移動します。

1. Azure サービスの一覧から、 **[IoT Hub]** を選択します。

1. **［作成］** を選択します

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="上部のツール バーで作成ボタンを選択します。" lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. **[管理]** タブを選択し、 **[Defender for IoT]** トグルが **[有効]** に設定されていることを確認します。

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Defender for IoT のトグルが有効に設定されていることを確認します。":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Defender for IoT を既存の IoT Hub にオンボードする

デバイス ID 管理およびデバイスとクラウド間の通信パターンを監視するには、次の操作を実行してサービスを開始します。 

1. IoT Hub に移動します。 

1.  **[セキュリティの概要]**   メニューを選択します。 

1. [Secure your IoT solution]\(IoT ソリューションのセキュリティ保護\) をクリックし、オンボード フォームの記入を完了します。 


## <a name="next-steps"></a>次のステップ

次の記事に進んで、ソリューションを構成してください。

> [!div class="nextstepaction"]
> [Defender for IoT マイクロ エージェントのモジュール ツインを作成する (プレビュー)](quickstart-create-micro-agent-module-twin.md)
