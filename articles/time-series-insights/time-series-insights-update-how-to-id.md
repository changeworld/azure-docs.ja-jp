---
title: 時系列 ID の選択のベスト プラクティス - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights プレビューで時系列 ID を選択する場合のベスト プラクティスについて説明します。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: cf826c47c61e3714a05dd81fe3eea4e6ee0b03f4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012505"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>タイム シリーズ ID の選択に関するベスト プラクティス

この記事では、Azure Time Series Insights プレビュー環境での時系列 ID の重要性と、それを選択するためのベスト プラクティスをまとめています。

## <a name="choose-a-time-series-id"></a>タイム シリーズ ID の選択

タイム シリーズ ID の選択は、データベースでのパーティション キーの選択のようなものです。 これは、Time Series Insights プレビュー環境を作成しているときに選択されている必要があります。 これは*不変*プロパティです。 つまり、時系列 ID を持つ Time Series Insights プレビュー環境を作成した後は、その環境に合わせて変更することはできません。 

> [!IMPORTANT]
> 時系列 ID では大文字と小文字が区別されます。

適切な時系列 ID を選択することが重要です。 ここに、実行可能なベスト プラクティスをいくつか示します。

* 多数 (たとえば数百から数千) の個別の値を備えたパーティション キーを選択します。 多くの場合、これは JSON のデバイス ID、センサー ID、またはタグ ID である可能性があります。
* タイム シリーズ ID は、[タイム シリーズ モデル](./time-series-insights-update-tsm.md)のリーフ ノード レベルで一意である必要があります。
* イベント ソースが IoT ハブの場合、時系列 ID は、通常、*iothub-connection-device-id* になります。
* 時系列 ID のプロパティ名文字列の文字制限は、128 です。 時系列 ID のプロパティ値の場合、文字数の制限は 1024 です。
* 時系列 ID の一意のプロパティ値がない場合は、null 値として扱われ、一意性制約と同じルールに従います。
* 時系列 ID として最大 *3* 個のキー プロパティも選択できます。 これらの組み合わせは、時系列 ID を表す複合キーになります。  

  > [!NOTE]
  > 3 個のキー プロパティは文字列である必要があります。
  > 一度に 1 つのプロパティではなく、この複合キーに対してクエリを実行する必要があります。

次のシナリオでは、時系列 ID として複数のキー プロパティを選択する場合について説明します。  

### <a name="example-1-time-series-id-with-a-unique-key"></a>例 1:一意のキーを持つ時系列 ID

* 従来の保有機材の資産があります。 それぞれには一意のキーがあります。
* 1 つの車両は、プロパティ **deviceId** によって一意に識別されます。 別の車両では、一意のプロパティは **objectId** です。 どの車両にも、他の車両の一意プロパティは含まれません。 この例では、一意キーとして 2 つのキー **deviceId** と **objectId** を選択します。
* null 値が受け付けられ、イベント ペイロードにプロパティが存在しない場合は null 値としてカウントされます。 またこれは、2 つのイベント ソースにデータを送信し、各イベント ソースのデータが一意の時系列 ID を持っている場合の処理にも適した方法です。

### <a name="example-2-time-series-id-with-a-composite-key"></a>例 2:複合キーを持つ時系列 ID

* 資産の同じフリート内で複数のプロパティが一意である必要があります。 
* あなたはスマート ビルディングのメーカーで、すべての部屋にセンサーをデプロイしています。 通常、各部屋の **sensorId** の値は同じです。 例として、**sensor1**、**sensor2**、および **sensor3** となります。
* 建物には、プロパティ **flrRm** のサイト間で、重複しているフロア番号と部屋番号があります。 これらの数値には、**1a**、**2b**、**3a** などの値があります。
* プロパティ **location** には、**Redmond**、**Barcelona**、**Tokyo** などの値が含まれています。 一意性を作成するには、時系列 ID のキーとして、3 つのプロパティ **sensorId**、**flrRm**、**location** を指定します。

未加工イベントの例:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Azure portal では、次のようにこの複合キーを入力できます。 

`[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]`

## <a name="next-steps"></a>次の手順

* [データ モデリング](./time-series-insights-update-tsm.md)の詳細を確認します。

* [Azure Time Series Insights プレビューの環境](./time-series-insights-update-plan.md)を計画します。