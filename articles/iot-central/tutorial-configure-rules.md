---
title: Azure IoT Central でルールとアクションを構成する | Microsoft Docs
description: このチュートリアルでは、作成者として Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 5ac19e0e25ea3e25ede4d87776c01f8bcaea4655
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202239"
---
# <a name="2---configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>2 - Azure IoT Central でデバイスのルールとアクションを構成する

このチュートリアルでは、作成者として Microsoft Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。

このチュートリアルでは、コネクテッド空調デバイスの温度が華氏 90 度を超えた場合にメールが送信されるルールを作成します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * テレメトリベースのルールを作成する
> * アクションを追加する

## <a name="prerequisites"></a>前提条件

開始する前に、[アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type.md)に関するチュートリアルを完了し、操作する **Connected Air Conditioner** デバイス テンプレートを作成しておく必要があります。

## <a name="create-a-telemetry-based-rule"></a>テレメトリベースのルールを作成する

1. 新しいテレメトリベースのルールをアプリケーションに追加するには、左側のナビゲーション メニューで **[Device Explorer]** を選択します。

    ![Device Explorer のページ](media/tutorial-configure-rules/explorerpage.png)

    前のチュートリアルで作成した **Connected Air Conditioner (1.0.0)** デバイス テンプレートと **Connected Air Conditioner-1** デバイスが表示されます。

1. コネクテッド空調デバイスのカスタマイズを開始するには、前のチュートリアルで作成したデバイスを選択します。

    ![コネクテッド空調機のページ](media/tutorial-configure-rules/builderdevicelist.png)

1. **[ルール]** ビューでルールの追加を開始するには、**[ルール]** を選択します。

    ![[ルール] ビュー](media/tutorial-configure-rules/builderrulesview.png)

1. このチュートリアルでは、しきい値に基づいたテレメトリ ルールを追加します。 しきい値に基づいたルールの作成を開始するには、**[新しいルール]**、**[テレメトリ]** の順に選択します。

1. ルールを定義するには、次の表の情報を使用します。

    | Setting     | 値                          |
    | ----------- | ------------------------------ |
    | Name        | Air conditioner temperature    |
    | ルールの有効化 | 反対に、                             |
    | 条件   | Temperature is greater than 90 |

    ![温度ルールの条件](media/tutorial-configure-rules/buildertemperaturerule.png)

## <a name="add-an-action"></a>アクションを追加する

ルールを定義する場合、ルールの条件が満たされたときに実行されるアクションも定義します。 このチュートリアルでは、ルールによってトリガーされた通知としてメールが送信されるというアクションを追加します。

1. **アクション**を追加するには、**[Configure Telemetry Rule]\(テレメトリ ルールの構成\)** パネルまで下へスクロールし、**[アクション]** の横にある **[+]** を選択してから **[メール]** を選択します。

    ![温度ルールのアクション](media/tutorial-configure-rules/builderaddaction.png)

1. アクションを定義するには、次の表の情報を使用します。

    | Setting   | 値                          |
    | --------- | ------------------------------ |
    | ターゲット        | メール アドレス             |
    | メモ     | Temperature in air conditioner exceeded threshold. |

    > [!NOTE]
    > メール通知を受け取るには、メール アドレスが[アプリケーションのユーザー ID](howto-administer.md) である必要があります。また、そのユーザーが少なくとも一度アプリケーションにサインインしている必要があります。

    ![アプリケーション ビルダーの温度アクション](media/tutorial-configure-rules/buildertemperatureaction.png)

1. **[保存]** を選択します。 ルールが **[ルール]** ページに表示されます。

    ![アプリケーション ビルダーのルール](media/tutorial-configure-rules/builderrules.png)

## <a name="test-the-rule"></a>ルールをテストする

ルールは保存した後すぐに有効になります。 ルールで定義された条件が満たされると、アプリケーションによって、アクションで指定したメール アドレスにメッセージが送信されます。

![メール アクション](media/tutorial-configure-rules/email.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * テレメトリベースのルールを作成する
> * アクションを追加する

これでしきい値に基づいたルールを定義できました。推奨される次の手順は、[オペレーターのビューのカスタマイズ](tutorial-customize-operator.md)です。

Azure IoT Central のさまざまな種類のルールとルール定義をパラメーター化する方法の詳細については、以下を参照してください。
* [テレメトリ ルールを作成して通知を設定する](howto-create-telemetry-rules.md)。
* [イベント ルールを作成して通知を設定する](howto-create-event-rules.md)。

<!-- Next tutorials in the sequence -->