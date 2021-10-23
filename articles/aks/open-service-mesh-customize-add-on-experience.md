---
title: OSM CLI のエクスペリエンスをカスタマイズする
description: osmconfig ファイルを使用して OSM CLI エクスペリエンスをカスタマイズします。
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
ms.openlocfilehash: 9a18db63f247ed3508f09426841e84ae65c34923
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994767"
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