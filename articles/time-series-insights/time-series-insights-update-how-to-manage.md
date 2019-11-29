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
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: f66925c20f6767286eb98a7f5fab180845da4c33
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014793"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューのプロビジョニングと管理

このドキュメントでは、[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights プレビュー環境を作成し、管理する方法について説明します。

## <a name="overview"></a>概要

Azure Time Series Insights プレビュー環境は、従量課金制 (PAYG) の環境です。

Azure Time Series Insights プレビュー環境をプロビジョニングする際に、次のこれらの Azure リソースを作成します。

* Azure Time Series Insights Preview 環境  
* Azure Storage 汎用 v1 アカウント
* 高速で無制限のクエリを行うためのオプションのウォームストア
  
[環境を計画する方法](./time-series-insights-update-plan.md)に関するページをご覧ください。

Azure Time Series Insights プレビューの各環境を 1 つのイベント ソースに関連付けます。 詳細については、[イベント ハブ ソースの追加方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)に関するページと [IoT ハブ ソースの追加方法](./time-series-insights-how-to-add-an-event-source-iothub.md)に関するページを参照してください。 この手順では、タイムスタンプ ID プロパティと一意のコンシューマー グループを指定します。 この指定により、環境から適切なイベントへのアクセスが確実になります。

> [!NOTE]
> 前の手順は、Time Series Preview 環境を作成するときのプロビジョニング ワークフローでは省略可能です。 ただし、データがその環境でフローを開始できるように、イベント ソースを環境にアタッチする必要があります。

プロビジョニングが完了したら、ビジネスの要件に合わせ、アクセス ポリシーやその他の環境属性を変更できます。

## <a name="create-the-environment"></a>環境の作成

Azure Time Series Insights プレビューの環境を作成するには:

1. **[SKU]** メニューで **[PAYG]** ボタンを選択します。 環境名を入力し、使用するサブスクリプション グループとリソース グループを選択します。 次に、環境をホストするサポートされている場所を選択します。

   [![Azure Time Series Insights インスタンスを作成する。](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. タイム シリーズ ID を入力します。

    >[!NOTE]
    > * タイム シリーズ ID では大文字と小文字が区別され、変更できません。 (設定後は変更できません。)
    > * タイム シリーズ ID は最大 3 つのキーから構成されます。
    > * タイム シリーズ ID の選択の詳細については、[タイム シリーズ ID の選択](./time-series-insights-update-how-to-id.md)に関するページをご覧ください。

1. ストレージ アカウント名を選択し、レプリケーションを指定することで Azure ストレージ アカウントを作成します。 この作業で Azure Storage 汎用 v1 アカウントが作成されます。 アカウントは、先に選択した Azure Time Series Insights プレビュー環境と同じリージョンで作成されます。

    [![インスタンス用の Azure ストレージ アカウントを作成する](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(省略可能)** 環境内の最新のデータに対して高速かつ無制限のクエリが必要な場合は、環境に対してウォームストアを有効にします。 また、Time Series Insights プレビュー環境を作成した後、左側のナビゲーション ウィンドウの **[ストレージ構成]** オプションを使用して、ウォームストアを作成または削除することもできます。

1. **(省略可能)** ここでイベント ソースを追加できます。 または、インスタンスがプロビジョニングされるまで待機することもできます。

   * Time Series Insights では、イベント ソースのオプションとして [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) と [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) がサポートされています。 環境を作成するときはイベント ソースを 1 つしか追加できませんが、後でイベント ソースを追加できます。 イベント ソースを追加するときは、既存のコンシューマー グループを選択するか、新しいコンシューマー グループを作成できます。 すべてのイベントが Azure Time Series Insights プレビュー環境に確実に表示されるように、一意のコンシューマー グループを作成することをお勧めします。

   * 適切なタイムスタンプ プロパティを選択してください。 既定では、Azure Time Series Insights では、各イベント ソースに対して、メッセージが待ち行列に入った時刻が使用されます。

     > [!TIP]
     > メッセージが待ち行列に入った時刻は、バッチ イベント シナリオや履歴データをアップロードする場合、最適な構成設定ではないことがあります。 そのような場合、タイムスタンプ プロパティを使用するかどうかの判断を確認してください。

     [![[イベント ソース] タブ](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. 希望の設定で環境がプロビジョニングされていることを確認します。

    [![[確認と作成]](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox) タブ

## <a name="manage-the-environment"></a>環境の管理

Azure portal を使用して Azure Time Series Insights プレビュー環境を管理できます。 Azure portal を通じて管理する場合、従量課金制の Azure Time Series Insights プレビュー環境と一般に利用可能な S1 または S2 環境との間に、いくつかの重要な違いがあります。

* Azure portal の **[概要]** ブレードは次の点を除いて Azure Time Series Insights でも同じです。
  * 容量は、従量課金制環境には適用されないため、削除されます。
  * タイム シリーズ ID プロパティが追加されます。 これによってデータのパーティション方法が決定されます。
  * 参照データ セットが削除されています。
  * 表示された URL から [Azure Time Series Insights Preview エクスプローラー](./time-series-insights-update-explorer.md)に移動できます。
  * お使いの Azure ストレージ アカウント名が表示されます。

* Azure portal の **[構成]** ブレードは Azure Time Series Insights プレビューでは削除されています。PAYG 環境は構成できないためです。 ただし、 **[ストレージの構成]** を使用して、新しく導入されたウォームストアを構成できます。

* Azure portal の **[参照データ]** ブレードは Azure Time Series Insights プレビューで削除されています。参照データは従量課金制環境の構成要素ではないためです。

[![Azure portal での Time Series Insights プレビュー環境](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>次の手順

- Time Series Insights の一般に利用可能な環境とプレビュー環境の詳細については、「[環境を計画する](./time-series-insights-update-plan.md)」を参照してください。

- [イベント ハブ ソースを追加する](./time-series-insights-how-to-add-an-event-source-eventhub.md)方法を学習します。

- [IoT ハブ ソース](./time-series-insights-how-to-add-an-event-source-iothub.md)を構成します。
