---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 3620e8021cfe5adf6226f007ea240921951ea6fe
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110061267"
---
## <a name="create-the-cloud-components"></a>クラウド コンポーネントを作成する

### <a name="create-the-iot-central-application"></a>Azure IoT Central アプリケーションを作成する

デバイスを Azure IoT に接続する方法は複数あります。 このセクションでは、Azure IoT Central を使用してデバイスを接続する方法について説明します。 IoT Central は、IoT ソリューションの作成と管理のコストと複雑さを軽減する、IoT アプリケーション プラットフォームです。

新しいアプリケーションを作成するには:
1. [Azure IoT Central ポータル](https://apps.azureiotcentral.com/)で、サイド ナビゲーション メニューの **[マイ アプリ]** を選択します。
1. **[新しいアプリケーション]** を選択します。
1. **[カスタム アプリ]** を選択します。
1. アプリケーション名と URL を追加します。
1. 7 日間の試用版をアクティブ化するには、 **[無料]** 価格プランを選択します。

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-custom.png" alt-text="Azure IoT Central でカスタム アプリを作成する":::

1. **［作成］** を選択します

    IoT Central によってアプリケーションがプロビジョニングされると、自動的に新しいアプリケーション ダッシュボードにリダイレクトされます。

    > [!NOTE]
    > 既存の IoT Central アプリケーションがある場合は、新しいアプリケーションを作成するのではなく、それを使用してこの記事の手順を実行できます。

### <a name="create-a-new-device"></a>新しいデバイスを作成する

このセクションでは、IoT Central アプリケーション ダッシュボードを使用して、新しいデバイスを作成します。 新しく作成したデバイスの接続情報を使用して、後のセクションで物理デバイスを安全に接続します。

デバイスを作成するには:
1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. **[+ 新規]** を選択して **[新しいデバイスを作成する]** ウィンドウを開きます。
1. [デバイス テンプレート] は **[Unassigned]\(未割り当て\)** のままにしてください。
1. 目的のデバイス名とデバイス ID を入力します。

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-device.png" alt-text="Azure IoT Central でデバイスを作成する":::

1. **[作成]** ボタンを選択します。
1. 新しく作成したデバイスが **[すべてのデバイス]** 一覧に表示されます。  デバイス名を選択すると詳細が表示されます。
1. 右上のメニュー バーで **[接続]** を選択して、次のセクションでデバイスの構成に使用する接続情報を表示します。

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-device-connection-info.png" alt-text="デバイス接続の詳細を表示する":::

1. **[接続]** ダイアログに表示される次の接続文字列パラメーターの接続値を確認します。 これらの値は、次の手順で構成ファイルに追加します。

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`