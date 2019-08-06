---
title: Azure Kubernetes Service を実行する
titleSuffix: Text Analytics - Azure Cognitive Services
description: Text Analytics コンテナーを感情分析イメージと一緒に Azure Kubernetes Service にデプロイし、それを Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 290a01e7e478f718607c0550702474cd31979a63
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377403"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>Azure Kubernetes Service に感情分析コンテナーをデプロイする

Azure Cognitive Services の [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) コンテナーを感情分析イメージと一緒に Azure Kubernetes Service (AKS) にデプロイする方法について説明します。 この手順では、Text Analytics リソースを作成する方法、関連付けられた感情分析イメージを作成する方法、およびこの 2 つのオーケストレーションをブラウザーから実行する方法を示します。 コンテナーを使用すると、インフラストラクチャの管理に注意を払うことなく、アプリケーション開発に専念することができます。

## <a name="prerequisites"></a>前提条件

この手順には、ローカルでインストールして実行する必要があるいくつかのツールが必要です。 Azure Cloud Shell は使用しないでください。 以下のものが必要になります。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* [Visual Studio Code](https://code.visualstudio.com/download) などのテキスト エディター。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) がインストールされていること。
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) がインストールされていること。
* 適切な価格レベルの Azure リソース。 すべての価格レベルでこのコンテナーを使用するわけではありません。
    * F0 または Standard 価格レベルのみの **Azure Text Analytics** リソース。
    * S0 価格レベルの **Azure Cognitive Services** リソース。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>AKS クラスターに Text Analytics コンテナーをデプロイする

1. Azure CLI を開き、Azure にサインインします。

    ```azurecli
    az login
    ```

1. AKS クラスターにサインインします。 `your-cluster-name` と `your-resource-group` を、適切な値に置き換えます。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    このコマンドを実行すると、次のようなメッセージが表示されます。

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure アカウントに利用可能な複数のサブスクリプションがあり、`az aks get-credentials` コマンドでエラーが返される場合、よくある問題は、間違ったサブスクリプションを使用していることです。 Azure CLI セッションのコンテキストを、リソースを作成したときと同じサブスクリプションを使用するように設定して、やり直してください。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 任意のテキスト エディターを開きます。 この例では、Visual Studio Code を使います。

    ```azurecli
    code .
    ```

1. テキスト エディターで "_sentiment.yaml_" という名前の新しいファイルを作成し、以下の YAML を貼り付けます。 必ず `billing/value` と `apikey/value` を独自の情報に置き換えてください。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. ファイルを保存して、テキスト エディターを閉じます。
1. ターゲットとして "_sentiment.yaml_" を指定して Kubernetes `apply` コマンドを実行します。

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    コマンドによってデプロイ構成が正常に適用された後、次の出力のようなメッセージが表示されます。

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. ポッドがデプロイされたことを確認します。

    ```console
    kubectl get pods
    ```

    ポッドの実行状態の出力:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. サービスが利用できることを確認し、IP アドレスを取得します。

    ```console
    kubectl get services
    ```

    ポッドの "_センチメント_" サービスの実行状態の出力:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>次の手順

* さらに [Cognitive Services コンテナー](../../cognitive-services-container-support.md)を使用する
* [Text Analytics 接続済みサービス](../vs-text-connected-service.md)を使用する
