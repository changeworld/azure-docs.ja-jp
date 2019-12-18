---
title: クイックスタート - Azure IoT Central でルールとアクションを構成する
description: このクイックスタートでは、作成者として Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: f81171d0f545bbf7f903d54bee761817e9451d9d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706717"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>クイック スタート:Azure IoT Central でデバイスのルールとアクションを構成する (プレビュー機能)

*この記事は、オペレーター、ビルダー、および管理者に適用されます。*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

このクイックスタートでは、環境センサー デバイスの温度が華氏 90&deg; を超えた場合にメールを送信するルールを作成します。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、「[Azure IoT Central アプリケーションを作成する](./quick-deploy-iot-central.md)」と「[シミュレーションされたデバイスを IoT Central アプリケーションに追加する](./quick-create-pnp-device.md)」の 2 つのクイックスタートを完了して、作業に使用する**環境センサー** デバイス テンプレートを作成する必要があります。

## <a name="create-a-telemetry-based-rule"></a>テレメトリベースのルールを作成する

1. 新しいテレメトリベースのルールを対象のアプリケーションに追加するには、左側のペインで **[規則]** を選択します。

1. 新しいルールを作成するには、 **[+ 新規]** を選択します。

1. ルール名として「**Environmental temperature**」と入力します。

1. **[ターゲット デバイス]** セクションで、デバイス テンプレートとして **[Environment Sensor]** を選択します。 このオプションは、デバイス テンプレートの種類によってルールが適用されるデバイスをフィルター処理します。 **[+ フィルター]** を選択して、さらにフィルター条件を追加できます。

1. **[条件]** セクションで、ルールをトリガーする条件を定義します。 次の情報を使用して、温度テレメトリに基づいて条件を定義します。

    | フィールド                                        | 値                             |
    | -------------------------------------------- | ------------------------------    |
    | Measurement                                  | 気温                       |
    | Operator                                     | が次の値より大きい                   |
    | 値                                        | 90                                |

    さらに条件を追加するには、 **[+ 条件]** を選択します。

    ![ルール条件を作成する](./media/quick-configure-rules/condition.png)

1. ルールがトリガーされたときに実行するメール アクションを追加するには、 **[+ 電子メール]** を選択します。

1. 次の表の情報を使用して、アクションを定義します。

    | Setting   | 値                                             |
    | --------- | ------------------------------------------------- |
    | Display name | オペレーターの電子メール アクション                          |
    | ターゲット        | メール アドレス                                |
    | メモ     | 環境温度がしきい値を超えました。 |

    > [!NOTE]
    > メール通知を受け取るには、メール アドレスが[アプリケーションのユーザー ID](howto-administer.md) である必要があります。また、そのユーザーが少なくとも一度アプリケーションにサインインしている必要があります。

    ![ルール アクションを作成する](./media/quick-configure-rules/action.png)

1. **[保存]** を選択します。 ルールが **[ルール]** ページに表示されます。

## <a name="test-the-rule"></a>ルールをテストする

ルールは保存した後すぐに有効になります。 ルールで定義された条件が満たされると、アプリケーションによって、アクションで指定したメール アドレスにメッセージが送信されます。

> [!NOTE]
> テストの完了後は、ルールを無効にして、受信トレイに届くアラートを停止してください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、次の方法について説明しました。

* テレメトリベースのルールを作成する
* アクションを追加する

アプリケーションに接続されているデバイスの監視の詳細については、以下のクイックスタートをご覧ください。

> [!div class="nextstepaction"]
> [Azure IoT Central を使用してデバイスを監視する](quick-monitor-devices.md)。
