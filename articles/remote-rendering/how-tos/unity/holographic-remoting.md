---
title: Unity で Holographic Remoting と Remote Rendering を使用する
description: Holographic Remoting のプレビューを Azure Remote Rendering と組み合わせて使用する方法
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679205"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Unity で Holographic Remoting と Remote Rendering を使用する

[Holographic Remoting](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) と Azure Remote Rendering は、1 つのアプリケーション内で同時に使用できません。 そのため、[Unity の再生モード](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode)も使用できません。

Unity エディターの実行ごとに使用できるのは、2 つのうちの一方のみです。 もう一方を使用する場合は、最初に Unity を再起動してください。

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Unity の再生モードを使用して Hololens 2 上でプレビューする

 Unity の再生モードは、アプリケーションの UI をテストするためなどに引き続き使用できます。 ただし、ARR を絶対に初期化しないことが重要です。 そうしないと、ARR がクラッシュします。

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>WMR VR ヘッドセットを使用してデスクトップ上でプレビューする

Windows Mixed Reality VR ヘッドセットがある場合は、Unity 内でプレビューするために使用できます。 この場合、ARR を初期化しても問題ありませんが、WMR ヘッドセットの使用中はセッションに接続できなくなります。
