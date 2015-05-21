<properties 
	pageTitle="オペレーション インサイト ダッシュボード" 
	description="オペレーション インサイトの基本的なダッシュ ボードの使用についての情報記事" 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/30/2015" 
	ms.author="banders"/>

# オペレーション インサイト ダッシュボード

[AZURE.INCLUDE [operational-insights-note-moms](../includes/operational-insights-note-moms.md)]

このガイドでは、環境をわかりやすく表示しながら、オペレーション インサイトが保存したすべての検索を視覚化する方法について説明します。

![サンプルのダッシュ ボード](./media/operational-insights-use-dashboards/example-dash.png)

## ダッシュ ボードの作成方法。

まず、左側のナビゲーションで \[概要\] ボタンをクリックして、Azure オペレーション インサイトの概要に移動します。左側に「My Dashboard」タイルが表示されます。クリックしてダッシュボードへドリルダウンします。

![概要](./media/operational-insights-use-dashboards/overview.png)



## タイルの追加

ダッシュ ボードでは、タイルは、保存済みの検索が提供します。オペレーション インサイトには、すぐ始められるように、事前に作成され、保存された多くの検索が付属しています。開始方法を説明した次の図をご覧ください。

![図](./media/operational-insights-use-dashboards/pictorial.png)

My Dashboard ビューで、ページの下部の \[カスタマイズ\] の歯車をクリックし、カスタマイズ モードに移動します。ページの右側に表示されたパネルに、ワークスペースが保存したすべての検索が表示されます。

![タイル 1 の追加](./media/operational-insights-use-dashboards/add-tile1.png)

保存した検索をタイルとして表示するには、左側の空の領域にドラッグします。ドラッグすると、タイルになります。

![タイル 2 の追加](./media/operational-insights-use-dashboards/add-tile2.png)

![タイル 3 の追加](./media/operational-insights-use-dashboards/add-tile3.png)


## タイルを編集する

My Dashboard ビューで、ページの下部の \[カスタマイズ\] の歯車をクリックし、カスタマイズ モードに移動します。編集するタイルをクリックします。右側のパネルで編集し、オプションを選択します。![タイルの編集](./media/operational-insights-use-dashboards/edit-tile.png)

### タイルの視覚化#
タイルの視覚化は、次の 2 種類から選択できます。

**横棒グラフ** <p> ![横棒グラフ](./media/operational-insights-use-dashboards/bar-chart.png)

保存した検索結果のタイムラインを表示します。あるいは検索がフィールドごとに集計されているかどうかによって、フィールドごとに結果の一覧を表示します。

**メトリック** <p> ![メトリック](./media/operational-insights-use-dashboards/metric.png)

検索結果の合計のヒット数をタイルに数値として表示します。メトリックのタイルでは、しきい値に達したときに、タイルを強調表示するしきい値を設定することができます。

### しきい値
メトリックの視覚エフェクトを使用して、タイルのしきい値を作成できます。タイルにしきい値の値を作成するを選択します。値が選択したしきい値未満、または以上の場合に強調表示するかどうかを選択し、しきい値を設定します。

## ダッシュ ボードの整理
ダッシュボードを整理するには、My Dashboard ビューに移動し、ページの下部の \[カスタマイズ\] の歯車をクリックし、カスタマイズ モードに移動します。移動するタイルをクリックしてドラッグし、タイルを任意の場所に移動します。

![ダッシュ ボードの整理](./media/operational-insights-use-dashboards/organize.png)

## タイルの削除
タイルを削除するには、My Dashboard ビューに移動し、ページの下部の **\[カスタマイズ\]** の歯車をクリックし、カスタマイズ モードに移動します。削除するタイルを選択し、右側のパネルで **\[タイルの削除\]** を選択します。![タイルの削除](./media/operational-insights-use-dashboards/remove-tile.png)
<!--HONumber=54-->