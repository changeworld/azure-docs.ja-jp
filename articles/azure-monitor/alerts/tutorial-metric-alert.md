---
title: チュートリアル - Azure リソースのメトリック アラートを作成する
description: Azure メトリックス エクスプローラーを使用し、メトリック グラフを作成する方法について説明します。
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 431ef4418741a0406eeb4d350879dbc1be7ae737
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349875"
---
# <a name="tutorial-create-a-metric-alert-for-an-azure-resource"></a>チュートリアル: Azure リソースのメトリック アラートを作成する
Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 メトリック アラート ルールは、Azure リソースからのメトリック値がしきい値を超えた場合にアラートを生成します。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * メトリックス エクスプローラーからメトリック アラート ルールを作成する
> * アラートのしきい値を設定する
> * 通知の詳細を定義するアクション グループを作成する

## <a name="prerequisites"></a>必須コンポーネント
このチュートリアルを完了するには、以下が必要です。 

- 監視する Azure リソース。 メトリックをサポートする Azure サブスクリプションの任意のリソースを使用できます。 リソースがメトリックをサポートしているかどうかを判断するには、Azure portal のメニューにアクセスし、メニューの **[監視]** セクションに **[メトリック]** オプションがあることを確認します。
- メトリックス エクスプローラー内の、アラートを作成する 1 つまたは複数のメトリックを含むグラフ。 「[チュートリアル: Azure リソースのメトリックを分析する](../essentials/tutorial-metrics.md)」を完了します。

## <a name="create-new-alert-rule"></a>新しいアラート ルールの作成
メトリックス エクスプローラーで、 **[新しいアラート ルール]** をクリックします。 メトリックス エクスプローラーで選択したターゲット オブジェクトとメトリックを使用して、ルールが事前構成されます。

:::image type="content" source="media/tutorial-metric-alert/new-alert-rule.png" alt-text="新しいアラート ルール" lightbox="media/tutorial-metric-alert/new-alert-rule.png":::

## <a name="configure-alert-logic"></a>アラート ロジックの構成
リソースは既に選択されています。 アラート ルールのしきい値とその他の詳細を指定するには、シグナル ロジックを変更する必要があります。 

これらの設定を表示するには、 **[条件名]** をクリックします。 

:::image type="content" source="./media/tutorial-metric-alert/configuration.png" lightbox="./media/tutorial-metric-alert/configuration.png" alt-text="アラート ルールの構成":::

グラフには、選択したシグナルの値の経時変化が表示されるため、アラートがいつ発生することになるかを確認できます。 このグラフは、シグナル ロジックを指定すると更新されます。

:::image type="content" source="./media/tutorial-metric-alert/signal-logic.png" lightbox="./media/tutorial-metric-alert/signal-logic.png" alt-text="アラート ルールのシグナル ロジック":::

**アラート ロジック** は、条件と評価する時間によって定義されます。 この条件が true の場合、アラートが発生します。 アラート ルールの **[しきい値]** を指定し、 **[演算子]** と **[集計の種類]** を変更して必要なロジックを定義します。

:::image type="content" source="./media/tutorial-metric-alert/alert-logic.png" lightbox="./media/tutorial-metric-alert/alert-logic.png" alt-text="アラート ルールのアラート ロジック":::

既定の時間粒度をそのまま使用できますし、要件に合わせて変更することもできます。 **[評価の頻度]** は、アラート ロジックを評価する頻度を定義します。 **[集約粒度]** は、収集された値を集約する時間間隔を定義します。

シグナル ロジックの構成が終わったら、 **[完了]** をクリックします。

## <a name="configure-actions"></a>アクションを構成する
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>詳細を構成する
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="./media/tutorial-metric-alert/alert-details.png" lightbox="./media/tutorial-metric-alert/alert-details.png" alt-text="アラート ルールの詳細":::


**[アラート ルールの作成]** をクリックして、アラート ルールを作成します。


## <a name="view-the-alert"></a>アラートを表示する
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>次のステップ
Azure リソースのメトリック アラートを作成する方法を学習できましたので、次のチュートリアルのいずれかを使用してログ データを収集します。

> [!div class="nextstepaction"]
> [Azure リソースからリソース ログを収集する](../essentials/tutorial-resource-logs.md)
> [!div class="nextstepaction"]
> [Azure 仮想マシンからゲストのログとメトリックを収集する](../vm/tutorial-monitor-vm-guest.md)