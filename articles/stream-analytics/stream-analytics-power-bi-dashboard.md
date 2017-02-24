---
title: "Stream Analytics の Power BI ダッシュ ボード | Microsoft Docs"
description: "リアルタイム ストリーミング Power BI ダッシュボードを使用して、ビジネス インテリジェンスを収集して Stream Analytics ジョブからの大量のデータを分析します。"
keywords: "分析ダッシュボード、リアルタイム ダッシュボード"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics と Power BI: ストリーミング データのリアルタイム分析ダッシュボード
Azure Stream Analytics では、主要なビジネス インテリジェンス ツールの&1; つである、Microsoft Power BI を利用することができます。 ここでは、Azure Stream Analytics を使用して、大量のストリーミング データを分析し、リアルタイム Power BI 分析ダッシュボードで確認する方法を説明します。

[Microsoft Power BI](https://powerbi.com/) を使用すると、ライブ ダッシュボードがすぐに作成されます。 [このシナリオを示したビデオをご覧ください](https://www.youtube.com/watch?v=SGUpT-a99MA)。

この記事では、Azure Stream Analytics ジョブの出力として Power BI を使用して独自のカスタム ビジネス インテリジェンス ツールを作成し、リアルタイム ダッシュボードを活用する方法について説明します。

## <a name="prerequisites"></a>前提条件
* Microsoft Azure アカウント
* Power BI 用の会社または学校のアカウント
* 作業開始シナリオ "[リアルタイムの不正行為の検出](stream-analytics-real-time-fraud-detection.md)" を完了する。 この記事はこのワークフローに基づいており、Power BI ストリーミング データセット出力を追加します。

## <a name="add-power-bi-output"></a>Power BI 出力の追加
ジョブの入力が存在するため、Power BI への出力を定義できます。 ジョブ ダッシュボード**出力**の中央にあるボックスを選択します。 次に、使い慣れた **[+ 追加]** ボタンをクリックし、出力を作成します。

![出力を追加する](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

**[出力のエイリアス]** を指定 - 簡単に参照できるように出力のエイリアスを設定できます。 この出力のエイリアスは、ジョブに複数出力を設定する場合に特に便利です。 その場合は、クエリ内でこの出力を参照する必要があります。 たとえば、出力エイリアス値 = "StreamAnalyticsRealTimeFraudPBI" を使用しましょう。

次に、**[承認]** ボタンをクリックします。

![承認を追加する](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

Azure 資格情報 (職場または学校アカウント) を指定するためのウィンドウが表示され、Azure ジョブに Power BI 領域へのアクセスを提供します。

![フィールドを承認する](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

正しい情報を入力すると承認ウィンドウから新しい出力領域に変わり、その領域にはデータセット名とテーブル名のフィールドが表示されます。

![ワークスペース](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

フィールドは次のように定義します。
* **[グループ ワークスペース]** – データセットを作成する Power BI テナント内のワークスペースを選択します。
* **[データセット名]** - Power BI 出力に設定するデータセット名を入力します。 たとえば、"StreamAnalyticsRealTimeFraudPBI" を使用しましょう。
* **[テーブル名]** - Power BI 出力のデータセットの下にテーブル名を入力します。 たとえば、"StreamAnalyticsRealTimeFraudPBI" にします。 現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に&1; つのテーブルのみを保持できます。

**[作成]** をクリックすると、出力の構成が完了します。

> [!WARNING]
> この Stream Analytics ジョブで提供したものと同じ名前のデータセットとテーブルが Power BI に既に存在する場合は、既存のデータが上書きされることに注意してください。
> また、Power BI アカウントには、このデータセットとテーブルを明示的に作成する必要はありません。 Stream Analytics ジョブを開始すると自動的に作成され、Power BI への出力が開始されます。 ジョブ クエリで結果が返されない場合、データセットとテーブルは作成されません。
> 
> 

データセットは、次の設定で作成されます。
* defaultRetentionPolicy: BasicFIFO - データは FIFOで、最大行数は 200k です
* defaultMode: pushStreaming: ストリーミング タイルと従来のレポート ベース ビジュアル (プッシュ) の両方をサポートしています
* この時点では他のフラグでのデータセットの作成はサポートされていません

Power BI データセットの詳細については、[Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) リファレンスを参照してください。


## <a name="write-query"></a>クエリの記述
ジョブの **[クエリ]** タブに移動します。 Power BI で目的の出力を得るためのクエリを記述します。 たとえば、次の SQL クエリのようなクエリを記述して、電気通信業界で SIM 不正行為をキャッチします。


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Power BI でのダッシュボードの作成

[Powerbi.com](https://powerbi.com) にアクセスして、会社または学校の ID でログインします。 Stream Analytics ジョブ クエリで結果が出力された場合は、データセットが既に作成されていることがわかります。

![ストリーミング データセット](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

[追加] タイルをクリックし、カスタム ストリーミング データを選択します。

![カスタム ストリーミング データセット](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

次に、一覧からデータセットを選択します。

![ストリーミング データセット](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

視覚化カードを作成し、フィールド fraudulentcalls を選択します。

![不正行為を追加する](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

不正行為カウンターができました。

![不正行為カウンター](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

タイルを再度追加しますが、次は折れ線グラフを選択します。 値として fraudulentcalls を、windowend として軸を追加します。 ここでは過去 10 分間を選択しました。

![不正行為の呼び出し](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


このチュートリアルでは、データセットに&1; 種類のグラフを作成する方法を示しましたが、 Power BI は、組織の他の顧客のビジネス インテリジェンス ツールを作成するのに役立ちます。 Power BI ダッシュボードの別の例については、[Power BI の概要](https://youtu.be/L-Z_6P56aas?t=1m58s)ビデオをご覧ください。

Power BI 出力の構成と Power BI グループの利用の詳細については、「[Stream Analytics 出力について](stream-analytics-define-outputs.md "Stream Analytics 出力について")」の「[Power BI](stream-analytics-define-outputs.md#power-bi)」セクションをご覧ください。 Power BI を使用したダッシュボードの作成の詳細については、その他の役立つリソースとして、「 [Power BI のダッシュボード](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)」を参照してください。

## <a name="limitations-and-best-practices"></a>制限事項とベスト プラクティス
Power BI は、 [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Power BI の価格")

現在、Power BI は、約&1; 秒に&1; 回呼び出すことができます。 ストリーミング ビジュアルでは、15 kb のパケットがサポートされます。 これを超えると、ストリーミング ビジュアルは失敗します (ただし、プッシュは動作し続けます)。

そのため必然的に、Power BI は、Azure Stream Analytics で大幅なデータ負荷の低減が見られるケースへと落ち着きます。
データ プッシュが最大 1 プッシュ/秒になり、クエリがスループット要件の範囲内に収まるようにするために、TumblingWindow または HoppingWindow を使用することをお勧めします。次の式を使用して、現在のウィンドウに設定する値 (秒単位) を計算できます。

![式&1;](./media/stream-analytics-power-bi-dashboard/equation1.png)  

たとえば、1 秒ごとにデータを送信する 1,000 台のデバイスがあるとします。1,000,000 行/時に対応する Power BI Pro SKU を使用しており、Power BI でデバイスごとの平均データを取得する場合、1 台のデバイスにつき最大 4 秒ごとに 1 回プッシュできます (次の式を参照)。  

![式&2;](./media/stream-analytics-power-bi-dashboard/equation2.png)  

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


### <a name="renew-authorization"></a>承認の更新
ジョブが作成されてから、または最後の認証以降にパスワードが変わっている場合、Power BI アカウントを再認証する必要があります。 Azure Active Directory (AAD) テナント上で Multi-Factor Authentication (MFA) が構成されている場合は、Power BI の承認を 2 週間ごとに更新することも必要になります。 この問題の症状として、ジョブ出力が返されないことや、操作ログで "ユーザーの認証エラー" が発生することが挙げられます。

同様に、トークンの期限が切れているときに、ジョブを開始しようとすると、エラーが発生し、ジョブは開始できません。 この問題を解決するには、実行中のジョブを停止し、Power BI 出力に移動します。  [承認の更新] リンクをクリックし、データの損失を避けるため、"最後に停止した時刻" からジョブを再開します。 Power BI で認証が更新されたら、承認の領域に緑色のアラートが表示され、問題が解決されたことが反映されます。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


