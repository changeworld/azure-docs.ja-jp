---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97821521"
---
圧縮オーディオの処理は、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、Speech SDK では GStreamer バイナリはコンパイルおよびリンクされません。 開発者は、いくつかの依存関係とプラグインをインストールする必要があります。「[Windows にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)」または「[Linux にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)」を参照してください。 Speech SDK が実行時にバイナリを読み込めるように、GStreamer バイナリはシステム パスに存在する必要があります。 たとえば Windows では、Speech SDK が実行時に `libgstreamer-1.0-0.dll` を見つけることができる場合は、gstreamer バイナリがシステムパスにあることを意味します。

