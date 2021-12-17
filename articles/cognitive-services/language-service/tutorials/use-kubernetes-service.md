---
title: キー フレーズ抽出コンテナーを Azure Kubernetes Service にデプロイする
titleSuffix: Azure Cognitive Services
description: キー フレーズ抽出コンテナー イメージを Azure Kubernetes Service にデプロイし、それを Web ブラウザーでテストします。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a71910bde7718c0a60eda87ea51f6c49e473304a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091957"
---
# <a name="deploy-a-key-phrase-extraction-container-to-azure-kubernetes-service"></a>キー フレーズ抽出コンテナーを Azure Kubernetes Service にデプロイする

[キーフレーズ抽出 Docker コンテナー](../key-phrase-extraction/how-to/use-containers.md) イメージを Azure Kubernetes Service (AKS) にデプロイする方法について説明します。 この手順では、言語リソースを作成する方法、コンテナー イメージを関連付ける方法、ブラウザーからこの 2 つのオーケストレーションを実行する方法を示します。 コンテナーを使用すると、インフラストラクチャの管理に注意を払うことなく、アプリケーション開発に専念することができます。 この記事では、キー フレーズ抽出コンテナーを例として使いますが、このプロセスは、Azure Cognitive Service for Language によって提供される他のコンテナーにも使用できます

## <a name="prerequisites"></a>前提条件

この手順には、ローカルでインストールして実行する必要があるいくつかのツールが必要です。 Azure Cloud Shell は使用しないでください。 以下のものが必要になります。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services) を作成してください。
* [Visual Studio Code](https://code.visualstudio.com/download) などのテキスト エディター。
* [Azure CLI](/cli/azure/install-azure-cli) がインストールされていること。
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) がインストールされていること。
* 適切な価格レベルの Azure リソース。 すべての価格レベルでこのコンテナーを使用するわけではありません。
    * F0 または Standard 価格レベルのみの **Azure 言語** リソース。
    * S0 価格レベルの **Azure Cognitive Services** リソース。

[!INCLUDE [Create a Cognitive Services Language resource](../includes/containers/create-text-analytics-resource.md)]

[!INCLUDE [Create a language container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]


## <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>キーフレーズ抽出コンテナーを AKS クラスターにデプロイする

1. Azure CLI を開き、Azure にサインインします。

    ```azurecli
    az login
    ```

1. AKS クラスターにサインインします。 `your-cluster-name` と `your-resource-group` を、適切な値に置き換えます。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    このコマンドを実行すると、次のようなメッセージが表示されます。

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure アカウントに利用可能な複数のサブスクリプションがあり、`az aks get-credentials` コマンドでエラーが返される場合、よくある問題は、間違ったサブスクリプションを使用していることです。 Azure CLI セッションのコンテキストを、リソースを作成したときと同じサブスクリプションを使用するように設定して、やり直してください。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 任意のテキスト エディターを開きます。 この例では、Visual Studio Code を使います。

    ```console
    code .
    ```

1. テキスト エディターで "*keyphrase.yaml*" という名前の新しいファイルを作成し、以下の YAML を貼り付けます。 必ず `billing/value` と `apikey/value` を独自の情報に置き換えてください。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. ファイルを保存して、テキスト エディターを閉じます。
1. ターゲットとして *keyphrase.yaml* ファイルを指定して Kubernetes `apply` コマンドを実行します。

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    コマンドによってデプロイ構成が正常に適用された後、次の出力のようなメッセージが表示されます。

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. ポッドがデプロイされたことを確認します。

    ```console
    kubectl get pods
    ```

    ポッドの実行状態の出力:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. サービスが利用できることを確認し、IP アドレスを取得します。

    ```console
    kubectl get services
    ```

    ポッドの "*キーフレーズ*" サービスの実行状態の出力:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

## <a name="verify-the-key-phrase-extraction-container-instance"></a>キー フレーズ抽出コンテナー インスタンスを確認する

1. **[概要]** タブを選択し、IP アドレスをコピーします。
1. 新しいブラウザー タブを開き、IP アドレスを入力します。 たとえば、「`http://<IP-address>:5000 (http://55.55.55.55:5000`)」と入力します。 コンテナーのホーム ページが表示され、コンテナーが実行中であることを知らせます。

    ![コンテナーのホーム ページを表示してコンテナーが実行中であることを確認します](../media/swagger-container-documentation.png)

1. **[Service API Description]\(サービス API の説明\)** リンクを選択して、コンテナーの Swagger ページに移動します。

1. いずれかの **POST** APIを選択して **[試してみる]** を選択します。次の入力の例を含むパラメーターが表示されます。

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. 入力を次の JSON コンテンツに置き換えます。

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. **showStats** を `true` に設定します。

1. **[実行]** を選択してテキストの感情を判定します。

    コンテナーにパッケージ化されたモデルでは、0 から 1 のスコアが生成されます (0 は否定的、1 は肯定的)。

    返される JSON 応答には、更新されたテキスト入力の感情が含まれます。

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

これで、応答ペイロードの JSON データのドキュメント `id` を、元の要求ペイロード ドキュメント `id` に関連付けることができます。 結果的に生成されるドキュメントには `keyPhrases` 配列が与えられます。この配列には、対応する入力ドキュメントから抽出されたキーフレーズの一覧が含まれます。 また、結果的に生成される各ドキュメントに、`characterCount` や `transactionCount` など、さまざまな統計値があります。

## <a name="next-steps"></a>次のステップ

* さらに [Cognitive Services コンテナー](../../cognitive-services-container-support.md)を使用する
* [キー フレーズ抽出の概要](../overview.md)
