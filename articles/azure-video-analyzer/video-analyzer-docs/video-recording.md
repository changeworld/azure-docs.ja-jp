---
title: Azure Video Analyzer で再生するためのビデオを記録する
description: この記事では、Azure Video Analyzer で再生するためのビデオの記録について説明します。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 4c5d0121df90791d8eb77782fb6d949548a0185e
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604358"
---
# <a name="record-video-for-playback"></a>再生用のビデオの記録

CCTV カメラ用のビデオ管理システムのコンテキストでは、ビデオ記録とは、カメラからビデオをキャプチャし、モバイルおよびブラウザー アプリで後で表示するためにそれを記録するプロセスのことです。 ビデオ記録は、連続ビデオ記録とイベントベースのビデオ記録に分類できます。

## <a name="continuous-video-recording"></a>継続的なビデオ記録

継続的なビデオ記録 (CVR) とは、ビデオ ソースからキャプチャされるすべてのビデオを継続的に記録するプロセスのことを言います。 CVR を使用すると、ビデオの目的の部分 ( **[記録ポリシー](#recording-policy)** で規定) を後で確実に分析または監査できます。


## <a name="event-based-video-recording"></a>イベントベースのビデオ記録

イベントベースのビデオ記録 (EVR) とは、イベントによってトリガーされるビデオ記録のプロセスのことです。 対象となるイベントは、ビデオ信号自体の処理が原因で発生したもの (動きの検出など) でも、独立したソースからのもの (たとえば、ドア センサーからの) でもかまいません。 EVR によってストレージが節約される場合がありますが、これにはイベントを生成してビデオ記録をトリガーする追加のコンポーネントが必要です。 つまり、EVR では、ビデオ記録をトリガーするイベントとそのビデオ記録の期間について、追加の決定を行う必要があります。 例としては、次のようなものが考えられます。たとえば、イベント時刻の 30 秒前からビデオを 2 分間記録します。

## <a name="choosing-recording-modes"></a>記録モードの選択

CVR と EVR のどちらを使用するかは、ビジネス目標によって異なります。 Azure Video Analyzer (AVA) には、CVR と EVR の両方に対応するプラットフォーム機能が用意されています。 詳細については、 **[CVR](continuous-video-recording.md)** と **[EVR](event-based-video-recording-concept.md)** のシナリオに関する記事をご覧ください。

## <a name="recording-policy"></a>記録ポリシー

記録ポリシーとは、保存されるビデオ記録の長さまたは期間を規定したポリシーを指します。 記録ポリシーを使用すると、ストレージ コストとビジネス要件のバランスを取ることができます。 CVR の場合、記録ポリシーは、ビデオを保存する日数を定義します (たとえば、過去 7 日間)。 詳細については、 **[CVR](continuous-video-recording.md)** のシナリオに関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

- [EVR のシナリオについて確認する](event-based-video-recording-concept.md)
- [CVR のシナリオについて確認する](continuous-video-recording.md)
