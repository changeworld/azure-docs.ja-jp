---
title: SQL insights で警告を作成する (プレビュー)
description: Azure Monitor で SQL insights を使用して警告を作成する
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: 5fe853ee0f7a113bfb8b0511744d9087f67927c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608902"
---
# <a name="create-alerts-with-sql-insights-preview"></a>SQL insights で警告を作成する (プレビュー)
SQL insights には、一般的な SQL の問題について [Azure Monitor で警告ルール](../alert/../alerts/alerts-overview.md)を作成するために使用できる一連の警告ルール テンプレートが含まれています。 SQL insights の警告ルールは、Azure Monitor ログの *InsightsMetrics* テーブルに格納されているパフォーマンス データに基づくログ警告ルールです。  

> [!NOTE]
> その他の SQL insights の警告ルール テンプレートが必要な場合は、このページの下部にあるリンクを使用するか、Azure portal の SQL insights フィードバック リンクを使用して、フィードバックをお送りください。

## <a name="enable-alert-rules"></a>アラート ルールを有効にする 
次の手順に従って、Azure portal から Azure Monitor の警告を有効にします。 作成される警告ルールのスコープは、選択した監視プロファイルで監視されているすべての SQL リソースになります。  警告ルールがトリガーされるときは、特定の SQL インスタンスまたはデータベースでトリガーされます。

> [!NOTE]
> *InsightsMetrics* テーブルのデータセットに対してクエリを実行し、それらのクエリを警告ルールとして保存することによって、カスタムの[ログ警告ルール](../alerts/alerts-log.md)を作成することもできます。 

Azure portal で、Azure Monitor メニューの **[Insights]** セクションから **[SQL (プレビュー)]** を選択します。 **[警告]** をクリックします。

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="[警告] ボタン":::

ページの右側に **[警告]** ペインが開きます。 既定ではここに、既に作成した警告ルールに基づいて、選択した監視プロファイル内の SQL リソースに対して発生した警告が表示されます。 **[警告テンプレート]** を選択すると、警告ルールの作成に使用できるテンプレートの一覧が表示されます。

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="警告テンプレート":::

**[警告ルールの作成]** ページで、ルールの既定の設定を確認し、必要に応じて編集します。 また、[アクション グループ](../alerts/action-groups.md)を選択して、警告ルールがトリガーされたときの通知とアクションを作成することもできます。 すべてのプロパティを確認したら、 **[警告ルールを有効にする]** をクリックして警告ルールを作成します。


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="[警告ルール] ページ":::

警告ルールを直ちに展開するには、 **[警告ルールの展開]** をクリックします。 ルール テンプレートを実際に展開する前に表示する場合は、 **[テンプレートの表示]** をクリックします。

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="警告ルールの展開":::

テンプレートを表示する場合は、[テンプレート] ページの **[展開]** を選択して、警告ルールを作成します。

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="テンプレートの表示の [展開]":::


## <a name="next-steps"></a>次のステップ

[Azure Monitor のアラート](../alerts/alerts-overview.md)について、さらに詳しく学習します。

