---
title: プレビュー環境のプロビジョニングと管理 - Azure Time Series | Microsoft Docs
description: Azure Time Series Insights プレビュー環境をプロビジョニングし、管理する方法について説明します。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/19/2019
ms.custom: seodec18
ms.openlocfilehash: b70604c62ae21f9c433b3cd7d9e59f4ccebb61bd
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861729"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューのプロビジョニングと管理

このドキュメントでは、[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights プレビュー環境を作成し、管理する方法について説明します。

## <a name="overview"></a>概要

Azure Time Series Insights プレビュー環境は、*従量課金制* (PAYG) の環境です。

Azure Time Series Insights プレビュー環境をプロビジョニングする際に、次のこれらの Azure リソースを作成します。

* Azure Time Series Insights Preview 環境  
* Azure Storage 汎用 v1 アカウント
* 高速で無制限のクエリを行うためのオプションのウォームストア

> [!TIP]
> * [環境を計画する方法](./time-series-insights-update-plan.md)に関するページをご覧ください。
> * [イベント ハブ ソースを追加する](./time-series-insights-how-to-add-an-event-source-eventhub.md)方法、または [IoT ハブ ソースを追加する](./time-series-insights-how-to-add-an-event-source-iothub.md)方法に関する記事を参照してください。

学習内容:

1. **(省略可能)** Azure Time Series Insights プレビューの各環境を 1 つのイベント ソースに関連付けます。 また、タイムスタンプ ID プロパティと一意のコンシューマー グループを指定し、環境が適切なイベントにアクセスできるようにします。

   > [!NOTE]
   > 前の手順は、環境をプロビジョニングする時点では省略可能です。 この手順を省略した場合は、後で環境にイベントソースをアタッチして、環境内でデータにアクセスできるようにする必要があります。

1. プロビジョニングが完了したら、ビジネスの要件に合わせ、アクセス ポリシーやその他の環境属性を変更できます。

## <a name="create-the-environment"></a>環境の作成

Azure Time Series Insights プレビューの環境を作成するには:

1. **階層**として **[PAYG]** を選択します。 環境名を入力し、使用するサブスクリプション グループとリソース グループを選択します。 次に、環境をホストするサポートされている場所を選択します。

   [![Azure Time Series Insights インスタンスを作成する。](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. タイム シリーズ ID を入力します。

    > [!NOTE]
    > * タイム シリーズ ID では*大文字と小文字が区別され*、*変更できません*。 (設定後は変更できません。)
    > * タイム シリーズ ID は最大 *3 つ*のキーから構成されます。
    > * 詳細は、[タイム シリーズ ID を選択する方法](time-series-insights-update-how-to-id.md)に関する記事を参照してください

1. ストレージ アカウント名を選択し、レプリケーションの選択肢を指定して、Azure Storage アカウントを作成します。 この作業で Azure Storage 汎用 v1 アカウントが作成されます。 アカウントは、先に選択した Azure Time Series Insights プレビュー環境と同じリージョンで作成されます。

    [![コールド ストレージの構成](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(省略可能)** 環境内の最新のデータに対して高速かつ無制限のクエリが必要な場合は、環境に対してウォームストアを有効にします。 また、Time Series Insights プレビュー環境を作成した後、左側のナビゲーション ウィンドウの **[ストレージ構成]** オプションを使用して、ウォームストアを作成または削除することもできます。

    [![ウォーム ストレージの構成](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(省略可能)** ここでイベント ソースを追加できます。 インスタンスがプロビジョニングされるまで待機することもできます。

   * Time Series Insights では、イベント ソースのオプションとして [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) と [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) がサポートされています。 環境を作成するときはイベント ソースを 1 つしか追加できませんが、後でイベント ソースを追加できます。 
   
     イベント ソースを追加するときは、既存のコンシューマー グループを選択するか、新しいコンシューマー グループを作成できます。 すべてのイベントが Azure Time Series Insights プレビュー環境に確実に表示されるように、一意のコンシューマー グループを作成することをお勧めします。

   * 適切なタイムスタンプ プロパティを選択してください。 既定では、Azure Time Series Insights では、各イベント ソースに対して、メッセージが待ち行列に入った時刻が使用されます。

     > [!TIP]
     > メッセージが待ち行列に入った時刻は、バッチ イベント シナリオや履歴データをアップロードする場合、最適な構成設定ではないことがあります。 そのような場合、タイムスタンプ プロパティを使用するかどうかの判断を確認してください。

     [![[イベント ソース] 構成タブ](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 環境がプロビジョニングされ、必要な方法で構成されていることを確認します。

    [![[確認と作成]](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox) タブ

## <a name="manage-the-environment"></a>環境の管理

Azure portal を使用して Azure Time Series Insights プレビュー環境を管理できます。 Azure portal を使用して環境を管理する場合、PAYG の Azure Time Series Insights プレビュー環境と、一般に利用可能な S1 または S2 環境との間には、注意すべき重要な違いがいくつかあります。

* Azure portal プレビューの **[概要]** ブレードには、次の変更点があります。

  * 容量は、PAYG 環境には適用されないため、削除されます。
  * **タイム シリーズ ID** プロパティが追加されます。 これによってデータのパーティション方法が決定されます。
  * 参照データ セットが削除されています。
  * 表示された URL から [Azure Time Series Insights Preview エクスプローラー](./time-series-insights-update-explorer.md)に移動できます。
  * お使いの Azure Storage アカウント名が表示されます。

* Azure portal の **[構成]** ブレードは Azure Time Series Insights プレビューでは削除されています。PAYG 環境は構成できないためです。 ただし、 **[ストレージの構成]** を使用して、新しく導入されたウォームストアを構成できます。

* 参照データは PAYG 環境の構成要素ではないため、Azure portal の **[参照データ]** ブレードは Azure Time Series Insights プレビューでは削除されています。

[![Azure portal での Time Series Insights プレビュー環境](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>次のステップ

- Time Series Insights の一般に利用可能な環境とプレビュー環境の詳細については、「[環境を計画する](./time-series-insights-update-plan.md)」を参照してください。

- [イベント ハブ ソースを追加する](./time-series-insights-how-to-add-an-event-source-eventhub.md)方法を学習します。

- [IoT ハブ ソース](./time-series-insights-how-to-add-an-event-source-iothub.md)を構成します。
