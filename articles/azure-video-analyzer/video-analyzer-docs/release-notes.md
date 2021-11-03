---
title: Azure Video Analyzer リリース ノート - Azure
description: このトピックでは、Azure Video Analyzer のリリース、機能強化、バグ修正、および既知の問題に関するリリース ノートを提供します。
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8b17c19d96699a6442bd6d606ffbc88b25088afe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087041"
---
# <a name="azure-video-analyzer-release-notes"></a>Azure Video Analyzer リリース ノート

>この URL (`https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us`) をコピーして、お使いの RSS フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

この記事では、次の項目に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

<hr width=100%>

## <a name="november-2-2021"></a>2021 年 11 月 2 日

このリリースは、Video Analyzer エッジ モジュールと Video Analyzer サービスを更新したものです。 エッジ モジュールのリリース タグは次のとおりです。

```
mcr.microsoft.com/media/video-analyzer:1.1.0
```

> [!NOTE]
> クイックスタートおよびチュートリアルでは、配置マニフェストで 1 のタグ (video-analyzer:1) を使用します。 そのため、新しいタグがリリースされると、このようなマニフェストを再配置するだけで、エッジとデバイスでモジュールが更新されます。

Video Analyzer サービスの ARM API バージョンは次のとおりです。

```
2021-11-01-preview
```

### <a name="new-functionalities"></a>新機能

* Cognitive Services から空間分析 AI サービスの [Computer Vision](edge/computer-vision-for-spatial-analysis.md) と共に Video Analyzer を使用する場合、ライブ ビデオにおける人の速度、向き、軌跡などの新しい分析情報を生成して表示することができます。
* エッジ デバイスのローカル サブネットで、[ONVIF 対応デバイスを検出](edge/camera-discovery.md)できます。
* [ライブ ビデオをキャプチャしてクラウドに直接録画](cloud/connect-cameras-to-cloud.md)できます。
  * [低待機時間ストリーミング](playback-recordings-how-to.md#low-latency-streaming)を使用して、RTSP カメラのライブ ビデオを約 2 秒間のエンドツーエンドの待機時間で表示することができます。
  * [Azure Video Analyzer の IoT PnP コントラクト](cloud/connect-devices.md)を RTSP カメラに実装して、デバイスから Azure Video Analyzer サービスへのビデオ キャプチャを有効にすることができます。
* [録画されたビデオの目的の部分を MP4 ファイルにエクスポート](cloud/export-portion-of-video-as-mp4.md)できます。
* 録画されたビデオのアイテム保持ポリシーを指定できます。この場合、サービスは、指定した日数よりも古いコンテンツを定期的にトリミングします。
* Azure Video Analyzer edge モジュールを使用して録画されたビデオには、[プレビュー イメージ](edge/enable-video-preview-images.md)またはサムネイルを定期的に含めることができ、閲覧エクスペリエンスが向上します。

## <a name="october-1-2021"></a>2021 年 10 月 1 日
オーストラリア東部で、Video Analyzer サービスが (プレビュー段階で) 使用できるようになりました。 利用可能な最新の情報については、[ここ](https://azure.microsoft.com/global-infrastructure/services/?products=video-analyzer&regions=all)を参照してください。

[Power BI 内に Azure Video Analyzer のウィジェットを埋め込む](embed-player-in-power-bi.md)方法を説明する新しい記事が追加されました。

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
<!--REDIRECT* Manage [pipeline topologies](pipeline.md#pipeline-topologies) remotely or locally using [direct method](direct-methods.md) calls-->

## <a name="next-steps"></a>次のステップ

[概要](overview.md)
