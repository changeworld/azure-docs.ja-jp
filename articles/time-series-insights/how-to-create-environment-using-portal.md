---
title: Azure portal を使用して Gen2 の環境を設定する - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure portal を使用して Azure Time Series Insights Gen2 で環境を設定する方法について説明します。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 9ac665304e874c711306b0fae16d2970de7be6a9
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135467"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Azure portal を使用して Azure Time Series Insights Gen2 環境を作成する

このドキュメントでは、[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights Gen 2 環境を作成する方法について説明します。

環境のプロビジョニングに関するチュートリアルでは、そのプロセスについて説明します。 適切な時系列 ID を選択する方法を学習し、2 つの JSON ペイロードから例を表示します。</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>概要

Azure Time Series Insights Gen 2 環境をプロビジョニングする際に、次の Azure リソースを作成します。

* 従量課金制の価格モデルに従う Azure Time Series Insights Gen2 環境
* Azure ストレージ アカウント
* 高速で無制限のクエリを行うためのオプションのウォームストア

> [!TIP]
>
> * [環境を計画する方法](./how-to-plan-your-environment.md)に関するページをご覧ください。
> * [イベント ハブ ソースを追加する](./how-to-ingest-data-event-hub.md)方法、または [IoT ハブ ソースを追加する](./how-to-ingest-data-iot-hub.md)方法に関する記事を参照してください。

学習内容:

1. Azure Time Series Insights Gen 2 の各環境を 1 つのイベント ソースに関連付けます。 また、タイムスタンプ ID プロパティと一意のコンシューマー グループを指定し、環境が適切なイベントにアクセスできるようにします。

1. プロビジョニングが完了したら、ビジネス ニーズに合わせ、アクセス ポリシーやその他の環境属性を変更できます。

   > [!NOTE]
   > 最初の手順は、環境をプロビジョニングする時点では省略可能です。 この手順を省略した場合は、環境へのデータのフローを開始したり、クエリを使用してアクセスしたりできるように、後で環境にイベント ソースをアタッチする必要があります。

## <a name="create-the-environment"></a>環境の作成

Azure Time Series Insights Gen 2 環境を作成するには:

1. [Azure portal](https://portal.azure.com/)で、 *[モノのインターネット]* の下に Azure Time Series Insights のリソースを作成します。

1. **[階層]** として **[Gen2(L1)]** を選択します。 環境名を入力し、使用するサブスクリプション グループとリソース グループを選択します。 次に、環境をホストするサポートされている場所を選択します。

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Azure Time Series Insights インスタンスを作成する。" lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. タイム シリーズ ID を入力します。

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Azure Time Series Insights 環境構成を作成する (続き)" lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * タイム シリーズ ID では *大文字と小文字が区別され*、*変更できません*。 (設定後は変更できません。)
   > * タイム シリーズ ID は最大 *3 つ* のキーから構成されます。 この ID は、データベースの主キーと考えてください。これは、環境にデータを送信する各デバイス センサーを一意に表します。 1 つのプロパティ、または最大 3 つのプロパティの組み合わせにすることができます。
   > * 詳細は、[タイム シリーズ ID を選択する方法](./how-to-select-tsid.md)に関する記事を参照してください

1. ストレージ アカウント名とアカウントの種類を選択し、[レプリケーション](../storage/common/redundancy-migration.md?tabs=portal)の選択肢を指定して、Azure Storage アカウントを作成します。 これにより、Azure Storage アカウントが自動的に作成されます。 既定では、[汎用 v2](../storage/common/storage-account-overview.md) アカウントが作成されます。 アカウントは、先に選択した Azure Time Series Insights Gen2 環境と同じリージョンで作成されます。
または、新しい Azure Time Series Gen2 環境を作成するときに、[Azure Resource Manager テンプレート](./time-series-insights-manage-resources-using-azure-resource-manager-template.md)を使用して BYOS (Bring Your Own Storage) を行うこともできます。

1. **(省略可能)** 環境内の最新のデータに対して高速かつ無制限のクエリが必要な場合は、環境に対してウォームストアを有効にします。 また、Azure Time Series Insights Gen2 環境を作成した後、左側のナビゲーション ウィンドウの **[ストレージ構成]** オプションを使用して、ウォーム ストアを作成または削除することもできます。

1. **(省略可能)** ここでイベント ソースを追加できます。 インスタンスがプロビジョニングされるまで待機することもできます。

   * Azure Time Series Insights では、イベント ソースのオプションとして [Azure IoT Hub](./how-to-ingest-data-iot-hub.md) と [Azure Event Hubs](./how-to-ingest-data-event-hub.md) がサポートされています。 環境を作成するときはイベント ソースを 1 つしか追加できませんが、後でイベント ソースを追加できます。

     イベント ソースを追加するときは、既存のコンシューマー グループを選択するか、新しいコンシューマー グループを作成できます。 環境でデータが読み込まれるように、イベント ソースでは一意のコンシューマー グループが使用されるようにしてください。

   * イベント ソースからのデータの収集をいつ開始するかを選択します。 既定では、イベント ソースが作成された時点から収集を開始します。

     > [!TIP]
     > イベント ソースから既存のデータを収集するために **[すべてのデータ]** オプションを選択した場合、Azure Time Series Insights Gen2 環境によってすべてのデータが処理されるため、初期の待機時間が長くなる可能性があります。 この待機時間は、データのインデックスが作成されると最終的に短くなります。

   * 適切なタイムスタンプ プロパティを選択してください。 既定では、Azure Time Series Insights では、各イベント ソースに対して、メッセージが待ち行列に入った時刻が使用されます。

     > [!TIP]
     > メッセージが待ち行列に入った時刻は、バッチ イベント シナリオや履歴データをアップロードする場合、最適な構成設定ではないことがあります。 そのような場合、タイムスタンプ プロパティを使用するかどうかの判断を確認してください。

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="[イベント ソース] 構成タブ" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. **[確認と作成]** を選択して、環境がプロビジョニングされ、必要な方法で構成されていることを確認します。

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="[確認と作成] タブ" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>次のステップ

* Azure Time Series Insights の一般に利用可能な環境と Gen2 環境の詳細については、[環境の計画](./how-to-plan-your-environment.md)に関する記事を参照してください。
* Azure Time Series Insights Gen2 環境の[ストリーミング インジェストのイベント ソース](./concepts-streaming-ingestion-event-sources.md)をご覧ください。
* [環境を管理する方法](./how-to-provision-manage.md)の詳細を確認します。
