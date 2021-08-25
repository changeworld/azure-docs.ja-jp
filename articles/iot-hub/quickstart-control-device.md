---
title: クイックスタート - Azure IoT Hub からのデバイスの制御 | Microsoft Docs
description: このクイックスタートでは、2 つのサンプル アプリケーションを実行します。 1 つは、Hub に接続されたデバイスをリモートで制御できるサービス アプリケーションです。 もう 1 つのアプリケーションは、ハブに接続されたリモートで制御できるデバイスをシミュレートします。
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 07/26/2021
zone_pivot_groups: iot-hub-set1
ms.openlocfilehash: b89e13a6cf1a9dd6b30a9acc489969bb388ab3d1
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860948"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub"></a>クイック スタート: IoT ハブに接続されたデバイスを制御する

このクイック スタートでは、"ダイレクト メソッド" を使って、IoT Hub に接続されているシミュレートされたデバイスを制御します。 IoT Hub は、クラウドから IoT デバイスを管理し、大量のデバイス テレメトリを格納または処理のためにクラウドに取り込むことができるようにする Azure サービスです。 ダイレクト メソッドを使うと、IoT ハブに接続されたデバイスの動作をリモートで変更できます。

:::zone pivot="programming-language-csharp"

[!INCLUDE [quickstart-control-device-dotnet](../../includes/quickstart-control-device-dotnet.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [quickstart-control-device-java](../../includes/quickstart-control-device-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [quickstart-control-device-node](../../includes/quickstart-control-device-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [quickstart-control-device-python](../../includes/quickstart-control-device-python.md)]

:::zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、サービス アプリケーションからデバイス上のダイレクト メソッドを呼び出し、シミュレートされたデバイス アプリケーションでダイレクト メソッド呼び出しに応答しました。

デバイスからクラウドへのメッセージをクラウド内の異なる宛先にルーティングする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:処理のために利用統計情報を異なるエンドポイントにルーティングする](tutorial-routing.md)