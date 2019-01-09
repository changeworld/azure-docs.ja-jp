---
title: Azure Time Series Preview 管理 - Azure Time Series Preview をプロビジョニングし、管理する方法 | Microsoft Docs
description: Azure Time Series Insights Preview をプロビジョニングし、管理する方法について
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273515"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights Preview をプロビジョニングし、管理する方法

このドキュメントでは、[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights Preview 環境を作成し、管理する方法について説明します。

## <a name="overview"></a>概要

Azure Time Series Insights Preview 環境をプロビジョニングする方法を短くまとめると次のようになります。

* Azure Time Series Insights Preview 環境は **PAYG** 環境です。
  * 作成プロセスの一環として、タイム シリーズ ID を指定する必要があります。 タイム シリーズ ID は最大 3 つのキーから構成されます。 タイム シリーズ ID の選択については、[タイム シリーズ ID の選択方法](./time-series-insights-update-how-to-id.md)に関するページをご覧ください。
  * Azure Time Series Insights Preview 環境をプロビジョニングする際に、次の 2 つの Azure リソースを作成します。

    * Azure Time Series Insights Preview 環境  
    * Azure Storage 汎用 V1 アカウント
  
    [環境を計画する方法](./time-series-insights-update-plan.md)に関するページをご覧ください。

    >[!IMPORTANT]
    > V2 アカウントをご利用の場合、使用するストレージ アカウントでコールド/アーカイブのプロパティを有効にしないでください。

* Azure Time Series Insights Preview の各環境を 1 つのイベント ソースに (任意で) 関連付けることができます。 詳細については、[イベント ハブ ソースの追加方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)に関するページと [IoT ハブ ソースの追加方法](./time-series-insights-how-to-add-an-event-source-iothub.md)に関するページを参照してください。
  * この手順では、タイムスタンプ ID プロパティと一意のコンシューマー グループを指定します。 この指定により、適切なイベントに環境はアクセスできます。

* プロビジョニングが完了したら、ビジネスの要件に合わせ、アクセス ポリシーやその他の環境属性を変更できます。

## <a name="new-environment-creation"></a>新しい環境の作成

次の手順は、Azure Time Series Insights Preview 環境の作成方法です。

1. **[SKU]** メニューで **[PAYG]** ボタンを選択します。 環境名、サブスクリプション グループ、使用するリソース グループを指定します。 次に、環境をホストする場所を選択します (サポートされている場所を選択します)。

1. タイム シリーズ ID を入力します。

    >[!NOTE]
    > * タイム シリーズ ID は大文字と小文字が区別され、不変です (設定した後では変更できません)。
    > * タイム シリーズ ID は最大 3 つのキーから構成されます。
    > * タイム シリーズ ID の選択については、[タイム シリーズ ID の選択方法](./time-series-insights-update-how-to-id.md)に関するページをご覧ください。

1. Azure Storage アカウント名を選択し、レプリケーションを指定することで Azure Storage アカウントを作成します。 この作業で Azure Storage 汎用 V1 アカウントが作成されます。 先に選択した Azure Time Series Insights Preview 環境と同じリージョンで作成されます。

    ![Azure Time Series Insights インスタンスを作成します。][1]

1. 任意で、イベント ソースを追加できます。

   * Time Series Insights では、オプションとして [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) と [Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) がサポートされています。 環境作成時はイベント ソースを 1 つだけ追加できますが、後で追加できます。 すべてのイベントが Azure Time Series Insights Preview インスタンスに表示されるように、一意のコンシューマー グループを作成することをお勧めします。 イベント ソースを追加するときは、既存のコンシューマー グループを選択するか、新しいコンシューマー グループを作成できます。

   * 適切なタイムスタンプ プロパティも指定してください。 既定では、Azure Time Series Insights では、各イベント ソースに対して、メッセージが待ち行列に入った時刻が使用されます。

     > [!TIP]
     > メッセージが待ち行列に入った時刻は、イベント バッチや履歴データをアップロードする場合、最適な構成設定ではないことがあります。 そのような場合、タイムスタンプ プロパティを使用するかどうかの判断を確認してください。

    ![インスタンスにイベント ソースを追加します。][2]

1. [Review and create] (確認および作成)

    ![インスタンスにイベント ソースを追加します。][3]

希望の設定で環境がプロビジョニングされていることを確認します。

## <a name="management"></a>管理

Azure portal を使用して Azure Time Series Insights Preview 環境を管理できます。 Azure portal を使用した **PAYG** Azure Time Series Insights Preview 環境の管理は、S1 または S2 環境と比較すると、次のような大きな違いがあります。

* Azure portal の *[概要]* ブレードは次の点を除いて Azure Time Series Insights でも同じです。
  * 容量という概念は **PAYG** 環境にないため、容量は削除されています。
  * タイム シリーズ ID プロパティが追加されています。 これによってデータのパーティション方法が決定されます。
  * 参照データ セットが削除されています。
  * 表示された URL から [Azure Time Series Insights Preview エクスプローラー](./time-series-insights-update-explorer.md)に移動できます。
  * お使いの Azure Storage アカウント名が表示されます。

* Azure Portal の *[構成]* ブレードは Azure Time Series Insights Preview では削除されています。**PAYG** 環境は構成できないためです。

* Azure portal の *[参照データ]* ブレードは Azure Time Series Insights Preview で削除されています。参照データは **PAYG** 環境の構成要素ではないためです。

![インスタンスを確認します。][4]

## <a name="next-steps"></a>次の手順

[環境を計画する方法](./time-series-insights-update-plan.md)に関するページをご覧ください。

[イベント ハブ ソースを追加する方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)に関するページをご覧ください。

[IoT ハブ ソースを追加する方法](./time-series-insights-how-to-add-an-event-source-iothub.md)に関するページをご覧ください。

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
