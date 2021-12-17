---
title: OSM クライアント ライブラリのダウンロード
description: Open Service Mesh (OSM) クライアント ライブラリをダウンロードして構成する
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2524dca92126ac0fdd6c0ace502cc16d78b6d480
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066845"
---
# <a name="download-and-configure-the-open-service-mesh-osm-client-library"></a>Open Service Mesh (OSM) クライアント ライブラリをダウンロードして構成する

この記事では、AKS の OSM アドオンの操作と構成に使用する OSM クライアント ライブラリをダウンロードする方法と、環境のバイナリを構成する方法について説明します。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-windows.md)]

::: zone-end

> [!WARNING]
> `osm install` を使用してバイナリから OSM のインストールを試行しないでください。 こうすると、AKS のアドオンとして統合されていない OSM がインストールされます。

## <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>OSM_CONFIG ファイルを使用して OSM CLI 変数を構成する

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
