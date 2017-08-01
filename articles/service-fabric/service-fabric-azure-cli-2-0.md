---
title: "Azure Service Fabric と Azure CLI 2.0 の概要"
description: "Azure CLI バージョン 2.0 に含まれている Azure Service Fabric コマンド モジュールの使用方法について説明します。 クラスターに接続して、アプリケーションを管理する方法についても説明します。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Azure Service Fabric と Azure CLI 2.0

Azure コマンドライン ツール (Azure CLI) バージョン 2.0 には、Azure Service Fabric クラスターの管理のためのコマンドが追加されています。 Azure CLI と Service Fabric の操作方法について説明します。

## <a name="install-azure-cli-20"></a>Azure CLI 2.0 のインストール

Azure CLI 2.0 コマンドを使用して、Service Fabric クラスターの操作と管理を行うことができます。 最新バージョンの Azure CLI は、[Azure CLI 2.0 の標準的なインストール プロセス](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)に従って入手できます。

詳細については、[Azure CLI 2.0 の概要](https://docs.microsoft.com/en-us/cli/azure/overview)に関するページを参照してください。

## <a name="azure-cli-syntax"></a>Azure CLI 構文

Azure CLI の Service Fabric コマンドは、いずれも `az sf` で始まります。 使用できるコマンドについて調べたい場合は、`az sf -h` を使用すると、コマンド全般についての情報が表示されます。 `az sf <command> -h` を使用すると、特定のコマンドについてのヘルプ情報が表示されます。

Azure CLI の Service Fabric コマンドには、次のパターンに従って名前が付けられています。

```azurecli
az sf <object> <action>
```

`<object>` は `<action>` の対象です。

## <a name="select-a-cluster"></a>クラスターの選択

何らかの操作を実行するには、まず、接続先のクラスターを選択する必要があります。 例として、次のコードを参照してください。 このコードは、セキュリティ保護されていないクラスターに接続します。

> [!WARNING]
> セキュリティ保護されていない Service Fabric クラスターを運用環境で使用することは避けてください。

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

クラスターのエンドポイントには、プレフィックスとして `http` または `https` を付けたうえで、 HTTP ゲートウェイのポートを含める必要があります。 このポートとアドレスは、Service Fabric Explorer の URL と同じです。

証明書で保護されているクラスターの場合は、暗号化されていない .pem ファイルか、.crt ファイルと .key ファイルの対を使用できます。 For example:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

詳細については、[セキュリティ保護された Azure Service Fabric クラスターへの接続](service-fabric-connect-to-secure-cluster.md)に関するページを参照してください。

> [!NOTE]
> この `select` コマンドは、制御を返すまでの間、要求を一切行いません。 クラスターが正しく指定されていることを確認するには、`az sf cluster health` などのコマンドを使用し、 エラーが返されないことを確認してください。

## <a name="basic-operations"></a>基本操作

クラスター接続情報は Azure CLI セッションをまたいで維持されます。 Service Fabric クラスターを選択したら、任意の Service Fabric コマンドをクラスターに対して実行できます。

たとえば Service Fabric クラスターの正常性の状態を取得するには、次のコマンドを使用します。

```azurecli
az sf cluster health
```

このコマンドの出力は次のようになります (Azure CLI 構成で JSON 出力が指定されている場合)。

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

## <a name="tips-and-troubleshooting"></a>ヒントとトラブルシューティング

Azure CLI で Service Fabric コマンドを使っているときに問題が発生した場合に役立つと思われる情報を紹介します。

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>PFX 形式から PEM 形式への証明書の変換

Azure CLI は、クライアント側の証明書を PEM (拡張子 .pem) ファイルとしてサポートします。 Windows の PFX ファイルを使用している場合は、これらの証明書を PEM 形式に変換する必要があります。 PFX ファイルを PEM ファイルに変換するには、次のコマンドを使用します。

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

詳細については、[ のドキュメント](https://www.openssl.org/docs/)を参照してください。

### <a name="connection-issues"></a>接続に関する問題

一部の操作において、次のメッセージが表示される場合があります。

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

この場合は、指定したクラスター エンドポイントが利用可能で、リッスン状態になっていることを確認してください。 また、そのホストとポートで Service Fabric Explorer の UI を利用できることも確認してください。 エンドポイントを更新するには `az sf cluster select` を使用します。

### <a name="detailed-logs"></a>詳細ログ

問題のデバッグやレポート作成の多くで、詳細ログが役立ちます。 Azure CLI には、ログ ファイルの詳細レベルを上げるためのグローバルな `--debug` フラグが用意されています。

### <a name="command-help-and-syntax"></a>コマンドのヘルプと構文

Service Fabric のコマンドは、Azure CLI と同じ規則を踏襲しています。 特定のコマンドや、コマンドのグループについてのヘルプ情報を表示するには、次の例のように、`-h` フラグを使用します。

```azurecli
az sf application -h
```

別の例:

```azurecli
az sf application create -h
```

