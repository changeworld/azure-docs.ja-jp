---
title: "Log Analytics のビューを作成してデータを分析する | Microsoft Docs"
description: "Log Analytics のビュー デザイナーを使用すると、Azure Portal に表示されるカスタム ビューを作成し、Log Analytics ワークスペースのデータをさまざまな方法で視覚化することができます。 この記事では、ビュー デザイナーの概要と、カスタム ビューの作成と編集を行うための手順について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Log Analytics のビュー デザイナーを使用してカスタム ビューを作成する
[Log Analytics](log-analytics-overview.md) のビュー デザイナーを使用すると、Azure Portal に、Log Analytics ワークスペースのデータのさまざまな視覚化を含むカスタム ビューを作成できます。 この記事では、ビュー デザイナーの概要と、カスタム ビューの作成と編集を行うための手順について説明します。

ビュー デザイナーについてはこの他に次の記事があります。

* [タイル リファレンス](log-analytics-view-designer-tiles.md) - カスタム ビューで使用できるタイルごとの設定のリファレンス。
* [視覚化パーツ リファレンス](log-analytics-view-designer-parts.md) - カスタム ビューで使用できるタイルごとの設定のリファレンス。

>[!NOTE]
> ワークスペースが[新しい Log Analytics クエリ言語](log-analytics-log-search-upgrade.md)にアップグレードされている場合、すべてのビューのクエリを[新しいクエリ言語](https://go.microsoft.com/fwlink/?linkid=856078)で記述する必要があります。  ワークスペースのアップグレード前に作成されたすべてのビューは、自動的に変換されます。

## <a name="concepts"></a>概念
ビューは、Azure Portal の Log Analytics ワークスペースの **[概要]**ページに表示されます。  各カスタム ビューのタイルは、同じワークスペースにインストールされたソリューションのタイルと共に、アルファベット順に表示されます。

![[概要] ページ](media/log-analytics-view-designer/overview-page.png)

ビュー デザイナーで作成されるビューには、次の表に示す要素が含まれます。

| パーツ | [説明] |
|:--- |:--- |
| タイル |Log Analytics ワークスペースの [概要] ページに表示されます。  カスタム ビューに含まれる情報を視覚的にまとめたものを表示します。  タイルの種類に応じて、レコードが視覚化されて表示されます。  タイルをクリックすると、カスタム ビューが開きます。 |
| カスタム ビュー |タイルをクリックすると表示されます。  1 つまたは複数の視覚化パーツが含まれます。 |
| 視覚化パーツ |1 つまたは複数の[ログ検索](log-analytics-log-searches.md)に基づく Log Analytics ワークスペース内のデータの視覚化。  ほとんどのパーツには、高レベルの視覚化を提供するヘッダーと上位の結果の一覧が含まれています。  パーツの種類に応じて、Log Analytics ワークスペース内のレコードが視覚化されて表示されます。  詳細レコードを提供するログ検索を実行するには、パーツの要素をクリックします。 |


## <a name="work-with-an-existing-view"></a>既存のビューの使用
ビュー デザイナーで作成されたビューを開くと、次の表に示すオプションがメニューに表示されます。

![[概要] メニュー](media/log-analytics-view-designer/overview-menu.png)


| オプション | [説明] |
|:--|:--|
| 更新   | 最新データで表示を更新します。 | 
| [分析] | [[高度な分析] ポータル](log-analytics-log-search-portals.md#advanced-analytics-portal)を開き、ログ検索 (log-analytics-log-search-portals.md#advanced-analytics-portal) でデータを分析します。 |
| filter    | ビューに含まれたデータの時間フィルターを設定します。 |
| 編集      | ビュー デザイナーでビューを開き、内容と構成を編集します。   |
| 複製     | 新しいビューを作成して、ビュー デザイナーで開きます。  新しいビューの名前は、元の名前の末尾に "コピー" が追加されたものになります。 |


## <a name="create-a-new-view"></a>新しいビューを作成する
。Azure Portal で Log Analytics ワークスペースの [概要] ページにある [ビュー デザイナー] タイルをクリックして、**ビュー デザイナー**で新しいビューを作成します。

![ビュー デザイナー タイル](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>ビュー デザイナーの操作
新しいビューを作成したり既存のビューを編集したりするには、ビュー デザイナーを操作します。  

ビュー デザイナーには、3 つのウィンドがあります。  **[デザイン]**  ウィンドウには、作成または編集しているカスタム ビューが含まれています。  **[コントロール]** ウィンドウから**[デザイン]** ウィンドウに、タイルとパーツを追加します。  **プロパティ** ウィンドウには、タイルまたは選択したパーツのプロパティが表示されます。

![ビュー デザイナー](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>ビューのタイルを構成する
カスタム ビューは、タイルを 1 つだけ持つことができます。  **タイル** ウィンドウの **タイル** タブを選択して現在のタイルを表示するか、別のタイルを選択します。  **プロパティ** ウィンドウには、現在のタイルのプロパティが表示されます。  「[タイル リファレンス](log-analytics-view-designer-tiles.md)」の詳細情報に従ってタイルのプロパティを構成し、**[適用]** をクリックして変更を保存します。

### <a name="configure-visualization-parts"></a>視覚化パーツを構成する
ビューには、任意の数の視覚化パーツを含めることができます。  **[ビュー]** タブを選択し、ビューに追加する視覚化パーツを選択します。  **プロパティ** ウィンドウに、選択したパーツのプロパティが表示されます。  「[視覚化パーツ リファレンス](log-analytics-view-designer-parts.md)」の詳細情報に従ってビューのプロパティを構成し、**[適用]** をクリックして変更を保存します。

ビューには、1 列の視覚化パーツのみが含まれています。  パーツをクリックし、新しい場所にドラッグすることで、既存のパーツの順序を変更します。

ビューから視覚化パーツを削除するには、パーツの右上隅にある **[X]** ボタンをクリックします。


### <a name="menu-options"></a>メニュー オプション
編集モードでビューを操作するときは、次の表に示したメニュー オプションがあります。

![[編集] メニュー](media/log-analytics-view-designer/edit-menu.png)

| オプション | [説明] |
|:--|:--|
| 保存        | 変更を保存し、ビューを閉じます。 |
| キャンセル      | 変更を破棄し、ビューを閉じます。 |
| ビューの削除 | ビューを削除します。 |
| エクスポート      | 別のワークスペースにインポートできる [Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)にビューをエクスポートします。  ファイルの名前は、ビューの名前に拡張子 *omsview* が付加されたものになります。 |
| [インポート]      | 別のワークスペースからエクスポートした *omsview* ファイルをインポートします。  これにより、既存のビューの構成が上書きされます。 |
| 複製       | 新しいビューを作成して、ビュー デザイナーで開きます。  新しいビューの名前は、元の名前の末尾に "コピー" が追加されたものになります。 |
| [発行]     | [管理ソリューション](../operations-management-suite/operations-management-suite-solutions-resources-views.md)に挿入できる JSON ファイルにビューをエクスポートします。  ファイルの名前は、ビューの名前に拡張子*json* が付加されたものになります。 2 番目のファイルは、拡張子 *resjson* を付加して作成され、JSON ファイルに定義されたリソースの値を含みます。

## <a name="next-steps"></a>次の手順
* [タイル](log-analytics-view-designer-tiles.md)をカスタム ビューに追加します。
* [視覚化パーツ](log-analytics-view-designer-parts.md)をカスタム ビューに追加します。
