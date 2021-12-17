---
title: Azure Monitor ビュー デザイナーからブックへの変換の概要とアクセス
description: Azure Monitor のビューからの移行時にブックにアクセスするために必要なアクセス許可。
author: shijatsu
ms.author: shijain
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: d79e36c77505635cb37573712a028b98b66af1ff
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472334"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>ビュー デザイナーからブックへの変換の概要とアクセス
[ビュー デザイナー](view-designer.md)は Azure Monitor の機能で、Log Analytics ワークスペース内のデータを、グラフ、リスト、タイムラインを使用して視覚化するのに役立つカスタム ビューを作成できます。 これらは段階的に廃止され、追加機能を提供するブックに置き換えられています。 この記事では、概要サマリーを作成する方法と、ブックにアクセスするために必要なアクセス許可について詳しく説明します。

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Azure ダッシュボードからワークスペースの概要を作成する
ビュー デザイナーのユーザーは、ビューのセットを表す概要タイルがあることに慣れているでしょう。 ビュー デザイナーのワークスペースの概要のような視覚的な概要を維持するために、ブックにはピン留めされた手順が用意されています。それらを [Azure portal ダッシュボード](../../azure-portal/azure-portal-dashboards.md)にピン留めすることができます。 ワークスペースの概要にある概要タイルと同様に、ピン留めされたブック アイテムは、ブック ビューに直接リンクしています。

Azure ダッシュボードに用意されている高度なカスタマイズ機能を利用できます。これにより、ピン留めされたアイテムや視覚化に対する自動更新、移動、サイズ変更、追加のフィルター処理が可能になります。 

![ワークスペースの概要という名前の、カスタマイズされた Azure ダッシュボードを示すスクリーンショット。](media/view-designer-conversion-access/dashboard.png)

新しい Azure ダッシュボードを作成するか、既存のダッシュボードを選択して、ブック アイテムのピン留めを開始します。

個々のアイテムをピン留めするには、特定の手順のピン留めアイコンを有効にする必要があります。 これを行うには、手順に対応した **[編集]** ボタンを選択し、次に歯車アイコンを選択して **[詳細設定]** を開きます。 **[この手順のピン アイコンが常に表示されます]** のオプションをオンにすると、手順の右上隅にピン留めアイコンが表示されます。 このピンを使用すると、概要のタイルなどの特定の視覚化をダッシュボードにピン留めできます。

![手順のピン留め](media/view-designer-conversion-access/pin-step.png)


また、ダッシュボードにブックから複数の視覚化をピン留めすることや、ブックの内容全体をピン留めすることもできます。 ブック全体をピン留めするには、上部のツール バーにある **[編集]** を選択して **編集モード** を切り替えます。 ピン留めアイコンが表示されます。これにより、ブック アイテム全体をピン留めすることも、ブック内の個々の手順と視覚化をすべてピン留めすることもできます。

![すべて固定](media/view-designer-conversion-access/pin-all.png)

## <a name="sharing-and-viewing-permissions"></a>共有と表示のアクセス許可 

ブックを共有するには、**編集モード** で上部ツール バーの **[共有]** アイコンを選択します。 ブックを **[共有レポート]** に移動するように求められます。これにより、ブックへの直接アクセスを提供するリンクが生成されます。

共有ブックを表示するには、ユーザーはブックが保存されたサブスクリプションとリソース グループの両方にアクセスできる必要があります。

![サブスクリプションベースのアクセス](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>次のステップ

- [一般的なタスク](view-designer-conversion-tasks.md)