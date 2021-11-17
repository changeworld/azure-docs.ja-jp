---
title: チュートリアル - Azure リソースのログ クエリ アラートを作成する
description: Azure リソースのログ クエリ アラートを作成するチュートリアルです。
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 09/16/2021
ms.openlocfilehash: 526abdb9a5e3c16fe58551c2aa8784caf329d1c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350163"
---
# <a name="tutorial-create-a-log-query-alert-for-an-azure-resource"></a>チュートリアル:Azure リソースのログ クエリ アラートを作成する
Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 ログ クエリ アラート ルールは、ログ クエリが特定の結果を返す場合にアラートを作成します。 たとえば、仮想マシンで特定のイベントが作成された場合にアラートを受信したり、ストレージ アカウントに対して過剰な数の匿名要求が行われた場合に警告を送信したりします。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * さまざまな種類のリソースのアラート ルールをサポートするように設計された事前構築済みのログ クエリにアクセスする
> * ログ クエリ アラート ルールを作成する
> * 通知の詳細を定義するアクション グループを作成する


## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するには、以下が必要です。 

- 監視する Azure リソース。 診断設定がサポートされている、Azure サブスクリプション内の任意のリソースを使用できます。 リソースで診断設定がサポートされているかどうかを調べるには、Azure portal でリソースのメニューの **[監視]** セクションに **[診断設定]** オプションがあることを確認します。


仮想マシン以外の Azure リソースを使用している場合は、以下が必要です。

- Azure リソースから Log Analytics ワークスペースにリソース ログを送信する診断設定。 [チュートリアル: Azure Monitor での Log Analytics ワークスペースの作成](../essentials/tutorial-resource-logs.md)に関するページをご覧ください。

Azure 仮想マシンを使用している場合は、以下が必要です。

- ゲストのログとメトリックを Log Analytics ワークスペースに送信するデータ収集ルール。 「[チュートリアル: Azure 仮想マシンからゲストのログとメトリックを収集する](../vm/tutorial-monitor-vm-guest.md)」をご覧ください。

   
 
 ## <a name="select-a-log-query-and-verify-results"></a>ログ クエリを選択し、結果を確認する
Log Analytics ワークスペースからデータを取得するには、Kusto クエリ言語 (KQL) で記述したログ クエリを使用します。 Azure Monitor の分析情報とソリューションには、特定のサービスのデータを取得するためのログ クエリが用意されていますが、Azure portal では Log Analytics を使用してログ クエリとその結果を直接処理することができます。 

リソースのメニューから **[ログ]** を選択します。 Log Analytics が **[クエリ]** ウィンドウとともに開き、 **[リソースの種類]** に対する事前構築済みのクエリが表示されます。 **[アラート]** を選択して、アラート ルール専用に設計されたクエリを表示します。

> [!NOTE]
> **[クエリ]** ウィンドウが開かない場合は、右上にある **[クエリ]** をクリックします。 

:::image type="content" source="media/tutorial-log-alert/queries.png" lightbox="media/tutorial-log-alert/queries.png"alt-text="[クエリ] ウィンドウが開かれた Log Analytics":::

クエリを選択し、 **[実行]** をクリックしてクエリ エディターに読み込み、結果を返します。 クエリを変更して、もう一度実行することもできます。 たとえば、ストレージ アカウントに対する **匿名要求の表示** クエリが次に示されています。 **[AuthenticationType]** を変更したり、別の列でフィルター処理したりできます。

:::image type="content" source="media/tutorial-log-alert/query-results.png" lightbox="media/tutorial-log-alert/query-results.png"alt-text="クエリの結果":::


## <a name="create-alert-rule"></a>アラート ルールの作成
クエリを確認したら、アラート ルールを作成できます。 **[新しいアラート ルール]** を選択し、現在のログ クエリに基づいて新しいアラート ルールを作成します。 **[スコープ]** は既に現在のリソースに設定されています。 この値を変更する必要はありません。

:::image type="content" source="media/tutorial-log-alert/create-alert-rule.png" lightbox="media/tutorial-log-alert/create-alert-rule.png"alt-text="アラート ルールの作成":::
## <a name="configure-condition"></a>条件の構成

**[条件]** タブでは、 **[Log query]\(ログ クエリ\)** は既に入力されています。 **[測定]** セクションでは、ログ クエリからのレコードの測定方法を定義します。 クエリで集計が行われない場合、唯一のオプションは **[テーブルの行]** を **[カウント]** することです。 クエリに 1 つ以上の集計された列が含まれる場合は、 **[テーブルの行]** の数または集計された列に基づく計算を使用するオプションを選択できます。 **[集約粒度]** は、収集された値を集約する時間間隔を定義します。 

:::image type="content" source="media/tutorial-log-alert/alert-rule-condition.png" lightbox="media/tutorial-log-alert/alert-rule-condition.png"alt-text="アラート ルールの条件":::

### <a name="configure-dimensions"></a>ディメンションの構成
**[Split by dimensions]\(ディメンションで分割\)** すると、さまざまなリソースに対して個別のアラートを作成できます。 この設定は、複数のリソースに適用されるアラート ルールを作成する場合に便利です。 スコープが 1 つのリソースに設定されている場合、この設定は通常使用されません。

:::image type="content" source="media/tutorial-log-alert/alert-rule-dimensions.png" lightbox="media/tutorial-log-alert/alert-rule-dimensions.png"alt-text="アラート ルールのディメンション":::


## <a name="configure-alert-logic"></a>アラート ロジックの構成
アラート ロジックで **[演算子]** と **[しきい値]** を設定して、測定から返された値と比較します。  この値が true の場合、アラートが作成されます。 **[評価の頻度]** の値を選択します。この値は、ログ クエリを実行して評価する頻度を定義します。 アラート ルールのコストは、頻度の値が小さいほど増加します。 頻度を選択すると、一定期間のクエリ結果のプレビューに加えて、推定月額コストが表示されます。

たとえば、測定が **[テーブルの行]** の場合、アラート ロジックは少なくとも 1 件のレコードが返されたことを示す **[Greater than 0]\(0 より大きい\)** とすることができます。 測定が列の値の場合、ロジックは特定のしきい値より大きいか、またはより小さい必要があります。 次の例では、ログ クエリによってストレージ アカウントに対する匿名要求を検索しています。 匿名の要求が行われた場合には、アラートをトリガーする必要があります。 この場合、1 行が返された時点でアラートをトリガーします。そのため、アラート ロジックは **[Greater than 0]\(0 より大きい\)** となります。

:::image type="content" source="media/tutorial-log-alert/alert-rule-alert-logic.png" lightbox="media/tutorial-log-alert/alert-rule-alert-logic.png"alt-text="アラート ロジック":::



## <a name="configure-actions"></a>アクションを構成する
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>詳細の構成
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="media/tutorial-log-alert/alert-rule-details.png" lightbox="media/tutorial-log-alert/alert-rule-details.png"alt-text="アラート ルールの詳細":::

**[アラート ルールの作成]** をクリックして、アラート ルールを作成します。

## <a name="view-the-alert"></a>アラートの表示
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>次の手順
Azure リソースのログ クエリ アラートを作成する方法を学習したので、監視データのインタラクティブな視覚化を作成するためのブックを確認します。

> [!div class="nextstepaction"]
> [Azure Monitor ブック](../visualize/workbooks-overview.md)