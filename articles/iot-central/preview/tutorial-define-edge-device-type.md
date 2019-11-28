---
title: Azure IoT Central で新しい Azure IoT Edge デバイスの種類を定義する | Microsoft Docs
description: このチュートリアルでは、作成者向けに、Azure IoT Central アプリケーションで新しい Azure IoT Edge デバイスを作成する方法について説明します。 種類に対して、テレメトリ、状態、プロパティ、コマンドを定義します。
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892028"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>チュートリアル:Azure IoT Central アプリケーションで新しい Azure IoT Edge デバイスの種類を定義する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

このチュートリアルでは、作成者向けに、デバイス テンプレートを使用して Microsoft Azure IoT Central アプリケーションで Azure IoT Edge デバイスの新しい種類を定義する方法について説明します。 

Azure IoT Edge の概要については、[こちらの記事を参照してください](overview-iot-central.md)。 

Azure IoT Edge は、次の 3 つのコンポーネントで構成されます。
* **IoT Edge モジュール**: Azure のサービス、サードパーティのサービス、または独自のコードを実行するコンテナーです。 モジュールは、IoT Edge デバイスにデプロイされ、そのデバイス上のローカルで実行されます。
* **IoT Edge ランタイム**: 個々の IoT Edge デバイス上で動作し、各デバイスにデプロイされたモジュールを管理します。
* **クラウドベースのインターフェイス**: IoT Edge デバイスをリモートから監視して管理します。 IoT Central がクラウド インターフェイスになります。

**Azure IoT Edge** デバイスは、Azure IoT Edge デバイスに接続するダウンストリーム デバイスがあるゲートウェイ デバイスにすることができます。 ダウンストリーム デバイスの接続パターンについては、このチュートリアルで説明します。

**デバイス テンプレート**には、デバイスと IoT Edge のモジュールの機能が定義されています。 機能には、モジュールが送信するテレメトリ、モジュール プロパティ、モジュールが応答するコマンドが含まれます。

このチュートリアルでは、**環境センサー**のデバイス テンプレートを作成します。 環境センサー デバイス:

* 温度などのテレメトリを送信する。
* クラウドで更新されたときに、テレメトリの送信間隔などの書き込み可能なプロパティに応答する。
* 温度のリセットなどのコマンドに応答する。

また、このチュートリアルでは、**環境ゲートウェイ** デバイス テンプレートも作成します。 環境ゲートウェイ デバイス:

* 温度などのテレメトリを送信する。
* クラウドで更新されたときに、テレメトリの送信間隔などの書き込み可能なプロパティに応答する。
* 温度のリセットなどのコマンドに応答する。
* 他のデバイス機能モデルへのリレーションシップを許可する


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しい Azure IoT Edge デバイス テンプレートを作成する。
> * 配置マニフェストを作成する。
> * 各モジュールに対してテレメトリ、プロパティ、コマンドなどの機能を作成する
> * モジュール テレメトリの視覚化を定義する。
> * ダウンストリーム デバイス テンプレートにリレーションシップを追加する
> * デバイス テンプレートの公開。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure IoT Central アプリケーションが必要です。 こちらのクイックスタートに従って、[Azure IoT Central アプリケーションを作成](quick-deploy-iot-central.md)します。


## <a name="downstream-device-relationships-with-gateway--modules"></a>ゲートウェイおよびモジュールとのダウンストリーム デバイスのリレーションシップ

ダウンストリーム デバイスは、$edgeHub モジュールを使用して Azure IoT Edge ゲートウェイ デバイスに接続できます。 このシナリオでは、この Azure IoT Edge デバイスは透過的なゲートウェイになります

![Central アプリケーション ページ](./media/tutorial-define-edge-device-type/gateway-transparent.png)

ダウンストリーム デバイスは、カスタム モジュールを使用して Azure IoT Edge ゲートウェイ デバイスに接続できます。 次のシナリオでは、ダウンストリーム デバイスは Modbus カスタム モジュールを使用して接続しており、ダウンストリーム デバイスは $edgeHub モジュールを使用して Azure IoT Edge ゲートウェイ デバイスに接続できます。

![Central アプリケーション ページ](./media/tutorial-define-edge-device-type/gateway-module.png)

ダウンストリーム デバイスは、カスタム モジュールを使用して Azure IoT Edge ゲートウェイ デバイスに接続できます。 次のシナリオでは、ダウンストリーム デバイスは Modbus カスタム モジュールを使用して接続しています。 

![Central アプリケーション ページ](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

ダウンストリーム デバイスは、複数のカスタム モジュールを使用して Azure IoT Edge ゲートウェイ デバイスに接続できます。 次のシナリオでは、ダウンストリーム デバイスは Modbus カスタム モジュールを使用して接続しており、BLE カスタム モジュールとダウンストリーム デバイスは $edgeHub モジュールを使用して Azure IoT Edge ゲートウェイ デバイスに接続できます。 

![Central アプリケーション ページ](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>テンプレートの作成

作成者は、アプリケーションで Azure IoT Edge デバイス テンプレートを作成および編集できます。 デバイス テンプレートを公開した後は、デバイス テンプレートを実装する実デバイスを接続できます。

### <a name="select-device-template-type"></a>デバイス テンプレートの種類を選択する 

新しいデバイス テンプレートをアプリケーションに追加するには、 **[デバイス テンプレート]** ページに移動します。 そのためには、左側のペインで **[デバイス テンプレート]** タブを選択します。

![Central アプリケーション ページ](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

**[+ 新規]** をクリックして、新しいデバイス テンプレートの作成を開始します。

![デバイス テンプレート - 新規](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

デバイス テンプレートの種類の選択ページが表示されます。 **[Azure IoT Edge]** タイルを選択し、一番下の **[Next: Customize]\(次へ: カスタマイズ\)** ボタンをクリックします

![デバイス テンプレートの選択 - Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>デバイス テンプレートをカスタマイズする

Azure IoT Edge では、ビジネス ロジックをモジュールの形式でデプロイして管理できます。 **Azure IoT Edge モジュール**は、IoT Edge によって管理される最小の計算単位であり、Azure Stream Analytics などの Azure サービスや独自のソリューション固有のコードを含めることができます。 モジュールがどのように開発、デプロイ、維持されるかについては、[IoT Edge モジュール](../../iot-edge/iot-edge-modules.md)に関する記事をお読みください。

大まかに言えば、配置マニフェストとは、必要なプロパティを使用して構成されたモジュール ツインのリストです。 インストールするモジュールとその構成方法は、配置マニフェストから IoT Edge デバイス (1 つまたは複数のデバイス) に伝えられます。 配置マニフェストには、各モジュール ツインの必要なプロパティが含まれています。 IoT Edge デバイスは、各モジュールの報告されたプロパティを返します。

Visual Studio Code を使用して配置マニフェストを作成します。 [配置マニフェスト](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)の作成方法に関するドキュメントに従ってください。

このチュートリアルで使用する例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。 以下の JSON をコピーし、JSON ファイルとして保存します。 

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

**Azure IoT Edge 配置マニフェストをアップロードする**

**[参照]** ボタンをクリックします 

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Azure IoT Edge Gateway デバイス テンプレートを作成する予定の場合は、 **[Gateway device with downstream devices]\(ダウンストリーム デバイスがあるゲートウェイ デバイス\)** チェックボックスを必ずオンにしてください

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

ファイルの選択ダイアログが表示されます。 配置マニフェスト ファイルを選択し、 **[開く]** ボタンをクリックします。

配置マニフェスト ファイルがスキーマに照らして検証されます。 検証が正常に行われたら、 **[Review]\(レビュー\)** ボタンをクリックします

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

IoT Central での配置マニフェストのライフサイクル フローを次に示します。

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

配置マニフェストの詳細を含む [Review]\(レビュー\) ページが表示されます。 配置マニフェストのモジュール一覧が [Review]\(レビュー\) ページに表示されます。 このチュートリアルでは、SimulatedTemperatureSensor モジュールが一覧表示されます。 **[作成]** をクリックします。

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

ゲートウェイ デバイスを選択した場合は、このレビュー ページが表示されます

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


新しいデバイス テンプレートを作成すると、デバイス テンプレートが IoT Central で作成されていることを示すスピナーが表示されます。

デバイス テンプレートは、モジュール機能モデルと共に作成されます。 このチュートリアルでは、SimulatedTemperatureSensor モジュール機能モデルが作成されます。 

デバイス テンプレートのタイトルを "Environment Sensor Device Template" (環境センサー デバイス テンプレート) に変更します。

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Azure IoT Edge デバイスのプラグ アンド プレイ モデリングは、次のように行われます
* すべての Azure IoT Edge デバイス テンプレートには、**デバイス機能モデル**があります
* 配置マニフェストに一覧表示されているすべてのカスタム モジュールに対して、**モジュール機能モデル**が生成されます
* 各モジュール機能モデルとデバイス機能モデルの間に**リレーションシップ**が確立されます
* モジュール機能モデルは、**モジュール インターフェイス**を実装します
* 各モジュール インターフェイスには次が含まれます
   - テレメトリ
   - properties
   - command

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**モジュール機能モデルに機能を追加する**

SimulatedTemperatureSensor モジュールからの出力を次に示します
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

上記の JSON を反映する機能を SimulatedTemperatureSensor モジュールに追加します。 

* SimulatedTemperatureSensor モジュール機能モデルのインターフェイスの **[Manage]\(管理\)** をクリックします。 **[Add Capability]\(機能の追加\)** をクリックします。 

    ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* "machine" は複合型であるため、オブジェクト型として追加します
  
    ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    **[定義]** をクリックします。 ポップアップ モーダルで、オブジェクト名を "machine" に変更し、温度と圧力のプロパティを作成し、 **[適用]** をクリックします
  
    ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* "ambient" は複合型であるため、オブジェクト型として追加します

    **[定義]** をクリックします。 ポップアップ モーダルで、オブジェクト名を "ambient" に変更し、温度と湿度のプロパティを作成し、 **[適用]** をクリックします
  
    ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* DateTime の種類として timeCreated を追加し、 **[保存]** をクリックします
  
    ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>リレーションシップを追加する

Azure IoT Edge デバイスをゲートウェイ デバイスにするよう選択した場合は、ゲートウェイ デバイスに接続するデバイスのデバイス機能モデルにダウンストリーム リレーションシップを追加できます。
  
  ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

リレーションシップは、デバイスまたはモジュールで追加できます。
  
  ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


ダウンストリームのデバイス機能モデルを選択するか、アスタリスクを選択できます。 
  
  ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  このチュートリアルでは、アスタリスクを選択します。これは、すべてのダウンストリーム リレーションシップが許可されることを意味します。 **[保存]**

  ![デバイス テンプレート - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>クラウド プロパティを追加する

デバイス テンプレートにはクラウド プロパティを含めることができます。 クラウド プロパティは IoT Central アプリケーション内のみに存在しており、デバイスとの間で送受信されることはありません。

1. **[クラウド プロパティ]** 、 **[+ クラウド プロパティの追加]** の順に選択します。 下表の情報に従って、デバイス テンプレートにクラウド プロパティを追加します。

    | 表示名      | セマンティックの種類 | スキーマ |
    | ----------------- | ------------- | ------ |
    | Last Service Date | なし          | Date   |
    | お客様名     | なし          | string |

2. **[保存]** を選択して変更を保存します。

  
    ![クラウド プロパティ - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>カスタマイズを追加する

インターフェイスを変更する必要がある場合や、デバイス機能モデルのバージョン管理が不要な機能に IoT Central 固有の機能を追加する場合は、カスタマイズを利用します。 機能モデルがドラフト状態か公開済み状態であるときに、フィールドをカスタマイズできます。 カスタマイズできるのは、インターフェイスの互換性を損なうことのないフィールドのみです。 たとえば、次のようなことができます。

- 機能の表示名や単位をカスタマイズする。
- グラフに値が表示されるときに使用する既定の色を追加する。
- プロパティの初期値、最小値、最大値を指定する。

機能名や機能の種類をカスタマイズすることはできません。 **[保存]**
  
![カスタマイズ - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>ビューの作成

作成者は、環境センサー デバイスの関連情報がオペレーターに表示されるよう、アプリケーションをカスタマイズできます。 カスタマイズを行うことで、オペレーターがアプリケーションに接続された環境センサー デバイスを管理できるようになります。 オペレーター向けのデバイス操作用のビューを 2 種類作成できます。

* デバイス プロパティとクラウド プロパティを表示および編集するためのフォーム。
* デバイスを視覚化するためのダッシュボード。

### <a name="configure-a-view-to-visualize-devices"></a>ビューを構成してデバイスを視覚化する

オペレーターは、デバイス ダッシュボードでグラフやメトリックを利用してデバイスを視覚化できます。 作成者はデバイス ダッシュボードに表示される情報を定義できます。 デバイスに対し、複数のダッシュボードを定義することができます。 環境センサーのテレメトリを視覚化するダッシュボードを作成するために、 **[ビュー]** 、 **[デバイスの視覚化]** の順に選択します。

  
![ビュー - Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Ambient テレメトリと Machine テレメトリは複合型オブジェクトのため、グラフを作成するには次のようにします

Ambient テレメトリをドラッグし、折れ線グラフを選択します。 
  
![ビュー - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

[構成] アイコンをクリックし、データを視覚化するために温度と湿度を選択し、 **[Update configuration]\(構成の更新\)** ボタンをクリックします。 
  
![ビュー - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

**[保存]** を選択してビューを保存します。

別のプロパティやテレメトリ値を表示するタイルをさらに追加できます。 また、静的テキストやリンク、画像も追加できます。 ダッシュボード上のタイルを移動したり、そのサイズを変更したりするには、そのタイルの上にマウス ポインターを移動して、タイルを新しい場所にドラッグするか、そのサイズを変更します。
  
![ビュー - Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>デバイス フォームを追加する

デバイス フォームでは、オペレーターが、書き込み可能なデバイス プロパティとクラウド プロパティを編集できます。 作成者は複数のフォームを定義し、各フォームに表示するデバイス プロパティとクラウド プロパティを選択することができます。 フォームには、読み取り専用のデバイス プロパティを表示することもできます。

環境センサーのプロパティを確認および編集するためのビューを作成には:

**[環境センサー]** テンプレートの **[ビュー]** に移動します。 **[デバイスとクラウドのデータの編集]** タイルを選択し、新しいビューを追加します。
  
![ビュー - Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

フォーム名として「**環境センサーのプロパティ**」と入力します。

**[顧客名]** と **[Last service date]\(前回点検日\)** の各クラウド プロパティを、フォームの既存のセクションにドラッグします。
  
![ビュー - Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

**[保存]** を選択してビューを保存します。

### <a name="generate-default-views"></a>既定のビューを生成する

Azure IoT Edge テンプレートでは、既定ビューの生成機能はサポートされていません 

## <a name="publish-device-template"></a>デバイス テンプレートの公開

シミュレートされた環境センサーの作成や、実物の環境センサーの接続を行うには、デバイス テンプレートを公開する必要があります。

デバイス テンプレートを公開するには:

1. **[デバイス テンプレート]** ページで、作成したデバイス テンプレートに移動します。

2. **[発行]** を選択します。
  
    ![ビュー - 公開](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. **[Publish a Device Template]\(デバイス テンプレートの公開\)** ダイアログで、 **[公開]** を選択します。
  
    ![ビュー - 公開](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

デバイス テンプレートを公開すると、 **[デバイス]** ページに表示され、オペレーターが確認できるようになります。 公開済みのデバイス テンプレートで、新しいバージョンを作成せずにデバイス機能モデルを編集することはできません。 ただし、公開済みのデバイス テンプレートでも、クラウド プロパティ、カスタマイズ、ビューについては、新バージョンを作成することなく更新できます。 変更完了後に **[公開]** を選択すると、変更内容がオペレーターにプッシュされます。
  
![ビュー - 公開](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

* 新しい Edge をリーフ デバイス テンプレートとして作成する
* アップロードされた配置マニフェストからモジュールを生成する
* 複合型のテレメトリとプロパティを追加する
* クラウド プロパティの作成。
* カスタマイズの作成。
* デバイス テレメトリの視覚化の定義。
* Edge デバイス テンプレートを公開する。

ここでは、Azure IoT Central アプリケーションでデバイス テンプレートを作成しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [デバイスの接続](./tutorial-connect-pnp-device.md)
