---
title: AKS と Azure Container Registry で Draft を使用します。
description: AKS と Azure Container Registry で Draft を使用します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8f273a5a2c47b25dc339fd63df127d141fe2f8e2
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130245"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Draft の使用

オープン ソース ツールである Draft を使用すると、Kubernetes クラスターにこれらのコンテナーを含めてデプロイできるため、開発サイクル (開発に重点を置いた "内部ループ") に集中できます。 Draft はコードの開発時に、バージョン コントロールにコミットする前に機能します。 Draft を使用すると、コードの変更が発生したときに、アプリケーションを Kubernetes にすばやく再デプロイできます。 Draft の詳細については、[Github の Draft のドキュメント][draft-documentation]をご覧ください。

このドキュメントでは、AKS の Kubernetes クラスターで Draft を使用する方法について詳しく説明します。

## <a name="prerequisites"></a>前提条件

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの kubectl 接続が確立されていることを想定しています。 これらの項目が必要な場合は、[AKS のクイック スタート][aks-quickstart]を参照してください。

Azure Container Registry (ACR) のプライベート Docker レジストリも必要です。 ACR インスタンスをデプロイする手順については、[Azure Container Registry のクイックスタート][acr-quickstart]をご覧ください。

Helm も AKS クラスターにインストールする必要があります。 Helm のインストールについて詳しくは、「[Azure Kubernetes Service (AKS) での Helm の使用][aks-helm]」をご覧ください。

最後に、[Docker](https://www.docker.com) をインストールする必要があります。

## <a name="install-draft"></a>Draft のインストール

Draft CLI は、開発システムで実行され、Kubernetes クラスターへのコードの迅速なデプロイを可能にするクライアントです。

> [!NOTE]
> バージョン 0.12 以前の Draft をインストールしている場合、`helm delete --purge draft` を使ってクラスターから Draft を削除してから、`rm -rf ~/.draft` を実行してローカルの構成を削除してください。 MacOS の場合は、`brew upgrade draft` を実行します。

Draft CLI を Mac にインストールするには、`brew` を使用します。 その他のインストール オプションについては、[Draft のインストール ガイド][draft-documentation]をご覧ください。

```console
brew tap azure/draft
brew install draft
```

次に、`draft init` コマンドを使用して Draft を初期化します。

```console
draft init
```

## <a name="configure-draft"></a>Draft の構成

Draft によってコンテナー イメージをローカルにビルドし、ローカル レジストリからデプロイするか (Minikube の場合)、ユーザーが使用するイメージ レジストリを指定する必要があります。 この例では Azure Container Registry (ACR) を使用するため、AKS クラスターと ACR レジストリの間の信頼関係を確立して、コンテナーを ACR にプッシュするように Draft を構成する必要があります。

### <a name="create-trust-between-aks-cluster-and-acr"></a>AKS クラスターと ACR 間に信頼を作成する

AKS クラスターと ACR レジストリ間の信頼を確立するために、ACR レジストリのスコープを使用して共同作成者ロールを追加して、AKS と共に使用する Azure Active Directory サービス プリンシパルを変更します。 このためには、次のコマンドを実行します。_&lt;aks-rg-name&gt;_ と _&lt;aks-cluster-name&gt;_ をそれぞれ AKS クラスターのリソース グループと名前に置き換え、_&lt;acr-rg-nam&gt;_ と _&lt;acr-registry-name&gt;_ を信頼の作成に使用する ACR レジストリのリソース グループとレジストリ名に置き換えます。

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-registry-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(これらの手順や ACR にアクセスするためのその他の認証メカニズムについては、[ACR を使用した認証](../container-registry/container-registry-auth-aks.md)に関する記事をご覧ください。)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>ACR にプッシュされ ACR からデプロイされるように Draft を構成する

AKS と ACR 間の信頼関係を確立したら、次の手順に従って AKS クラスターからの ACR の使用を有効にします。
1. Draft 構成 `registry` の値を `draft config set registry <registry name>.azurecr.io` を実行して設定します。ここで、_&lt;レジストリ名前&lt;_ は ACR レジストリの名前です。
2. `az acr login -n <registry name>` を実行して ACR レジストリにログオンします。

ACR に現在ローカルにログオンし、AKS と ACR に信頼関係を作成したため、ACR から AKS へのプッシュまたはプルにはパスワードまたはシークレットは必要ありません。 認証は、Azure Active Directory を使用して Azure Resource Manager レベルで実行されます。

## <a name="run-an-application"></a>アプリケーションの実行

Draft リポジトリには、Draft のデモに使用できるサンプル アプリケーションがいくつか含まれています。 リポジトリの複製されたコピーを作成します。

```console
git clone https://github.com/Azure/draft
```

Java の examples ディレクトリに移動します。

```console
cd draft/examples/example-java/
```

`draft create` コマンドを使用して、プロセスを開始します。 このコマンドは、Kubernetes クラスターでアプリケーションを実行する際に使用されるアーティファクトを作成します。 これらの項目には、Dockerfile、Helm チャート、`draft.toml` ファイル (Draft 構成ファイル) が含まれます。

```console
draft create
```

出力:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Kubernetes クラスターでアプリケーションを実行するには、`draft up` コマンドを使用します。 このコマンドは、コンテナー イメージを作成するように Dockerfile をビルドして、イメージを ACR にプッシュし、最後に Helm チャートをインストールして AKS でアプリケーションを起動します。

これを初めて実行したとき、コンテナー イメージのプッシュとプルに時間がかかる場合があります。ベース レイヤーがキャッシュされると所要時間が大幅に減ります。

```console
draft up
```

出力:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションをテストするには、`draft connect` コマンドを使用します。 このコマンドは、セキュリティで保護されたローカル接続を可能にする Kubernetes ポッド への接続をプロキシします。 完了すると、提供された URL でアプリケーションにアクセスできます。

コンテナー イメージがダウンロードされ、アプリケーションが起動するまで数分かかる場合があります。 アプリケーションにアクセスしたときにエラーが発生した場合は、接続を再試行してください。

```console
draft connect
```

出力:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

次に、http://localhost:46143 を参照してアプリケーションをテストします (前の例の場合。ポートが異なる場合もあります)。 アプリケーションのテストが完了したら、`Control+C` を使用してプロキシ接続を停止します。

> [!NOTE]
> `draft up --auto-connect` コマンドを使用して、アプリケーションをビルドしてデプロイし、最初に実行されるコンテナーに直ちに接続して反復サイクルを高速化することもできます。

## <a name="expose-application"></a>アプリケーションを公開する

Kubernetes でアプリケーションをテストするときに、アプリケーションをインターネットで使用できるようにすることもできます。 そのためには、種類が [LoadBalancer][kubernetes-service-loadbalancer] または[イングレス コントローラー][kubernetes-ingress]の Kubernetes サービスを使用します。 このドキュメントでは、Kubernetes サービスの使用方法を詳しく説明します。


まず、Draft パックを更新して、種類が `LoadBalancer` のサービスを作成することを指定する必要があります。 これを行うには、`values.yaml` ファイルでサービスの種類を更新します。

```console
vi charts/java/values.yaml
```

`service.type` プロパティを見つけ、値を `ClusterIP` から `LoadBalancer` に更新します。

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

`draft up` を実行してアプリケーションを再実行します。

```console
draft up
```

サービスからパブリック IP アドレスが返されるまで数分かかることがあります。 進行状況を監視するには、ウォッチを指定した `kubectl get service` コマンドを使用します。

```console
kubectl get service -w
```

最初は、サービスの *EXTERNAL-IP* が `pending` と表示されます。

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

EXTERNAL-IP アドレスが `pending` から `IP address` に変わったら、`Control+C` を使用して kubectl ウォッチ プロセスを停止します。

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

アプリケーションを表示するには、外部 IP アドレスを参照します。

```console
curl 52.175.224.118
```

出力:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>アプリケーションの反復

Draft が構成され、アプリケーションが Kubernetes で実行中であり、コードを反復する準備が整いました。 更新されたコードをテストするときは、`draft up` コマンドを毎回実行して、実行中のアプリケーションを更新します。

この例では、Java の Hello World アプリケーションを更新します。

```console
vi src/main/java/helloworld/Hello.java
```

Hello World のテキストを更新します。

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

`draft up --auto-connect` コマンドを実行して、ポッドに応答の準備ができたらすぐにアプリケーションを再デプロイします。

```console
draft up --auto-connect
```

出力

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

最後に、アプリケーションを表示して更新内容を確認します。

```console
curl 52.175.224.118
```

出力:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>次の手順

Draft の使用方法の詳細については、GitHub の Draft のドキュメントをご覧ください。

> [!div class="nextstepaction"]
> [Draft のドキュメント][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
