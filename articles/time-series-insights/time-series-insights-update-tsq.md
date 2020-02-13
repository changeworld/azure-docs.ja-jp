---
title: データのクエリ (プレビュー) - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Preview のデータのクエリの概念と HTTP REST API の概要
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 898515f49672a19ed8bf1c62439128b6727afc73
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087411"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューのデータのクエリ

Azure Time Series Insights プレビューでは、パブリック サーフェス API を使用して、環境に格納されているイベントおよびメタデータのデータのクエリを実行できます。 これらの API は、[Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)でも使用されます。

Time Series Insights では、3 つの主要な API カテゴリを使用できます。

* **Environment API**:これらの API により、Time Series Insights 環境自体に対してクエリを実行できます。 クエリの例は、呼び出し元がアクセス権を持っている環境や環境のメタデータの一覧です。
* **Time Series Model-Query (TSM-Q) API**:タイム シリーズ モデルの環境部分に格納されているメタデータの作成、読み取り、更新、削除 (CRUD) 操作を実行できます。 たとえば、インスタンス、種類、階層などです。
* **Time Series Query (TSQ) API**:テレメトリまたはイベントのデータをソース プロバイダーからの記録と同時に取得したり、スカラーおよび集約関数によって格納される変数の部分を使用してデータを削減したりします。 これらの API は、時系列データの計算を変換、結合、適用する操作を実行できます。

Time Series Insights は、機能が豊富な文字列ベースの式言語である [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) を計算の記述に使用します。

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights プレビューのコア API

次のコア API がサポートされています。

[![Time Series Query の概要](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Environment API

次の Environment API を使用できます。

* [Environment API の取得](/rest/api/time-series-insights/management/environments/get):呼び出し元がアクセスを許可されている環境の一覧が返されます。
* [Environments Availability API の取得](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability):イベントのタイムスタンプ `$ts` に対するイベント数の分布が返されます。 この API は、イベントの数を返すことによって (ある場合) タイムスタンプにイベントが存在するかどうかを判断するのに役立ちます。
* [Get Event Schema API](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema):特定の検索範囲に対するイベント スキーマのメタデータが返されます。 この API は、特定の検索範囲に対してスキーマで使用できるすべてのメタデータとプロパティを取得するのに役立ちます。

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series Model-Query (TSM-Q) API

次の Time Series Model-Query API を使用できます。 これらの API のほとんどでバッチ実行操作がサポートされるため、複数の時系列モデル エンティティに対してバッチ CRUD 操作を実行できます。

* [Model Settings API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api):環境の既定の型とモデル名を *GET* および *PATCH* できます。
* [Types API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api):タイム シリーズの種類とそれに関連する変数に対して CRUD を実行できます。
* [Hierarchies API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api):タイム シリーズの階層とそれに関連するフィールド パスに対して CRUD を実行できます。
* [Instances API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api):タイム シリーズのインスタンスとそれに関連するインスタンス フィールドに対して CRUD を実行できます。 さらに、Instances API は次の操作もサポートしています。
  * [Search](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search):インスタンス属性に基づいて、時系列インスタンス検索のヒットの部分リストを取得します。
  * [Suggest](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest):インスタンス属性に基づいて、時系列インスタンス検索のヒットの部分リストを検索しサジェストします。

## <a name="time-series-query-tsq-apis"></a>Time Series Query (TSQ) API

次の Time Series Query API を使用できます。 これらの API は、サポートされている Time Series Insights のすべての多層ストレージで利用できます。 クエリ URL パラメーターは、クエリを実行する[ストアの種類](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)を指定するために使用されます。

* [Get Events API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents):ソース プロバイダーから Time Series insights に記録されているイベントからの Time Series Insights データのクエリと取得を実行できます。 この API を使用すると、特定の時系列 ID と検索範囲に対する未加工のイベントを取得できます。 この API は、改ページ位置の自動修正をサポートしており、選択された入力に対する完全なデータセットを取得できます。 

* [Get Series API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries):ネットワーク上で記録されたデータを使用してキャプチャされたイベントからの Time Series Insights データのクエリと取得を実行できます。 返される値は、モデルで定義されているかインラインで提供されている変数に基づきます。 この API は、改ページ位置の自動修正をサポートしており、選択された入力に対する完全なデータセットを取得できます。 この API は、計算されたプロパティまたは列を定義するのに役立ちます。

    >[!NOTE]
    > Aggregation 句は、モデルで指定されているかインラインで提供されていても無視されます。

  Get Series API では、各間隔の各変数に対するタイム シリーズの値が返されます。 時系列値は、Time Series Insights でクエリからの出力 JSON に使用される形式です。 返される値は、タイム シリーズ ID と提供された変数のセットに基づきます。

* [Aggregate Series API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable):記録されたデータをサンプリングおよび集計することによって、キャプチャされたイベントからの Time Series Insights データのクエリと取得を実行できます。 この API では、[継続トークン](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage)を使用することで、継続可能な実行がサポートされています。

  Aggregate Series API では、各間隔の各変数に対するタイム シリーズの値が返されます。 値は、タイム シリーズ ID と提供された変数のセットに基づきます。 Aggregate Series API では、タイム シリーズ モデルに格納されている変数またはインラインで提供された変数を使用してデータを集計またはサンプリングすることにより、削減が実現されます。

## <a name="next-steps"></a>次のステップ

- Azure Time Series Insights プレビューの[ストレージとイングレス](./time-series-insights-update-storage-ingress.md)の詳細を確認する。
- Time Series Insights プレビューでの[データ モデリング](./time-series-insights-update-tsm.md)に関する記事を読む。
- [時系列 ID の選択に関するベスト プラクティス](./time-series-insights-update-how-to-id.md)を確認する。
