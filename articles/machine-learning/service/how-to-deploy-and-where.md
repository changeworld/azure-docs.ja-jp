---
title: Web サービスとしてモデルをデプロイする
titleSuffix: Azure Machine Learning service
description: お客様の Azure Machine Learning service モデルをデプロイする方法と場所について説明します (Azure Container Instances、Azure Kubernetes Service、Azure IoT Edge、フィールド プログラマブル ゲート アレイ)。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 0c171ff768395540c123c4ef2a19168d926b0661
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633829"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning service を使用してモデルをデプロイする

Azure Machine Learning service には、SDK を使用してお客様のトレーニング済みのモデルをデプロイする方法が複数用意されています。 このドキュメントでは、Web サービスとして Azure クラウドに、または IoT エッジ デバイスに、モデルをデプロイする方法を説明します。

次のコンピューティング ターゲットにモデルをデプロイできます。

| コンピューティング ターゲット | デプロイの種類 | 説明 |
| ----- | ----- | ----- |
| [Azure Container Instances (ACI)](#aci) | Web サービス | 高速なデプロイ。 開発またはテストに適しています。 |
| [Azure Kubernetes Service (AKS)](#aks) | Web サービス | 高スケールの運用デプロイに適しています。 自動スケール、および高速な応答時間を実現します。 |
| [Azure IoT Edge](#iotedge) | IoT モジュール | IoT デバイスにモデルをデプロイします。 推論はデバイス上で行われます。 |
| [フィールド プログラマブル ゲート アレイ (FPGA)](#fpga) | Web サービス | リアルタイムの推論に適した超低遅延。 |

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning サービス ワークスペースと、Azure Machine Learning SDK for Python がインストール済み。 これら前提条件を取得する方法については､[Azure Machine Learning のクイック スタートの概要](quickstart-get-started.md)で説明しています｡

- pickle (`.pkl`) または ONNX (`.onnx`) 形式のトレーニング済みのモデル。 トレーニング済みのモデルがない場合は、[モデルのトレーニング](tutorial-train-models-with-aml.md)に関するチュートリアルの手順を使用して、Azure Machine Learning service で 1 つトレーニングして登録します。

- コードのセクションでは、お客様の Machine Learning ワークスペースが `ws` によって参照されることを前提としています。 たとえば、「 `ws = Workspace.from_config()` 」のように入力します。

## <a name="deployment-workflow"></a>デプロイのワークフロー

モデルのデプロイのプロセスは、すべてのコンピューティング先で同様です。

1. モデルをトレーニングします。
1. モデルを登録します。
1. イメージの構成を作成します。
1. イメージを作成します。
1. イメージをコンピューティング先にデプロイします。
1. 展開をテスト
1. (オプション) 成果物をクリーンアップします。

    * **Web サービスとしてデプロイする**場合、3 つのデプロイ オプションがあります。

        * [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-): この方法を使用する場合、モデルを登録したり、イメージを作成したりする必要はありません。 ただし、モデルまたはイメージの名前、あるいは関連付けられたタグおよび説明を制御することはできません。
        * [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-): この方法を使用する場合、イメージを作成する必要はありません。 ただし、作成されるイメージの名前を制御することはできません。
        * [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-): この方法を使用する前に、モデルを登録し、イメージを作成してください。

        このドキュメントの例では、`deploy_from_image` を使用します。

    * **IoT Edge モジュールとしてデプロイする**場合は、モデルを登録してイメージを作成する必要があります。

## <a name="register-a-model"></a>モデルを登録する

デプロイできるのは、トレーニング済みのモデルのみです。 モデルは、Azure Machine Learning や別のサービスを使用してトレーニングできます。 ファイルからモデルを登録するには、次のコードを使用します。

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

> [!NOTE]
> 例では pickle ファイルとして格納されたモデルを使用していますが、ONNX モデルを使用することもできます。 ONNX モデルの使用の詳細については、[ONNX と Azure Machine Learning](how-to-build-deploy-onnx.md) に関するドキュメントを参照してください。

詳細については、[Model クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)のリファレンス ドキュメントを参照してください。

## <a id="configureimage"></a>イメージの構成を作成する

デプロイされたモデルは、イメージとしてパッケージ化されます。 イメージには、モデルの実行に必要な依存関係が含まれています。

**Azure Container Instance**、**Azure Kubernetes Service**、**Azure IoT Edge** のデプロイでは、イメージの構成を作成するために `azureml.core.image.ContainerImage` クラスが使用されます。 そして、新しい Docker イメージを作成するために、このイメージの構成が使用されます。 

次のコードは、新しいイメージの構成を作成する方法を示しています。

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

この構成では、`score.py` ファイルを使用して要求がモデルに渡されます。 このファイルには 2 つの関数が含まれています。

* `init()`:通常は、この関数によってモデルがグローバル オブジェクトに読み込まれます。 この関数は、Docker コンテナーを開始するときに 1 回だけ実行されます。 

* `run(input_data)`:この関数では、モデルを使用して、入力データに基づいて値が予測されます。 実行に対する入力と出力は、通常、JSON を使用してシリアル化およびシリアル化解除が実行されますが、その他の形式もサポートされています。

`score.py` ファイルの例については、[画像の分類のチュートリアル](tutorial-deploy-models-with-aml.md#make-script)に関するページを参照してください。 ONNX モデルが使用される例については、[ONNX と Azure Machine Learning](how-to-build-deploy-onnx.md) に関するドキュメントを参照してください。

`conda_file` パラメーターは、Conda 環境ファイルを指定するために使用されます。 このファイルによって、デプロイされたモデルの Conda 環境が定義されます。 このファイルの作成の詳細については、[環境ファイル (myenv.yml) の作成](tutorial-deploy-models-with-aml.md#create-environment-file)に関するページを参照してください。

詳細については、[ContainerImage クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)のリファレンス ドキュメントを参照してください。

## <a id="createimage"></a>イメージを作成する

イメージの構成を作成したら、それを使用してイメージを作成できます。 このイメージは、お客様のワークスペースのコンテナー レジストリに格納されます。 一度作成すれば、同じイメージを複数のサービスにデプロイできます。

```python
# Create the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**推定所要時間**: 約 3 分です。

複数のイメージを同じ名前で登録すると、イメージは自動的にバージョン管理されます。 たとえば、最初に `myimage` として登録されたイメージには、ID `myimage:1` が割り当てられます。 次に `myimage` としてイメージを登録すると、新しいイメージの ID は `myimage:2` になります。

詳細については、[ContainerImage クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)のリファレンス ドキュメントを参照してください。

## <a name="deploy-the-image"></a>イメージをデプロイする

デプロイに取りかかる際、お客様がデプロイするコンピューティング先によってプロセスが若干異なります。 次のセクションの情報を使用して、デプロイ方法を確認してください。

* [Azure Container Instances](#aci)
* [Azure Kubernetes Services](#aks)
* [Project Brainwave (フィールド プログラマブル ゲート アレイ)](#fpga)
* [Azure IoT Edge デバイス](#iotedge)

### <a id="aci"></a>Azure Container Instances へのデプロイ

以下の条件が 1 つ以上満たされている場合は、Azure Container Instances を使用してお客様のモデルを Web サービスとしてデプロイします。

- モデルを迅速にデプロイおよび検証する必要があります。 ACI のデプロイは、5 分もかからずに完了します。
- 開発中のモデルをテストします。 ACI のクォータとリージョンの可用性を確認するには、「[Azure Container Instances のクォータとリージョンの可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)」を参照してください。

Azure Container Instances にデプロイするには、次の手順を使用します。

1. デプロイ構成を定義します。 次の例では、1 つの CPU コアと 1 GB のメモリが使用される構成を定義しています。

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. このドキュメントの「[イメージを作成する](#createimage)」セクションで作成されたイメージをデプロイするには、次のコードを使用します。

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **推定所要時間**: 約 3 分です。

    > [!TIP]
    > デプロイ中にエラーが発生した場合は、`service.get_logs()` を使用して AKS サービス ログを表示します。 ログに記録された情報に、エラーの原因が示されている可能性があります。

詳細については、[AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) クラスと [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) クラスのリファレンス ドキュメントを参照してください。

### <a id="aks"></a>Azure Kubernetes Service へのデプロイ

お客様のモデルを高スケールの運用 Web サービスとしてデプロイするには、Azure Kubernetes Service (AKS) を使用します。 既存の AKS クラスターを使用するか、Azure Machine Learning SDK、CLI、または Azure portal を使用して新しいクラスターを作成できます。

AKS クラスターの作成は、ワークスペースに対する 1 回限りのプロセスです。 複数のデプロイでこのクラスターを再利用できます。 クラスターを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。

Azure Kubernetes Service には次の機能があります。

* 自動スケール
* ログの記録
* モデル データ収集
* Web サービスの高速な応答時間

Azure Kubernetes Service にデプロイするには、次の手順を使用します。

1. AKS クラスターを作成するには、次のコードを使用します。

    > [!IMPORTANT]
    > AKS クラスターの作成は、ワークスペースの 1 回限りのプロセスです。 一度作成すれば、複数のデプロイでこのクラスターを再利用できます。 クラスターまたはそれを含むリソース グループを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。
    > [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) で、agent_count と vm_size にカスタム値を使用する場合は、agent_count と vm_size の積が 12 仮想 CPU 以上である必要があります。 たとえば、vm_size に "Standard_D3_v2" (4 仮想 CPU) を使用する場合、agent_count は 3 以上にする必要があります。

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget

    # Use the default configuration (you can also provide parameters to customize this)
    prov_config = AksCompute.provisioning_configuration()

    aks_name = 'aml-aks-1' 
    # Create the cluster
    aks_target = ComputeTarget.create(workspace = ws, 
                                        name = aks_name, 
                                        provisioning_configuration = prov_config)

    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

    **推定所要時間**: 約 20 分です。

    > [!TIP]
    > お客様の Azure サブスクリプションに既に AKS クラスターがあり、そのバージョンが 1.11.* である場合は、それを使用してお客様のイメージをデプロイできます。 次のコードは、既存のクラスターをお客様のワークスペースにアタッチする方法を示しています。
    >
    > ```python
    > from azureml.core.compute import AksCompute, ComputeTarget
    > # Set the resource group that contains the AKS cluster and the cluster name
    > resource_group = 'myresourcegroup'
    > cluster_name = 'mycluster'
    > 
    > # Attatch the cluster to your workgroup
    > attach_config = AksCompute.attach_configuration(resource_group = resource_group,
    >                                          cluster_name = cluster_name)
    > aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
    > 
    > # Wait for the operation to complete
    > aks_target.wait_for_completion(True)
    > ```

2. このドキュメントの「[イメージを作成する](#createimage)」セクションで作成されたイメージをデプロイするには、次のコードを使用します。

    ```python
    from azureml.core.webservice import Webservice, AksWebservice

    # Set configuration and service name
    aks_config = AksWebservice.deploy_configuration()
    aks_service_name ='aks-service-1'
    # Deploy from image
    service = Webservice.deploy_from_image(workspace = ws, 
                                                name = aks_service_name,
                                                image = image,
                                                deployment_config = aks_config,
                                                deployment_target = aks_target)
    # Wait for the deployment to complete
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

    > [!TIP]
    > デプロイ中にエラーが発生した場合は、`service.get_logs()` を使用して AKS サービス ログを表示します。 ログに記録された情報に、エラーの原因が示されている可能性があります。

詳細については、[AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) クラスと [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py) クラスのリファレンス ドキュメントを参照してください。

### <a id="fpga"></a>フィールド プログラマブル ゲート アレイ (FPGA) へのデプロイ

Project Brainwave を使用すると、リアルタイムの推論要求に対して超低遅延を実現できます。 Project Brainwave では、Azure クラウドのフィールド プログラマブル ゲート アレイにデプロイされているディープ ニューラル ネットワーク (DNN) が高速化されます。 一般に使用される DNN は、転移学習の Featurizer として使用したり、独自のデータからトレーニングされた重みでカスタマイズしたりできます。

Project Brainwave を使用したモデルのデプロイのチュートリアルについては、[FPGA へのデプロイ](how-to-deploy-fpga-web-service.md)に関するドキュメントを参照してください。

### <a id="iotedge"></a>Azure IoT Edge へのデプロイ

Azure IoT Edge デバイスとは、Azure IoT Edge ランタイムを実行している Linux または Windows ベースのデバイスです。 機械学習モデルは、これらのデバイスに IoT Edge モジュールとしてデプロイできます。 IoT Edge デバイスにモデルをデプロイすると、データを処理するためにクラウドに送信する必要がなくなり、デバイスがモデルを直接使用できます。 応答時間が早くなり、データ転送量が少なくなります。

Azure IoT Edge モジュールは、コンテナー レジストリからお客様のデバイスにデプロイされます。 お客様のモデルからイメージを作成すると、お客様のワークスペースのコンテナー レジストリにそれが格納されます。

#### <a name="set-up-your-environment"></a>環境をセットアップする

* 開発環境。 詳細については、「[開発環境を構成する方法](how-to-configure-environment.md)」のドキュメントを参照してください。

* Azure サブスクリプション内の [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)。 

* トレーニング済みのモデル。 モデルをトレーニングする方法の例は、「[Azure Machine Learning で画像分類モデルをトレーニングする方法](tutorial-train-models-with-aml.md)」のドキュメントを参照してください。 あらかじめトレーニングされたモデルは、[Azure IoT Edge 用 AI ツールキットの GitHub リポジトリ](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)で入手できます。

#### <a name="prepare-the-iot-device"></a>IoT デバイスの準備
IoT ハブを作成し、[このスクリプト](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister)を使用してデバイスを登録または再利用する必要があります。

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you want to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

結果として得られる接続文字列を "cs":"{copy this string}" の後ろに保存します。

[このスクリプト](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge)を UbuntuX64 IoT Edge ノードまたは DSVM にダウンロードしてお客様のデバイスを初期化し、次のコマンドを実行します。

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

IoT Edge ノードは、お客様の IoT ハブの接続文字列を受け入れる準備が整っています。 ```device_connection_string:``` の行を探して、引用符に囲まれた上記の接続文字列を貼り付けます。

また、お客様のデバイスを登録して IoT ランタイムをインストールする方法を詳しく確認するには、「[クイック スタート: 初めての IoT Edge モジュールを Linux x64 デバイスに展開する](../../iot-edge/quickstart-linux.md)」のドキュメントに従ってください。


#### <a name="get-the-container-registry-credentials"></a>コンテナー レジストリの資格情報の取得
IoT Edge モジュールをお客様のデバイスにデプロイするには、Azure Machine Learning service によって Docker イメージが格納されるコンテナー レジストリの資格情報が Azure IoT に必要です。

次の 2 つの方法で、必要なコンテナー レジストリの資格情報を簡単に取得できます。

+ **Azure portal**:

  1. [Azure Portal](https://portal.azure.com/signin/index) にサインインします。

  1. Azure Machine Learning サービス ワークスペースに移動し、__[概要]__ を選択します。 コンテナー レジストリの設定に移動するには、__[レジストリ]__ リンクを選択します。

     ![コンテナー レジストリ エントリの画像](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. コンテナー レジストリに入ったら、**[アクセス キー]** を選択して、管理者ユーザーを有効にします。
 
     ![アクセス キー画面の画像](./media/how-to-deploy-and-where/findaccesskey.png)

  1. **[ログイン サーバー]**、**[ユーザー名]**、**[パスワード]** の値を保存します。 

+ **Python スクリプト**:

  1. コンテナーを作成するために上記で実行したコードの後に、次の Python スクリプトを使用します。

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. ContainerURL、servername、username、password の値を保存します。 

     これらの資格情報は、お客様のプライベート コンテナー レジストリ内のイメージに IoT Edge デバイスからアクセスできるようにするために必要です。

#### <a name="deploy-the-model-to-the-device"></a>デバイスにモデルをデプロイする

[このスクリプト](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel)を実行し、上記の手順で取得した情報 (コンテナー レジストリ名、ユーザー名、パスワード、イメージの場所の URL、目的のデプロイの名前、IoT Hub 名、お客様が作成したデバイスの ID) を指定することで、モデルを簡単にデプロイできます。 次の手順に従って、VM でこれを実行できます。 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

または、「[Azure portal から Azure IoT Edge モジュールをデプロイする](../../iot-edge/how-to-deploy-modules-portal.md)」のドキュメントの手順に従って、イメージをお客様のデバイスにデプロイできます。 デバイスの__レジストリ設定__を構成するときは、お客様のワークスペースにあるコンテナー レジストリの__ログイン サーバー__、__ユーザー名__、__パスワード__を使用します。

> [!NOTE]
> Azure IoT を使い慣れていない場合は、サービスの概要について次のドキュメントで確認してください。
>
> * [クイック スタート: 初めての IoT Edge モジュールを Linux デバイスにデプロイする](../../iot-edge/quickstart-linux.md)
> * [クイック スタート: 初めての IoT Edge モジュールを Windows デバイスにデプロイする](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>Web サービスのデプロイのテスト

Web サービスのデプロイをテストするには、Webservice オブジェクトの `run` メソッドを使用できます。 次の例では、JSON ドキュメントが Web サービスに設定され、結果が表示されます。 送信されるデータは、モデルによって期待されるデータと一致する必要があります。 この例では、データ形式は糖尿病モデルによって期待される入力と一致しています。

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

## <a name="update-the-web-service"></a>Web サービスを更新する

Web サービスを更新するには、`update` メソッドを使用します。 次のコードは、Web サービスを更新して新しいイメージを使用する方法を示しています。

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> イメージの更新時に、Web サービスは自動的には更新されません。 新しいイメージを使用するには、各サービスを手動で更新する必要があります。

## <a name="clean-up"></a>クリーンアップ

デプロイされた Web サービスを削除するには、`service.delete()` を使用します。

イメージを削除するには、`image.delete()` を使用します。

登録済みのモデルを削除するには、`model.delete()` を使用します。

## <a name="next-steps"></a>次の手順

* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [バッチ予測を実行する方法](how-to-run-batch-predictions.md)
