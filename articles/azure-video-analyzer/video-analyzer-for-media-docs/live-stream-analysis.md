---
title: Azure Video Analyzer for Media (旧称 Video Indexer) を使用したライブ ストリーム分析
description: この記事では、Azure Video Analyzer for Media (旧称 Video Indexer) を使用してライブ ストリーム分析を実行する方法について説明します。
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 18017bbe4d83d11e360efebd7d69eed8d67ae15a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627446"
---
# <a name="live-stream-analysis-with-video-analyzer-for-media"></a>Video Analyzer for Media を使用したライブ ストリーム分析

Azure Video Analyzer for Media は、ビデオおよびオーディオ ファイルからオフラインで詳細な分析情報を抽出するように設計された Azure サービスです。 これは、事前に作成された特定のメディア ファイルを分析するためのものです。 ただし、一部のユース ケースでは、ライブ フィードからメディアの分析情報をできるだけ迅速に取得し、時間の余裕がない運用やその他のユース ケースを開放することが重要です。 たとえば、ライブ ストリームのこのようなリッチ メタデータは、コンテンツ プロデューサーがテレビ制作を自動化するために使用できます。

この記事で説明されているソリューションでは、顧客はほぼリアルタイムの解像度で、ライブ フィードに対して Video Analyzer for Media を使用できます。 このソリューションを使用したインデックス付けの遅延は、インデックスを付けるデータのチャンク、入力解像度、コンテンツの種類、およびこのプロセスで使用されるコンピューティング能力に応じて、4 分ほどの少なさになる場合があります。

![ライブ ストリームでの Video Analyzer for Media メタデータ](./media/live-stream-analysis/live-stream-analysis01.png)

"*図 1 - ライブ ストリームでの Video Analyzer for Media メタデータを表示するサンプル プレーヤー*"

手元にある[ストリーム分析ソリューション](https://aka.ms/livestreamanalysis)は、Azure Functions と 2 つの Logic Apps を使用して、Azure Media Services のライブ チャンネルからのライブ プログラムを Video Analyzer for Media で処理し、ほぼリアルタイムに結果のストリームを表示する Azure Media Player で結果を表示します。

高レベルでは、これは 2 つの主なステップで構成されています。 最初のステップは 60 秒ごとに実行され、再生された最後の 60 秒間のサブクリップを取得します。次にそれから資産を作成し、Video Analyzer for Media によってインデックスを付けます。 その後、インデックス付けが完了すると 2 番目のステップが呼び出されます。 キャプチャされた分析情報が処理され、Azure Cosmos DB に送信されて、インデックスが付けられたサブクリップは削除されます。

サンプル プレーヤーはライブ ストリームを再生し、専用の Azure 関数を使用して Azure Cosmos DB から分析情報を取得します。 そこには、ライブ ビデオと同期してメタデータとサムネイルが表示されます。

![クラウドで 1 分ごとにライブ ストリームを処理する 2 つのロジック アプリ](./media/live-stream-analysis/live-stream-analysis02.png)

*図 2 – クラウドで 1 分ごとにライブ ストリームを処理する 2 つのロジック アプリ。*

## <a name="step-by-step-guide"></a>ステップバイステップ ガイド 

完全なコードと、結果をデプロイするためのステップバイステップ ガイドは、[Video Analyzer for Media を使用したライブ メディア分析に関する GitHub プロジェクト](https://aka.ms/livestreamanalysis)にあります。 

## <a name="next-steps"></a>次の手順

[Video Analyzer for Media の概要](video-indexer-overview.md)
