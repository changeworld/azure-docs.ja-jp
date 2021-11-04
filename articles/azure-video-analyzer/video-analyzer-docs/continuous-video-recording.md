---
title: エッジからの継続的なビデオ記録 - Azure Video Analyzer
description: 継続的なビデオ記録 (CVR) とは、ライブ ビデオ ソースから連続して記録するプロセスを指します。 このトピックでは、CVR について、また Azure Video Analyzer で CVR を使用する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d737536cca3e014b0412a00a99ce034f6d28e36
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556561"
---
# <a name="continuous-video-recording"></a>継続的なビデオ記録    

継続的なビデオ記録 (CVR) とは、ビデオ ソースからの継続的なビデオ記録プロセスのことを指します。 Azure Video Analyzer は、RTSP ソース ノードとビデオ シンク ノードで構成されるビデオ プロセスの[パイプライン トポロジー](pipeline.md)を介して、CCTV カメラから 24 時間 365 日ベースで連続してビデオを記録することをサポートしています。 このようなパイプラインをグラフィカルに表したものが次の図です。 トポロジの JSON 表現は、こちらの[ドキュメント](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)で確認できます。 このようなトポロジを使用して、任意の長さの記録 (数年分のコンテンツ) を作成できます。 録画のタイムスタンプは UTC で格納されます。  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="継続的なビデオ記録":::

上図のパイプライン トポロジのインスタンスは、Video Analyzer サービスのエッジ デバイス上で実行でき、ビデオ シンクは[ビデオ リソース](terminology.md#video)に記録されます。 パイプラインがアクティブ状態にある限り、ビデオは記録されます。 録画されたビデオは、Video Analyzer のストリーミング機能を使用して再生できます。 詳細については、[ビデオ レコーディングの再生](playback-recordings-how-to.md)に関するページを参照してください。

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

先に進む前に、次の記事を読むことをお勧めします。

* [パイプライン トポロジの概念](pipeline.md)
* [ビデオ記録の概念](video-recording.md) 
 
## <a name="resilient-recording"></a>回復性がある記録

Video Analyzer エッジ モジュールは、エッジ デバイスが時折クラウドとの接続を失ったり、使用可能な帯域幅が低下することがある状況での運用をサポートしています。 これを考慮して、ソースからのビデオはローカルでキャッシュに記録され、定期的にビデオ リソースと自動同期されます。 [パイプライン トポロジ](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)を調べると、次のプロパティが定義されていることがわかります。

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
```

後者の 2 つのプロパティは、回復性がある記録に関連しています (両方ともビデオ シンク ノードの必須プロパティです)。 `localMediaCachePath` プロパティは、ビデオ シンクに、そのフォルダー パスを使用して、クラウドにアップロードする前にメディア データをキャッシュするように指示しています。 [この](../../iot-edge/how-to-access-host-storage-from-module.md)記事を読むと、エッジ モジュールがデバイスのローカル ストレージをどのように利用できるかを理解することができます。 `localMediaCacheMaximumSizeMiB` プロパティは、ビデオ シンクがキャッシュとして使用できるディスク容量を定義しています (1 MiB = 1024 * 1024 バイト)。 

エッジ モジュールが長時間接続を失い、キャッシュ フォルダーに格納されているコンテンツが `localMediaCacheMaximumSizeMiB` 値に達すると、ビデオ シンクによって、キャッシュの最も古いデータからデータ破棄が開始されます。 たとえば、デバイスの接続が午前 10 時に失われ、キャッシュが午後 6 時に上限に達すると、ビデオ シンクによって、午前 10 時に記録されたデータの削除が開始されます。 

ネットワーク接続が復元されると、ビデオ シンクによて、キャッシュからのアップロードが、再び最も古いデータから開始されます。 上の例では、接続が復元された時刻 (たとえば午後 6 時 2 分) までにキャッシュから 5 分間のビデオを破棄する必要があったとすると、ビデオ シンクによって、午前 10 時 5 分のマークからアップロードが開始されます。

たとえば、何らかの理由でパイプラインを再起動した場合など、記録のギャップが発生することもあります。 また、パイプラインを停止して後で再起動することもできます。カメラの設定が変更されない限り、Video Analyzer の同じビデオ リソースに引き続き記録できます。

## <a name="segmented-recording"></a>セグメント化された記録  

上記の `segmentLength` プロパティを使用して、ビデオ リソースが記録されているストレージ アカウントへのデータ書き込みに伴う書き込みトランザクション コストを制御できます。 たとえば、この値を 30 秒から 5 分に増やした場合、ストレージ トランザクションの数は 10 分の 1 (5*60/30) に減少します。

プロパティ `segmentLength` は、`segmentLength` 秒ごとに最大 1 回、ビデオがストレージ アカウントに書き込まれることを保証します。 このプロパティの最小値は 30 秒 (既定値) で、30 秒刻みで最大 5 分まで増やすことができます。

このプロパティは、Video Analyzer エッジ モジュールと Video Analyzer サービスの両方に適用されます。 `segmentLength` が再生に与える影響については、[ビデオ レコーディングの再生](playback-recordings-how-to.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目

* [イベントベースのビデオ記録](event-based-video-recording-concept.md) 
* [ビデオ レコーディングの再生](playback-recordings-how-to.md) 

## <a name="next-steps"></a>次のステップ

[チュートリアル: 継続的なビデオ記録](edge/use-continuous-video-recording.md) 
