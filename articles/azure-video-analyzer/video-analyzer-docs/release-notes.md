---
title: Azure Video Analyzer リリース ノート - Azure
description: このトピックでは、Azure Video Analyzer のリリース、機能強化、バグ修正、および既知の問題に関するリリース ノートを提供します。
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: ef1c395bb10000599f901e62cf77b3573146aeee
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2021
ms.locfileid: "111591226"
---
# <a name="azure-video-analyzer-release-notes"></a>Azure Video Analyzer リリース ノート

>この URL (`https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us`) をコピーして、お使いの RSS フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

この記事では、次の項目に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

<hr width=100%>

## <a name="june-3-2021"></a>2021 年 6 月 3 日

モジュールの 2021 年 6 月の更新に関するこのリリース タグは次のとおりです。

```
     mcr.microsoft.com/media/video-analyzer:1.0.1
```
> [!NOTE]
> クイックスタートおよびチュートリアルでは、配置マニフェストで 1 のタグ (video-analyzer:1) を使用します。 そのため、新しいタグがリリースされると、このようなマニフェストを再配置するだけで、エッジとデバイスでモジュールが更新されます。

### <a name="module-updates"></a>モジュールの更新
* RTSP カメラに接続するための資格情報内の Unicode 文字をサポートします
* デバッグ モードで詳細なログを有効にする更新

<hr width=100%>

## <a name="may-25-2021"></a>2021 年 5 月 25 日

このリリースは、Azure Video Analyzer の最初のパブリック プレビュー リリースです。 リリース タグは、次のとおりです。

```
mcr.microsoft.com/media/video-analyzer:1.0.0
```

> [!NOTE]
> クイックスタートおよびチュートリアルでは、配置マニフェストで 1 のタグ (video-analyzer:1) を使用します。 そのため、新しいタグがリリースされると、このようなマニフェストを再配置するだけで、エッジとデバイスでモジュールが更新されます。

### <a name="supported-functionalities"></a>サポートされる機能

* AI モジュールを使用してライブ ビデオ ストリームを分析し、必要に応じて、エッジ デバイスまたはクラウドにビデオを記録する
* クラウドで推論メタデータと共にビデオを記録する
* 独自のオブジェクト検出モデルを使用してオブジェクトが指定値を超えるとイベントをトリガーする
* 独自の検出モデルによって検出されたオブジェクトを追跡する 
* Video Analyzer プレーヤー ウィジェット (Web コンポーネント) を使用して、記録されたビデオと推論メタデータを再生する
* Azure portal または Visual Studio Code を使用して、IoT Hub 経由でモジュールをデプロイおよび構成する
* [ダイレクト メソッド](direct-methods.md)呼び出しを使用して[パイプライン トポロジー](pipeline.md#pipeline-topologies)をリモートまたはローカルで管理する

## <a name="next-steps"></a>次のステップ

[概要](overview.md)
