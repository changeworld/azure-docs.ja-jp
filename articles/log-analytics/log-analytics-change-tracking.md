<properties
	pageTitle="Log Analytics の変更の追跡ソリューション | Microsoft Azure"
	description="環境内のソフトウェアや Windows サービスに対して行われた変更は、Log Analytics の構成変更の追跡ソリューションを使用して容易に特定できます。こうした構成の変更を特定することによって、運用上の問題点をピンポイントで突き止めることができます。"
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
	ms.date="04/26/2016"
	ms.author="banders"/>

# Log Analytics の変更の追跡ソリューション


環境内のソフトウェアや Windows サービスに対して行われた変更は、Log Analytics の構成変更の追跡ソリューションを使用して容易に特定できます。こうした構成の変更を特定することによって、運用上の問題点をピンポイントで突き止めることができます。

このソリューションをインストールすると、インストールしたエージェントの種類が更新されます。監視対象サーバーにインストールされているソフトウェアと Windows サービスの変更が読み取られた後、そのデータがクラウドの Log Analytics サービスに送信され、処理されます。受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。変更が見つかると、変更があるサーバーが [変更の追跡] ダッシュボードに表示されます。[変更の追跡] ダッシュボードの情報を使用して、サーバー インフラストラクチャで行われた変更を簡単に確認できます。

## ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

- Operations Manager は、変更の追跡ソリューションに必須です。
- 変更を監視する各コンピューターに、Windows エージェントまたは Operations Manager エージェントが必要です。
- 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従って変更の追跡ソリューションを OMS ワークスペースに追加します。 さらに手動で構成する必要はありません。


## 変更の追跡データ収集の詳細

次の表は、変更の追跡におけるデータの収集手段と、データ収集方法に関する各種情報をまとめたものです。


| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
|---|---|---|---|---|---|---|
|Windows|![はい](./media/log-analytics-change-tracking/oms-bullet-green.png)|![はい](./media/log-analytics-change-tracking/oms-bullet-green.png)|![なし](./media/log-analytics-change-tracking/oms-bullet-red.png)| ![いいえ](./media/log-analytics-change-tracking/oms-bullet-red.png)|![あり](./media/log-analytics-change-tracking/oms-bullet-green.png)| 時間単位|


## 変更の追跡を使用する

ソリューションをインストールした後で、OMS の **[概要]** ページにある **[変更の追跡]** タイルを使用すると、監視対象サーバーの変更の概要を確認できます。

![[変更の追跡] タイルの画像](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

インフラストラクチャに対する変更を確認し、変更の詳細を次のカテゴリ別に表示することができます。

- ソフトウェアや Windows サービスに対する構成の種類ごとの変更
- アプリケーションに対するソフトウェアの変更および個々のサーバーに対する更新
- 各アプリケーションのソフトウェア変更の合計数
- 個々のサーバーでの Windows サービスの変更

![[変更の追跡] ダッシュボードの画像](./media/log-analytics-change-tracking/oms-changetracking01.png)

![[変更の追跡] ダッシュボードの画像](./media/log-analytics-change-tracking/oms-changetracking02.png)

### 変更の種類別に変更を表示するには

1. **[概要]** ページで、**[変更の追跡]** タイルをクリックします。
2. **[変更の追跡]** ダッシュボードにあるいずれかの変更の種類ブレードで概要情報を確認し、**ログの検索**ページで、詳細情報を表示する変更の 1 つをクリックします。
3. どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。結果を絞り込むファセットを使用してフィルター処理することもできます。

## 次のステップ

- [Log Analytics のログ検索機能](log-analytics-log-searches.md)を使用して、詳細な変更追跡データを確認してください。

<!---HONumber=AcomDC_0518_2016-->