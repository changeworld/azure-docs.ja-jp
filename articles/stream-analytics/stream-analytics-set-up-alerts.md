<properties 
	pageTitle="Stream Analytics のクエリのアラートを設定する |Microsoft Azure" 
	description="Stream Analytics のアラートについて理解する" 
	keywords="アラートを設定する"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>


# Azure Stream Analytics ジョブのアラートを設定する

## 概要: 監視ページ

メトリックが指定した条件に達したときにアラートをトリガーするルールを設定できます。

例: "過去 15 分間の出力イベントが 100 未満の場合は電子メール ID: xyz@company.com に電子メール通知を送信する"。

ルールは、ポータルでメトリックに対して設定することも、操作ログのデータに対して[プログラムによって](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)構成することもできます。

## Azure クラシック ポータルを使用してアラートを設定する

Microsoft Azure 管理ポータルでアラートを設定する方法は 2 つあります。

1.	Stream Analytics ジョブの **[監視]** タブ
2.	Management Services の操作ログ

## ポータルでジョブの [監視] タブを使用してアラートを設定する

1.	[監視] タブでメトリックを選択し、ダッシュボードの下部にある **[ルールの追加]** をクリックし、ルールを設定します。

    ![ダッシュボード](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	アラートの名前と説明を定義します。

    ![ルールの作成](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	しきい値、アラート評価ウィンドウ、およびアラートのアクションを入力します。

    ![条件の定義](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## 操作ログを使用してアラートを設定する

1.	[Azure クラシック ポータル](https://manage.windowsazure.com)で、Management Services の **[アラート]** タブに移動します。
2.	**[ルールの追加]** をクリックします。

    ![条件](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	アラートの名前と説明を定義します。[サービスの種類] として [Stream Analytics] を選択し、[サービス名] としてジョブの名前を選択します。

    ![アラートの定義](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Azure ポータルでアラートを設定する ##

Azure ポータルで、アラートを有効にする Stream Analytics ジョブを参照し、**[監視]** セクションをクリックします。表示された **[メトリック]** ブレードで、**[アラートの追加]** コマンドをクリックします。

  ![Azure ポータルのセットアップ](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)

アラート ルールに名前を付け、通知メールに表示される説明を選択できます。

[メトリック] を選択する場合は、メトリックの条件としきい値を選択します。

  ![Azure ポータルでのメトリックの選択](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)

Azure ポータルでのアラートの構成の詳細については、「[アラート通知の受信](../azure-portal/insights-receive-alert-notifications.md)」を参照してください。

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->