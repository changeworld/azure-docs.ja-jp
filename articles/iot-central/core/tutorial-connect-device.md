---
title: チュートリアル - 汎用のクライアント アプリを Azure IoT Central に接続する | Microsoft Docs
description: このチュートリアルでは、C、C#、Java、JavaScript、Python のいずれかのクライアント アプリが実行されているデバイスを Azure IoT Central アプリケーションに接続する方法を説明します。 自動的に生成されるデバイス テンプレートに変更を加え、接続されているデバイスをオペレーターが対話的に操作できるビューを追加します。
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 7be12549d6e4afaa981a3d3dc83ad817ff5d9c9d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471320"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>チュートリアル:クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する

このチュートリアルでは、クライアント アプリケーションを Azure IoT Central アプリケーションに接続する方法を説明します。 このアプリケーションは、温度コントローラー デバイスの動作をシミュレートします。 このアプリケーションは、IoT Central に接続するときに、温度コントローラー デバイス モデルのモデル ID を送信します。 IoT Central は、このモデル ID を使用してデバイス モデルを取得し、自動的にデバイス テンプレートを作成します。 デバイス テンプレートにカスタマイズとビューを追加して、オペレーターがデバイスと対話できるようにしてみましょう。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス コードを作成して実行し、それが IoT Central アプリケーションに接続されることを確認する。
> * デバイスから送信されるシミュレートされたテレメトリを表示する。
> * カスタム ビューをデバイス テンプレートに追加する。
> * デバイス テンプレートを発行する。
> * ビューを使用してデバイス プロパティを管理する。
> * コマンドを呼び出してデバイスを制御する。

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>生データを表示する

**[生データ]** ビューを使用して、デバイスから IoT Central に送信されている生データを調べることができます。

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="[生データ] ビュー":::

このビューでは、表示する列を選択したり、確認対象の時間の範囲を設定したりできます。 **[Unmodeled data]\(モデル化されていないデータ\)** 列には、デバイス テンプレート内のプロパティまたはテレメトリ定義と一致しないデバイス データが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

一連の IoT Central チュートリアルを続行し、IoT Central ソリューションの構築方法の詳細を確認するには、次のページを参照してください。

> [!div class="nextstepaction"]
> [ゲートウェイ デバイス テンプレートを作成する](./tutorial-define-gateway-device-type.md)
