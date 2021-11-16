---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 09/17/2021
ms.author: amishu
ms.openlocfilehash: 50383f178faaffa149eb371d21e95d05acdaad0a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156721"
---
Speech SDK では、[GStreamer](https://gstreamer.freedesktop.org) を使用して圧縮オーディオを処理できます。 ただし、ライセンスの理由から、Speech SDK では GStreamer バイナリはコンパイルおよびリンクされません。 開発者は、いくつかの依存関係とプラグインをインストールする必要があります。「[Linux にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)」を参照してください。 Go 言語は Linux プラットフォームの Speech SDK でのみサポートされています。 Go で Microsoft Speech SDK の使用を開始するには、[Speech SDK for Go](../../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser) に関するページを参照してください。 

