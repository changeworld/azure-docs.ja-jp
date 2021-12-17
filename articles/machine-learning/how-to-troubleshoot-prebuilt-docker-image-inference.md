---
title: 事前構築済み Docker イメージのトラブルシューティング
titleSuffix: Azure Machine Learning
description: 推論用の事前構築済み Docker イメージを使用する際のトラブルシューティング手順。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, troubleshoot
ms.openlocfilehash: f57a59ffa9ad316e0ae1da76efb843673303541f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556162"
---
# <a name="troubleshooting-prebuilt-docker-images-for-inference"></a>推論用の事前構築済み Docker イメージに関するトラブルシューティング

Azure Machine Learning で、推論用の事前構築済み Docker イメージを使用する際に発生する可能性のある問題をトラブルシューティングする方法について説明します。

> [!IMPORTANT]
> Azure Machine Learning での[事前構築済み Docker イメージ用 Python パッケージ拡張性](how-to-prebuilt-docker-images-inference-python-extensibility.md)の使用は、現在プレビュー段階です。 プレビュー機能では、サポートやサービス レベル アグリーメントは保証されず、"現状有姿" で提供されます。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="model-deployment-failed"></a>モデル デプロイが失敗した

モデル デプロイが失敗した場合、[Azure Machine Learning スタジオ](https://ml.azure.com/)にはログが表示されず、`service.get_logs()` から None が返されます。
score.py の init() 関数に問題がある場合は、`service.get_logs()` からそのログが返されます。

そのため、次に示すコマンドのいずれかを使用してローカルでコンテナーを実行し、`<MCR-path>` をイメージ パスで置き換えます。 イメージとパスの一覧については、「[推論用の事前構築済み Docker イメージ](concept-prebuilt-docker-images-inference.md)」を参照してください。

### <a name="mounting-extensibility-solution"></a>機能拡張ソリューションのマウント

`score.py` を含むディレクトリに移動し、次を実行します。

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_PYTHON_LIB_PATH="myenv/lib/python3.7/site-packages" <mcr-path>
```

### <a name="requirementstxt-extensibility-solution"></a>requirements.txt 機能拡張ソリューション

`score.py` を含むディレクトリに移動し、次を実行します。

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_REQUIREMENTS_TXT="requirements.txt" <mcr-path>
```

## <a name="enable-local-debugging"></a>ローカル デバッグを有効にする

ローカル推論サーバーを使用すると、エントリ スクリプト (`score.py`) をすばやくデバッグできます。 基になるスコア スクリプトにバグがある場合、サーバーはモデルの初期化やサービスの提供に失敗します。 代わりに、例外と、問題が発生した場所がスローされます。 [Azure Machine Learning 推論 HTTP サーバーの詳細](how-to-inference-server-http.md)

## <a name="for-common-model-deployment-issues"></a>一般的なモデル デプロイの問題

Azure Machine Learning から Azure Container Instances (ACI) または Azure Kubernetes Service (AKS) にモデルをデプロイする際の問題については、「[モデル デプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)」を参照してください。

## <a name="init-or-run-failing-to-write-a-file"></a>init() または run() でファイルの書き込みに失敗する

事前構築済み Docker イメージ内の HTTP サーバーは、"*非ルート ユーザー*" として実行されるため、すべてのディレクトリに対するアクセス権が付与されているとは限りません。 アクセス権のあるディレクトリにのみ書き込みができます。 たとえば、コンテナー内の `/tmp` ディレクトリです。

## <a name="extra-python-packages-not-installed"></a>追加の Python パッケージがインストールされない

* 環境変数またはファイル名に入力ミスがないかを確認します。
* コンテナー ログをチェックして、`pip install -r <your_requirements.txt>` がインストールされているかどうかを確認します。
* [推論構成](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor)コンストラクターでソース ディレクトリが正しく設定されているかどうかを確認します。
* インストールが見つからず、ログに "ファイルが見つかりません" と表示される場合は、ログに表示されているファイル名が正しいかどうかを確認します。
* インストールが開始されたが失敗またはタイムアウトした場合は、クリーンな環境 (つまり、キャッシュ ディレクトリなし、`pip install --no-cache-dir -r requriements.txt`) で同じ Python および PIP バージョンを使用して、ローカルで同じ `requirements.txt` をインストールしてみてください。 ローカルで問題を再現できるかどうかを確認します。

## <a name="mounting-solution-failed"></a>ソリューションのマウントに失敗した

* 環境変数またはディレクトリ名に入力ミスがないかを確認します。
* 環境変数は、`score.py` ファイルの相対パスに設定する必要があります。
* [推論構成](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor)コンストラクターでソース ディレクトリが正しく設定されているかどうかを確認します。
* ディレクトリは、環境の "サイトパッケージ" ディレクトリである必要があります。
* モジュールがディレクトリにマウントされているはずなのに `score.py` が引き続き `ModuleNotFound` を返す場合は、`init()` または `run()` で `sys.path` を出力して、不足しているパスがないかを確認してください。

## <a name="building-an-image-based-on-the-prebuilt-docker-image-failed"></a>事前構築済み Docker イメージに基づくイメージのビルドに失敗した

* apt パッケージのインストール中に失敗した場合は、apt コマンドを実行する前に、ユーザーがルートに設定されていないか確認してください (必ず非ルート ユーザーに切り替えてください) 

## <a name="image-built-based-on-the-prebuilt-docker-image-cant-boot-up"></a>事前構築済み Docker イメージに基づいて構築されたイメージを起動できない

* 非ルート ユーザーが `dockeruser` である必要があります。 そうなっていない場合、次のディレクトリの所有者を、イメージの実行時に使用するユーザー名に設定する必要があります。

    ```bash
    /var/runit
    /var/log
    /var/lib/nginx
    /run
    /opt/miniconda
    /var/azureml-app
    ```

* 新しくビルドされたイメージで `ENTRYPOINT` が変更されている場合、HTTP サーバーと関連コンポーネントは、`runsvdir /var/runit` ごとに読み込む必要があります

## <a name="next-steps"></a>次の手順

* [Python パッケージを事前構築済みのイメージに追加する](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [新しい Dockerfile のベースとして事前構築済みのパッケージを使用する](how-to-extend-prebuilt-docker-image-inference.md)。