---
title: ダッシュボードを使用したエラー調査
description: このドキュメントには、ダッシュボードを使用したエラー調査に関する情報が含まれています
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9a43318ed935dd1e71b6da75c9f078fbebbd29eb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599014"
---
# <a name="error-investigation-using-the-dashboard"></a>ダッシュボードを使用したエラー調査

このページには、ITSM コネクタ ダッシュボードに関する情報が含まれています。 このダッシュボードは、ITSM コネクタの状態を調査するのに役立ちます。

## <a name="how-to-view-the-dashboard"></a>ダッシュボードの表示方法

ダッシュボードにエラーを表示するには、次の手順に従います。

1. **[すべてのリソース]** で、**ServiceDesk( *<実際のワークスペース名>* )** を探します。

   ![Azure portal の最新のリソースを示すスクリーンショット。](media/itsmc-definition/create-new-connection-from-resource.png)

2. 左側のウィンドウの **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** を選択します。

   ![[ITSM 接続] メニュー項目を示すスクリーンショット。](media/itsmc-overview/add-new-itsm-connection.png)

3. 左側のボックス **[IT Service Management Connector]** の **[概要]** で **[概要の表示]** を選択します。

    ![[概要の表示] を示すスクリーンショット。](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. 左側のボックス **[IT Service Management Connector]** の **[概要]** でグラフをクリックします。

    ![グラフのクリックを示すスクリーンショット。](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. このダッシュボードを使用すると、コネクタの状態とエラーを確認できます。
    ![コネクタの状態を示すスクリーンショット。](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>ダッシュボードの要素

ダッシュボードには、このコネクタを使用して ITSM ツールに送信されたアラートに関する情報が含まれています。
ダッシュボードは、4 つの部分に分かれています。

1. 作業項目作成済: グラフと下の表に、作業項目の種類ごとの数が含まれています。 グラフまたはテーブルをクリックすると、作業項目に関する詳細を表示できます。
    ![作成済みの作業項目を示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. 影響を受けたコンピューター: テーブルに、構成項目を作成した構成項目の詳細が含まれています。
    テーブル内の行をクリックすると、構成項目に関するさらに詳細な情報を表示できます。
    [すべて表示] をクリックして一覧を表示する場合は、テーブルに含まれる行の数が制限されます。
    ![影響を受けたコンピューターを示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. コネクタの状態: グラフと下の表に、コネクタの状態に関するメッセージが含まれています。 テーブル内の行のグラフをクリックすると、コネクタの状態のメッセージの詳細を表示できます。
    [すべて表示] をクリックして一覧を表示する場合は、テーブルに含まれる行の数が制限されます。

    テーブル内のメッセージの詳細については、[こちら](itsmc-dashboard-errors.md)を参照してください。

    ![コネクタの状態を示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. アラート ルール: テーブルに、検出されたアラート ルールの数に関する情報が含まれています。
    テーブル内の行をクリックすると、検出されたルールに関するさらに詳細な情報を表示できます。
    [すべて表示] をクリックして一覧を表示する場合は、テーブルに含まれる行の数が制限されます。
    ![アラート ルールを示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)