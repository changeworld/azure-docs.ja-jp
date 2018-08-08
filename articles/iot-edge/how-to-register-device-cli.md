---
title: 新しい Azure IoT Edge デバイスの登録 (CLI) | Microsoft Docs
description: Azure CLI 2.0 の IoT 拡張機能を使用して、新しい IoT Edge デバイスを登録する
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 451f4df31cd1c520b14227829923f72fe80c38c3
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325498"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Azure CLI 2.0 を使用して新しい Azure IoT Edge デバイスを登録する

Azure IoT Edge で IoT デバイスを使用する前に、それらを IoT ハブに登録する必要があります。 デバイスを登録すると、Edge ワークロード用にデバイスを設定するために使用できる接続文字列を受け取ります。 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンド ライン ツールです。 これを使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。 新しい IoT 拡張機能によって、Azure CLI 2.0 には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

この記事では、Azure CLI 2.0 を使用して新しい IoT Edge デバイスを登録する方法を示します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。 
* ご使用の環境内の [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 2.0 のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az –-version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。 
* [Azure CLI 2.0 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="create-a-device"></a>デバイスの作成

次のコマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

このコマンドには、3 つのパラメーターが含まれます。
* **device-id**: IoT ハブに対して一意のわかりやすい名前を指定します。
* **hub-name**: IoT ハブの名前を指定します。
* **edge-enabled**: このパラメーターは、デバイスが IoT Edge に使用されることを宣言します。

   ![IoT Edge デバイスを作成する](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>すべてのデバイスを表示する

IoT ハブのすべてのデバイスを表示するには、次のコマンドを使用します。

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。 

## <a name="retrieve-the-connection-string"></a>接続文字列の取得

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。 次のコマンドを使用して、単一デバイスの接続文字列を取得します。

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

device id パラメーターでは大文字と小文字が区別されます。 接続文字列を囲む引用符はコピーしないでください。 

## <a name="next-steps"></a>次の手順

[Azure CLI 2.0 でモジュールをデバイスにデプロイ](how-to-deploy-modules-cli.md)する方法を学習します