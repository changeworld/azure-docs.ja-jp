---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: pafarley
ms.openlocfilehash: ebd29149e623430ce2b1c8831ca2d80b74c0697f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646727"
---
圧縮オーディオの処理は、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、Speech SDK では GStreamer バイナリはコンパイルおよびリンクされません。 開発者は、いくつかの依存関係とプラグインをインストールする必要があります。「[Windows にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)」または「[Linux にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)」を参照してください。 Speech SDK が実行時にバイナリを読み込めるように、GStreamer バイナリはシステム パスに存在する必要があります。 たとえば Windows では、Speech SDK が実行時に `libgstreamer-1.0-0.dll` または `gstreamer-1.0-0.dll` (最新の GStreamer 用) を見つけることができる場合は、GStreamer バイナリがシステム パスにあることを意味します。

