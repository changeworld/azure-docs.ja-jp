---
title: クイックスタート - Azure IoT Central でデバイスを監視する
description: クイックスタート - Azure IoT Central アプリケーションを使用してデバイスを監視する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 49ebe27f655734aeba3201f55305d42f57074360
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108765235"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>クイック スタート:Azure IoT Central を使用してデバイスを監視する

このクイックスタートでは、Azure IoT Central アプリケーションを使用して、デバイスを監視し、設定を変更する方法について説明します。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、「[Azure IoT Central アプリケーションを作成する](./quick-deploy-iot-central.md)」、「[シミュレーションされたデバイスを IoT Central アプリケーションに追加する](./quick-create-simulated-device.md)」、「[デバイスのルールとアクションを構成する](quick-configure-rules.md)」の 3 つのクイックスタートを完了する必要があります。

## <a name="receive-a-notification"></a>通知の受信

Azure IoT Central では、デバイスに関する通知を電子メール メッセージとして送信します。 ビルダーは、接続されているデバイス センサーの湿度がしきい値を超えたときに、オペレーターに通知を送信するルールを追加しました。 オペレーターは通知メールを確認します。

「[デバイスのルールとアクションを構成する](quick-configure-rules.md)」クイックスタートの最後で受信した電子メール メッセージを開きます。 メール内のデバイスへのリンクを選択します。

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="通知メールを示すスクリーンショット":::

前のクイックスタートで作成したシミュレートされたデバイスの **[概要]** ページがブラウザーで開きます。

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="通知をトリガーしたデバイスの概要を示すスクリーンショット":::

## <a name="investigate-an-issue"></a>問題の調査

オペレーターは、 **[概要]** 、 **[詳細]** 、および **[コマンド]** の各ページでデバイスに関する情報を確認できます。 ビルダーは、オペレーターがデバイス情報を編集したりデバイス プロパティを設定したりできるように、 **[デバイスの管理]** ビューを作成しました。

ダッシュボードのグラフには、デバイスの湿度のプロットが表示されます。 デバイスの湿度が高すぎると判断したとします。

## <a name="remediate-an-issue"></a>問題の修復

デバイスに変更を加えるには、 **[デバイスの管理]** ページを使用します。

**[Target temperature]\(目標温度\)** を 80 に変更してデバイスを温め、湿度を下げます。 **[保存]** を選択してデバイスを更新します。 デバイスで設定の変更が確認されると、プロパティの状態が **[同期済み]** に変わります。

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="デバイスの更新された目標温度設定を示すスクリーンショット":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、次の方法について説明しました。

* 通知の受信
* 問題の調査
* 問題の修復

ここでは、デバイスを監視する方法について説明しました。推奨される次の手順は次のとおりです。

> [!div class="nextstepaction"]
> [デバイス テンプレートの構築および管理](howto-set-up-template.md)。
