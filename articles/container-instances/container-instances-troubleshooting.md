---
title: "Azure Container Instances のトラブルシューティング"
description: "Azure Container Instances に関する問題のトラブルシューティングを行う方法について説明します"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 561729e5e495500222ccec5b4b536a3152cb25e3
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Azure Container Instances でのデプロイに関する問題をトラブルシューティングする

この記事では、Azure Container Instances にコンテナーをデプロイする際の問題をトラブルシューティングする方法を示します。 また、発生する可能性があるいくつかの一般的な問題についても説明します。

## <a name="get-diagnostic-events"></a>診断イベントの取得

アプリケーション コードからコンテナー内のログを表示するには、[az container logs][az-container-logs] コマンドを使用できます。 ただし、コンテナーが正常にデプロイされていない場合は、Azure Container Instances のリソース プロバイダーによって提供される診断情報を見直す必要があります。 コンテナーのイベントを表示するには、[az container show][az-container-show] コマンドを実行します。

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

出力には、コンテナーの主要プロパティとデプロイ イベント (抜粋) が含まれます。

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>一般的なデプロイ問題

デプロイで発生するほとんどのエラーの主な原因となる少数の一般的な問題があります。

## <a name="image-version-not-supported"></a>イメージ バージョンがサポートされない

Azure Container Instances がサポートできないイメージが指定された場合、フォーム `ImageVersionNotSupported` でエラーが返されます。 エラーの値には `The version of image '{0}' is not supported.` が表示されます。 このエラーは、LTS Windows イメージの使用を軽減するために、現時点では Windows 1709 イメージに適用されます。 Windows 1709 イメージのサポートは実施されています。

## <a name="unable-to-pull-image"></a>イメージをプルできない

Azure Container Instances は、最初にイメージをプルすることができなかった場合、最終的に失敗するまで一定期間再試行します。 イメージをプルできない場合は、[az container show][az-container-show] の出力に次のようなイベントが表示されます。

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

この問題を解決するには、コンテナーを削除し、デプロイをやり直します。このとき、細心の注意を払って、イメージ名を正しく入力します。

## <a name="container-continually-exits-and-restarts"></a>コンテナーが絶えず終了して再起動する

お使いのコンテナーが最後まで実行され、自動的に再起動される場合、[再起動ポリシー](container-instances-restart-policy.md)を **OnFailure** または **Never** に設定する必要が生じることがあります。 **OnFailure** を指定してもそのまま再起動された場合、お使いのコンテナーで実行されるアプリケーションまたはスクリプトに問題が生じている可能性があります。

Container Instances API には `restartCount` プロパティが含まれています。 コンテナーの再起動の回数を確認するには、Azure CLI 2.0 の [az container show][az-container-show] コマンドを使用できます。 次の出力例 (簡略化のため一部のみ) では、出力の末尾に `restartCount` プロパティを確認できます。

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux ディストリビューションのほとんどのコンテナー イメージは、既定のコマンドとして、bash などのシェルを設定します。 独自のシェルは実行時間の長いサービスではないため、既定の **[常時]** 再起動ポリシーを利用して構成された場合、これらのコンテナーはすぐに終了し、再起動ループ状態に陥ります。

## <a name="container-takes-a-long-time-to-start"></a>コンテナーの起動に時間がかかる

コンテナーが起動するまで時間がかかるが、最終的に起動する場合は、コンテナー イメージのサイズを調べることから始めてください。 Azure Container Instances は、要求に応じてコンテナー イメージをプルするため、起動時間はそのサイズと直接的に関係しています。

コンテナー イメージのサイズは、Docker CLI を使用して表示できます。

```bash
docker images
```

出力:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

イメージのサイズを小さくしておくための鍵は、最終イメージに実行時に不要なものが含まれないようにすることです。 これを行う 1 つの方法は、[マルチステージ ビルド][docker-multi-stage-builds]を使用することです。 マルチステージ ビルドを使用すると、最終イメージにはアプリケーションに必要な成果物のみが含まれ、ビルド時に必要であった余分なコンテンツは含まれないようにすることを簡単に実行できます。

コンテナーの起動時に発生するイメージ プルの影響を軽減する他の方法は、Azure Container Instances を使用する予定のリージョンと同じリージョン内の Azure Container Registry を使用してコンテナー イメージをホストすることです。 これにより、コンテナー イメージを伝送する必要があるネットワーク パスが短縮され、ダウンロード時間が大幅に短くなります。

## <a name="resource-not-available-error"></a>リソース使用不可エラー

Azure ではリージョンによってリソースの読み込みに変化があるため、コンテナー インスタンスをデプロイしようとすると、以下のエラーが表示される場合があります。

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

このエラーは、デプロイを試行しているリージョンで高負荷になっているため、コンテナーに指定されたリソースが、その時点では割り当てできないことを示しています。 以下に示す 1 つ以上の軽減策の手順を使用して、問題を解決してください。

* コンテナーのデプロイ設定が、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md#region-availability)」 (Azure Container Instances のクォータとリージョンの可用性) で定義されているパラメーター内に収まっていることを確認する
* コンテナーに低い CPU およびメモリ設定を指定する。
* 別の Azure リージョンにデプロイする
* 後でデプロイする

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/

<!-- LINKS - Internal -->
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show