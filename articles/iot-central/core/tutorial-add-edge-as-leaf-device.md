---
title: Azure IoT Edge デバイスを Azure IoT Central に追加する | Microsoft Docs
description: オペレーターとして、Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加します
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "77026395"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>チュートリアル:Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加する

このチュートリアルでは、Azure IoT Edge デバイスを構成して、お使いの Azure IoT Central アプリケーションに追加する方法について説明します。 このチュートリアルでは、Azure Marketplace から入手した IoT Edge 対応 Linux 仮想マシン (VM) を使用して、IoT Edge デバイスをシミュレートします。 この IoT Edge デバイスは、シミュレートされた環境のテレメトリを生成するモジュールを使用します。 テレメトリは、Azure IoT Central アプリケーションのダッシュボードに表示されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * IoT Edge デバイス用のデバイス テンプレートを作成する
> * IoT Central で IoT Edge デバイスを作成する
> * シミュレートされた IoT Edge デバイスを Linux VM にデプロイする

## <a name="prerequisites"></a>前提条件

**[カスタム アプリ] > [カスタム アプリケーション]** テンプレートを使用して IoT Central アプリケーションを作成するには、「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」クイックスタートを完了します。

このチュートリアルの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

GitHub から IoT Edge マニフェスト ファイルをダウンロードします。 次のリンクを右クリックし、 **[名前を付けてリンク先を保存]** を選択します: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>デバイス テンプレートを作成する

このセクションでは、IoT Central アプリケーションに接続する IoT Edge デバイス用のデバイス テンプレートを作成します。 開始するには IoT Edge マニフェストをインポートしてから、テレメトリの定義とビューを追加するようテンプレートを変更します。

### <a name="import-manifest-to-create-template"></a>マニフェストをインポートしてテンプレートを作成する

IoT Edge マニフェストからデバイス テンプレートを作成するには:

1. IoT Central アプリケーションで、 **[デバイス テンプレート]** に移動し、 **[+ 新規]** を選択します。

1. **[Select template type]\(テンプレートの種類の選択\)** ページで **[Azure IoT Edge]** タイルを選択します。 次に、 **[次のステップ: カスタマイズ]** を選択します。

1. **[Upload an Azure IoT Edge deployment manifest]\(Azure IoT Edge 配置マニフェストをアップロードする\)** ページで **[参照]** を選択し、先ほどダウンロードした **EnvironmentalSensorManifest.json** をアップロードします。 次に、**次のステップ: 確認\)** をクリックします。

1. **[Review]\(レビュー\)** ページで、 **[Create]\(作成\)** を選択します。

1. テンプレートが作成されたら、その名前を「*Environmental Sensor Edge Device*」に変更します。

1. **SimulatedTemperatureSensor** モジュールで **[管理]** インターフェイスを選択して、そのマニフェストで定義されている 2 つのプロパティを表示します。

![IoT Edge マニフェストから作成されたデバイス テンプレート](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>マニフェストにテレメトリを追加する

IoT Edge マニフェストでは、モジュールが送信するテレメトリが定義されていません。 デバイス テンプレートにテレメトリの定義を追加する必要があります。 **SimulatedTemperatureSensor** モジュールは、次の JSON のようなテレメトリ メッセージを送信します。

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

デバイス テンプレートにテレメトリの定義を追加するには:

1. **Environmental Sensor Edge Device** テンプレートで **[管理]** インターフェイスを選択します。

1. **[+ 機能の追加]** を選択します。 **[表示名]** として「*machine*」と入力し、 **[機能の種類]** が **[テレメトリ]** になっていることを確認します。

1. スキーマの種類として **[オブジェクト]** を選択し、 **[定義]** を選択します。 オブジェクトの定義ページで、**Double** 型の属性として *temperature* と *pressure* を追加し、 **[適用]** を選択します。

1. **[+ 機能の追加]** を選択します。 **[表示名]** として「*ambient*」と入力し、 **[機能の種類]** が **[テレメトリ]** になっていることを確認します。

1. スキーマの種類として **[オブジェクト]** を選択し、 **[定義]** を選択します。 オブジェクトの定義ページで、**Double** 型の属性として *temperature* と *humidity* を追加し、 **[適用]** を選択します。

1. **[+ 機能の追加]** を選択します。 **[表示名]** として「*timeCreated*」と入力し、 **[機能の種類]** が **[テレメトリ]** になっていることを確認します。

1. スキーマの種類として **[DateTime]\(日時\)** を選択します。

1. **[保存]** を選択してテンプレートを更新します。

これで、 **[管理]** インターフェイスにテレメトリの種類として **machine**、**ambient**、および **timeCreated** が表示されるようになりました。

![テレメトリの種類 machine と ambient が表示されているインターフェイス](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>ビューをテンプレートに追加する

デバイス テンプレートには、オペレーターが IoT Edge デバイスのテレメトリを確認できるビューがまだありません。 デバイス テンプレートにビューを追加するには:

1. **[Environmental Sensor Edge Device]** テンプレートで **[ビュー]** を選択します。

1. **[新しいビューを追加するための選択]** ページで、 **[デバイスの視覚化]** タイルを選択します。

1. ビュー名を「*View IoT Edge device telemetry*」に変更します。

1. テレメトリの種類として **[ambient]** と **[machine]** を選択します。 次に、 **[タイルの追加]** を選択します。

1. **[保存]** を選択して、**View IoT Edge device telemetry** ビューを保存します。

![テレメトリ ビューが表示されたデバイス テンプレート](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>テンプレートを発行する

**Environmental Sensor Edge Device** テンプレートを使用するデバイスを追加できるように、テンプレートを発行しておく必要があります。

**[Environmental Sensor Edge Device]** テンプレートに移動し、 **[発行]** を選択します。 次に、 **[発行]** を選択してテンプレートを発行します。

![デバイス テンプレートを公開する](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>IoT Edge デバイスを追加する

**Environmental Sensor Edge Device** テンプレートを発行したので、お使いの IoT Central アプリケーションにデバイスを追加できます。

1. お使いの IoT Central アプリケーションの **[デバイス]** ページに移動し、使用可能なテンプレートの一覧で **[Environmental Sensor Edge Device]** を選択します。

1. テンプレートから新しいデバイスを追加するには、 **[+]** を選択します。 **[新しいデバイスの作成]** ページで **[作成]** を選択します。

これで、状態が **[登録済み]** の新しいデバイスが作成されました。

![デバイス テンプレートを公開する](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>デバイスの資格情報を取得する

このチュートリアルで後ほど IoT Edge デバイスをデプロイする際、お使いの IoT Central アプリケーションへの接続をデバイスに許可する資格情報が必要になります。 デバイスの資格情報を取得するには、次の手順を実行します。

1. **[デバイス]** ページで、作成したデバイスを選択します。

1. **[接続]** を選択します。

1. **[デバイス接続]** ページの **[ID スコープ]** 、 **[デバイス ID]** 、 **[主キー]** を書き留めておきます。 これらの値は後で使用します。

1. **[閉じる]** を選択します。

これで、IoT Edge デバイスが接続できるようにするための IoT Central アプリケーションの構成が完了しました。

## <a name="deploy-an-iot-edge-device"></a>IoT Edge デバイスをデプロイする

このチュートリアルでは、Azure に作成された Azure IoT Edge 対応 Linux VM を使用して、IoT Edge デバイスをシミュレートします。 IoT Edge 対応 VM を作成するには、次の手順を実行します。

1. Azure Marketplace の [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) に移動します。 次に、 **[今すぐ入手する]** を選択します。

1. **[Azure でこのアプリを作成する]** ページで、 **[続行]** を選択します。 このリンクから Azure portal に移動します。ここでは、Azure サブスクリプションへのサインインが必要になる場合があります。

1. Azure portal の **[Azure IoT Edge on Ubuntu]** ページで、 **[作成]** を選択します。

1. **[仮想マシンの作成] > [基本]** ページで:

    - Azure サブスクリプションを選択します。
    - **iot-edge-devices** という名前の新しいリソース グループを作成します。
    - 仮想マシン名として **iotedgevm** を使用します。
    - 最寄りのリージョンを選択します。
    - 認証の種類を **[パスワード]** に設定します。
    - ユーザー名とパスワードを選択します。
    - 他のオプションは既定値のままでかまいません。
    - **[Review + create]\(レビュー + 作成\)** を選択します。

1. 検証が完了したら、 **[作成]** を選択します。

数分後にデプロイが完了したら、 **[リソースに移動]** を選択します。

### <a name="provision-vm-as-an-iot-edge-device"></a>IoT Edge デバイスとして VM をプロビジョニングする 

IoT Edge デバイスとして VM をプロビジョニングするには:

1. **[サポート + トラブルシューティング]** セクションで、 **[シリアル コンソール]** を選択します。

1. **Enter** キーを押して、`login:` プロンプトを表示します。 ユーザー名とパスワードを入力してサインインします。

1. 次のコマンドを実行して、IoT Edge ランタイムのバージョンを確認します。 この記事の執筆時点では、バージョンは 1.0.8 です。

    ```bash
    sudo iotedge --version
    ```

1. `nano` エディターを使用して、IoT Edge config.yaml ファイルを開きます。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. `# Manual provisioning configuration` が表示されるまで下にスクロールします。 次のスニペットに表示されているように、その次の 3 行をコメント アウトします。

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. `# DPS symmetric key provisioning configuration` が表示されるまで下にスクロールします。 次のスニペットに表示されているように、その次の 8 行をコメント解除します。

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. `{scope_id}` を、前に書き留めておいた **ID スコープ**に置き換えます。

1. `{registration_id}` を、前に書き留めておいた**デバイス ID** に置き換えます。

1. `{symmetric_key}` を、前に書き留めておいた**主キー**に置き換えます。

1. 変更内容を保存し (**Ctrl + O** キー)、`nano` エディターを終了します (**Ctrl + X** キー)。

1. 次のコマンドを実行して、IoT Edge デーモンを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge モジュールの状態を確認するには、次のコマンドを実行します。

    ```bash
    iotedge list
    ```

    出力は次のようになります。

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>テレメトリを表示する

シミュレートされた IoT Edge デバイスが VM で実行されるようになりました。 お使いの IoT Central アプリケーションの **[デバイス]** ページでは、デバイスの状態が **[プロビジョニング済み]** になっています。

![プロビジョニング済みのデバイス](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

テレメトリは、 **[View IoT Edge device telemetry]** ページで確認できます。

![デバイス テレメトリ](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

**[モジュール]** ページには、IoT Edge モジュールの状態が表示されます。

![デバイス テレメトリ](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>次のステップ

ここでは、IoT Central で IoT Edge デバイスを操作および管理する方法について学習しました。推奨される次の手順は以下のとおりです。

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [透過的なゲートウェイを構成する](../../iot-edge/how-to-create-transparent-gateway.md)
