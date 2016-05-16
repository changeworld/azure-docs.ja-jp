<properties
	pageTitle="Log Analytics でのカスタム ダッシュボードの作成| Microsoft Azure"
	description="このガイドでは、環境をわかりやすく表示できるように、Log Analytics ダッシュボードが保存済みのすべてのログ検索を視覚化するしくみについて説明します。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Log Analytics でのカスタム ダッシュボードの作成

このガイドでは、環境をわかりやすく表示できるように、Log Analytics ダッシュボードが保存済みのすべてのログ検索を視覚化するしくみについて説明します。

![サンプルのダッシュ ボード](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

## ダッシュ ボードの作成方法。

まず、左側のナビゲーションで [概要] ボタンをクリックして、OMS の概要に移動します。左側に「My Dashboard」タイルが表示されます。クリックしてダッシュボードへドリルダウンします。

![概要](./media/log-analytics-dashboards/oms-dashboards-overview.png)



## タイルの追加

ダッシュ ボードでは、タイルは、保存済みのログ検索が提供します。OMS には事前に作成された保存済みの多くのログ検索が付属しているので、すぐに開始できます。開始方法を説明した次の図をご覧ください。

![図](./media/log-analytics-dashboards/oms-dashboards-pictorial.png)

My Dashboard ビューで、ページの下部の [カスタマイズ] の歯車をクリックし、カスタマイズ モードに移動します。ページの右側に表示されたパネルに、ワークスペースが保存したすべてのログ検索が表示されます。

![タイル 1 の追加](./media/log-analytics-dashboards/oms-dashboards-add-tile1.png)

保存したログ検索をタイルとして表示するには、左側の空の領域にドラッグします。ドラッグすると、タイルになります。

![タイル 2 の追加](./media/log-analytics-dashboards/oms-dashboards-add-tile2.png)

![タイル 3 の追加](./media/log-analytics-dashboards/oms-dashboards-add-tile3.png)


## タイルを編集する

My Dashboard ビューで、ページの下部の [カスタマイズ] の歯車をクリックし、カスタマイズ モードに移動します。編集するタイルをクリックします。右側のパネルで編集し、オプションを選択します。

![タイルの編集](./media/log-analytics-dashboards/oms-dashboards-edit-tile.png)

### タイルの視覚化#
タイルの視覚化は、次の 2 種類から選択できます。

|グラフの種類|実行内容|
|---|---|
|![横棒グラフ](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|保存済みのログ検索の結果のタイムラインを表示します。または、ログ検索の結果がフィールドごとに集計されているかどうかに応じて、フィールドごとに結果の一覧を表示します。
|![メトリック](./media/log-analytics-dashboards/oms-dashboards-metric.png)|ログ検索結果の合計ヒット数を数値としてタイルに表示します。メトリックのタイルでは、しきい値に達したときに、タイルを強調表示するしきい値を設定することができます。|

### しきい値
メトリックの視覚エフェクトを使用して、タイルのしきい値を作成できます。タイルにしきい値の値を作成するを選択します。値が選択したしきい値未満、または以上の場合に強調表示するかどうかを選択し、しきい値を設定します。

## ダッシュ ボードの整理
ダッシュボードを整理するには、My Dashboard ビューに移動し、ページの下部の [カスタマイズ] の歯車をクリックし、カスタマイズ モードに移動します。移動するタイルをクリックしてドラッグし、タイルを任意の場所に移動します。

![ダッシュ ボードの整理](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## タイルの削除
タイルを削除するには、My Dashboard ビューに移動し、ページの下部の **[カスタマイズ]** の歯車をクリックし、カスタマイズ モードに移動します。削除するタイルを選択し、右側のパネルで **[タイルの削除]** を選択します。

![タイルの削除](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## 次のステップ

- Log Analytics でアラートを作成して、通知を生成し、問題を修正します。

<!---HONumber=AcomDC_0504_2016-->