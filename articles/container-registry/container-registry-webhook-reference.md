---
title: Azure Container Registry webhook スキーマ リファレンス
description: Azure Container Registry の webhook 要求 JSON ペイロードのリファレンスです。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: f62477a4c68abf1617d9689047913fd820ee5461
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166009"
---
# <a name="azure-container-registry-webhook-reference"></a>Azure Container Registry webhook リファレンス

コンテナー レジストリに対して特定のアクションが実行されたときにイベントを生成する、コンテナー レジストリの [webhook を構成する](container-registry-webhook.md)ことができます。 たとえば、コンテナー イメージの `push` および `delete` 操作でトリガーされる webhook を有効にできます。 webhook がトリガーされると、Azure Container Registry は、イベントに関する情報を含む HTTP または HTTPS 要求を、ユーザーが指定したエンドポイントに対して発行します。 エンドポイントは webhook を処理し、適切に対応できます。

以下のセクションでは、サポートされるイベントによって生成される webhook 要求のスキーマについて詳しく説明します。 イベントに関するセクションには、そのイベントの種類のペイロード スキーマ、要求のペイロードの例、および webhook をトリガーするコマンドの例が含まれます。

Azure Container Registry webhook の構成方法については、「[Azure Container Registry webhook の使用](container-registry-webhook.md)」をご覧ください。

## <a name="webhook-requests"></a>webhook の要求

### <a name="http-request"></a>HTTP 要求

トリガーされた webhook は、ユーザーが webhook を構成するときに指定した URL エンドポイントに対して、HTTP `POST` 要求を行います。

### <a name="http-headers"></a>HTTP ヘッダー

ユーザーが webhook の `Content-Type` カスタム ヘッダーを指定していない場合、webhook 要求には `application/json` の `Content-Type` が含まれます。

ユーザーが webhook に指定したカスタム ヘッダー以外に、他のヘッダーが要求に追加されることはありません。

## <a name="push-event"></a>push イベント

コンテナー イメージがリポジトリにプッシュされるとトリガーされる webhook です。

### <a name="push-event-payload"></a>push イベントのペイロード

|要素|type|[説明]|
|-------------|----------|-----------|
|`id`|String|webhook イベントの ID。|
|`timestamp`|Datetime|webhook イベントがトリガーされた日時。|
|`action`|String|webhook イベントをトリガーしたアクション。|
|[target](#target)|複合型|webhook イベントをトリガーしたイベントのターゲット。|
|[request](#request)|複合型|webhook イベントを生成した要求。|

### <a name="target"></a>target

|要素|type|[説明]|
|------------------|----------|-----------|
|`mediaType`|String|参照されているオブジェクトの MIME の種類。|
|`size`|Int32|コンテンツのバイト数。 length フィールドと同じです。|
|`digest`|String|コンテンツのダイジェスト。Registry V2 HTTP API 仕様で定義されています。|
|`length`|Int32|コンテンツのバイト数。 size フィールドと同じです。|
|`repository`|String|リポジトリの名前。|
|`tag`|String|イメージ タグの名前。|

### <a name="request"></a>request

|要素|type|[説明]|
|------------------|----------|-----------|
|`id`|String|イベントを開始した要求の ID。|
|`host`|String|レジストリ インスタンスの外部からアクセス可能なホスト名。受信した要求の HTTP ホスト ヘッダーで指定されています。|
|`method`|String|イベントを生成した要求メソッド。|
|`useragent`|String|要求のユーザー エージェント ヘッダー。|

### <a name="payload-example-push-event"></a>ペイロードの例: push イベント

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

**push** イベント webhook をトリガーする [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) コマンドの例:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>インベントリを削除する

リポジトリまたはマニフェストが削除されるとトリガーされる webhook です。 タグが削除されたときはトリガーされません。

### <a name="delete-event-payload"></a>delete イベントのペイロード

|要素|type|[説明]|
|-------------|----------|-----------|
|`id`|String|webhook イベントの ID。|
|`timestamp`|Datetime|webhook イベントがトリガーされた日時。|
|`action`|String|webhook イベントをトリガーしたアクション。|
|[target](#delete_target)|複合型|webhook イベントをトリガーしたイベントのターゲット。|
|[request](#delete_request)|複合型|webhook イベントを生成した要求。|

### <a name="delete_target"></a> target

|要素|type|[説明]|
|------------------|----------|-----------|
|`mediaType`|String|参照されているオブジェクトの MIME の種類。|
|`digest`|String|コンテンツのダイジェスト。Registry V2 HTTP API 仕様で定義されています。|
|`repository`|String|リポジトリの名前。|

### <a name="delete_request"></a> request

|要素|type|[説明]|
|------------------|----------|-----------|
|`id`|String|イベントを開始した要求の ID。|
|`host`|String|レジストリ インスタンスの外部からアクセス可能なホスト名。受信した要求の HTTP ホスト ヘッダーで指定されています。|
|`method`|String|イベントを生成した要求メソッド。|
|`useragent`|String|要求のユーザー エージェント ヘッダー。|

### <a name="payload-example-delete-event"></a>ペイロードの例: delete イベント

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

**delete** イベント webhook をトリガーする [Azure CLI 2.0](/cli/azure/acr) コマンドの例:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>次の手順

[Azure Container Registry webhook の使用](container-registry-webhook.md)