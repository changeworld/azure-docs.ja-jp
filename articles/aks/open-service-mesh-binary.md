---
title: OSM クライアント ライブラリのダウンロード
description: Open Service Mesh (OSM) クライアント ライブラリのダウンロード
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 128d0371e65d06e413f503094b56522c67ea83c0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440099"
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