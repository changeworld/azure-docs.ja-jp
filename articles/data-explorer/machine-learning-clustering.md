---
title: Azure Data Explorer の機械学習機能
description: Azure Data Explorer での根本原因分析には機械学習クラスタリングを使用します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769933"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Azure Data Explorer の機械学習機能

ビッグ データ分析プラットフォームである Azure Data Explorer では、組み込みの[異常検出と予測](/azure/data-explorer/anomaly-detection)機能を使用してサービスの正常性、QoS、または正常に機能していないデバイスの異常な動作を監視することができます。 異常なパターンが検出されると、異常を軽減または解決するために根本原因分析 (RCA) が実行されます。

診断プロセスは、複雑で時間がかかり、ドメインの専門家によって行われます。 このプロセスには、さまざまなソースからの同じ換算時間中の追加データの取り込みと結合、複数のディメンション上での値の分布の変化の検索、およびドメインに関する知識と直感に基づくその他の手法が含まれます。 これらの診断シナリオは Azure Data Explorer では一般的であることから、診断フェーズをより簡単にし、RCA の期間を短縮するための機械学習プラグインが提供されています。

Azure Data Explorer には、[`autocluster`](/azure/kusto/query/autoclusterplugin)、[`basket`](/azure/kusto/query/basketplugin)、および[`diffpatterns`](/azure/kusto/query/diffpatternsplugin) の 3 つ機械学習プラグインが用意されています。 すべてのプラグインで、クラスタリング アルゴリズムが実装されます。 `autocluster` プラグインと `basket` プラグインでは、単一のレコード セットがクラスター化され、`diffpatterns` プラグインでは 2 つのレコード セット間の差異がクラスター化されます。

## <a name="clustering-a-single-record-set"></a>単一レコード セットのクラスター化

一般的なシナリオには、異常な動作、高温状態のデバイスの測定値、長時間におよんでいるコマンド、および消費量の多いユーザーを示している時間ウィンドウなど、特定の条件によって選択されたデータ セットが含まれます。 データ内の共通するパターン (セグメント) を簡単かつ迅速に検索する方法を必要としています。 パターンとは、複数のディメンション (カテゴリ列) にわたって同じ値を共有するレコードを持つデータ セットのサブセットです。 次のクエリでは、サービスの例外の時系列が 1 週間にわたり 10 分間のビンで作成され表示されます。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![サービスの例外の時間グラフ](media/machine-learning-clustering/service-exceptions-timechart.png)

サービスの例外の数は、サービスの全体のトラフィックと関連しています。 月曜日から金曜日までの営業日の毎日のパターンがはっきりとわかります。正午にサービスの例外の数が増加し、夜間にはその数が減少しています。 週末には数は少なく横ばい状態になっています。 Azure Data Explorer 内の[時系列の異常検出](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection)を使用すれば、例外の急激な増加を検出できます。

データの急激な増加が 2 番目に発生しているのは火曜日の午後です。 このような急激な増加を詳しく診断するには、次のクエリを使用します。 このクエリを使用すると、急激な増加を示している付近のグラフがより高い解像度 (1 分間のビンで 8 時間) で再描画され、鋭角で急増しているかどうかや、その境界を確認することができます。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=) **\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![急激な増加の時間グラフに焦点を合わせる](media/machine-learning-clustering/focus-spike-timechart.png)

15 時 00 分から 15 時 02 分までの 2 分間という短い期間に急激な増加が発生したことがわかります。 次のクエリでは、この 2 分間の時間枠での例外の数がカウントされます。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

次のクエリでは、972 個の例外の中から 20 個をサンプリングします。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | リージョン | ScaleUnit | DeploymentId                     | Tracepoint | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>autocluster() を使用して単一レコード セットをクラスタ化する

例外の数が 1,000 未満であっても、各列には複数の値が含まれているため、共通するセグメントを見つけにくいことには変りありません。 [`autocluster()`](/azure/kusto/query/autoclusterplugin) を使用すれば、次のクエリに示されているように、共通するセグメントの小規模なリストを瞬時に抽出し、急激に増加した 2 分間において興味深いクラスターを検索することができます。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| セグメント ID | Count | Percent | リージョン | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

上記の結果からは、最も際立っているセグメントには例外レコードの合計の 65.74% が含まれていて、そのセグメントでは 4 つのディメンションが共有されていることがわかります。 次のセグメントの場合、共通するものはずっと少なく、全レコードの 9.67% しか含まれていません。3 つのディメンションが共有されています。 その他のセグメントもすべて、共通するものは少なくなっています。 

Autocluster では、複数のディメンションをマイニングして、興味深いセグメントを抽出するために独自のアルゴリズムが使用されています。 "興味深い" とは、各セグメントのレコード セットと機能セットの両方のカバレッジが重大であることを意味します。 セグメントも困難になった場合、それぞれが、他のユーザーから大幅に異なることを意味します。 これらのセグメントの 1 つまたは複数が、RCA プロセスに関連する可能性があります。 セグメントのレビューと評価を最小限に抑えるために、autocluster では小規模なセグメント リストのみが抽出されます。

### <a name="use-basket-for-single-record-set-clustering"></a>basket() を使用して単一レコード セットをクラスタ化する

次のクエリに示すように、[`basket()`](/azure/kusto/query/basketplugin) プラグインを使用することもできます。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| セグメント ID | Count | Percent | リージョン | ScaleUnit | DeploymentId | Tracepoint | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

basket では、項目セットのマイニングのため Apriori アルゴリズムが実装され、レコード セットのカバレッジがしきい値 (既定値は 5%) を超えているすべてのセグメントが抽出されます。 より多くのセグメントが抽出され、類似のセグメント (たとえば、セグメント 0、1 または 2、3) が含まれていることがわかります。

プラグインは両方とも強力で使いやすいのですが、いずれの場合も非監視モードの方法 (ラベルを使用しない) で単一のレコード セットがクラスター化されるため、大きな制限を伴います。 したがって、抽出されたパターンが、選択したレコード セット (異常なレコード) またはグローバルなレコード セットのいずれの特徴を示しているのかが明確ではありません。

## <a name="clustering-the-difference-between-two-records-sets"></a>2 つのレコード セットの差異をクラスター化する

[`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) プラグインでは、`autocluster` と `basket` における制限が克服されています。 `Diffpatterns` を使用すると、2 つのレコード セットを受け取り、それらの間で異なっている主要なセグメントを抽出できます。 一方のセットには、通常、調査中の異常なレコード セットが含まれます (`autocluster` と `basket` によって分析されているもの)。 もう一方のセットには、参照レコード セット (ベースライン) が含まれています。 

次のクエリでは、`diffpatterns` を使用して、ベースライン内のクラスターとは異なる、急激に増加した 2 分間での興味深いクラスターが検索されます。 15 時 00 分 (急激な増加が始まった時刻) より前の 8 分をベースライン ウィンドウとして定義します。 また、特定のレコードがベースラインまたは異常セットのどちらに属するかを指定するバイナリ列 (AB) によって拡張する必要があります。 `Diffpatterns` では監視下学習アルゴリズムが実装されます。ここで、異常対ベースライン フラグ (AB) によって 2 つのクラス ラベルは生成済みです。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| セグメント ID | CountA | CountB | PercentA | PercentB | PercentDiffAB | リージョン | ScaleUnit | DeploymentId | Tracepoint |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

最も際立っているセグメントは、`autocluster` によって抽出されたセグメントと同じです。2 分間の異常なウィンドウでのそのカバレッジは 65.74% です。 ただし、8 分間のベースライン ウィンドウでのそのカバレッジは 1.7% に過ぎません。 差異は 64.04% です。 この差異は、異常な急増に関連しているようです。 この想定を検証するには、次のクエリに示されるように、元のグラフを、この問題あるセグメントに属するレコードのグラフと他のセグメントに属するレコードのグラフに分割します。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

!['diffpattern' セグメントの時間グラフの検証](media/machine-learning-clustering/validating-diffpattern-timechart.png)

このグラフでは、火曜日の午後に発生した急激な増加は、`diffpatterns` を使用して検出されたこの特定のセグメントからの例外が原因であることを確認できます。

## <a name="summary"></a>まとめ

Azure Data Explorer の機械学習プラグインは多くのシナリオで役に立ちます。 `autocluster` および `basket` では、非監視モードの学習アルゴリズムが実装され、両方とも使いやすくなっています。 `Diffpatterns` では監視モードの学習アルゴリズムが実装されます。より複雑になりますが、RCA のためにセグメントの差異を抽出する際はより力を発揮します。

これらのプラグインは、アドホックのシナリオおよびリアルタイムに近い自動監視サービスにおいて、対話形式で使用されます。 Azure Data Explorer では、時系列の異常検の後に診断プロセスが続きます。このプロセスは必要なパフォーマンス基準を満たすように高度に最適化されます。
