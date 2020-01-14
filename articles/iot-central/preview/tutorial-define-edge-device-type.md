---
title: チュートリアル - Azure IoT Central で新しい Azure IoT Edge デバイスの種類を定義する
description: このチュートリアルでは、作成者向けに、Azure IoT Central アプリケーションで新しい Azure IoT Edge デバイスを作成する方法について説明します。 種類に対して、テレメトリ、状態、プロパティ、コマンドを定義します。
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5aa41023e1d7f84e8de095d13835d5ebdf66f57
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434873"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>チュートリアル:Azure IoT Central アプリケーションで新しい Azure IoT Edge デバイスの種類を定義する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

このチュートリアルでは、作成者向けに、デバイス テンプレートを使用して Microsoft Azure IoT Central アプリケーションで Azure IoT Edge デバイスの新しい種類を定義する方法について説明します。 

概要については、「[Azure IoT Central (プレビュー機能) とは](overview-iot-central.md)」を参照してください。 

IoT Edge は、次の 3 つのコンポーネントで構成されます。
* **IoT Edge モジュール**: Azure サービス、パートナー サービス、またはカスタム コードを実行するコンテナーです。 モジュールは、IoT Edge デバイスにデプロイされ、そのデバイスでローカルに実行されます。
* **IoT Edge ランタイム**: 個々の IoT Edge デバイス上で動作し、各デバイスにデプロイされたモジュールを管理します。
* **クラウドベースのインターフェイス**: IoT Edge デバイスをリモートから監視して管理します。 IoT Central がクラウド インターフェイスです。

**Azure IoT Edge** デバイスは、IoT Edge デバイスに接続するダウンストリーム デバイスがあるゲートウェイ デバイスにすることができます。 このチュートリアルでは、ダウンストリーム デバイスの接続パターンに関する詳細情報を共有します。

**デバイス テンプレート**により、デバイスと IoT Edge のモジュールの機能が定義されます。 機能には、モジュールが送信するテレメトリ、モジュール プロパティ、モジュールが応答するコマンドが含まれます。

このチュートリアルでは、環境センサーのデバイス テンプレートを作成します。 環境センサー デバイス:

* 温度などのテレメトリを送信する。
* クラウドで更新されたときに、テレメトリの送信間隔などの書き込み可能なプロパティに応答する。
* 温度のリセットなどのコマンドに応答する。

また、このチュートリアルでは、環境ゲートウェイ デバイス テンプレートも作成します。 環境ゲートウェイ デバイス:

* 温度などのテレメトリを送信する。
* クラウドで更新されたときに、テレメトリの送信間隔などの書き込み可能なプロパティに応答する。
* 温度のリセットなどのコマンドに応答する。
* 他のデバイス機能モデルへのリレーションシップを許可する。


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しい Azure IoT Edge デバイス テンプレートを作成する。
> * 配置マニフェストを作成する。
> * 各モジュールに対してテレメトリ、プロパティ、コマンドなどの機能を作成する。
> * モジュール テレメトリの視覚化を定義する。
> * ダウンストリーム デバイス テンプレートにリレーションシップを追加する。
> * デバイス テンプレートの公開。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、[Azure IoT Central アプリケーションを作成する](quick-deploy-iot-central.md)必要があります。


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>ゲートウェイおよびモジュールとのダウンストリーム デバイスのリレーションシップ

ダウンストリーム デバイスは、`$edgeHub` モジュールを使用して IoT Edge ゲートウェイ デバイスに接続できます。 このシナリオでは、この IoT Edge デバイスは透過的なゲートウェイになります。

![透過的なゲートウェイの図](./media/tutorial-define-edge-device-type/gateway-transparent.png)

ダウンストリーム デバイスは、カスタム モジュールを使用して IoT Edge ゲートウェイ デバイスに接続することもできます。 次のシナリオでは、ダウンストリーム デバイスは Modbus カスタム モジュールを使用して接続します。

![カスタム モジュール接続の図](./media/tutorial-define-edge-device-type/gateway-module.png)

次の図は、両方の種類のモジュール (カスタムおよび `$edgeHub`) を使用した IoT Edge ゲートウェイ デバイスへの接続を示しています。  

![両方の接続モジュールを使用した接続の図](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

さらに、ダウンストリーム デバイスは、複数のカスタム モジュールを使用して IoT Edge ゲートウェイ デバイスに接続できます。 次の図は、Modbus カスタム モジュール、BLE カスタム モジュール、および `$edgeHub` モジュールを使用したダウンストリーム デバイス接続を示しています。 

![複数のカスタム モジュールを使用した接続の図](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>テンプレートの作成

作成者は、アプリケーションで IoT Edge デバイス テンプレートを作成および編集できます。 デバイス テンプレートを公開した後は、デバイス テンプレートを実装する実デバイスを接続できます。

### <a name="select-device-template-type"></a>デバイス テンプレートの種類を選択する 

新しいデバイス テンプレートをアプリケーションに追加するには、左ウィンドウで **[デバイス テンプレート]** を選択します。

![[デバイス テンプレート] が強調表示されている [アプリケーションのプレビュー] のスクリーンショット](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

**[+ 新規]** を選択して、新しいデバイス テンプレートの作成を開始します。

![[新規] が強調表示された [デバイス テンプレート] ページのスクリーンショット](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

**[Select template type]\(テンプレートの種類の選択\)** ページで **[Azure IoT Edge]** を選択し、 **[次へ:カスタマイズ]** を選択します。

![[Select template type]\(テンプレートの種類の選択\) ページのスクリーンショット](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>デバイス テンプレートをカスタマイズする

IoT Edge では、ビジネス ロジックをモジュールの形式でデプロイして管理できます。 IoT Edge モジュールは、IoT Edge によって管理される最小の計算単位であり、Azure Stream Analytics などの Azure サービスや独自のソリューション固有のコードを含めることができます。 モジュールがどのように開発、デプロイ、維持されるかについては、[IoT Edge モジュール](../../iot-edge/iot-edge-modules.md)に関する記事を参照してください。

大まかに言えば、配置マニフェストとは、必要なプロパティを使用して構成されたモジュール ツインのリストです。 インストールするモジュールとその構成方法は、配置マニフェストから IoT Edge デバイス (1 つまたは複数のデバイス) に伝えられます。 配置マニフェストには、各モジュール ツインの必要なプロパティが含まれています。 IoT Edge デバイスは、各モジュールの報告されたプロパティを返します。

Visual Studio Code を使用して配置マニフェストを作成します。 詳細については、「[Visual Studio Code 用の Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)」を参照してください。

このチュートリアルで使用する例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。 次の JSON をコピーし、.json ファイルとして保存します。 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

#### <a name="upload-an-iot-edge-deployment-manifest"></a>IoT Edge 配置マニフェストをアップロードする

**[デバイスのカスタマイズ]** ページの **[Upload an Azure IoT Edge deployment manifest]\(Azure IoT Edge 配置マニフェストをアップロードする\)** で、 **[参照]** を選択します。 

![[参照] が強調表示された [デバイスのカスタマイズ] ページのスクリーンショット](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

IoT Edge Gateway デバイス テンプレートを作成する予定の場合は、 **[Gateway device with downstream devices]\(ダウンストリーム デバイスがあるゲートウェイ デバイス\)** を必ず選択してください。

![[Gateway device with downstream devices]\(ダウンストリーム デバイスがあるゲートウェイ デバイス\) が強調表示されている [デバイスのカスタマイズ] ページのスクリーンショット](./media/tutorial-define-edge-device-type/gateway-upload.png)

[ファイルの選択] ダイアログ ボックスで、配置マニフェスト ファイルを選択し、 **[開く]** を選択します。

IoT Edge により、配置マニフェスト ファイルがスキーマと照合されて検証されます。 検証が成功した場合は、 **[レビュー]** を選択します。

![[配置マニフェスト] と [レビュー] が強調表示されている [デバイスのカスタマイズ] ページのスクリーンショット](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

次のフローチャートは、IoT Central での配置マニフェストのライフ サイクルを示しています。

![配置マニフェストのライフ サイクルのフローチャート](./media/tutorial-define-edge-device-type/dmflow.png)

次に、配置マニフェストの詳細を含む [レビュー] ページが表示されます。 このページには、配置マニフェストのモジュールの一覧が表示されます。 このチュートリアルでは、`SimulatedTemperatureSensor` モジュールが表示されていることに注目します。 **作成** を選択します。

![[モジュール] および [作成] が強調表示されている [レビュー] ページのスクリーンショット](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

ゲートウェイ デバイスを選択した場合は、次の [レビュー] ページが表示されます。

![[Azure IoT Edge Gateway]\(Azure IoT Edge ゲートウェイ\) が強調表示されている [レビュー] ページのスクリーンショット](./media/tutorial-define-edge-device-type/gateway-review.png)


モジュール機能モデルと共にデバイス テンプレートを作成します。 このチュートリアルでは、`SimulatedTemperatureSensor` モジュール機能モデルと共にデバイス テンプレートを作成します。 

デバイス テンプレートのタイトルを **Environment Sensor Device Template** (環境センサー デバイス テンプレート) に変更します。

![更新されたタイトルが強調表示されているデバイス テンプレートのスクリーンショット](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

IoT Edge デバイスで、IoT プラグ アンド プレイを次のようにモデル化します。
* すべての IoT Edge デバイス テンプレートに、デバイス機能モデルがあります。
* 配置マニフェストに一覧表示されているすべてのカスタム モジュールに対して、モジュール機能モデルが生成されます。
* 各モジュール機能モデルとデバイス機能モデルの間にリレーションシップが確立されます。
* モジュール機能モデルは、モジュール インターフェイスを実装します。
* 各モジュール インターフェイスには、テレメトリ、プロパティ、およびコマンドが含まれます。

![IoT Edge モデリングの図](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>モジュール機能モデルに機能を追加する

`SimulatedTemperatureSensor` モジュールからの出力例を次に示します。
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

前の出力を反映する機能を `SimulatedTemperatureSensor` モジュールに追加できます。 

1. `SimulatedTemperatureSensor` モジュール機能モデルのインターフェイスを管理するには、 **[管理]**  >  **[機能の追加]** を選択します。 

    ![[機能の追加] が強調表示された [Environment Sensor Template]\(環境センサー テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. machine をオブジェクトの種類として追加します。
  
    ![[スキーマ] が強調表示された [Environment Sensor Template Capabilities]\(環境センサー テンプレートの機能\) ページのスクリーンショット](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. **[定義]** を選択します。 表示されるダイアログ ボックスで、オブジェクト名を **machine** に変更します。 temperature プロパティおよび pressure プロパティを作成し、 **[適用]** を選択します。
  
    ![各種オプションが強調表示されている属性ダイアログ ボックスのスクリーンショット](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. オブジェクトの種類として **ambient** を追加します。

1. **[定義]** を選択します。 表示されるダイアログ ボックスで、オブジェクト名を **ambient** に変更します。 temperature および humidity プロパティを作成し、 **[適用]** を選択します。
  
    ![各種オプションが強調表示されている属性ダイアログ ボックスのスクリーンショット](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. `DateTime` タイプとして `timeCreated` を追加し、 **[保存]** を選択します。
  
    ![[保存] が強調表示されている [Environment Sensor Template]\(環境センサー テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>リレーションシップの追加

IoT Edge デバイスをゲートウェイ デバイスにするよう選択した場合は、ゲートウェイ デバイスに接続するデバイスのデバイス機能モデルにダウンストリーム リレーションシップを追加できます。
  
  ![リレーションシップの追加 が強調表示されている Environment Gateway Template\(環境ゲートウェイ テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

リレーションシップは、デバイスまたはモジュールで追加できます。
  
  ![デバイス レベルおよびモジュール レベルのリレーションシップが強調表示されている Environment Gateway Template\(環境ゲートウェイ テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


ダウンストリームのデバイス機能モデル、またはアスタリスク記号を選択できます。 
  
  ![ターゲット が強調表示されている Environment Gateway Template\(環境ゲートウェイ テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  このチュートリアルでは、アスタリスクを選択します。 このオプションを選択すると、すべてのダウンストリーム リレーションシップが許可されます。 次に、 **[保存]** を選択します。

  ![ターゲット が強調表示されている Environment Gateway Template\(環境ゲートウェイ テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>クラウド プロパティを追加する

デバイス テンプレートにはクラウド プロパティを含めることができます。 クラウド プロパティは IoT Central アプリケーション内のみに存在しており、デバイスとの間で送受信されることはありません。

1. **[クラウド プロパティ]**  >  **[+ クラウド プロパティの追加]** を選択します。 下表の情報に従って、デバイス テンプレートにクラウド プロパティを追加します。

    | Display name      | セマンティックの種類 | スキーマ |
    | ----------------- | ------------- | ------ |
    | Last Service Date | なし          | Date   |
    | お客様名     | なし          | String |

2. **[保存]** を選択します。

  
    ![[保存] が強調表示されている [Environment Sensor Template]\(環境センサー テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>カスタマイズを追加する

インターフェイスを変更する場合や、デバイス機能モデルのバージョン管理が不要な機能に IoT Central 固有の機能を追加する場合は、カスタマイズを利用します。 機能モデルがドラフト状態か公開済み状態であるときに、フィールドをカスタマイズできます。 カスタマイズできるのは、インターフェイスの互換性を損なうことのないフィールドのみです。 たとえば、次のように操作できます。

- 機能の表示名や単位をカスタマイズする。
- グラフに値が表示されるときに使用する既定の色を追加する。
- プロパティの初期値、最小値、最大値を指定する。

機能名や機能の種類をカスタマイズすることはできません。

カスタマイズが完了したら、 **[保存]** を選択します。
  
![[Environment Sensor Template Customize]\(環境センサー テンプレートのカスタマイズ\) ページのスクリーンショット](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>ビューの作成

作成者は、環境センサー デバイスの関連情報がオペレーターに表示されるよう、アプリケーションをカスタマイズできます。 カスタマイズを行うことで、オペレーターがアプリケーションに接続された環境センサー デバイスを管理できるようになります。 オペレーター向けのデバイス操作用のビューを 2 種類作成できます。

* デバイス プロパティとクラウド プロパティを表示および編集するためのフォーム。
* デバイスを視覚化するためのダッシュボード。

### <a name="configure-a-view-to-visualize-devices"></a>ビューを構成してデバイスを視覚化する

オペレーターは、デバイス ダッシュボードでグラフやメトリックを使用してデバイスを視覚化できます。 作成者はデバイス ダッシュボードに表示される情報を定義できます。 デバイスに対し、複数のダッシュボードを定義することができます。 環境センサーのテレメトリを視覚化するダッシュボードを作成するために、 **[ビュー]**  >  **[デバイスの視覚化]** を選択します。

  
![[デバイスの視覚化] が強調表示されている [Environment Sensor Template Views]\(環境センサー テンプレート ビュー\) ページのスクリーンショット](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Ambient テレメトリと Machine テレメトリは複合型オブジェクトです。 グラフを作成するには:

1. **[Ambient Telemetry]\(Ambient テレメトリ\)** をドラッグし、 **[折れ線グラフ]** を選択します。 
  
   ![[Ambient Telemetry]\(Ambient テレメトリ\) と [折れ線グラフ] が強調表示されている [Environment Sensor Template]\(環境センサー テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. [構成] アイコンを選択します。 **[Temperature]\(気温\)** と **[Humidity]\(湿度\)** を選択してデータを視覚化し、 **[Update configuration]\(構成の更新\)** を選択します。 
  
   ![各種オプションが強調表示されている [Environment Sensor Template]\(環境センサー テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. **[保存]** を選択します。

別のプロパティやテレメトリ値を表示するタイルをさらに追加できます。 また、静的テキストやリンク、画像も追加できます。 ダッシュボード上のタイルを移動したり、そのサイズを変更したりするには、そのタイルの上にマウス ポインターを移動して、タイルを新しい場所にドラッグするか、そのサイズを変更します。
  
![[Environment Sensor Template Dashboard]\(環境センサー テンプレート ダッシュボード\) ビューのスクリーンショット](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>デバイス フォームを追加する

デバイス フォームでは、オペレーターが、書き込み可能なデバイス プロパティとクラウド プロパティを編集できます。 作成者は複数のフォームを定義し、各フォームに表示するデバイス プロパティとクラウド プロパティを選択することができます。 フォームには、読み取り専用のデバイス プロパティを表示することもできます。

環境センサーのプロパティを確認および編集するためのビューを作成には:

1. **[Environmental Sensor Template]\(環境センサー テンプレート\)** で、 **[ビュー]** に移動します。 **[デバイスとクラウドのデータの編集]** タイルを選択し、新しいビューを追加します。
  
   ![[デバイスとクラウドのデータの編集] が強調表示されている [Environmental Sensor Template Views]\(環境センサー テンプレート ビュー\) ページのスクリーンショット](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. フォーム名として「**環境センサーのプロパティ**」と入力します。

1. **[顧客名]** と **[Last service date]\(前回点検日\)** の各クラウド プロパティを、フォームの既存のセクションにドラッグします。
  
   ![各種オプションが強調表示されている [Environmental Sensor Template Views]\(環境センサー テンプレート ビュー\) ページのスクリーンショット](./media/tutorial-define-edge-device-type/views-properties.png)

1. **[保存]** を選択します。

## <a name="publish-a-device-template"></a>デバイス テンプレートを発行する

シミュレートされた環境センサーの作成や、実物の環境センサーの接続を行うには、デバイス テンプレートを公開する必要があります。

デバイス テンプレートを公開するには:

1. **[デバイス テンプレート]** ページで、作成したデバイス テンプレートに移動します。

2. **[発行]** を選択します。
  
    ![[発行] が強調表示されている [Environmental Sensor Template]\(環境センサー テンプレート\) のスクリーンショット](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. **[Publish a Device Template]\(デバイス テンプレートの発行\)** ダイアログで、 **[発行]** を選択します。
  
    ![[発行] が強調表示されている [デバイス テンプレートを発行する] ダイアログ ボックスのスクリーンショット](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

デバイス テンプレートを公開すると、 **[デバイス]** ページに表示され、オペレーターが確認できるようになります。 公開済みのデバイス テンプレートで、新しいバージョンを作成せずにデバイス機能モデルを編集することはできません。 ただし、発行されたデバイス テンプレートで、クラウド プロパティ、カスタマイズ、およびビューを更新できます。 これらの更新によって新しいバージョンが作成されることはありません。 変更を行った後、 **[発行]** を選択すると、変更内容がオペレーターにプッシュされます。
  
![発行済みテンプレートのデバイス テンプレート一覧のスクリーンショット](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

* 新しい Edge をリーフ デバイス テンプレートとして作成する。
* アップロードされた配置マニフェストからモジュールを生成する。
* 複合型のテレメトリとプロパティを追加する。
* クラウド プロパティの作成。
* カスタマイズの作成。
* デバイス テレメトリの視覚化の定義。
* Edge デバイス テンプレートを公開する。

Azure IoT Central アプリケーションでデバイス テンプレートを作成したので、次の手順に進むことができます。

> [!div class="nextstepaction"]
> [Edge デバイスを接続する](./tutorial-add-edge-as-leaf-device.md)
