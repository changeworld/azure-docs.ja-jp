---
title: VS Code を使用してオンライン エンドポイントをローカルでデバッグする (プレビュー)
titleSuffix: Azure Machine Learning
description: Visual Studio Code を使用して、オンライン エンドポイントを Azure にデプロイする前にローカルでテストおよびデバッグする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: luisquintanilla
ms.author: luquinta
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 33b46c490912d0c651c5629da17795efe074b6d9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565600"
---
# <a name="debug-online-endpoints-locally-in-visual-studio-code-preview"></a>Vvisual Studio Code を使用してオンライン エンドポイントをローカルでデバッグする (プレビュー)

Visual Studio Code (VS Code) デバッガーを使用して、オンライン エンドポイントを Azure にデプロイする前にローカルでテストおよびデバッグする方法について説明します。

Azure Machine Learning のローカル エンドポイントは、スコアリング スクリプト、環境構成、コード構成、および機械学習モデルをローカルでテスト、デバッグするのに役立ちます。

## <a name="online-endpoint-local-debugging"></a>オンライン エンドポイントのローカル デバッグ

エンドポイントをクラウドにデプロイする前にローカルでデバッグすることで、コードと構成のエラーを早期に発見することができます。 VS Code を使用してエンドポイントをローカルでデバッグするには、さまざまなオプションがあります。

- [Azure Machine Learning 推論 HTTP サーバー (プレビュー)](how-to-inference-server-http.md)
- ローカル エンドポイント

このガイドでは、ローカル エンドポイントに焦点を当てています。

次の表に、目的に適した方法を選択するのに役立つシナリオの概要を示しています。

| シナリオ | 推論 HTTP サーバー | ローカル エンドポイント |
|--|--|--|
| Docker イメージを再構築 **することなく**、ローカルの python 環境を更新する | はい | いいえ |
| スコアリング スクリプトを更新する | はい | はい |
| デプロイ構成を更新する (デプロイ、環境、コード、モデル) | いいえ | はい |
| VS Code デバッガーの統合 | はい | はい |

## <a name="prerequisites"></a>前提条件

このガイドでは、PC に以下の項目がローカルにインストールされていることを前提としています。

- [Docker](https://docs.docker.com/engine/install/)
- [VS Code](https://code.visualstudio.com/#alt-downloads)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI `ml` 拡張機能](how-to-configure-cli.md)

詳細については、[マネージド オンライン エンドポイントをデプロイするためにシステムを準備する方法](how-to-deploy-managed-online-endpoints.md#prepare-your-system)に関するガイドを参照してください。

この記事の例は、[azureml-examples](https://github.com/azure/azureml-examples) リポジトリに含まれているコード サンプルを基にしています。 YAML などのファイルをコピーして貼り付けることなくコマンドをローカルで実行するには、リポジトリを複製し、ディレクトリをリポジトリ内の `cli` ディレクトリに変更します。

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

まだ Azure CLI の既定値を設定していない場合は、既定の設定を保存する必要があります。 サブスクリプション、ワークスペース、およびリソース グループの値が複数回渡されることを防ぐには、以下のコマンドを使用します。 次のパラメーターを、実際の構成の値に置き換えます。

* `<subscription>` は、Azure サブスクリプション ID に置き換えてください。
* `<workspace>` は、ご利用の Azure Machine Learning ワークスペース名に置き換えます。
* `<resource-group>` は、ワークスペースが含まれている Azure リソース グループに置き換えます。
* `<location>` は、ワークスペースが含まれている Azure リージョンに置き換えます。

> [!TIP]
> `az configure -l` コマンドを使用すると、現在の既定値を確認できます。

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```

## <a name="launch-development-container"></a>開発コンテナーの起動

Azure Machine Learning のローカル エンドポイントは、Docker と VS Code の開発コンテナーを使用して、ローカルのデバッグ環境を構築および構成します。 開発コンテナーを使用すると、Docker コンテナー内から VS Code の機能を利用できます。 開発コンテナーの詳細については、[開発コンテナーを作成する](https://code.visualstudio.com/docs/remote/create-dev-container)方法に関するページを参照してください。

オンライン エンドポイントを VS Code でローカルにデバッグするには、Azure Machine Learning のオンライン デプロイを作成または更新する際に `--vscode-debug` フラグを使用します。 次のコマンドは、サンプル リポジトリのデプロイ例を使用しています。

```azurecli
az ml online-deployment create --file endpoints/online/managed/sample/blue-deployment.yml --local --vscode-debug
```

> [!IMPORTANT]
> Linux 用 Windows サブシステム (WSL) では、PATH 環境変数を更新して VS コードの実行可能ファイルへのパスを含めるか、WSL 相互運用を使用する必要があります。 詳細については、[Linux と Windows の相互運用性](/windows/wsl/interop)に関する記事をご覧ください。

Docker イメージはローカルに構築されます。 環境構成やモデルフ ァイルのエラーは、この段階で表面化します。

> [!NOTE]
> 新規または更新された開発コンテナーを初めて起動するときには、数分かかることがあります。

イメージが正常に構築されると、開発コンテナーが VS Code ウィンドウで開きます。

開発コンテナーでデプロイをデバッグするには、いくつかの VS Code 拡張機能を使用します。 Azure Machine Learning の場合、これらの拡張機能は自動的に開発コンテナーにインストールされます。

- 推論のデバッグ
- [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)
- [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)
- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

> [!IMPORTANT]
> デバッグ セッションを開始する前に、開発コンテナーへの VS Code 拡張機能のインストールが完了していることを確認してください。  

## <a name="start-debug-session"></a>デバッグ セッションの開始

環境の設定が完了したら、VS Code デバッガーを使用して、デプロイをローカルでテストおよびデバッグします。

1. Visual Studio Code でスコアリング スクリプトを開きます。

    > [!TIP]
    > 前にデプロイしたエンドポイントで使用されている score.py スクリプトは、クローンしたリポジトリの `azureml-samples/cli/endpoints/online/managed/sample/score.py` にあります。 ただし、このガイドの手順では、どのスコアリング スクリプトでも機能します。

1. スコアリング スクリプト内の任意の場所にブレークポイントを設定します。

    - スタートアップ動作をデバッグするには、`init` 関数内にブレークポイントを配置します。
    - スコアリング動作をデバッグするには、`run` 関数内にブレークポイントを配置します。

1. VS Code の実行ビューを選択します。
1. [実行とデバッグ] ドロップダウンから **[Azure ML: Debug Local Endpoint]\(Azure ML: ローカル エンドポイントのデバッグ\)** を選択して、エンドポイントのデバッグをローカルで開始します。

    実行ビューの **[ブレークポイント]** セクションで、次のことを確認します。

    - **[Raised Exceptions]\(すべての例外\)** が **オフ** になっている
    - **[Uncaught Exceptions]\(キャッチされない例外\)** が **オン** になっている

    :::image type="content" source="media/how-to-debug-managed-online-endpoints-visual-studio-code/configure-debug-profile.png" alt-text="Azure ML のローカル環境のデバッグにおけるデバッグ プロファイルの構成":::

1. [実行とデバッグ] ドロップダウンの横にある再生アイコンを選択して、デバッグ セッションを開始します。

    この時点で、`init` 関数内のすべてのブレークポイントがキャッチされます。 デバッグ アクションを使用して、コードをステップ実行します。 デバッグ アクションの詳細については、[デバッグ アクション ガイド](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)のページを参照してください。

VS Code デバッガーの詳細については、[VS Code におけるデバッグ](https://code.visualstudio.com/Docs/editor/debugging)に関するページを参照してください

## <a name="debug-your-endpoint"></a>エンドポイントをデバッグする

アプリケーションがデバッガーで実行されたので、スコアリング スクリプトをデバッグするための予測を行います。

`ml` 拡張機能の `invoke` コマンドを使用して、ローカル エンドポイントに要求を行います。

```azurecli
az ml online-endpoint invoke --name <ENDPOINT-NAME> --request-file <REQUEST-FILE> --local
```

この場合、`<REQUEST-FILE>` は、以下の JSON と同様に、モデルが予測を行うための入力データ サンプルを含む JSON ファイルです。

```json
{"data": [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]}
```

> [!TIP]
> スコアリング URI は、エンドポイントが要求をリッスンするアドレスです。 スコアリング URI を取得するには、`ml` 拡張機能を使用します。
>
>    ```azurecli
>    az ml online-endpoint show --name <ENDPOINT-NAME> --local
>    ```
>
> 出力は次のようになります。
>
> ```json
> {
>  "auth_mode": "aml_token",
>  "location": "local",
>  "name": "my-new-endpoint",
>  "properties": {},
>  "provisioning_state": "Succeeded",
>  "scoring_uri": "http://localhost:5001/score",
>  "tags": {},
>  "traffic": {},
>  "type": "online"
>}
>```
>
>スコアリング URI は、`scoring_uri` プロパティで確認できます。

この時点で、`run` 関数内のすべてのブレークポイントがキャッチされます。 デバッグ アクションを使用して、コードをステップ実行します。 デバッグ アクションの詳細については、[デバッグ アクション ガイド](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)のページを参照してください。

## <a name="edit-your-endpoint"></a>エンドポイントを編集する

アプリケーションのデバッグとトラブルシューティングを行う際は、スコアリング スクリプトと構成を更新する必要がある場面に遭遇します。

コードに変更を適用するには、以下の手順を実行します。

1. コードを更新する
1. コマンド パレットの `Developer: Reload Window` コマンドを使用して、デバッグ セッションを再起動します。 詳細については、[コマンド パレットのドキュメント](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)を参照してください。

> [!NOTE]
> コードとエンドポイント資産が格納されているディレクトリは開発コンテナーにマウントされているため、開発コンテナーで行ったすべての変更はローカルのファイル システムと同期されます。

環境やエンドポイントの構成の更新を含む、より広範囲な変更を行う場合は、`ml` 拡張機能の `update` コマンドを使用してください。 これにより、変更した内容でイメージの完全な再構築が行われます。

```azurecli
az ml online-deployment update --file <DEPLOYMENT-YAML-SPECIFICATION-FILE> --local --vscode-debug
```

更新されたイメージが構築され、開発コンテナーが起動したら、VS Code デバッガーを使用して、更新されたエンドポイントをテストおよびトラブルシューティングします。

## <a name="next-steps"></a>次の手順

- [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)
- [マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](how-to-troubleshoot-managed-online-endpoints.md)
