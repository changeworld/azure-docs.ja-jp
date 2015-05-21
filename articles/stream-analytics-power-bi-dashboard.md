<properties 
	pageTitle="Stream Analytics Power BI ダッシュボード | Azure" 
	description="Power BI のライブ ダッシュボードに Stream Analytics ジョブのデータを設定する方法について説明します。" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>
	
# Azure Stream Analytics & Power BI: ストリーミング データのリアルタイム分析に関するライブ ダッシュボード

Azure Stream Analytics の一般的な使用例の 1 つとして、大量のストリーミング データをリアルタイムで分析し、ライブ ダッシュボード \(ユーザーがブラウザーを更新する必要なくリアルタイムで更新されるダッシュボード\) で確認します。[Microsoft Power BI](https://powerbi.com/) は、すぐにライブ ダッシュボードを作成するのに最適です。[このシナリオを示したビデオの例をご覧ください](https://www.youtube.com/watch?v=SGUpT-a99MA)。この記事では、Azure Stream Analytics ジョブの出力として Power BI を使用する方法について説明します。注: 現時点では、Azure Stream Analytics は一般公開されていますが、Power BI 出力は Azure Stream Analytics のプレビュー機能です。

## 前提条件

* 組織 ID を使用している Microsoft Azure アカウント \(Power BI は組織 ID のみに対応しています。組織 ID とは、xyz@mycompany.com などの仕事またはビジネス用電子メール アドレスです。xyz@hotmail.com などの個人用電子メールは組織 ID ではありません。[組織 ID の詳細については、こちらをご覧ください](https://www.arin.net/resources/request/org.html)\)。
* ストリーミング データの使用元となる ASA \(Azure Stream Analytics\) ジョブの入力ストリーム。現時点では、ASA は、Azure イベント ハブまたは Azure BLOB ストアからの入力を受け入れます。  

## Azure Stream Analytics ジョブの作成

[Azure ポータル](https://manage.windowsazure.com)で、**\[新規\]、\[データ サービス\]、\[Stream Analytics\]、\[簡易作成\]** の順にクリックします。

次の値を指定してから、**\[Stream Analytics ジョブの作成\]** をクリックします。

* **\[ジョブ名\]** - ジョブ名を入力します。たとえば、**DeviceTemperatures** にします。
* **\[リージョン\]** - ジョブを配置するリージョンを選択します。パフォーマンスを確実に向上し、リージョン間のデータ転送にコストがかからないようにするために、ジョブとイベント ハブを同じリージョンに配置することを検討してください。
* **\[ストレージ アカウント\]** - このリージョン内で実行されているすべての Stream Analytics ジョブの監視データを格納するために使用するストレージ アカウントを選択します。既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。

Stream Analytics ジョブの一覧を表示するには、左側のウィンドウにある **\[Stream Analytics\]** をクリックします。

![図 1][graphic1]

> [AZURE.TIP]新しいジョブは、**\[未開始\]** の状態で表示されます。ページの下部にある **\[開始\]** ボタンが無効になっていることがわかります。ジョブを開始する前に、ジョブの入力、出力、クエリなどを構成する必要があるため、これは予期された動作です。

## ジョブの入力の指定

このチュートリアルでは、JSON シリアル化と UTF-8 エンコードによる入力としてイベント ハブを使用することを前提としています。

* ジョブの名前をクリックします。
* ページ上部にある **\[入力\]** をクリックし、**\[入力の追加\]** をクリックします。表示されたダイアログ ボックスには、入力を設定する手順がいくつか表示されます。
*	**\[データ ストリーム\]** を選択し、右側のボタンをクリックします。
*	**\[イベント ハブ\]** を選択してから、右側のボタンをクリックします。
*	3 ページ目で、次の値を入力または選択します。
  *	**\[入力のエイリアス\]** - このジョブの入力のフレンドリ名を入力します。後でクエリでこの名前を使用します。
  * **\[イベント ハブ\]** - 作成したイベント ハブが Stream Analytics ジョブと同じサブスクリプションにある場合は、イベント ハブがある名前空間を選択します。
*	イベント ハブが他のサブスクリプションにある場合、**\[別のサブスクリプションのイベント ハブを使用\]** を選択し、**\[Service Bus 名前空間\]**、**\[イベント ハブ名\]**、**\[イベント ハブ ポリシー名\]**、**\[イベント ハブ ポリシー キー\]**、**\[イベント ハブ パーティション数\]** の情報を手動で入力します。

> [AZURE.NOTE]このサンプルでは、既定のパーティション数 16 を使用します。

* **\[イベント ハブ名\]** - 所有する Azure イベント ハブの名前を選択します。
* **\[イベント ハブ ポリシー名\]** - 使用しているイベント ハブのイベント ハブ ポリシーを選択します。このポリシーに管理アクセス許可があることを確認します。
*	**\[イベント ハブ コンシューマー グループ\]** - 空にしておくことも、イベント ハブ上にあるコンシューマー グループを指定することもできます。イベント ハブの各コンシューマー グループに同時に含めることができる閲覧者は 5 名までであることに注意してください。したがって、それに応じて、ジョブに適したコンシューマー グループを決定してください。フィールドを空白のままにすると、既定のコンシューマー グループが使用されます。

*	右側のボタンをクリックします。
*	次の値を指定します。
  *	**\[イベント シリアライザー形式\]** - JSON
  *	**\[エンコード\]** - UTF8
*	チェック ボタンをクリックしてこのソースを追加し、Stream Analytics がイベント ハブに正常に接続できることを確認します。

## Power BI 出力の追加

1.  ページの上部にある **\[出力\]** をクリックしてから、**\[出力の追加\]** をクリックします。Power BI が出力オプションとして表示されます。

![図 2][graphic2]

> [AZURE.NOTE]注: Power BI 出力は、組織 ID を使用した Azure アカウントでのみ使用可能です。Azure アカウントに組織 ID を使用していない場合 \(ライブ ID、個人用 Microsoft アカウントなど\)、Power BI 出力オプションは表示されません。

2.  **\[Power BI\]** を選択してから、右のボタンをクリックします。
3.  次のような画面が表示されます。

![図 3][graphic3]

4.  この手順では、ASA ジョブに使用しているものと同じ組織 ID を慎重に使用する必要があります。現時点では、Power BI 出力は、ASA ジョブで使用されるものと同じ組織 ID を使用する必要があります。既に同じ組織の ID を使用した Power BI アカウントがある場合は、\[今すぐ承認\] を選択します。ない場合は、\[今すぐサインアップしてください\] を選択し、Power BI へのサインアップ時に、Azure アカウントと同じ組織 ID を使用します。[Power BI のサインアップの詳細については、こちらのブログをご覧ください](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx)。
5.  次のような画面が表示されます。

![図 4][graphic4]

次に示すように値を入力します。

* **\[出力のエイリアス\]** - 簡単に参照できるように出力のエイリアスを設定できます。この出力のエイリアスは、ジョブに複数出力を設定する場合に特に便利です。その場合は、クエリ内でこの出力を参照する必要があります。たとえば、出力のエイリアス値として "OutPbi" を使用します。
* **\[データセット名\]** - Power BI 出力に設定するデータセット名を入力します。たとえば、"pbidemo" を使用します。
*	**\[テーブル名\]** - Power BI 出力のデータセットの下にテーブル名を入力します。たとえば、"pbidemo" という名前にします。現在、ASA ジョブからの Power BI 出力では、1 つのデータセット内に 1 つのテーブルのみを保持できます。

>	[AZURE.NOTE] Note - You should not explicitly create this dataset and table in your Power BI account. They will be automatically created when you start your ASA job and the job starts pumping output into Power BI. If your ASA job query doesn’t return any results, the dataset and table will not be created.

*	**\[OK\]**、**\[接続のテスト\]** の順にクリックします。これで、出力の構成は完了です。

>	[AZURE.WARNING] Also be aware that if Power BI already had a dataset and table with the same name as the one you provided in this ASA job, the existing data will be overwritten.


## クエリの記述

ジョブの **\[クエリ\]** タブに移動します。Power BI で目的の出力を得るためのクエリを記述します。たとえば、次の SQL クエリのようなクエリを記述します。

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
        OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,1),
    	dspl

    
    
ジョブを開始します。イベント ハブがイベントを受け取り、クエリによって予期された結果が生成されることを確認します。クエリによって行が出力されない場合、Power BI データセットとテーブルは自動的に作成されません。

## Power BI でのダッシュボードの作成

[Powerbi.com](https://powerbi.com) にアクセスして、組織 ID でログインします。ASA ジョブ クエリで結果が出力された場合は、データセットが既に作成されていることがわかります。

![図 5][graphic5]

ダッシュ ボードを作成するために、\[ダッシュボード\] オプションに移動し、新しいダッシュボードを作成します。

![図 6][graphic6]

この例では、"Demo Dashboard" というラベルを付けます。

ここで、ASA ジョブによって作成されたデータセット \(現在の例では pbidemo\) をクリックします。このデータセットを基にグラフを作成するページが表示されます。作成できるレポートの一例を次に示します。

\[Σ temp\] フィールドと \[time\] フィールドを選択します。これらは、自動的にグラフの値と軸に設定されます。

![図 7][graphic7]

これにより、次に示すようなグラフが自動的に表示されます。

![図 8][graphic8]

\[値\] セクションで、\[temp\] のドロップダウンをクリックして、温度の **\[平均\]** を選択し、グラフ上で **\[視覚化\]** をクリックして **\[折れ線グラフ\]** を選択します。

![図 9][graphic9]

これで、平均の推移の折れ線グラフが表示されます。次に示すようにピン オプションを使用して、以前に作成したダッシュボードにレポートをピン留めできます。

![図 10][graphic10]

これで、このレポートがピン留めされたダッシュボードを表示すると、レポートがリアルタイムに更新されることがわかります。イベント内のデータを変更してみます \(温度の急上昇など\)。その結果がダッシュボードにリアルタイムに反映されることがわかります。

このチュートリアルでは、データセットに 1 種類のグラフを作成する方法を示しましたが、Power BI の可能性は無限です。Power BI ダッシュボードの別の例については、[Power BI の概要](https://youtu.be/L-Z_6P56aas?t=1m58s)ビデオをご覧ください。

Power BI を使用したダッシュボードの作成の詳細については、その他の役立つリソースとして、[Power BI プレビューのダッシュボード](http://support.powerbi.com/knowledgebase/articles/424868-dashboards-in-power-bi-preview)に関する記事をご覧ください。

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png

<!--HONumber=52-->
