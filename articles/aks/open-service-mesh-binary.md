---
title: OSM クライアント ライブラリのダウンロード
description: Open Service Mesh (OSM) クライアント ライブラリのダウンロード
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 83eec8682291a74bc9b23bf4ff1326cee7134e7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534934"
---
# <a name="download-the-open-service-mesh-osm-client-library"></a>Open Service Mesh (OSM) クライアント ライブラリのダウンロード
この記事では、AKS 用の OSM アドオンの操作と構成に使用する OSM クライアント ライブラリをダウンロードする方法について説明します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

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

> [!NOTE]
> OSM CLI を使用する前に、バイナリのインストール後に OSM CLI を構成して [OSM AKS アドオン エクスペリエンスをカスタマイズ](/open-service-mesh-customize-addon-experience.md)することをお勧めします。 