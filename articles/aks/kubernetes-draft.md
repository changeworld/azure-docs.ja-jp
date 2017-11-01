---
title: "AKS と Azure Container Registry で Draft を使用する | Microsoft Docs"
description: "AKS と Azure Container Registry で Draft を使用します。"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: draft, helm, aks, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, Draft, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3e607a6ce5662f6ff597fafbcec8c864f25ff54c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Azure Container Service (AKS) での Draft の使用

Draft は、Kubernetes クラスターでコードをパッケージ化し、実行する際に役立つオープン ソース ツールです。 Draft は、コードの開発時にバージョン管理にコミットする前の開発の反復サイクルを対象としています。 Draft を使用すると、コードの変更が発生したときに、アプリケーションを Kubernetes にすばやく再デプロイできます。 Draft の詳細については、[Github の Draft のドキュメント](https://github.com/Azure/draft/tree/master/docs)をご覧ください。

このドキュメントでは、AKS の Kubernetes クラスターで Draft を使用する方法について詳しく説明します。

## <a name="prerequisites"></a>前提条件

このドキュメントで説明する手順は、AKS クラスターを作成済みであり、クラスターとの kubectl 接続が確立されていることを前提としています。 これらの項目が必要な場合は、[AKS のクイックスタート](./kubernetes-walkthrough.md)をご覧ください。

Azure Container Registry (ACR) のプライベート Docker レジストリも必要です。 ACR インスタンスをデプロイする手順については、[Azure Container Registry のクイックスタート](../container-registry/container-registry-get-started-azure-cli.md)をご覧ください。

## <a name="install-helm"></a>Helm のインストール

Helm CLI は、開発システムで実行されるクライアントです。Helm CLI では、Helm チャートを使用してアプリケーションを起動、停止、管理することができます。

Helm CLI を Mac にインストールするには、`brew` を使用します。 その他のインストール オプションについては、「[Installing Helm (Helm のインストール)](https://github.com/kubernetes/helm/blob/master/docs/install.md)」をご覧ください。

```console
brew install kubernetes-helm
```

出力:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Draft のインストール

Draft CLI は、開発システムで実行され、Kubernetes クラスターへのコードの迅速なデプロイを可能にするクライアントです。

Draft CLI を Mac にインストールするには、`brew` を使用します。 その他のインストール オプションについては、[Draft のインストール ガイド](https://github.com/Azure/draft/blob/master/docs/install.md)をご覧ください。

```console
brew install draft
```

出力:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Draft の構成

Draft を構成するときは、コンテナー レジストリを指定する必要があります。 この例では、Azure Container Registry を使用します。

次のコマンドを実行して、ACR インスタンスの名前とログイン サーバー名を取得します。 ACR インスタンスを含むリソース グループの名前でコマンドを更新します。

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

ACR インスタンスのパスワードも必要です。

次のコマンドを実行すると、ACR のパスワードが返されます。 ACR インスタンスの名前でコマンドを更新します。

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

`draft init` コマンドを使用して Draft を初期化します。

```console
draft init
```

このプロセス中に、コンテナー レジストリの資格情報の入力を求められます。 Azure Container Registry を使用している場合、レジストリ URL は ACR のログイン サーバー名、ユーザー名は ACR インスタンス名、パスワードは ACR のパスワードです。

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

操作が完了すると、Kubernetes クラスターで Draft が構成され、Draft を使用できるようになります。

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>アプリケーションの実行

Draft リポジトリには、Draft のデモに使用できるサンプル アプリケーションがいくつか含まれています。 リポジトリの複製されたコピーを作成します。

```console
git clone https://github.com/Azure/draft
```

Java の examples ディレクトリに移動します。

```console
cd draft/examples/java/
```

`draft create` コマンドを使用して、プロセスを開始します。 このコマンドは、Kubernetes クラスターでアプリケーションを実行する際に使用されるアーティファクトを作成します。 これらの項目には、Dockerfile、Helm チャート、`draft.toml` ファイル (Draft 構成ファイル) が含まれます。

```console
draft create
```

出力:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Kubernetes クラスターでアプリケーションを実行するには、`draft up` コマンドを使用します。 このコマンドは、アプリケーション コードと構成ファイルを Kubernetes クラスターにアップロードします。 次に、Dockerfile を実行してコンテナー イメージを作成し、イメージをコンテナー レジストリにプッシュします。最後に、Helm チャートを実行してアプリケーションを起動します。

```console
draft up
```

出力:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションをテストするには、`draft connect` コマンドを使用します。 このコマンドは、セキュリティで保護されたローカル接続を可能にする Kubernetes ポッド への接続をプロキシします。 完了すると、提供された URL でアプリケーションにアクセスできます。

コンテナー イメージがダウンロードされ、アプリケーションが起動するまで数分かかる場合があります。 アプリケーションにアクセスしたときにエラーが発生した場合は、接続を再試行してください。

```console
draft connect
```

出力:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

アプリケーションのテストが完了したら、`Control+C` を使用してプロキシ接続を停止します。

## <a name="expose-application"></a>アプリケーションを公開する

Kubernetes でアプリケーションをテストするときに、アプリケーションをインターネットで使用できるようにすることもできます。 そのためには、種類が [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) または[イングレス コントローラー](https://kubernetes.io/docs/concepts/services-networking/ingress/)の Kubernetes サービスを使用します。 このドキュメントでは、Kubernetes サービスの使用方法を詳しく説明します。


まず、Draft パックを更新して、種類が `LoadBalancer` のサービスを作成することを指定する必要があります。 これを行うには、`values.yaml` ファイルでサービスの種類を更新します。

```console
vi chart/java/values.yaml
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
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

EXTERNAL-IP アドレスが `pending` から `IP address` に変わったら、`Control+C` を使用して kubectl ウォッチ プロセスを停止します。

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
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
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

`draft up` コマンドを実行して、アプリケーションを再デプロイします。

```console
draft up
```

出力

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

最後に、アプリケーションを表示して更新内容を確認します。

```console
curl 52.175.224.118
```

出力:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>次のステップ

Draft の使用方法の詳細については、GitHub の Draft のドキュメントをご覧ください。

> [!div class="nextstepaction"]
> [Draft のドキュメント](https://github.com/Azure/draft/tree/master/docs)