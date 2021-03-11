---
title: Defender for IoT マイクロ エージェントのモジュール ツインを作成する
titleSuffix: Azure Defender for IoT
description: 新しいデバイス用に個別の DefenderIotMicroAgent モジュール ツインを作成する方法について説明します。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: ea82fea89a9e81e66be6b3060aad067d3ceb8f5f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123005"
---
# <a name="create-a-defender-iot-micro-agent-module-twin"></a>Defender for IoT マイクロ エージェントのモジュール ツインを作成する 

新しいデバイス用に個別の  **DefenderIotMicroAgent** モジュール ツインを作成できます。 IoT Hub 内のすべてのデバイスに対して、モジュール ツインを一括作成することもできます。 

## <a name="device-twins"></a>デバイス ツイン 

Azure に構築された IoT ソリューションでは、デバイス ツインが、デバイスの管理とプロセスの自動化の両方で主要な役割を果たします。 

Defender for IoT には、既存の IoT デバイス管理プラットフォームと完全に統合する機能があります。 完全な統合により、デバイスのセキュリティの状態を管理し、既存のすべてのデバイス制御機能を使用できるようになります。 統合は、IoT Hub ツイン メカニズムを利用することによって実現されます。 

Azure IoT Hub の [デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md) の概念の詳細について学習してください。 

## <a name="security-module-twins"></a>セキュリティ モジュール ツイン 

Defender for IoT では、各デバイスに対してセキュリティ モジュール ツインが使用されます。 セキュリティ モジュール ツインでは、ソリューション内の特定のデバイスごとに、デバイスのセキュリティに関連するすべての情報が保持されます。 デバイスのセキュリティのプロパティは、通信の安全性を高め、更新を可能にし、必要なリソースが少なくて済むメンテナンスを可能にするために、専用のセキュリティ モジュール ツインを通じて構成されます。 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>DefenderIotMicroAgent モジュール ツインについて 

Azure に組み込まれる IoT ソリューションでは、デバイス ツインが、デバイスの管理とプロセスの自動化の両方で主要な役割を果たします。

Defender for IoT には、既存の IoT デバイス管理プラットフォームと完全に統合するための機能があり、デバイスのセキュリティ状態を管理したり、既存のデバイス制御機能を利用したりすることができます。 IoT Hub ツイン メカニズムを使用することで、Defender for IoT を統合できます。  

Azure IoT Hub でのモジュール ツインの一般的な概念の詳細については、 [IoT Hub モジュール ツイン](../iot-hub/iot-hub-devguide-module-twins.md)に関するページを参照してください。

Defender for IoT では、モジュール ツイン メカニズムを利用して、各デバイス用の `DefenderIotMicroAgent` というセキュリティ モジュール ツインが維持されます。 

Defender for IoT の機能を最大限に活用するには、サービス内のすべてのデバイスに対してセキュリティ モジュール ツインを作成、構成、および使用する必要があります。 

## <a name="create-defenderiotmicroagent-module-twin"></a>DefenderIotMicroAgent モジュール ツインの作成 

**DefenderIotMicroAgent** モジュール ツインは、各デバイスの特定の構成が含まれるように各モジュール ツインを手動で編集して作成できます。 

デバイス用の新しい  **DefenderIotMicroAgent** モジュール ツインを手動で作成するには、以下の操作を実行します。 

1. IoT Hub で、セキュリティ モジュール ツインの作成対象のデバイスを探して選択します。 

1.  **[モジュール ID の追加]** を選択します。 

1.  **[モジュール ID 名]**   フィールドに、「 `DefenderIotMicroAgent`」と入力します。 

1.  **[保存]** を選択します。 

## <a name="verify-the-creation-of-a-module-twin"></a>モジュール ツインの作成の確認 

特定のデバイスのセキュリティ モジュール ツインが存在するかどうかを確認するには、以下の操作を行います。 

1. Azure IoT Hub で、 **[エクスプローラー]**   メニューの  **[IoT デバイス]**   を選択します。 

1. デバイス ID を入力するか、 **[デバイスのクエリ]** フィールドのオプションを選択して、 **[デバイスのクエリ]** を選択します。  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="デバイスの一覧を取得するために、[デバイスのクエリ] を選択します。":::

1. デバイスを選択し、 **[デバイスの詳細]** ページを開きます。 

1.  **[モジュール ID]**   メニューを選択し、デバイスに関連付けられているモジュール ID の一覧に  **DefenderIotMicroAgent** モジュールがあることを確認します。  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="タブで [モジュール ID] を選択します。":::

## <a name="next-steps"></a>次の手順 

次の記事に進んで、[セキュリティに関する推奨事項を調査する](quickstart-investigate-security-recommendations.md)方法について学びましょう。
