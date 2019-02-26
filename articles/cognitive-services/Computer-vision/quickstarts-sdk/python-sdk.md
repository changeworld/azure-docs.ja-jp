---
title: クイック スタート:Python SDK
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、一般的なタスクに Python SDK を使用する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: pafarley
ms.openlocfilehash: 3043067f326f782c51be38382070ae0db0e90f4d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314172"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Python 用の Azure Cognitive Services Computer Vision SDK

Computer Vision サービスを使用すると、開発者は、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。 Computer Vision のアルゴリズムでは、ユーザーが関心を寄せた視覚的特徴に応じて、さまざまな方法で画像のコンテンツを分析します。 たとえば、Computer Vision では、画像が成人向けや人種差別的なコンテンツを含んでいるかどうかを判断したり、画像内の顔をすべて検出したり、手書きのテキストや印刷されたテキストを取得したりできます。 このサービスでは、JPEG や PNG などの一般的な形式の画像を操作します。 

自分のアプリケーションで Computer Vision を使用すると、以下のことができます。

- 画像を分析して分析情報を得る
- 画像からテキストを抽出する
- サムネイルを生成する

その他のドキュメントをお探しですか?

* [SDK のリファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Cognitive Services Computer Vision のドキュメント](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成できます][azure_sub]
* Azure [Computer Vision リソース][computervision_resource]
* [Python 3.6 以降][python]

Computer Vision API のアカウントが必要な場合、次の [Azure CLI][azure_cli] コマンドを使用して作成できます。

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind ComputerVision \
    --sku S1 \
    --yes
```

## <a name="installation"></a>インストール

[pip][pip] を使用して Azure Cognitive Services Computer Vision SDK を (必要に応じて[仮想環境][venv]内に) インストールします。

### <a name="configure-a-virtual-environment-optional"></a>仮想環境を構成する (オプション)

必須ではありませんが、[仮想環境][virtualenv]を使用すると、お使いのベース システムと Azure SDK 環境を相互に分離させておくことができます。 [venv][venv] を使用した次のコマンドを実行して、仮想環境 (例: `cogsrv-vision-env`) を構成した後にその仮想環境に入ります。

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```

### <a name="install-the-sdk"></a>SDK のインストール

[pip][pip] を使用して、Python 用の Azure Cognitive Services Computer Vision SDK [パッケージ][pypi_computervision]をインストールします。

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Computer Vision リソースを作成した後は、クライアント オブジェクトをインスタンス化するために、その**リージョン**とその**アカウント キー**の 1 つが必要になります。

[ComputerVisionAPI][ref_computervisionclient] クライアント オブジェクトのインスタンスを作成するときに、これらの値を使用します。 

### <a name="get-credentials"></a>資格情報の取得

次の [Azure CLI][cloud_shell] スニペットを使用して、2 つの環境変数に Computer Vision アカウントの**リージョン**とその**アカウント キー**の 1 つを設定します (これらの値は、[Azure portal][azure_portal] 内で見つけることもできます)。 このスニペットは Bash シェル用にフォーマットされています。

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```

### <a name="create-client"></a>クライアントの作成

`ACCOUNT_REGION` と `ACCOUNT_KEY` 環境変数を設定したら、[ComputerVisionAPI][ref_computervisionclient] クライアント オブジェクトを作成できます。

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

## <a name="usage"></a>使用法

[ComputerVisionAPI][ref_computervisionclient] クライアント オブジェクトを初期化すると、次のことができます。

* 画像を分析する。顔、色、タグなどの特定の特徴について画像を分析できます。   
* サムネイルを生成する。元の画像のサムネイルとして使用するカスタムの JPEG 画像を作成できます。
* 画像の説明を取得する。その主題の領域に基づいて画像の説明を取得できます。 

このサービスの詳細については、「[Computer Vision とは][computervision_docs]」を参照してください。

## <a name="examples"></a>例

次のセクションでは、以下に示す Computer Vision の最も一般的なタスクのいくつかに対応したコード スニペットをいくつか紹介します。

* [イメージを分析する](#analyze-an-image)
* [主題の領域の一覧を取得する](#get-subject-domain-list)
* [領域別に画像を分析する](#analyze-an-image-by-domain)
* [画像のテキスト説明を取得する](#get-text-description-of-an-image)
* [画像から手書きのテキストを読み取る](#get-text-from-image)
* [サムネイルを生成する](#generate-thumbnail)

### <a name="analyze-an-image"></a>イメージを分析する

[`analyze_image`][ref_computervisionclient_analyze_image] を使用して、特定の特徴について画像を分析できます。 [`visual_features`][ref_computervision_model_visualfeatures] プロパティを使用して、画像に対して実行する分析の種類を設定します。 一般的な値は `VisualFeatureTypes.tags` と `VisualFeatureTypes.description` です。

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>主題の領域の一覧を取得する

[`list_models`][ref_computervisionclient_list_models] を使用して、画像の分析に使用する主題の領域を確認します。 [領域別に画像を分析する](#analyze-an-image-by-domain)場合に、これらの領域名が使用されます。 領域の例は `landmarks` です。

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>領域別に画像を分析する

[`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain] を使用して、主題の領域別に画像を分析できます。 正しい領域名を使用するために、[サポートされている主題の領域の一覧](#get-subject-domain-list)を取得します。  

```Python
domain = "landmarks"
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>画像のテキスト説明を取得する

[`describe_image`][ref_computervisionclient_describe_image] を使用して、画像の、言語に基づくテキスト説明を取得できます。 画像に関連するキーワードのテキスト分析を実行する場合は、`max_description` プロパティを使用して、いくつかの説明を要求します。 次の画像のテキスト説明の例には、`a train crossing a bridge over a body of water`、`a large bridge over a body of water`、`a train crossing a bridge over a large body of water` が含まれています。

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>画像からテキストを取得する

画像から手書きのテキストや印刷されたテキストを取得できます。 これには、SDK に対する 2 つの呼び出し ([`recognize_text`][ref_computervisionclient_recognize_text] と [`get_text_operation_result`][ref_computervisionclient_get_text_operation_result]) が必要です。 recognize_text の呼び出しは非同期です。 get_text_operation_result 呼び出しの結果では、テキスト データを抽出する前に、[`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] を使用して、最初の呼び出しが完了しているかどうかを確認する必要があります。 結果には、テキストと、テキストの境界ボックスの座標が含まれます。 

```Python
url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>サムネイルを生成する

[`generate_thumbnail`][ref_computervisionclient_generate_thumbnail] を使用して、画像のサムネイル (JPG) を生成できます。 サムネイルは、縦横比が元の画像と同じである必要はありません。 

この例では、[Pillow][pypi_pillow] パッケージを使用して、新しいサムネイル画像をローカルに保存します。

```Python
from PIL import Image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="general"></a>全般

Python SDK を使用して [ComputerVisionAPI][ref_computervisionclient] クライアント オブジェクトを操作する場合、[`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] クラスを使用してエラーを返します。 このサービスによって返されるエラーは、REST API 要求に対して返される同じ HTTP 状態コードに対応しています。

たとえば、無効なキーを使用して画像を分析しようとすると、`401` エラーが返されます。 次のスニペットでは、例外をキャッチし、エラーに関する追加情報を表示することで、[エラー][ref_httpfailure]が適切に処理されます。

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>再試行による一時的なエラーを処理する

[ComputerVisionAPI][ref_computervisionclient] クライアントの操作中に、このサービスによって適用された[レート制限][computervision_request_units]によって一時的な障害が発生したり、ネットワークの停止など、他の一時的な問題が発生したりする可能性があります。 これらの種類の障害の処理については、クラウド設計パターン ガイドの「[再試行パターン][azure_pattern_retry]」および「[サーキット ブレーカー パターン][azure_pattern_circuit_breaker]」を参照してください。

## <a name="next-steps"></a>次の手順

### <a name="more-sample-code"></a>その他のサンプル コード

Computer Vision Python SDK のサンプルは、この SDK の GitHub リポジトリでいくつか公開されています。 これらのサンプルでは、Computer Vision の操作中によく発生する追加のシナリオのコード例を示しています。

* [recognize_text][recognize-text]

### <a name="additional-documentation"></a>その他のドキュメント

Computer Vision サービスに関するさらに詳細なドキュメントについては、docs.microsoft.com にある [Azure Computer Vision のドキュメント][computervision_docs]を参照してください。

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

