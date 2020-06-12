---
title: Live Video Analytics on IoT Edge リリース ノート - Azure
description: このトピックでは、Live Video Analytics on IoT Edge のリリース、機能強化、バグ修正、および既知の問題に関するリリース ノートを提供します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260325"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Live Video Analytics on IoT Edge リリース ノート

>この URL (`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`) をコピーして、お使いの RSS フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

この記事では、次の項目に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

## <a name="june-1-2020"></a>2020 年 6 月 1 日

このリリースは、Live Video Analytics on IoT Edge の最初のパブリック プレビュー リリースです。 リリース タグは、次のとおりです。

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>サポートされる機能
* AI モジュールを使用してライブ ビデオ ストリームを分析し、必要に応じて、エッジ デバイスまたはクラウドにビデオを記録する
* IoT Edge によって[サポートされている ](https://docs.microsoft.com/azure/iot-edge/support)Linux AMD64 オペレーティング システム上で使用する
* Azure portal または Visual Studio Code を使用して、IoT Hub 経由でモジュールをデプロイおよび構成する
* 次のダイレクト メソッド呼び出しを使用して、リモートまたはローカルに[グラフ トポロジーおよびグラフ インスタンス](media-graph-concept.md#media-graph-topologies-and-instances)を管理する

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>次のステップ

[概要](overview.md)
