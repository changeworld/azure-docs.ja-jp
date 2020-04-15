---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637287"
---
圧縮オーディオの処理は、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、Speech SDK では GStreamer バイナリはコンパイルおよびリンクされません。 開発者は、いくつかの依存関係とプラグインをインストールする必要があります。「[Windows にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)」を参照してください。 Gstreamerバイナリはシステムパスに存在する必要があるため、speech SDK は実行時にgstreamerバイナリを読み込むことができます。 音声SDK が実行時に libgstreamer-1.0 横-0 を見つけることができる場合は、gstreamer バイナリがシステムパスにあることを意味します。

