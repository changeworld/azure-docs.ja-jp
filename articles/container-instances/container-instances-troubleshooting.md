---
title: Azure Container Instances のトラブルシューティング
description: Azure Container Instances に関する問題のトラブルシューティングを行う方法について説明します
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a527939d6bc73e3dee5701bc53ef8312e68d2953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Azure Container Instances でのデプロイに関する問題をトラブルシューティングする

この記事では、Azure Container Instances にコンテナーをデプロイする際の問題をトラブルシューティングする方法を示します。 また、発生する可能性があるいくつかの一般的な問題についても説明します。

## <a name="view-logs-and-stream-output"></a>ログの表示と出力のストリーミング

コンテナーが正常に動作しない場合、[az container logs][az-container-logs] でそのログを確認することから始めます。次に、[az container attach][az-container-attach] でその標準出力と標準エラーをストリーミングします。

### <a name="view-logs"></a>ログを表示する。

アプリケーション コードからコンテナー内のログを表示するには、[az container logs][az-container-logs] コマンドを使用できます。

「[Azure Container Instances でコンテナー化タスクを実行する](container-instances-restart-policy.md)」で説明されるタスク ベースのコンテナーの例に、処理する無効な URL を指定した後の、コンテナーからのログ出力を次に示します。

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

### <a name="attach-output-streams"></a>出力ストリームのアタッチ

[az container attach][az-container-attach] コマンドによって、コンテナーの起動中の診断情報が提供されます。 コンテナーが起動すると、ローカルのコンソールにコンテナーの STDOUT と STDERR がストリーミングされます。

例として、「[Azure Container Instances でコンテナー化タスクを実行する](container-instances-restart-policy.md)」のタスク ベースのコンテナーに、処理する大きなテキスト ファイルの有効な URL を指定した後の、コンテナーからの出力を次に示します。

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>診断イベントの取得

コンテナーが正常にデプロイされない場合は、Azure Container Instances のリソース プロバイダーによって提供される診断情報を見直す必要があります。 コンテナーのイベントを表示するには、[az container show][az-container-show] コマンドを実行します。

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

以下のセクションでは、コンテナーをデプロイする際に発生するほとんどのエラーの原因となっている、一般的な問題について説明します。

* [イメージ バージョンがサポートされない](#image-version-not-supported)
* [イメージをプルできない](#unable-to-pull-image)
* [コンテナーが絶えず終了して再起動する](#container-continually-exits-and-restarts)
* [コンテナーの起動に時間がかかる](#container-takes-a-long-time-to-start)
* ["リソース使用不可" エラー](#resource-not-available-error)

## <a name="image-version-not-supported"></a>イメージ バージョンがサポートされない

Azure Container Instances がサポートできないイメージを指定した場合、`ImageVersionNotSupported` エラーが返されます。 エラーの値は `The version of image '{0}' is not supported.` で、このエラーは現在 Windows 1709 イメージに適用されます。 この問題を軽減するには、LTS Windows イメージを使用します。 Windows 1709 イメージのサポートは実施されています。

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

Azure Container Instances のコンテナーの起動時間に関係する 2 つの主な要素を、次に示します。

* [イメージ サイズ](#image-size)
* [イメージの場所](#image-location)

Windows イメージには、[追加の考慮事項](#use-recent-windows-images)があります。

### <a name="image-size"></a>イメージ サイズ

コンテナーが起動するまで時間がかかるが、最終的に起動する場合は、コンテナー イメージのサイズを調べることから始めてください。 Azure Container Instances は、要求に応じてコンテナー イメージをプルするため、起動時間はそのサイズと直接的に関係しています。

コンテナー イメージのサイズは、Docker CLI で `docker images` コマンドを使用することで表示できます。

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

イメージのサイズを小さくしておくための鍵は、最終イメージに実行時に不要なものが含まれないようにすることです。 これを行う 1 つの方法は、[マルチステージ ビルド][docker-multi-stage-builds]を使用することです。 マルチステージ ビルドを使用すると、最終イメージにはアプリケーションに必要な成果物のみが含まれ、ビルド時に必要であった余分なコンテンツは含まれないようにすることを簡単に実行できます。

### <a name="image-location"></a>イメージの場所

コンテナーの起動時に発生するイメージ プルの影響を軽減する別の方法は、コンテナー インスタンスをデプロイする予定のリージョンと同じリージョン内の [Azure Container Registry](/azure/container-registry/) で、コンテナー イメージをホストすることです。 これにより、コンテナー イメージを伝送する必要があるネットワーク パスが短縮され、ダウンロード時間が大幅に短くなります。

### <a name="use-recent-windows-images"></a>最新の Windows イメージの使用

Azure Container Instances では、キャッシュ メカニズムを使用して、特定の Windows イメージに基づくイメージに対するコンテナーの起動時間を高速化します。

Windows コンテナーの起動時間を最速にするには、次の **2 つのイメージ**の**最新の 3 つ**のバージョンのいずれかを、基本イメージとして使用します。

* [Windows Server 2016][docker-hub-windows-core] (LTS のみ)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

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
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show