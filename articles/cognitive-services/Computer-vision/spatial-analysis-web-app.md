---
title: 空間分析 Web アプリをデプロイする
titleSuffix: Azure Cognitive Services
description: Web アプリで空間分析を使用する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: ecccbb4e2741cc7f413e9b2076bd3199b00dd9a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752082"
---
# <a name="how-to-deploy-a-spatial-analysis-web-application"></a>方法: 空間分析 Web アプリをデプロイする

この記事では、IotHub から空間分析データ (分析情報) を収集して視覚化する Web アプリをデプロイする方法について説明します。 これには、さまざまなシナリオや業界で役立つアプリケーションが含まれます。 たとえば、会社が不動産スペースの使用を最適化する場合、さまざまなシナリオでソリューションをすばやく作成できます。 

このチュートリアルで学習する内容は次のとおりです。

* 空間分析コンテナーをデプロイする
* 操作とカメラを構成する
* Web アプリで IoT Hub 接続を構成する
* Web アプリをデプロイしてテストする

このアプリでは、次のシナリオについて紹介します。

* スペース/店舗に出入りする人の数
* チェックアウト エリア/ゾーンに入って出た人の数と、チェックアウト ライン内にいた時間 (滞在時間)
* フェイス マスクを着用している人の数 
* ソーシャル ディタンスのガイドラインに違反している人の数

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/cognitive-services/)
* Azure IoT Edge デプロイの構成と [Azure IoT Hub](../../iot-hub/index.yml) の基礎知識
* 構成済みの[ホスト コンピューター](spatial-analysis-container.md)

## <a name="deploy-the-spatial-analysis-container"></a>空間分析コンテナーをデプロイする

[ホスト コンピューターのセットアップ](./spatial-analysis-container.md)に関する記事に従って、ホスト コンピューターを構成し、IoT Edge デバイスを Azure IoT Hub に接続します。 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>サブスクリプションに Azure IoT Hub サービスをデプロイする

まず、Standard 価格レベル (S1) または Free レベル (S0) で Azure IoT Hub サービスのインスタンスを作成します。 次の手順に従って、Azure CLI を使用してこのインスタンスを作成します。

必要なパラメーターを入力します。
* サブスクリプション:Azure サブスクリプションの名前または ID。
* リソース グループ: リソース グループの名前を作成します。
* Iot Hub 名: IoT Hub の名前を作成します。
* IoTHub 名: 作成した IoT Hub の名前。 
* Edge Device Name\(エッジ デバイス名\): エッジ デバイスの名前を作成します。

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>ホスト コンピューター上の Azure IoT Edge にコンテナーをデプロイする

Azure CLI を使用して、**空間分析** コンテナーを IoT モジュールとしてホスト コンピューターにデプロイします。 デプロイ プロセスには、デプロイに必要なコンテナー、変数、構成の概要を示す配置マニフェスト ファイルが必要です。 サンプルの配置マニフェストは、すべてのシナリオの構築済みの構成を含む [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json) で確認できます。  

### <a name="set-environment-variables"></a>環境変数の設定

IoT Edge モジュールのほとんどの **環境変数** は、上記のリンク先のサンプル *DeploymentManifest.json* ファイルに既に設定されています。 このファイルで、次のように、`ENDPOINT` および `APIKEY` 環境変数を検索します。 以前に作成したエンドポイント URI と API キーに値を置き換えます。 EULA 値が "accept" に設定されていることを確認します。 

```json
"EULA": { 
    "value": "accept"
},
"BILLING":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"APIKEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>操作のパラメーターを構成する

必要なすべての構成 (操作、記録したビデオ ファイルの URL とゾーンなど) が既に存在するサンプル [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json)を使用している場合は、「**デプロイの実行**」セクションにスキップできます。

空間分析コンテナーの初期構成が完了しました。次に、操作のパラメーターを構成し、それらをデプロイに追加します。 

最初の手順では、サンプルの [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json) を更新し、必要な操作を構成します。 たとえば、cognitiveservices.vision.spatialanalysis-personcount の構成は次のようになります。

```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

配置マニフェストが更新されたら、カメラの製造元の指示に従ってカメラをインストールし、カメラの URL、ユーザー名、パスワードを構成します。 

次に、`VIDEO_URL` をカメラの RTSP URL に設定し、カメラに接続するための資格情報を設定します。

エッジ デバイスに複数の GPU がある場合は、この操作を実行する GPU を選択します。 一度に 1 つの GPU で実行される操作が 8 つ以下の場合は、必ず操作の負荷を分散してください。  

次に、人数をカウントするゾーンを構成します。 ゾーンの多角形を構成するには、まず、製造元の指示に従ってカメラからフレームを取得します。 多角形の各頂点を決定するには、フレーム上のポイントを選択し、フレームの左上隅を基準としたポイントの X、Y ピクセル座標を取得して、対応するフレーム サイズで除算します。 結果を頂点の X、Y 座標として設定します。 ゾーンの多角形構成は、`SPACEANALYTICS_CONFIG` フィールドで設定できます。

これは、サイズが 1920/1080 のフレームの頂点座標がどのように計算されるのかを示すサンプル ビデオ フレームです。
![サンプル ビデオ フレーム](./media/spatial-analysis/sample-video-frame.jpg)

検出された人数がカウントされ、イベントが生成されるときの信頼度のしきい値を選択することもできます。 すべてのイベントを出力する場合は、しきい値を 0 に設定します。

### <a name="execute-the-deployment"></a>デプロイを実行する

配置マニフェストが完成したら、Azure CLI で次のコマンドを使用して、コンテナーを IoT Edge モジュールとしてホスト コンピューターにデプロイします。

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

必要なパラメーターを入力します。

* IoT Hub 名: Azure IoT Hub の名前。
* DeploymentManifest.json:デプロイ ファイルの名前。
* IoT Edge デバイス名: ホスト コンピューターの IoT Edge デバイス名。
* サブスクリプション:サブスクリプションの ID または名前。

このコマンドによってデプロイが開始されます。Azure portal の Azure IoT Hub インスタンスでデプロイの状態を確認できます。 デバイスでコンテナー イメージのダウンロードが終了し、実行が開始されるまで、状態に "*417 - デバイスのデプロイ構造が設定されていません*" と表示されることがあります。

### <a name="validate-that-the-deployment-was-successful"></a>デプロイが成功したことを検証する

Azure portal の IoT Hub インスタンスで、spatial-analysis モジュールの [IoT Edge モジュールの設定] にある *[ランタイムの状態]* を見つけます。 **[ランタイムの状態]** の **[必要な値]** と *[報告された値]* に `Running` と表示されている必要があります。 これは、Azure portal で次のように表示されます。

![デプロイの検証の例](./media/spatial-analysis/deployment-verification.png)

この時点で、空間分析コンテナーは操作を実行しています。 操作の AI 分析情報が生成され、これらの分析情報がテレメトリとして Azure IoT Hub インスタンスにルーティングされます。 追加のカメラを構成するには、配置マニフェスト ファイルを更新し、デプロイをもう一度実行します。

## <a name="spatial-analysis-web-application"></a>空間分析 Web アプリ

空間分析 Web アプリを使用すると、開発者はサンプル Web アプリをすばやく構成し、Azure 環境でホストし、アプリを使用して E2E イベントを検証できます。

## <a name="build-docker-image"></a>Docker イメージをビルドする

[ガイド ](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/README.md#docker-image) に従って、イメージをビルドし、サブスクリプション内の Azure Container Registry にプッシュします。

## <a name="setup-steps"></a>セットアップの手順

コンテナーをインストールするには、新しい Azure App Service を作成し、必要なパラメーターを入力します。 次に、 **[Docker]** タブに移動し、 **[単一コンテナー]** 、 **[Azure Container Registry]** の順に選択します。 上記でイメージをプッシュした Azure Container Registry のインスタンスを使用します。

![イメージの詳細を入力する](./media/spatial-analysis/solution-app-create-screen.png)

上記のパラメーターを入力したら、 **[レビュー + 作成]** をクリックしてアプリを作成します。

### <a name="configure-the-app"></a>Configure the app 

セットアップが完了するまで待ち、Azure portal でリソースに移動します。 **構成** セクションに移動し、次の 2 つの **アプリケーション設定** を追加します。

* `EventHubConsumerGroup` - Azure IoT Hub のコンシューマー グループの文字列名。IoT Hub に新しいコンシューマー グループを作成することも、既定のグループを使用することもできます。 
* `IotHubConnectionString` - Azure IoT Hub への接続文字列。これは、Azure IoT Hub リソースの ![[パラメーターの構成]](./media/spatial-analysis/solution-app-config-page.png) のキー セクションから取得できます。

この 2 つの設定を追加したら、 **[保存]** をクリックします。 次に、左側のナビゲーション メニューで **[認証/承認]** をクリックし、目的の認証レベルで更新します。 Azure Active Directory (Azure AD) 簡易をお勧めします。 

### <a name="test-the-app"></a>アプリのテスト

Azure サービスに移動し、デプロイが成功したことと、Web アプリが実行されていることを確認します。 構成済みの URL (`<yourapp>.azurewebsites.net`) に移動して、実行中のアプリを表示します。

![展開をテスト](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>PersonCount ソース コードを取得する
このアプリケーションのソース コードを表示または変更する場合は、[GitHub](https://github.com/Azure-Samples/cognitive-services-spatial-analysis) を参照してください。

## <a name="next-steps"></a>次のステップ

* [空間分析の操作を構成する](./spatial-analysis-operations.md)
* [ロギングおよびトラブルシューティング](spatial-analysis-logging.md)
* [カメラの配置ガイド](spatial-analysis-camera-placement.md)
* [ゾーンとラインの配置ガイド](spatial-analysis-zone-line-placement.md)
