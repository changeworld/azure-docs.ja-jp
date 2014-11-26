<properties linkid="service-bus-monitor-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Monitor Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to monitor your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Monitor Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Service Bus メッセージング エンティティの監視方法

このトピックでは、[Azure 管理ポータル][Azure 管理ポータル]を使用してサービス バス エンティティを管理し監視する方法について説明します。ポータルを使用すると、キューとトピックの状態を包括的に把握できます。またその使用状況を監視することもできます。

## Service Bus キューのアクティビティの監視方法

サービス バス キューを監視するには、以下の手順を実行します。

1.  [Azure 管理ポータル][Azure 管理ポータル]へのログオン
2.  左側のナビゲーション バーで **[サービス バス]** アイコンをクリックして、サービス名前空間の一覧を表示します。
3.  監視するキューを含む名前空間をクリックします。
4.  ページ上部のピボット バーで、**[キュー]** をクリックします。
5.  監視するキューの名前をクリックします。キューのダッシュボードが表示されます。
6.  作成したキューのアクティビティが表示されます。この情報はいくつかの時間枠で表示できます。既定値は 1 時間ですが、時間枠の横にあるドロップダウン リストをクリックして、異なる時間枠を選択することができます。選択できるのは過去 24 時間、7 日間、または 30 日間です。データの測定期間の単位は、時間枠が 1 時間の場合は 5 分間、24 時間の場合は 1 時間、7 日間および 30 日間の場合は 1 日です。

キューの場合、次のグラフが表示されます。

-   **受信メッセージ**: この期間にキューに格納されたメッセージの数。
-   **送信メッセージ**: この期間にキューから削除されたメッセージの数。
-   **長さ**: この期間の終了時におけるエンティティのメッセージの数。
-   **サイズ**: この期間の終了時においてこのエンティティが使用しているストレージ空間 (MB 単位)。

### 概要

ダッシュボードの **[概要]** には、キューの現在のサイズが **[キューの長さ]** として反映されます。また、キューまたはトピックの他のプロパティも表示されます。この情報は 10 秒ごとに更新されます。

![][0]

## Service Bus トピックのアクティビティの監視方法

サービス バス トピックを監視するには、以下の手順を実行します。

1.  [Azure 管理ポータル][Azure 管理ポータル]へのログオン
2.  左側のナビゲーション バーで **[サービス バス]** アイコンをクリックして、サービス名前空間の一覧を表示します。
3.  監視するトピックを含む名前空間をクリックします。
4.  ページ上部のピボット バーで、**[トピック]** をクリックします。
5.  監視するトピックの名前をクリックします。トピックのダッシュボードが表示されます。

トピックのダッシュボードはキューのダッシュボードと似ていますが、使用状況のメトリックが異なります。トピックのダッシュボードには送信メッセージ数と長さが表示されません。この情報はトピックのサブスクリプションごとに異なるためです。**[監視]** タブで、トピック サブスクリプションごとに使用状況のメトリック (送信メッセージ数と長さ) を追加できます。メトリックを追加するには、**[監視]** タブをクリックします。ページの下部にある **[メトリックの追加]** をクリックして、トピックの下にあるサブスクリプションを選択します。

![][1]

  [Azure 管理ポータル]: http://manage.windowsazure.com
  [0]: ./media/service-bus-monitor-message-entities/QueueDashboard.png
  [1]: ./media/service-bus-monitor-message-entities/AddMetrics.png
