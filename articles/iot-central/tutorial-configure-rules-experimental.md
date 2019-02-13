---
title: Azure IoT Central でルールとアクションを構成する | Microsoft Docs
description: このチュートリアルでは、作成者として Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d8a5ca6285624720e23a4986917ab5e715f6ebfa
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768015"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>チュートリアル:Azure IoT Central でデバイスのルールとアクションを構成する (新しい UI デザイン)

*この記事は、オペレーター、ビルダー、および管理者に適用されます。*

このチュートリアルでは、コネクテッド空調デバイスの温度が華氏 90&deg; 度を超えた場合にメールが送信されるルールを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * テレメトリベースのルールを作成する
> * アクションを追加する

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>前提条件

開始する前に、[アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)に関するチュートリアルを完了し、操作する **Connected Air Conditioner** デバイス テンプレートを作成しておく必要があります。

## <a name="create-a-telemetry-based-rule"></a>テレメトリベースのルールを作成する

1. 新しいテレメトリベースのルールをアプリケーションに追加するには、左側のナビゲーション メニューで **[デバイス テンプレート]** を選択します。

    ![[デバイス テンプレート] ページ](media/tutorial-configure-rules-experimental/templatespage1.png)

    前のチュートリアルで作成した **Connected Air Conditioner (1.0.0)** デバイス テンプレートが表示されます。

2. デバイス テンプレートをカスタマイズするには、前のチュートリアルで作成した **Connected Air Conditioner** テンプレートをクリックします。

3. **[ルール]** ビューでテレメトリ ベースのルールを追加するには、**[ルール]** を選択し、**[+ 新しいルール]** をクリックして、**[テレメトリ]** を選択します。

    ![[ルール] ビュー](media/tutorial-configure-rules-experimental/newrule.png)

5. ルールを定義するには、次の表の情報を使用します。

    | Setting                                      | 値                             |
    | -------------------------------------------- | ------------------------------    |
    | Name                                         | 空調機の温度アラート |
    | [Enable rule for all devices of this template]\(このテンプレートのすべてのデバイスに対してルールを有効にする\) | On                                |
    | 条件                                    | Temperature is greater than 90    |
    | 集計                                  | なし                              |

    ![温度ルールの条件](media/tutorial-configure-rules-experimental/temperaturerule.png)

    その後、 **[保存]** をクリックします。

## <a name="add-an-action"></a>アクションを追加する

ルールを定義する場合、ルールの条件が満たされたときに実行されるアクションも定義します。 このチュートリアルでは、メール通知を送信するアクションに関するルールを作成します。

1. **アクション**を追加するには、最初にルールを**保存**してから、**[Configure Telemetry Rule]\(テレメトリ ルールの構成\)** パネルを下にスクロールします。 **[アクション]** の隣にある **[+]** を選択して、**[電子メール]** を選択します。

    ![温度ルールのアクション](media/tutorial-configure-rules-experimental/addaction.png)

2. アクションを定義するには、次の表の情報を使用します。

    | Setting   | 値                          |
    | --------- | ------------------------------ |
    | ターゲット        | メール アドレス             |
    | メモ     | 空調機の温度がしきい値を超えました。 |

    > [!NOTE]
    > メール通知を受け取るには、メール アドレスが[アプリケーションのユーザー ID](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) である必要があります。また、そのユーザーが少なくとも一度アプリケーションにサインインしている必要があります。

    ![温度のアクション](media/tutorial-configure-rules-experimental/temperatureaction.png)

3. **[Save]** をクリックします。 ルールが **[ルール]** ページに表示されます。

## <a name="test-the-rule"></a>ルールをテストする

ルールは保存した後すぐに有効になります。 ルールで定義された条件が満たされると、アプリケーションによって、アクションで指定したメール アドレスにメッセージが送信されます。

![メール アクション](media/tutorial-configure-rules-experimental/email.png)

> [!NOTE]
> テストの完了後は、ルールを無効にして、受信トレイに届くアラートを停止してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * テレメトリベースのルールを作成する
> * アクションを追加する

これでしきい値に基づいたルールを定義できました。推奨される次の手順は、[オペレーターのビューのカスタマイズ](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)です。

Azure IoT Central のさまざまな種類のルールとルール定義をパラメーター化する方法の詳細については、以下を参照してください。
* [テレメトリ ルールを作成して通知を設定する](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。
* [イベント ルールを作成して通知を設定する](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。

<!-- Next tutorials in the sequence -->
