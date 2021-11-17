---
title: チュートリアル - Azure リソースからリソース ログを収集する
description: このチュートリアルでは、Azure リソースから Log Analytics ワークスペースにリソース ログが送信されるように診断設定を構成します。ワークスペースでは、ログ クエリを使用してログを分析できます。
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 11/08/2021
ms.openlocfilehash: 90a1f193bf1f136e4d3501b68bfab8346604fbbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297979"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>チュートリアル:Azure リソースからリソース ログを収集して分析する
リソース ログを収集すると、Azure リソースの詳細な操作に関する分析情報が提供され、正常性と可用性を監視するのに役立ちます。 Azure リソースでは自動的にリソース ログが生成されますが、それを収集するための診断設定を作成する必要があります。 このチュートリアルでは、診断設定を作成して Log Analytics ワークスペースにリソース ログを送信し、ログ クエリを使用してそれらを分析できるようにするプロセスについて順を追って説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Monitor で Log Analytics ワークスペースを作成する
> * リソース ログを収集するための診断設定を作成する 
> * 簡単なログ クエリを作成してログを分析する


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。 

- 監視する Azure リソース。 診断設定がサポートされている、Azure サブスクリプション内の任意のリソースを使用できます。 リソースで診断設定がサポートされているかどうかを調べるには、Azure portal でリソースのメニューの **[監視]** セクションに **[診断設定]** オプションがあることを確認します。


> [!NOTE]
> Azure 仮想マシンでは、 **[診断設定]** メニューが診断拡張機能の構成に使用されているため、本手順は適用されません。

## <a name="create-a-log-analytics-workspace"></a>Log Analytics ワークスペースを作成する
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]

## <a name="create-a-diagnostic-setting"></a>診断設定の作成
[診断設定](../essentials/diagnostic-settings.md)では、特定のリソースのリソース ログを送信する場所を定義します。 1 つの診断設定で複数の[送信先](../essentials/diagnostic-settings.md#destinations)を指定できますが、このチュートリアルでは Log Analytics ワークスペースのみを使用します。

リソースのメニューの **[監視]** セクションで、 **[診断設定]** を選択して **[診断設定を追加する]** をクリックします。

> [!NOTE]
> リソースによっては、追加の選択が必要になる場合があります。 たとえばストレージ アカウントでは、 **[診断設定を追加する]** オプションが表示される前にリソースを選択する必要があります。 また、診断設定が現在パブリック プレビュー段階にある一部のリソースでは、 **[プレビュー]** ラベルが表示される場合があります。

:::image type="content" source="media/tutorial-resource-logs/diagnostic-settings.png" lightbox="media/tutorial-resource-logs/diagnostic-settings.png"alt-text="診断設定":::


各診断設定には、次の 3 つの基本部分があります。
 
   - **Name**:これには大きな影響はなく、わかりやすいものにする必要だけがあります。
   - **カテゴリ**: 各送信先に送信するログのカテゴリ。 カテゴリのセットは、Azure サービスごとに異なります。
   - **宛先**:ログを送信する 1 つ以上の送信先。 可能な同じ送信先のセットを、すべての Azure サービスで共有します。 各診断設定では、1 つまたは複数の送信先を定義できますが、特定の種類の送信先は 1 つだけです。 

診断設定の名前を入力し、収集するカテゴリを選択します。 使用可能なカテゴリの定義については、各サービスのドキュメントを参照してください。 **[Allmetrics]** は、Azure Monitor メトリックでリソースに対して利用できるものと同じプラットフォーム メトリックをワークスペースに送信します。 これにより、ログ クエリを使用してこのデータを他の監視データとともに分析することができます。 **[Log Analytics への送信]** を選択し、作成したワークスペースを選択します。 

:::image type="content" source="media/tutorial-resource-logs/diagnostic-setting-details.png" lightbox="media/tutorial-resource-logs/diagnostic-setting-details.png"alt-text="診断設定の詳細":::

**[保存]** をクリックして診断設定を保存します。

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>ログ クエリを使用してログを取得する
Log Analytics ワークスペースからデータを取得するには、Kusto クエリ言語 (KQL) で記述したログ クエリを使用します。 多くの Azure サービスで、事前作成済みの一連のクエリを使用できます。そのため、作業を開始するために KQL の知識は必要ありません。

リソースのメニューから **[ログ]** を選択します。 Log Analytics が **[クエリ]** ウィンドウとともに開き、 **[リソースの種類]** に対する事前作成済みのクエリが表示されます。 

> [!NOTE]
> **[クエリ]** ウィンドウが開かない場合は、右上にある **[クエリ]** をクリックします。 

:::image type="content" source="media/tutorial-resource-logs/queries.png" lightbox="media/tutorial-resource-logs/queries.png"alt-text="スクリーンショットは、リソース ログを使用したサンプル クエリを示しています。":::


使用可能なクエリを参照します。 実行するものを特定し、 **[実行]** をクリックします。 クエリ ウィンドウにクエリが追加され、結果が返されます。

:::image type="content" source="media/tutorial-resource-logs/query-results.png" lightbox="media/tutorial-resource-logs/query-results.png"alt-text="スクリーンショットは、サンプル ログ クエリの結果を示しています。":::

## <a name="next-steps"></a>次の手順
リソース ログの収集を開始したので、ログ データで興味深いデータが特定されたときに事前に通知するログ クエリ アラートを作成します。

> [!div class="nextstepaction"]
> [Azure リソースのログ クエリ アラートを作成する](../alerts/tutorial-log-alert.md)
