<properties
	pageTitle="Stream Analytics の Power BI ダッシュ ボード | Microsoft Azure"
	description="リアルタイム ストリーミング Power BI ダッシュボードを使用して、ビジネス インテリジェンスを収集して Stream Analytics ジョブからの大量のデータを分析します。"
	keywords="分析ダッシュボード、リアルタイム ダッシュボード"
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

#  Stream Analytics と Power BI: ストリーミング データのリアルタイム分析ダッシュボード

Azure Stream Analytics では、主要なビジネス インテリジェンス ツールの 1 つである、Microsoft Power BI を利用することができます。ここでは、Azure Stream Analytics を使用して、大量のストリーミング データを分析し、リアルタイム Power BI 分析ダッシュボードで確認する方法を説明します。

[Microsoft Power BI](https://powerbi.com/) を使用すると、ライブ ダッシュボードがすぐに作成されます。[このシナリオを示したビデオをご覧ください](https://www.youtube.com/watch?v=SGUpT-a99MA)。

この記事では、Azure Stream Analytics ジョブの出力として Power BI を使用して独自のカスタム ビジネス インテリジェンス ツールを作成し、リアルタイム ダッシュボードを活用する方法について説明します。

## 前提条件

* Microsoft Azure アカウント
* ストリーミング データの使用元となる Stream Analytics ジョブの入力。Stream Analytics は、Azure Event Hubs または Azure BLOB ストレージストアからの入力を受け入れます。
* Power BI 用の会社または学校のアカウント

## Azure Stream Analytics ジョブの作成

[Azure クラシック ポータル](https://manage.windowsazure.com)で、**[新規]、[Data Services]、[Stream Analytics]、[簡易作成]** の順にクリックします。

次の値を指定してから、**[Stream Analytics ジョブの作成]** をクリックします。

* **[ジョブ名]** - ジョブ名を入力します。たとえば、**DeviceTemperatures** にします。
* **[リージョン]** - ジョブを配置するリージョンを選択します。パフォーマンスを確実に向上し、リージョン間のデータ転送にコストがかからないようにするために、ジョブとイベント ハブを同じリージョンに配置することを検討してください。
* **[ストレージ アカウント]** - このリージョン内で実行されているすべての Stream Analytics ジョブの監視データを格納するために使用するストレージ アカウントを選択します。既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。

Stream Analytics ジョブの一覧を表示するには、左側のウィンドウにある **[Stream Analytics]** をクリックします。

![図 1][graphic1]

> [AZURE.TIP] 新しいジョブは、**[未開始]** の状態で表示されます。ページの下部にある **[開始]** ボタンが無効になっていることがわかります。ジョブを開始する前に、ジョブの入力、出力、クエリなどを構成する必要があるため、これは予期された動作です。

## ジョブの入力の指定

このチュートリアルでは、JSON シリアル化と UTF-8 エンコードによる入力としてイベント ハブを使用することを前提としています。

* ジョブの名前をクリックします。
* ページ上部にある **[入力]** をクリックし、**[入力の追加]** をクリックします。表示されたダイアログ ボックスには、入力を設定する手順がいくつか表示されます。
*	**[データ ストリーム]** を選択し、右側のボタンをクリックします。
*	**[イベント ハブ]** を選択してから、右側のボタンをクリックします。
*	3 ページ目で、次の値を入力または選択します。
  *	**[入力のエイリアス]** - このジョブの入力のフレンドリ名を入力します。後でクエリでこの名前を使用します。
  * **[イベント ハブ]** - 作成したイベント ハブが Stream Analytics ジョブと同じサブスクリプションにある場合は、イベント ハブがある名前空間を選択します。
*	イベント ハブが他のサブスクリプションにある場合、**[別のサブスクリプションのイベント ハブを使用]** を選択し、**[Service Bus 名前空間]**、**[イベント ハブ名]**、**[イベント ハブ ポリシー名]**、**[イベント ハブ ポリシー キー]**、**[イベント ハブ パーティション数]** の情報を手動で入力します。

> [AZURE.NOTE]	このサンプルでは、既定のパーティション数 16 を使用します。

* **[イベント ハブ名]** - 所有する Azure イベント ハブの名前を選択します。
* **[イベント ハブ ポリシー名]** - 使用しているイベント ハブのイベント ハブ ポリシーを選択します。このポリシーに管理アクセス許可があることを確認します。
*	**[イベント ハブ コンシューマー グループ]** - 空にしておくことも、イベント ハブ上にあるコンシューマー グループを指定することもできます。イベント ハブの各コンシューマー グループに同時に含めることができる閲覧者は 5 名までであることに注意してください。したがって、それに応じて、ジョブに適したコンシューマー グループを決定してください。フィールドを空白のままにすると、既定のコンシューマー グループが使用されます。

*	右側のボタンをクリックします。
*	次の値を指定します。
  *	**[イベント シリアライザー形式]** - JSON
  *	**[エンコード]** - UTF8
*	チェック ボタンをクリックしてこのソースを追加し、Stream Analytics からイベント ハブに正常に接続できることを確認します。

## Power BI 出力の追加

1.  ページの上部にある **[出力]** をクリックしてから、**[出力の追加]** をクリックします。Power BI が出力オプションとして表示されます。

    ![図 2][graphic2]

2.  **[Power BI]** を選択してから、右のボタンをクリックします。
3.  次のような画面が表示されます。

    ![図 3][graphic3]

4.  この手順では、Stream Analytics ジョブ出力用の会社または学校のアカウントを指定します。既に Power BI アカウントがある場合は、**[今すぐ承認]** を選択します。ない場合は、**[今すぐサインアップ]** を選択します。[Power BI のサインアップの詳細については、こちらのブログをご覧ください](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx)。

    ![図 11][graphic11]

5.  次のような画面が表示されます。

    ![図 4][graphic4]

次に示すように値を入力します。

* **[出力のエイリアス]** - 簡単に参照できるように出力のエイリアスを設定できます。この出力のエイリアスは、ジョブに複数出力を設定する場合に特に便利です。その場合は、クエリ内でこの出力を参照する必要があります。たとえば、出力のエイリアス値として "OutPbi" を使用します。
* **[データセット名]** - Power BI 出力に設定するデータセット名を入力します。たとえば、"pbidemo" を使用します。
*	**[テーブル名]** - Power BI 出力のデータセットの下にテーブル名を入力します。たとえば、"pbidemo" という名前にします。現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に 1 つのテーブルのみを保持できます。
*	**[ワークスペース]** – データセットを作成する Power BI テナント内のワークスペースを選択します。

>	[AZURE.NOTE] お使いの Power BI アカウントでこのデータセットとテーブルを明示的に作成しないでください。これらは、Stream Analytics ジョブを開始し、そのジョブによって出力が Power BI に流し込まれるときに自動的に作成されます。ジョブ クエリから結果が返されない場合、データセットとテーブルは作成されません。

*	**[OK]**、**[接続のテスト]** の順にクリックします。これで、出力の構成は完了です。

>	[AZURE.WARNING] また、この Stream Analytics ジョブで提供したものと同じ名前のデータセットとテーブルが Power BI に既に存在する場合は、既存のデータが上書きされますので注意してください。


## クエリの記述

ジョブの **[クエリ]** タブに移動します。Power BI で目的の出力を得るためのクエリを記述します。たとえば、次の SQL クエリのようなクエリを記述します。

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

[Powerbi.com](https://powerbi.com) にアクセスして、会社または学校の ID でログインします。Stream Analytics ジョブ クエリで結果が出力された場合は、データセットが既に作成されていることがわかります。

![図 5][graphic5]

ダッシュ ボードを作成するために、[ダッシュボード] オプションに移動し、新しいダッシュボードを作成します。

![図 6][graphic6]

この例では、"Demo Dashboard" というラベルを付けます。

ここで、Stream Analytics ジョブによって作成されたデータセット (現在の例では pbidemo) をクリックします。このデータセットを基にグラフを作成するページが表示されます。作成できるレポートの一例を次に示します。

[Σ temp] フィールドと [time] フィールドを選択します。これらは、自動的にグラフの値と軸に設定されます。

![図 7][graphic7]

これにより、次に示すようなグラフが自動的に表示されます。

![図 8][graphic8]

[値] セクションで、[temp] のドロップダウンをクリックして、温度の **[平均]** を選択し、グラフ上で **[視覚化]** をクリックして **[折れ線グラフ]** を選択します。

![図 9][graphic9]

これで、平均の推移の折れ線グラフが表示されます。次に示すようにピン オプションを使用して、以前に作成したダッシュボードにレポートをピン留めできます。

![図 10][graphic10]

これで、このレポートがピン留めされたダッシュボードを表示すると、レポートがリアルタイムに更新されることがわかります。イベント内のデータを変更してみます (温度の急上昇など)。その結果がダッシュボードにリアルタイムに反映されることがわかります。

このチュートリアルでは、データセットに 1 種類のグラフを作成する方法を示しましたが、Power BI は、組織の他の顧客のビジネス インテリジェンス ツールを作成するのに役立ちます。Power BI ダッシュボードの別の例については、[Power BI の概要](https://youtu.be/L-Z_6P56aas?t=1m58s)ビデオをご覧ください。

Power BI 出力の構成と Power BI グループの利用の詳細については、「[Stream Analytics 出力について](stream-analytics-define-outputs.md "Stream Analytics 出力について")」の「[Power BI](stream-analytics-define-outputs.md#power-bi)」セクションを参照してください。Power BI を使用したダッシュボードの作成の詳細については、その他の役立つリソースとして、「[Power BI のダッシュボード](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)」を参照してください。

## 制限事項とベスト プラクティス

Power BI は、[https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Power BI の価格") で説明するように、同時実行性とスループットの制約の両方を採用しています。

そのため必然的に、Power BI は、Azure Stream Analytics で大幅なデータ負荷の低減が見られるケースへと落ち着きます。データ プッシュが最大 1 プッシュ/秒になり、クエリがスループット要件の範囲内に収まるようにするために、TumblingWindow または HoppingWindow を使用することをお勧めします。次の式を使用して、現在のウィンドウに設定する値 (秒単位) を計算できます。

![式 1](./media/stream-analytics-power-bi-dashboard/equation1.png)

たとえば、1 秒ごとにデータを送信する 1,000 台のデバイスがあるとします。1,000,000 行/時に対応する Power BI Pro SKU を使用しており、Power BI でデバイスごとの平均データを取得する場合、1 台のデバイスにつき最大 4 秒ごとに 1 回プッシュできます (次の式を参照)。

![式 2](./media/stream-analytics-power-bi-dashboard/equation2.png)

つまり、元のクエリが次のように変更されます。

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
    	TUMBLINGWINDOW(ss,4),
    	dspl

### PowerBI ビューの更新

よくある質問に、「PowerBI でダッシュボードが自動更新されないのはなぜか?」があります。

これを実現するには、PowerBI で Q & A を利用して、「タイムスタンプが今日の一時的な最大値」などの質問をし、そのタイルをダッシュボードにピン留めします。

### 承認の更新

ジョブが作成されてから、または最後の認証以降にパスワードが変わっている場合、Power BI アカウントを再認証する必要があります。Azure Active Directory (AAD) テナント上で Multi-Factor Authentication (MFA) が構成されている場合は、Power BI の承認を 2 週間ごとに更新することも必要になります。この問題の症状として、ジョブ出力が返されないことや、操作ログで "ユーザーの認証エラー" が発生することが挙げられます。

![図 12][graphic12]

同様に、トークンの期限が切れているときに、ジョブを開始しようとすると、エラーが発生し、ジョブは開始できません。エラーは次のようになります。

![PowerBI 検証エラー](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png)
 

この問題を解決するには、実行中のジョブを停止し、Power BI 出力に移動します。[承認の更新] リンクをクリックし、データの損失を避けるため、"最後に停止した時刻" からジョブを再開します。

![PowerBI 検証更新](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png)

Power BI で認証が更新されたら、承認の領域に緑色のアラートが表示されます。

![PowerBI 検証更新](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png)

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


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
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png

<!---HONumber=AcomDC_0921_2016-->