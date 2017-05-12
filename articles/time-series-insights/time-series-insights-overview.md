---
title: "Azure Time Series Insights の概要 | Microsoft Docs"
description: "時系列のデータ分析と IoT ソリューション向けの新しいサービスである Azure Time Series Insight の概要"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.lasthandoff: 04/26/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Azure Time Series Insights の概要

Azure Time Series Insights は、ストレージ、分析、および視覚化コンポーネントを備えた完全管理型のクラウド サービスです。これらのコンポーネントにより、非常に簡単に、数十億ものイベントを並行して取り込み、保存、調査、分析することができます。 Time Series Insights では、データの全体像を確認でき、隠れた傾向や異常の検出、ほぼリアルタイムでの根本原因分析の実施が可能になるため、IoT ソリューションの検証をすばやく行い、ミッションクリティカルなデバイスの大きなダウンタイムを防ぐことができます。 Time Series Insights は、イベント ブローカー (例: IoT Hub や Event Hubs) から時系列のデータを取り込み、そのデータにインデックスを作成して、構成可能な保持ポリシーに基づいてデータを削除します。 ユーザーは、直観的な UX または REST クエリ API を介してそのデータをビジネス シナリオに使用します。

![Time Series Insights の概要](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>主なシナリオ

* 短時間での IoT ソリューションの検証と監視
* 大規模な IoT データの直観的な視覚化と分析
* 根本原因分析と異常検出の迅速化
* 複数のデバイス、プラント、データの全体像の作成

## <a name="key-capabilities-and-benefits"></a>主要な機能と利点

* **簡単に開始できる**: Azure Time Series Insights は、事前のデータ準備が必要なく、非常に高速なため、Azure IoT Hub またはイベント ハブに含まれる十数億ものイベントに数分で接続することができます。 接続した後、数秒でセンサー データを視覚化および操作して、迅速に IoT ソリューションを検証できます。 Time Series Insights は非常に直感的で使いやすいため、コードを 1 行も記述することなく、データを操作できます。  さらに、新しい言語を習得する必要もありません。Time Series Insights には、上級ユーザー向けの自由書式の詳細なクエリ画面のほか、ポイントしてクリックするだけの初心者向けの調査機能が備わっています。

* **ほぼリアルタイムでの迅速な洞察**: Time Series Insights のストレージ、分析、視覚化の各コンポーネントが 1 か所に集約されているため、時系列のデータからより多くの価値が得られます。 Time Series Insights では、1 日あたり数億件のセンサー イベントを取り込むことができます (待ち時間は 1 分)。そのため、変化に迅速に対応できます。 Time Series Insights により、傾向や異常にすばやく焦点を当てることでセンサー データに対する理解が深まるため、簡単に複雑な根本原因分析を実施し、コストのかかるダウンタイムを回避することができます。 リアルタイム データと履歴データ間の相互相関が実現するため、Time Series Insights を使用すると、ユーザーはデータに隠れていた傾向を発見することができます。

* **カスタム ソリューションの構築**: Azure Time Series Insights のデータを既存のアプリケーションに埋め込んだり、Time Series Insights REST API を使用して新しいカスタム ソリューションを作成したりすることができます。  さらに、カスタマイズされたビューの作成と共有は簡単なため、検出したデータを他のユーザーが簡単に探索できるようになります。

* **スケーラビリティ**: Time Series Insights は、IoT 規模をサポートするように設計されています。 プレビューでは、1 日あたり 100 万～ 1 億件のイベントを受信できます (既定の保有期間は 31 日間)。 Time Series Insights を使用すると、ユーザーは、大量の履歴データと共に、ほぼリアルタイムでライブ データ ストリームを視覚化して分析できます。 今後は、絶え間なく進化するエンタープライズ規模に合わせて受信レートと保有率が大きくなります。

## <a name="taxonomy-of-time-series-insights"></a>Time Series Insights の分類

* **環境**: 環境は、受信とストレージの容量を備えた Azure リソースです。  導入ユーザーは、Azure Portal を使用して、必要な容量を備えた環境をプロビジョニングします。
* **イベント ソース**: イベント ソースは、Azure Event Hubs のようなイベント ブローカーから派生しています。  Time Series Insights は直接イベント ソースに接続するため、ユーザーがコードを 1 行も記述する必要なく、データ ストリームを取り込みます。 現在、Time Series Insights は Azure Event Hubs と Azure IoT Hub をサポートしており、将来的にはさらにイベント ソースが追加される予定です。
* **参照データ**: Time Series Insights により、ユーザーは、時系列のデータを参照データと結合することができます。  参照データには、デバイスに関するメタデータや、比較的変更頻度の低いその他の静的データを含めることができます。 Time Series Insights では参照データがデータ ストリームと結合されるため、ユーザーはこのデータをほぼリアルタイムで視覚化して分析できます。

