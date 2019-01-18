---
title: デプロイ トラブルシューティング ガイド
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用する AKS と ACI に関する一般的な Docker のデプロイ エラーの回避、解決、トラブルシューティング方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6bd3bc86aa828ab28462de9d45f660889634cbd7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100516"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Azure Machine Learning service の AKS および ACI デプロイのトラブルシューティング

この記事では、Azure Machine Learning service を使用する Azure Container Instances (ACI) と Azure Kubernetes Service (AKS) に関する一般的な Docker のデプロイ エラーの回避または解決する方法について説明します。

Azure Machine Learning サービスでモデルをデプロイするとき、さまざまなタスクが実行されます。 これはイベントの複雑な一連のイベントであり、問題が発生することがあります。 デプロイ タスクの内容:

1. ワークスペース モデル レジストリにモデルを登録します。

2. Docker イメージをビルドします。これには次のタスクが含まれます。
    1. 登録済みのモデルをレジストリからダウンロードします。 
    2. 環境 yaml ファイルに指定した依存関係に基づき、Python 環境を利用し、Dockerfile を作成します。
    3. Dockerfile で指定するモデル ファイルとスコアリング スクリプトを追加します。
    4. Dockerfile を使用して新しい Docker イメージをビルドします。
    5. ワークスペースに関連付けられている Azure Container Registry に Docker イメージを登録します。

3. Docker イメージを Azure Container Instance (ACI) サービスまたは Azure Kubernetes Service (AKS) にデプロイします。

4. ACI または AKS で新しいコンテナーを起動します。 

このプロセスの詳細は[モデル管理](concept-model-management-and-deployment.md)の概要にあります。

## <a name="before-you-begin"></a>開始する前に

問題に直面したら、最初に行うべきことは、(前述の) デプロイ タスクを個々の手順に分割し、問題を隔離することです。 

この作業は `Webservice.deploy` API または `Webservice.deploy_from_model` API を利用している場合に特に役立ちます。これらの関数では、前述の手順が 1 つのアクションにグループ化されるためです。 一般的にこれらの API は非常に便利ですが、以下の API 呼び出しで置き換えることで、トラブルシューティング時に手順を分割することが役立ちます。

1. モデルを登録します。 サンプル コードは次のようになります。

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. イメージをビルドします。 サンプル コードは次のようになります。

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. サービスとしてイメージをデプロイします。 サンプル コードは次のようになります。

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

デプロイ プロセスを個別タスクに分割できたところで、最も一般的なエラーをいくつか確認してみましょう。

## <a name="image-building-fails"></a>イメージをビルドできない
Docker イメージをビルドできない場合、`image.wait_for_creation()` 呼び出しが失敗し、エラー メッセージが表示されます。そのメッセージでヒントが見つかることがあります。 エラーに関する詳細は、イメージ ビルド ログにもあります。 下のサンプル コードでイメージ ビルド ログ URI の確認方法がわかります。

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items():
    print (img.name, img.version, img.image_build_log_uri)
```
イメージ ログ URI は、Azure Blob Storage に保存されているログ ファイルを指す SAS URL です。 URI をコピーし、ブラウザー ウィンドウに貼り付けます。ログ ファイルはダウンロードして参照できます。


## <a name="service-launch-fails"></a>サービスを起動できない
イメージが正常にビルドされると、デプロイ構成に基づき、ACI または AKS でコンテナーの起動が試行されます。 一般的に、ACI デプロイを先に試すことをお勧めします。ACI の方がよりシンプルな単一コンテナーのデプロイであるためです。 この方法で AKS 固有の問題を除外できます。

コンテナーの起動プロセスの一部として、スコアリング スクリプトの `init()` 関数が呼び出されます。 `init()` 関数でキャッチされない例外がある場合、エラー メッセージに **CrashLoopBackOff** エラーが表示されることがあります。 以下は問題解決に役立つヒントです。

### <a name="inspect-the-docker-log"></a>Docker ログを確認する
サービス オブジェクトから詳細な Docker エンジン ログ メッセージを出力できます。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Docker イメージをローカルでデバッグする
Docker ログでは問題に関する情報が十分に提供されないことがあります。 さらに一歩進め、ビルドされた Docker イメージを取得し、ローカル コンテナーを起動し、ライブ コンテナー内で直接、対話的にデバッグできます。 ローカル コンテナーを起動するには、Docker エンジンをローカルで実行しておく必要があります。[azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) もインストールしておくと非常に簡単になります。

まず、イメージの場所を見つける必要があります。

```python
# print image location
print(image.image_location)
```

イメージの場所の形式は `<acr-name>.azurecr.io/<image-name>:<version-number>` です。たとえば、`myworkpaceacr.azurecr.io/myimage:3` のようになります。 

次にコマンドライン ウィンドウに移動します。 azure-cli をインストールしている場合、次のコマンドを入力し、イメージが保存されているワークスペースに関連付けられている ACR (Azure Container Registry) にサインインできます。 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
azure-cli をインストールしていない場合、`docker login` コマンドを使用して ACR にログインできます。 ただし、先に Azure portal から ACR のユーザー名とパスワードを取得する必要があります。

ACR にログインしたら、Docker イメージを取得し、コンテナーをローカルで起動できます。その後、`docker run` コマンドを使用し、bash セッションを起動してデバッグできます。

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

実行中のコンテナーで bash セッションを開始すると、`/var/azureml-app` フォルダーでスコアリング スクリプトを見つけることができます。 その後、Python セッションを開始し、スコアリング スクリプトをデバッグできます。 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
スクリプトの編集にテキスト エディターが必要な場合、vim、nano、Emacs、普段ご利用のその他のエディターをインストールできます。

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Web サービスをローカルで起動し、それに HTTP トラフィックを送信することもできます。 Docker コンテナーの Flask サーバーはポート 5001 で実行されます。 ホスト コンピューターで利用できるその他のポートにマッピングできます。
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>get_model_path() 関数が失敗する
多くの場合、スコアリング スクリプトの `init()` 関数では、コンテナー内のモデル ファイルまたはモデル ファイルのフォルダーを見つける目的で `Model.get_model_path()` 関数が呼び出されます。 モデル ファイルまたはフォルダーが見つからないとき、多くの場合、これが失敗の原因です。 このエラーをデバッグする最も簡単な方法は、Container シェルで以下の Python コードを実行することです。

```python
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

スコアリング スクリプトによるモデル ファイルまたはフォルダーの検索が予想されるコンテナー内のローカル パス (`/var/azureml-app` の相対パス) が出力されます。 その後、ファイルまたはフォルダーが予想される場所にあるかどうかを確認できます。


## <a name="function-fails-runinputdata"></a>run(input_data) 関数が失敗する
サービスが正常にデプロイされたが、スコアリング エンドポイントにデータを投稿するとクラッシュする場合、代わりに詳細なエラー メッセージが返されるように `run(input_data)` 関数にエラーをキャッチするステートメントを追加できます。 例: 

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```
**メモ**:`run(input_data)` 呼び出しからエラー メッセージを返すことは、デバッグ目的のみで行ってください。 セキュリティ上の理由から、運用環境でこれを行うことはお勧めできません。


## <a name="next-steps"></a>次の手順

デプロイの詳細については、以下を参照してください。 
* [デプロイする方法とその場所](how-to-deploy-and-where.md)

* [チュートリアル:モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)
