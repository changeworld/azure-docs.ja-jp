---
title: OSM CLI のエクスペリエンスをカスタマイズする
description: osmconfig ファイルを使用して OSM CLI エクスペリエンスをカスタマイズします。
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1e5b39424b8e162feda65783eecc3a1a95fdaa7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535481"
---
# <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>OSM_CONFIG ファイルを使用して OSM CLI 変数を構成する

ユーザーは、既定の OSM CLI 構成をオーバーライドしてアドオン エクスペリエンスを強化できます。 これは、`kubeconfig` のような構成ファイルを作成することで実行できます。 構成ファイルは、`$HOME/.osm/config.yaml` で作成するか、`OSM_CONFIG` 環境変数を使用してエクスポートされる別のパスで作成できます。

このファイルには、次の YAML 形式のコンテンツが含まれている必要があります。

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

ファイルが `$HOME/.osm/config.yaml` で作成されていない場合は、構成ファイルが作成されるパスを指す `OSM_CONFIG` 環境変数を必ず設定してください。

OSM_CONFIG の設定後の `osm env` コマンドの出力は、次のようになります。 

```console
$ osm env
--- 
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```