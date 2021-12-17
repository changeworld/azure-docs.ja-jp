---
title: Azure Monitor Log Analytics にクエリを保存する (プレビュー)
description: Log Analytics にクエリを保存する方法について説明します。
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 0334fa67849acdf628ec77a55fcf7d7f5435cbf6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482959"
---
# <a name="save-a-query-in-azure-monitor-log-analytics-preview"></a>Azure Monitor Log Analytics にクエリを保存する (プレビュー)
[ログ クエリ](log-query-overview.md)は、Log Analytics ワークスペース内のデータを処理および取得できる、Azure Monitor での要求です。 ログ クエリを保存すると、次の処理が可能です。

- ワークスペースとリソース中心を含め、すべての Log Analytics コンテキストでクエリを使用します。
- 他のユーザーが同じクエリを実行できます。
- 組織向けの共通クエリのライブラリを作成します。

## <a name="save-options"></a>保存オプション
クエリを保存すると、クエリ パックに格納されます。クエリ パックには、前のワークスペースにクエリを格納する方法に対して次のような利点があります。 クエリ パックへの保存は、次の利点が得られる推奨される方法です。

- さまざまなプロパティでクエリをフィルター処理とグループ化する機能により、検出が容易になります。
- Log Analytics でリソース スコープを使うときにクエリが使用できます。
- サブスクリプション間でクエリを使用できます。
- クエリの説明と分類に使用できるデータが増えます。


## <a name="save-a-query"></a>クエリを保存する
クエリ パックにクエリを保存するには、Log Analytics の **[保存]** ドロップダウンから **[Log 分析クエリとして保存]** を選択します。

[![[クエリの保存] メニュー](media/save-query/save-query.png)](media/save-query/save-query.png#lightbox)

クエリ パックにクエリを保存すると、次のダイアログ ボックスが表示され、クエリのプロパティの値を指定できます。 クエリ のプロパティは、探しているクエリを見つけるのに役立つ類似のクエリのフィルター処理とグループ化に使用されます。 各プロパティの詳細については、「[クエリのプロパティ](queries.md#query-properties)」を参照してください。

ほとんどのユーザーは、 **[既定のクエリ パックに保存する]** オプションをそのままにして、クエリを[既定のクエリ パック](query-packs.md#default-query-pack)に保存する必要があります。 サブスクリプションに他のクエリパックがある場合は、このチェック ボックスをオフにします。 新しいクエリ パックの作成の詳細については、「[Azure Monitor ログでのクエリ パック (プレビュー)](query-packs.md)」を参照してください。

[![[クエリの保存] ダイアログ](media/save-query/save-query-dialog.png)](media/save-query/save-query-dialog.png#lightbox)

## <a name="edit-a-query"></a>クエリを編集する
既に保存されているクエリを編集することもできます。 クエリ自体を変更するか、プロパティを変更することができます。 Log Analytics で既存のクエリを開いた後、 **[保存]** ドロップダウンから **[クエリの詳細の編集]** を選択して編集できます。 これにより、編集したクエリを同じプロパティで保存したり、保存する前にプロパティを変更したりできます。

クエリを別の名前で保存する場合は、新しいクエリを作成する場合と同じように **[ログ分析クエリとして保存]** を選択します。 


## <a name="save-as-a-legacy-query"></a>レガシ クエリとして保存する
上記のクエリ パックの利点により、レガシ クエリとして保存することは推奨されません。 クエリ パックのリリース前にワークスペースに保存された他のクエリと組み合わせることで、クエリをワークスペースに保存することができます。 

レガシ クエリを保存するには、Log Analytics の **[保存]** ドロップダウンから **[Log 分析クエリとして保存]** を選択します。 **[レガシ クエリとして保存]** オプションを選択します。 レガシ カテゴリのオプションのみが使用できます。


## <a name="next-steps"></a>次のステップ

[KQL クエリの概要](get-started-queries.md)
