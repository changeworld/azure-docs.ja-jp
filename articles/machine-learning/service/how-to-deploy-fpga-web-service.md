---
title: Azure Machine Learning でモデルを Web サービスとして FPGA 上に配置する
description: FPGA 上で実行されるモデルを含む Web サービスを Azure Machine Learning で配置する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971486"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Azure Machine Learning でモデルを Web サービスとして FPGA 上に配置する

モデルを [FPGA (field programmable gate arrays)](concept-accelerate-with-fpgas.md) 上の Web サービスとしてデプロイできます。  FPGA を使用すると、単一のバッチ サイズでも、待機時間が極端に短い推論を実行できます。   

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- Azure Machine Learning ワークスペースと、Azure Machine Learning SDK for Python がインストール済み。 これらの前提条件を満たす方法については、「[Azure Machine Learning サービスの開発環境を構成する方法](how-to-configure-environment.md)」を参照してください。
 
  - ご利用のワークステーションが、*米国東部 2* リージョン内にある必要があります。

  - contrib extras をインストールします。

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>モデルを作成してデプロイする
パイプラインを作成して入力イメージを処理し、FPGA 上で ResNet 50 を使用して入力イメージを抽出した後、ImageNet データ セットでトレーニング済みの分類子を使って機能を実行します。

手順に従って次の操作を行います。

* モデル パイプラインを定義する
* モデルをデプロイする
* 配置したモデルを使用する
* 展開したサービスを削除する

> [!IMPORTANT]
> 待機時間とスループットを最適化するために、ご利用のクライアントは、エンドポイントと同じ Azure リージョンに存在する必要があります。  現時点では、この API は、Azure の米国東部リージョンに作成されています。

### <a name="get-the-notebook"></a>ノートブックを入手する

便利なように、このチュートリアルは Jupyter Notebook として提供されています。 `project-brainwave/project-brainwave-quickstart.ipynb` ノートブックを実行するには、以下のいずれかの方式を使用します。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>イメージの前処理
パイプラインの最初のステージは、イメージを処理するためのものです。

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>Featurizer を追加する
モデルを初期化して、特徴抽出器として使用される ResNet50 の量子化されたバージョンの TensorFlow チェックポイントをダウンロードします。

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>分類子を追加する
この分類子は ImageNet データ セットでトレーニングされています。

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>サービス定義を作成する
これで、イメージの前工程、特徴抽出器、およびサービスを実行する分類子を定義できたので、サービス定義を作成することができます。 サービス定義は、FPGA サービスにデプロイされているモデルから生成されたファイルのセットです。 サービス定義はパイプラインで構成されています。 パイプラインは順番に実行される一連のステージです。  TensorFlow ステージ、Keras ステージ、および BrainWave ステージがサポートされています。  ステージは、各ステージにおける後続ステージへの入力と出力を使って、サービス上で順番に実行されます。

TensorFlow ステージを作成するには、グラフ (この場合は、既定のグラフが使用されます) およびこのステージへの入力テンソルと出力テンソルを含むセッションを指定します。  この情報は、サービス上で実行できるように、グラフを保存するために使用されます。

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>モデルをデプロイする
サービス定義からサービスを作成します。  ご利用のワークステーションが、米国東部 2 の場所にある必要があります。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>サービスをテストする
イメージを API に送信して応答をテストするには、出力クラス ID から ImageNet クラス名にマッピングを追加します。

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

ご利用のサービスを呼び出し、以下のファイル名 "your-image.jpg" をご使用のマシンからのイメージに置き換えます。 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>サービスをクリーンアップする
サービスを削除します。

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>FPGA の Web サービスをセキュリティで保護する

FPGA 上で実行されている Azure Machine Learning モデルでは、SSL のサポートとキー ベースの認証を提供します。 これにより、サービスへのアクセスを制限し、クライアントによって送信されるデータをセキュリティで保護できます。

> [!IMPORTANT]
> 認証は、SSL 証明書とキーが提供されるサービスに対して有効にできます。 
>
> SSL を有効にしない場合、インターネット上のすべてのユーザーがサービスへの呼び出しを実行できます。
>
> SSL を有効にすると、サービスにアクセスするときに認証キーが要求されます。

SSL は、クライアントとサービス間で送信されるデータを暗号化します。 これは、クライアントがサーバーの ID を検証するためにも使用されます。

SSL が有効なサービスを展開するか、既に展開されているサービスを更新できます。 手順は同じです。

1. ドメイン名を取得します。

2. SSL 証明書を取得します。

3. SSL が有効なサービスを展開するか更新します。

4. サービスを指すように DNS を更新します。

### <a name="acquire-a-domain-name"></a>ドメイン名を取得する

ドメイン名をまだ所有していない場合は、__ドメイン名レジストラー__から購入できます。 プロセスと費用は、レジストラーによって異なります。 レジストラーも、ドメイン名を管理するためのツールを提供しています。 これらのツールを使用して、完全修飾ドメイン名 (www.contoso.com など) を、サービスがホストされる IP アドレスにマップします。

### <a name="acquire-an-ssl-certificate"></a>SSL 証明書を取得する

SSL 証明書を取得する方法はたくさんあります。 最も一般的な方法は、__証明機関__(CA) から購入することです。 証明書の取得場所に関係なく、次のファイルが必要です。

* __証明書__。 証明書は、完全な証明書チェーンを含み、PEM でエンコードされている必要があります。
* __キー__。 キーは、PEM でエンコードされている必要があります。

> [!TIP]
> 証明機関が、証明書とキーを PEM でエンコードされたファイルとして提供できない場合は、 [OpenSSL](https://www.openssl.org/) などのユーティリティを使用して、フォーマットを変更できます。

> [!IMPORTANT]
> 自己署名証明書は、開発時のみ使用してください。 運用環境では使用しないでください。
>
> 自己署名証明書を使用する場合は、「[自己署名証明書付きのサービスの使用](#self-signed)」セクションの具体的な指示を参照してください。

> [!WARNING]
> 証明書を要求するときは、サービスで使用するアドレスの完全修飾ドメイン名 (FQDN) を指定する必要があります。 例: www.contoso.com。 サービスの ID を検証するときに、証明書に記載されているアドレスとクライアントによって使用されるアドレスが比較されます。
>
> アドレスが一致しない場合、クライアントはエラーを受信します。 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>SSL が有効なサービスを展開するか更新する

SSL が有効なサービスを展開するには、`ssl_enabled` パラメーターを `True` に設定します。 `ssl_certificate` パラメーターを__証明書__ファイルの値に設定し、`ssl_key` を __キー__ ファイルの値に設定します。 次の例は、SSL が有効なサービスの展開を示しています。

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

`create_service` 操作の応答に、サービスの IP アドレスが含まれています。 この IP アドレスは、DNS 名をサービスの IP アドレスに マップするときに使用されます。

応答には、サービスを利用するときに使用される__主キー__と__セカンダリ キー__も含まれています。

### <a name="update-your-dns-to-point-to-the-service"></a>サービスを指すように DNS を更新する

ドメイン名レジストラーが提供しているツールを使用して、ドメイン名の DNS レコードを更新します。 レコードは、サービスの IP アドレスを指している必要があります。

> [!NOTE]
> レジストラと、ドメイン名に対して構成された Time to Live (TTL) に応じて、クライアントがドメイン名を解決できるまで、数分から数時間かかる可能性があります。

### <a name="consume-authenticated-services"></a>認証済みのサービスを使用する

次の例は、Python と C# を使用して認証済みのサービスを使用する方法を示しています。

> [!NOTE]
> `authkey` を、サービスの作成時に返された主キーまたはセカンダリ キー置き換えてください。

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

他の gRPC クライアントは、承認ヘッダーを設定することで、要求を認証できます。 一般的な方法は、`SslCredentials` と `CallCredentials` を組み合わせる `ChannelCredentials` オブジェクトを作成することです。 これは、要求の Authorization ヘッダーに追加されます。 特定のヘッダーに対するサポートの実装の詳細については、[https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html) を参照してください。

次の例は、C# と Go でヘッダーを設定する方法を示しています。

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>自己署名付き証明書サービスの使用

クライアントが自己署名証明書によってセキュリティ保護されているサーバーで認証されるようにするには、2 つの方法があります。

* クライアント システムで、クライアント システム上の `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 環境変数を、証明書ファイルを指すように設定します。

* `SslCredentials` オブジェクトを構築するときに、証明書ファイルの内容をコンストラクターに渡します。

どちらの方法を使用しても、gRPC は、証明書をルート証明書として使用するようになります。

> [!IMPORTANT]
> gRPC では、信頼されていない証明書は受け入れられません。 信頼されていない証明書を使用すると、状態コード `Unavailable` で失敗します。 エラーの詳細の中に、`Connection Failed` が含まれます。
