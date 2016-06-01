<properties
	pageTitle="Log Analytics のシステム更新評価ソリューション | Microsoft Azure"
	description="Log Analytics で、システムの更新ソリューションを使用して、インフラストラクチャのサーバーに不足している更新プログラムを効率よく適用することができます。"
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
	ms.date="05/06/2016"
	ms.author="banders"/>

# Log Analytics のシステム更新評価ソリューション


Log Analytics で、システムの更新ソリューションを使用して、インフラストラクチャのサーバーに不足している更新プログラムを効率よく適用することができます。ソリューションのインストール後、監視対象のサーバーに不足している更新プログラムを確認するには、OMS の **[Overview]** (概要) ページの **[System Update Assessment]** (システムの更新の評価) タイルを使用します。

不足している更新プログラムが見つかった場合、**[Updates]** (更新プログラム) ダッシュボードに表示されます。**[更新プログラム]** ダッシュボードを使用して、更新プログラムの適用漏れに対処したり、更新プログラムを必要とするサーバーに、それらを適用するためのプランを作成したりすることができます。

## ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

- 「[Add Log Analytics solutions from the Solutions Gallery (ソリューション ギャラリーから Log Analytics ソリューションを追加する)](log-analytics-add-solutions.md)」で説明されている手順に従ってシステム更新評価ソリューションを OMS ワークスペースに追加します。さらに手動で構成する必要はありません。

## システム更新データ収集の詳細

次の表は、システム更新評価におけるデータの収集手段と、データの収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
|---|---|---|---|---|---|---|
|Windows|![あり](./media/log-analytics-system-update/oms-bullet-green.png)|![はい](./media/log-analytics-system-update/oms-bullet-green.png)|![なし](./media/log-analytics-system-update/oms-bullet-red.png)| ![いいえ](./media/log-analytics-system-update/oms-bullet-red.png)|![あり](./media/log-analytics-system-update/oms-bullet-green.png)| 1 日に少なくとも 2 回、更新プログラムのインストールの 15 分後|


### 更新プログラムに関する作業を行うには

1. **[概要]** ページの **[System Update Assessment]** タイルをクリックします。![[概要] ページの画像](./media/log-analytics-system-update/oms-updates01.png)
2. **[更新プログラム]** ダッシュボードで更新プログラムのカテゴリを表示します。![[更新プログラム] ページの画像](./media/log-analytics-system-update/oms-updates02.png)
3. ページの右にスクロールし、**[Type of Updates Missing]** ブレードを表示し、**[セキュリティ更新プログラム]**をクリックします。![[更新プログラム] ページの画像](./media/log-analytics-system-update/oms-updates03.png)
4. [検索] ページでは、インフラストラクチャ内のサーバーから不足していると検出されたセキュリティ更新プログラムの一覧が表示されます。不足している更新プログラムの詳細については、サポート情報の記事 ID (KBID) をクリックします。この例では、*KBID 3032323* です。![[更新プログラム] ページの画像](./media/log-analytics-system-update/oms-updates04.png)
5. Web ブラウザーでは、更新プログラムを説明しているサポート情報の記事が表示されます。![image of the knowledge base article](./media/log-analytics-system-update/oms-updates05.png)
6. 検出された情報を基に、不足している更新プログラムを適用するためのプランを作成することができます。

## 次のステップ

- [ログを検索](log-analytics-log-searches.md)してシステム更新に関する詳しいデータを確認します。

<!---HONumber=AcomDC_0518_2016-->