---
title: Metrics Advisor に関してよく寄せられる質問
titleSuffix: Azure Cognitive Services
description: Metrics Advisor サービスに関してよく寄せられる質問です。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 0fde9a0f46073a2f3a24962ea58431581455f474
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931270"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Metrics Advisor に関してよく寄せられる質問

### <a name="what-is-the-cost-of-my-instance"></a>インスタンスのコストはどのくらいですか?

現在プレビュー期間中であり、インスタンスの使用にコストはかかりません。

### <a name="why-is-the-demo-website-readonly"></a>デモ Web サイトが読み取り専用なのはなぜですか?

[デモ Web サイト](https://anomaly-detector.azurewebsites.net/)は一般公開されています。 データが誤ってアップロードされるのを防ぐために、このインスタンスは読み取り専用となっています。

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>リソースを作成できないのはなぜですか? "価格レベル" を表示することができず、"このサブスクリプションに対して 1 件の S0 を既に作成していますか?" と表示されます。

:::image type="content" source="media/pricing.png" alt-text="F0 リソースが既に存在する場合のメッセージ":::

パブリック プレビュー中は、1 つのリージョンで、Metrics Advisor のインスタンスをサブスクリプションに 1 つだけ作成できます。

同じリージョンで、同じサブスクリプションを使用して、既にインスタンスが作成されている場合は、別のリージョンまたは別のサブスクリプションで、新しいインスタンスの作成を試みることができます。 また、既存のインスタンスを削除して、新しいインスタンスを作成することもできます。

既存のインスタンスを既に削除してあるのにエラーが表示される場合は、リソースを削除して約 20 分待ってから、新しいインスタンスを作成してください。

## <a name="basic-concepts"></a>基本的な概念

### <a name="what-is-multi-dimensional-time-series-data"></a>多次元時系列データとは何ですか?

用語集で、[多次元メトリック](glossary.md#multi-dimensional-metric)の定義を参照してください。

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Metrics Advisor で異常検出を開始するために必要なデータ量はどのくらいですか?

データ ポイントが 1 つ以上あれば、異常検出をトリガーできます。 ただし、これによって最適な精度が得られるわけではありません。 このサービスでは、データ フィードの作成時に "fill-gap" ルールとして指定した値を使用する、以前のデータ ポイントのウィンドウがあるものと想定されています。

検出対象のタイムスタンプの前にデータがあるようにすることをお勧めします。
データの細分性に基づく、推奨されるデータ量は次のようにさまざまです。

| 粒度 | 検出に推奨されるデータ量 |
| ----------- | ------------------------------------- |
| 5 分未満 | 4 日分のデータ |
| 5 分から 1 日 | 28 日分のデータ |
| 1 日分より多く、31 日分まで | 4 年分のデータ |
| 31 日分より多い | 48 年分のデータ |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Metrics Advisor で履歴データからの異常検出が行われないのはなぜですか?

Metrics Advisor は、ライブ ストリーミング データの検出を目的に設計されています。 このサービスによって検索および異常検出が実行される履歴データの最大長には、制限があります。 これは、特定の最も古いタイムスタンプより後のデータ ポイントに対してのみ、異常検出の結果が示されることを意味します。 最も古いタイムスタンプは、データの細分性によって異なります。

データの細分性に基づく、異常検出の結果が含まれる履歴データの長さは次のとおりです。

| 粒度 | 異常検出の対象となる履歴データの最大長 |
| ----------- | ------------------------------------- |
| 5 分未満 | オンボード時間 - 13 時間 |
| 5 分から 1 時間未満 | オンボード時間 - 4 日間  |
| 1 時間から 1 日未満 | オンボード時間 - 14 日間  |
| 1 日 | オンボード時間 - 28 日間  |
| 1 日より長く、31 日間未満 | オンボード時間 - 2 年間  |
| 31 日分より多い | オンボード時間 - 24 年間   |

### <a name="more-concepts-and-technical-terms"></a>その他の概念と技術用語

詳細については、[用語集](glossary.md)を参照してください。

## <a name="how-do-i-detect-such-kinds-of-anomalies"></a>そのような異常を検出するには、どうすればよいですか? 

### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>急増と急減を異常として検出するには、どうすればよいですか?

ハードしきい値が事前に定義されている場合は、[[Anomaly Detection Configurations]\(異常検出の構成\)](how-tos/configure-metrics.md#anomaly-detection-methods) で "ハードしきい値" を手動で設定できます。
しきい値がない場合は、AI を利用した "スマート検出" を使用できます。 詳細については、[検出構成の調整](how-tos/configure-metrics.md#tune-the-detecting-configuration)に関する記事を参照してください。

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>通常 (季節) のパターンへの非準拠を異常として検出するには、どうすればよいですか?

"スマート検出" には、季節のパターンなど、データのパターンを学習する機能があります。 学習すると、通常のパターンに準拠していない、そのようなデータ ポイントを、異常として検出します。 詳細については、[検出構成の調整](how-tos/configure-metrics.md#tune-the-detecting-configuration)に関する記事を参照してください。

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>直線を異常として検出するには、どうすればよいですか?

データが通常は非常に不安定で変動が大きく、非常に安定したり直線になってしまったりするときには警告を表示したい場合、変化が非常にわずかであれば、そのようなデータ ポイントが検出されるように "しきい値の変更" を構成することができます。
詳細については、[異常検出の構成](how-tos/configure-metrics.md#anomaly-detection-methods)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
- [Metrics Advisor の概要](overview.md)
- [デモ サイトを試す](quickstarts/explore-demo.md)
- [Web ポータルを使用する](quickstarts/web-portal.md)