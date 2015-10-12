<properties 
	pageTitle="ジョブを作成する | Microsoft Azure" 
	description="ジョブの作成のラーニング パス セグメント。"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>

# ジョブを作成する

Azure Stream Analytics の最上位のリソースは Stream Analytics ジョブです。ジョブは、1 つ以上の入力データ ソース、データ変換を表すクエリ、結果が書き込まれる 1 つ以上の出力ターゲットで構成されます。

Stream Analytics の使用を開始するには、新しい Stream Analytics ジョブを作成します。ジョブが開始されるまでこのアクションによる課金への影響はないことに注意してください。

1.  オンラインの [Microsoft Azure ポータル](http://manage.windowsazure.com)または Azure プレビュー ポータルにサインインします。
2.  Azure ポータルで **[新規] をクリック**し、次に **[Data Services]**、**[Stream Analytics]**、**[簡易作成]** の順にクリックします。

    ![簡易作成ウィザード](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

    Azure プレビュー ポータルで、[新規] をクリックし、次に [Data + Analytics]、[Azure Stream Analytics] の順にクリックします。

    ![Azure ポータルの作成ジョブ](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)

3.  Stream Analytics ジョブに必要な構成を指定します。
	- **[ジョブ名]** ボックスに、Stream Analytics ジョブを識別する名前を入力します。**ジョブ名**が検証されると、緑色のチェック マークが [ジョブ名] ボックスに表示されます。**[ジョブ名]** で使用できるのは英数字と "-" のみであり、文字数は 3 ～ 63 文字でなければなりません。
	- Azure ポータルの **[リージョン]**、または Azure プレビュー ポータルの **[場所]** を使用して、ジョブを実行する地理的な位置を指定します。
	- Azure ポータルを使用している場合は、**[地域モニタリング ストレージ アカウント]** として使用するストレージ アカウントを選択または作成します。このストレージ アカウントを使用して、この地域内で実行されるすべての Stream Analytics ジョブの監視データを格納します。
	- Azure プレビュー ポータルを使用している場合は、アプリケーションに関連するリソースを保持する、新規または既存の**リソース グループ**を指定します。

4.  新しい Stream Analytics ジョブのオプションを構成した後、**[Stream Analytics ジョブの作成]** をクリックします。Stream Analytics ジョブが作成されるまで数分かかる場合があります。Notification Hub で進行状況を監視し、状態を確認できます。

    ![Notfication Hub](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

    ![Azure プレビュー ポータルの作成ジョブ](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)

5.  新しいジョブが **[作成済み]** の状態で表示されます。**[開始]** ボタンが無効になっていることに注意してください。ジョブが開始できるようになるには、ジョブの入力、クエリ、出力を構成する必要があります。

    ![ジョブの状態](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

    ![Azure プレビュー ポータルのジョブの状態](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure Stream Analystics の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO1-->