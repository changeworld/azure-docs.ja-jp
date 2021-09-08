---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: pafarley
ms.openlocfilehash: e01c1e82e5949cd42765e290e686f48f9d6f3296
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646442"
---
圧縮オーディオの処理は、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、Speech SDK では GStreamer バイナリはコンパイルおよびリンクされません。 開発者は、いくつかの依存関係とプラグインをインストールする必要があります。

> [!NOTE]
> Linux で必須の一般的なセットアップについては、[システム要件とセットアップ手順](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)に関する記事を参照してください。

# <a name="ubuntudebian"></a>[Ubuntu/Debian](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos"></a>[RHEL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> - RHEL/CentOS 7 と RHEL/CentOS 8 で圧縮形式として "ANY" を使用している場合、ストリーム メディア形式プラグインが上記のインストール済みプラグインに含まれていない場合は、より多くの GStreamer プラグインをインストールする必要があります。 


---