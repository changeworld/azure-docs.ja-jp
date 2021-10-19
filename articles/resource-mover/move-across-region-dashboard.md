---
title: リージョン間移動ダッシュボード
description: リージョン間移動ダッシュボードを使用して、リージョン間でのリソースの移動を監視します。
author: Aarthi-Vijayaraghavan
manager: sutalasi
ms.service: resource-move
ms.topic: how-to
ms.date: 08/30/2021
ms.author: AarthiV
ms.openlocfilehash: 9339b61135d4b8b21f88b35a842a080dac9f8e69
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620925"
---
# <a name="move-across-region-dashboard"></a>リージョン間移動ダッシュボード
この記事では、Azure Resource Mover のリージョン間移動ダッシュボードを使用して、リージョン間で移動しているリソースを監視する方法について説明します。 
## <a name="monitor-via-the-dashboard"></a>ダッシュボードを使用して監視する
1. **Azure Resource Mover** で、左側のナビゲーション ウィンドウの **[概要]** を選択します。 **[Getting started]\(使用開始\)** と **[Move across region dashboard]\(リージョン間移動ダッシュボード\)** の 2 つのページを切り替えることができます。 **[Getting started]\(使用開始\)** ページには、サブスクリプション間、リソース グループ間、リージョン間でリソースを移動するためのオプションがあります。
**[Move across region dashboard]\(リージョン間移動ダッシュボード\)** ページには、リージョン間の移動に関するすべての監視情報が 1 か所にまとめられています。
    [![[Move across region dashboard]\(リージョン間移動ダッシュボード\) タブ](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)
2. このダッシュボードには、ユーザーが作成したすべての移動の組み合わせの一覧が表示されます。 リージョン間の移動の状態を把握するには、次の 2 つのセクションを使用します。
    **[移動状態別のリソース]** で、各状態のリソースの割合と数を監視します。
    **[Error Summary]\(エラーの概要\)** で、移動先リージョンに正常に移動する前に解決する必要があるアクティブなエラーを監視します。
    [![状態と問題のセクション](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)
> [!NOTE]
> 選択したサブスクリプションで既に作成されている移動元と移動先の組み合わせだけが、ダッシュボードに表示されます。

3. フィルターを使用して、適切な **[サブスクリプション]** 、 **[Source region]\(移動元リージョン\)** 、 **[Destination region]\(移動先リージョン\)** を選択します。
    [![フィルター](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)
4. 移動元 - 移動先の横にある **[すべてのリソースを表示]** を選択して、詳細ページに移動します。
    [![詳細](media\move-across-region-dashboard\move-across-region-dashboard-details.png)](media\move-across-region-dashboard\move-across-region-dashboard-details.png)
## <a name="next-steps"></a>次のステップ
移動プロセス[について学習します](about-move-process.md)。
