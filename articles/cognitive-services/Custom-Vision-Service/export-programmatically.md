---
title: プログラムを使用してモデルをエクスポートする
titleSuffix: Azure Cognitive Services
description: トレーニング済みのモデルをエクスポートするには、Custom Vision クライアント ライブラリを使用します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: pafarley
ms.openlocfilehash: b67c4e6165b90a580a3a29583bdfe93a98d327c8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361387"
---
# <a name="export-a-model-programmatically"></a>プログラムを使用してモデルをエクスポートする

[Custom Vision Web サイト](https://www.customvision.ai/)で使用できるすべてのエクスポート オプションは、クライアント ライブラリを使用してプログラムで実行することもできます。 こうすることで、ローカル デバイスで使用するモデルのイテレーションの再トレーニングと更新のプロセスを完全に自動化できます。

このガイドでは、Python SDK を使用して、モデルを ONNX ファイルにエクスポートする方法について説明します。

## <a name="create-a-training-client"></a>トレーニング クライアントを作成する

モデルのイテレーションをエクスポートするには、[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) オブジェクトが必要です。 Custom Vision トレーニング リソースの Azure エンドポイントとサブスクリプション キーの変数を作成し、それらを使用してクライアント オブジェクトを作成します。

```python
ENDPOINT = "PASTE_YOUR_CUSTOM_VISION_TRAINING_ENDPOINT_HERE"
training_key = "PASTE_YOUR_CUSTOM_VISION_TRAINING_SUBSCRIPTION_KEY_HERE"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)
```

> [!IMPORTANT]
> 完了したらコードからキーを削除し、それらを公開しないでください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../cognitive-services-security.md)に関する記事を参照してください。

## <a name="call-the-export-method"></a>エクスポート メソッドの呼び出し

**export_iteration** メソッドを呼び出します。
* エクスポートするモデルのプロジェクト ID、イテレーション ID を指定します。 
* *platform* パラメーターは、エクスポート先のプラットフォームを指定します。使用できる値は、`CoreML`、`TensorFlow`、`DockerFile`、`ONNX`、`VAIDK`、および `OpenVino` です。 
* *flavor* パラメーターは、エクスポートされるモデルの形式を指定します。使用できる値は、`Linux`、`Windows`、`ONNX10`、`ONNX12`、`ARM`、`TensorFlowNormal`、および `TensorFlowLite` です。
* *raw* パラメーターにより、オブジェクト モデルの応答と共に、生の JSON 応答を取得するオプションが得られます。

```python
project_id = "PASTE_YOUR_PROJECT_ID"
iteration_id = "PASTE_YOUR_ITERATION_ID"
platform = "ONNX"
flavor = "ONNX10"
export = trainer.export_iteration(project_id, iteration_id, platform, flavor, raw=False)
```

詳細については、 **[export_iteration](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#export-iteration-project-id--iteration-id--platform--flavor-none--custom-headers-none--raw-false----operation-config-)** メソッドを参照してください。

## <a name="download-the-exported-model"></a>エクスポートしたモデルのダウンロード

次に、**get_exports** メソッドを呼び出して、エクスポート操作の状態を確認します。 操作は非同期的に実行されるため、操作が完了するまでこのメソッドをポーリングする必要があります。 完了すると、モデルのイテレーションをデバイスにダウンロードできる URI を取得できます。

```python
while (export.status == "Exporting"):
    print ("Waiting 10 seconds...")
    time.sleep(10)
    exports = trainer.get_exports(project_id, iteration_id)
    # Locate the export for this iteration and check its status  
    for e in exports:
        if e.platform == export.platform and e.flavor == export.flavor:
            export = e
            break
    print("Export status is: ", export.status)
```

詳細については、 **[get_exports](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#get-exports-project-id--iteration-id--custom-headers-none--raw-false----operation-config-)** メソッドを参照してください。

その後、プログラムによって、エクスポートされたモデルをデバイス上の場所にダウンロードできます。

```python
if export.status == "Done":
    # Success, now we can download it
    export_file = requests.get(export.download_uri)
    with open("export.zip", "wb") as file:
        file.write(export_file.content)
```

## <a name="next-steps"></a>次のステップ

以下のいずれかの記事またはサンプルを調べて、エクスポートしたモデルをアプリケーションに統合します。

* [Python での TensorFlow モデルの使用](export-model-python.md)
* [Windows Machine Learning での ONNX モデルの使用](custom-vision-onnx-windows-ml.md)
* Swift によって画像をリアルタイム分類するために、[iOS アプリケーションでの CoreML モデル](https://go.microsoft.com/fwlink/?linkid=857726)のサンプルを確認してください。
* Android 上で画像をリアルタイム分類するために、[Android アプリケーションでの Tensorflow モデル](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)のサンプルを確認してください。
* Xamarin iOS アプリで画像をリアルタイム分類するために、[Xamarin を利用した CoreML モデル](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)のサンプルを確認してください。