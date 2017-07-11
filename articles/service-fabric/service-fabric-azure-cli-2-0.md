---
title: "Service Fabric と Azure CLI 2.0 の概要"
description: "Azure CLI バージョン 2.0 の Service Fabric コマンド モジュールの使用方法 (クラスターへの接続やアプリケーションの管理など)"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

# Service Fabric と Azure CLI 2.0

新しい Azure CLI 2.0 には、Service Fabric クラスターを管理するためのコマンドが追加されています。 このドキュメントには、Azure CLI の基本操作についての手順が記載されています。

<a id="install-azure-cli-20" class="xliff"></a>

## Azure CLI 2.0 のインストール

Azure CLI には、Service Fabric クラスターの操作と管理のためのコマンドが追加されています。 最新の Azure CLI は、[標準的なインストール プロセス](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)に従って入手できます。

詳細については、[Azure CLI 2.0 のドキュメント](https://docs.microsoft.com/en-us/cli/azure/overview)を参照してください。

<a id="cli-syntax" class="xliff"></a>

## CLI 構文

Azure CLI の Azure Service Fabric コマンドはいずれも `az sf` で始まります。 使用できるコマンドについて調べるには、`az sf -h` を実行してください。コマンド全般についての情報が表示されます。 特定のコマンドについての詳しいヘルプ情報を表示するには、`az sf <command> -h` を実行してください。

CLI の Azure Service Fabric コマンドには、次のパターンに従って名前が付けられています。

```azurecli
az sf <object> <action>
```

ここで、`<object>` は `<action>` の対象です。

<a id="selecting-a-cluster" class="xliff"></a>

## クラスターの選択

何らかの操作を実行するには、接続先のクラスターを選択する必要があります。 次のコード スニペットをご覧ください。これはセキュリティで保護されていないクラスターに接続する例です。

> [!WARNING]
> セキュリティで保護されていない Service Fabric クラスターを運用環境で使用することは避けてください。

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

クラスターのエンドポイントには、プレフィックスとして `http` または `https` を付けたうえで、HTTP ゲートウェイのポートを含める必要があります。 このポートとアドレスは、Service Fabric Explorer の URL と同じです。

証明書で保護されているクラスターの場合は、暗号化されていない `pem` ファイルがサポートされているほか、`crt` ファイルと `key` ファイルの対もサポートされています。

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

詳細については、[セキュリティで保護されたクラスターへの接続について詳しく説明されたドキュメント](service-fabric-connect-to-secure-cluster.md)を参照してください。

> [!NOTE]
> この select コマンドは、制御を返すまでの間、要求を一切行いません。 クラスターが正しく指定されていることを確認するには、`az sf cluster health` などのコマンドを実行して、エラーが返されないことを確かめてください。

<a id="performing-basic-operations" class="xliff"></a>

## 基本操作の実行

クラスター接続情報は Azure CLI セッションをまたいで維持されます。 Service Fabric クラスターを選択したら、任意の Service Fabric コマンドを実行できます。

たとえば Service Fabric クラスターの正常性の状態を取得するには、次のコマンドを実行します。

```azurecli
az sf cluster health
```

Azure CLI 構成で JSON 出力が指定されている場合、このコマンドの出力は次のようになります。

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

<a id="tips-and-faq" class="xliff"></a>

## ヒントと FAQ

ここでは、Azure CLI で Service Fabric コマンドを使っているときに問題が発生した場合に役立つと思われる情報を取り上げます。

<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

### PFX から PEM への証明書の変換

Azure CLI は、クライアント側の証明書を PEM (拡張子 `.pem`) ファイルとしてサポートします。 Windows の PFX ファイルを使用している場合、これらの証明書を PEM 形式に変換する必要があります。 PFX ファイルから PEM ファイルに変換するには、次のコマンドを使用します。

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

詳細については、[OpenSSL のドキュメント](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html)を参照してください。

<a id="connection-issues" class="xliff"></a>

### 接続に関する問題

操作を実行しているときに、次のエラーが発生する場合があります。

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

この場合は、指定したクラスター エンドポイントが到達可能で、かつリッスン状態になっていることを再確認してください。 また、そのホストとポートで Service Fabric Explorer の UI に到達可能であることも確認してください。 エンドポイントを更新するには `az sf cluster select` を使用します。

<a id="getting-detailed-logs" class="xliff"></a>

### 詳細ログの取得

問題をデバッグしたりレポートしたりする際は、詳細ログを含めることをお勧めします。 Azure CLI には、ログの詳細レベルを引き上げるグローバルな `--debug` フラグが用意されています。

<a id="command-help-and-syntax" class="xliff"></a>

### コマンドのヘルプと構文

Service Fabric のコマンドは、Azure CLI と同じ規則を踏襲しています。 特定のコマンドや、コマンドのグループについてのヘルプ情報を表示するには、`-h` フラグを指定します。 For example:

```azurecli
az sf application -h
```

または

```azurecli
az sf application create -h
```

