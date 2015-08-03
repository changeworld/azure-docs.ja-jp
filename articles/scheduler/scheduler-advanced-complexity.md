<properties 
 pageTitle="Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する方法" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="05/15/2015" 
 ms.author="krisragh"/>

# Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する方法  

## 概要

Azure Scheduler ジョブの中核となるのが*スケジュール*です。スケジュールは、Scheduler でジョブを実行するタイミングと方法を決定します。

Azure Scheduler では、ジョブに対して 1 回限りの別々のスケジュールと、定期的なスケジュールを指定できます。*1 回限り*のスケジュールは、指定された時間に起動します。実質的には、これらも 1 回だけ実行される*定期的な*スケジュールです。定期的なスケジュールは、事前に定義された頻度で起動します。

この柔軟性により、Azure Scheduler では、次のようなさまざまなビジネス シナリオをサポートできます。

-	定期的なデータ クリーンアップ: たとえば、3 か月よりも古いツイートをすべて削除する
-	アーカイブ: たとえば、毎月、バックアップ サービスに請求書の履歴を送信する
-	外部データの要求: たとえば、15 分ごとに、NOAA から新しいスキー場の気象レポートを取得する
-	画像処理: たとえば、すべての平日のピーク時以外に、クラウド コンピューティングを利用して、その日にアップロードされた画像を圧縮する


この記事では、Azure Scheduler で作成できるジョブの例を、手順に沿って説明します。説明には、各スケジュールを表す JSON データを使用します。[Scheduler REST API](https://msdn.microsoft.com/library/azure/dn528946.aspx) を使用する場合は、この同じ JSON を使用して [Azure Scheduler ジョブを作成](https://msdn.microsoft.com/library/azure/dn528937.aspx)できます。

## サポートされるシナリオ

このトピックに示す多くの例では、Azure Scheduler がサポートするさまざまなシナリオについて説明します。これらの例では、多くの動作パターンに対応するスケジュールの作成方法を幅広く示します。以下に、いくつかの例を示します。

-	特定の日時に 1 回実行する
-	明示した回数だけ実行を繰り返す
-	すぐに実行し繰り返す 
-	特定の時刻に開始し、*n* 分、時間、日、週、または月ごとに実行を繰り返す。
-	毎週または毎月の頻度で、特定の日、特定の曜日、または月の特定の日にのみ実行を繰り返す
-	ある期間内で複数回実行を繰り返す。たとえば、毎月の最終金曜日と月曜日、毎日の午前 5 時 15 分と午後 5 時 15 分など

## 日付と日付/時刻

Azure Scheduler ジョブにおける日付は、[ISO 8601 仕様](http://en.wikipedia.org/wiki/ISO_8601)に従い、日付のみを含みます。

Azure Scheduler ジョブにおける日付/時刻の参照は、[ISO 8601 仕様](http://en.wikipedia.org/wiki/ISO_8601)に従い、日付の部分と時刻の部分の両方を含みます。UTC オフセットを指定していない日付/時刻は UTC と見なされます。

## 方法: JSON や REST API を使用した、スケジュールの作成

この記事の JSON の例と Azure Scheduler REST API を使用して、簡単なスケジュールを作成するには、[最初にクラウド サービスを作成](https://msdn.microsoft.com/library/azure/dn528943.aspx)してから、[ジョブ コレクションを作成](https://msdn.microsoft.com/library/azure/dn528940.aspx)し、[最後にジョブを作成](https://msdn.microsoft.com/library/azure/dn528937.aspx)します。ジョブを作成するときに、JSON を使用して、以下に抜粋したようなスケジュール設定と繰り返しを指定できます。

	{
	    "startTime": "2012-08-04T00:00Z", // optional
	     …
	    "recurrence":                     // optional
	    {
	        "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
	        "interval": 1,                // optional, how often to fire (default to 1)
	        "schedule":                   // optional (advanced scheduling specifics)
	        {
	            "weekDays": ["monday", "wednesday", "friday"],
	            "hours": [10, 22]                      
	        },
	        "count": 10,                  // optional (default to recur infinitely)
	        "endTime": "2012-11-04",      // optional (default to recur infinitely)
	    },
	    …
	}
	
## 概要: ジョブのスキーマの基礎

次の表に、ジョブの繰り返しとスケジュール設定に関連する主要な要素の概要を示します。

|**JSON での名前**|**説明**|
|:--|:--|
|**_startTime_**|_startTime_ は、日付/時刻です。簡単なスケジュールの場合は、_startTime_ は最初の実行で、複雑なスケジュールの場合は、ジョブは _startTime_ と同時に起動します。|
|**_recurrence_**|_recurrence_ オブジェクトは、ジョブの繰り返しの規則と、ジョブの実行が繰り返される頻度を指定します。この recurrence オブジェクトは、_frequency、interval、endTime、count_、および _schedule_ という要素をサポートします。_recurrence_ を定義する場合は、_frequency_ が必要です。_recurrence_ の他の要素は省略可能です。|
|**_frequency_**|_frequency_ の文字列は、ジョブが繰り返される頻度の単位を表します。サポートされる値は、_"minute"、"hour"、"day"、"week"_、または_"month"_ です。|
|**_interval_**|_interval_ は正の整数で、ジョブの実行頻度を決定する _frequency_ の間隔を示します。たとえば、_interval_ が 3 で _frequency_ が "week" の場合は、ジョブは 3 週間おきに繰り返されます。Azure Scheduler でサポートする最大の _interval_ は、月の場合は 18 か月、週の場合は 78 週、日の場合は 548 日です。時間と分については、1 <= _interval_ <= 1000 の範囲をサポートします。|
|**_endTime_**|_endTime_ の文字列に指定した日付/時刻を過ぎると、ジョブは実行されません。過去の日時を _endTime_ に指定するのは無効です。_endTime_ または count が指定されていない場合は、ジョブが無期限で実行されます。_endTime_ と _count_ の両方を、同じジョブに対して指定することはできません。|
|**_count_**|<p>_count_ は、正の整数 (0 より大きい) で、このジョブが完了するまでに実行される回数を指定します</p><p>。_count_ は、ジョブが完了済みとして判定されるまでの、ジョブの実行回数を表します。たとえば、_count_ に 5 を指定したジョブが月曜日から毎日実行される場合は、このジョブは金曜日に実行された後に完了します。開始日が過去である場合は、最初の実行は、作成時刻から計算されます。_endTime_ </p><p>も、_count_ も指定されていないと、ジョブは無限に実行されます。_endTime_ と _count_ の両方を、同じジョブに対して指定することはできません。</p>|
|**_schedule_**|ジョブに頻度を指定すると、繰り返しのスケジュールに基づいて、そのジョブの繰り返しを変更できます。_schedule_ には、分、時間、曜日、月の日および週の数に基づいた変更を指定します。|

## 概要: ジョブのスキーマの既定値、制限、および例

この概要の後で、これらの各要素の詳細について説明します。

|**JSON での名前**|**値の型**|**必須**|**既定値**|**有効な値**|**例**|
|:---|:---|:---|:---|:---|:---|
|**_startTime_**|String|いいえ|なし|ISO-8601 の日付/時刻|<code>"startTime" : "2013-01-09T09:30:00-08:00"</code>|
|**_recurrence_**|オブジェクト|いいえ|なし|recurrence オブジェクト|<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code>|
|**_frequency_**|String|あり|なし|"minute"、"hour"、"day"、"week"、"month"|<code>"frequency" : "hour"</code> |
|**_interval_**|Number|いいえ|1|1 ～ 1000。|<code>"interval":10</code>|
|**_endTime_**|String|いいえ|なし|将来の時刻を表す日付/時刻の値|<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
|**_count_**|Number|いいえ|なし|>= 1|<code>"count": 5</code>|
|**_schedule_**|オブジェクト|いいえ|なし|schedule オブジェクト|<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code>|

## 詳細: _startTime_

次の表では、_startTime_ による、ジョブの実行の制御方法を説明しています。

|**startTime の値**|**繰り返しなし**|**繰り返しあり。スケジュールなし**|**スケジュールありの繰り返し**|
|:--|:--|:--|:--|
|**開始時刻なし**|すぐに 1 回実行|すぐに 1 回実行。2 回目以降のジョブは、最後の実行時間から計算して実行|<p>すぐに 1 回実行</p><p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行</p>|
|**開始時刻が過去に設定されている**|すぐに 1 回実行|<p>初回は、開始時刻の後に将来の最初の実行時刻を計算し、その時刻に実行</p><p>2 回目以降のジョブは、最後の実行時刻から計算して実行</p><p>詳細については、この表の後にある説明を参照してください</p>|<p>ジョブは、指定した開始時刻と_同時に_開始。最初は、開始時刻から計算したスケジュールに基づいて実行</p><p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行</p>|
|**開始時刻が将来または現在に設定されている**|指定した開始時刻に 1 回実行|<p>指定した開始時刻に 1 回実行</p><p>2 回目以降のジョブは、最後の実行時刻から計算して実行</p>|<p>ジョブは、指定した開始時刻と_同時に_開始。最初は、開始時刻から計算したスケジュールに基づいて実行</p><p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行</p>|

ここでは、_startTime_ が過去であり、_recurrence_ を設定し、_schedule_ を設定していない場合についての動作を説明します。現在の時刻が 2015 年 4 月 8 日 13 時 00 分、_startTime_ が 2015 年 4 月 7 日 14時 00 分で、_recurrence_ は 2 日ごと (_frequency_ に day と _interval_ に 2 を指定) であると仮定します。 _startTime_ が過去、つまり現在の時刻よりも前であることに注意してください。

この条件では、_最初の実行_は、2015 年 4 月 9 日 14 時 00 分になります。Scheduler エンジンは、開始時刻から実行を計算します。過去のインスタンスはすべて破棄されます。エンジンは、将来発生する次回のインスタンスを使用します。したがって、この場合は、_startTime_ が 2015 年 4 月 7 日午後 2 時 00 分となり、次回のインスタンスは 2 日後、つまり 2015 年 4 月 9 日午後 2 時 00 分となります。

startTime が 2015 年 4 月 5 日 14 時 00 分または 2015 年 4 月 1 日 14 時 00 分である場合でも、最初の実行は同じであることに注意してください。最初の実行後、スケジュールを使用して 2 回目以降の実行が計算されます。よって、2 回目以降の実行は、2015 年 4 月 11 日午後 2 時 00 分、2015 年 4 月 13 日午後 2 時 00 分、2015 年 4 月 15 日午後 2 時 00 分のように続きます。

最後に、ジョブにスケジュールが設定されている場合に、スケジュールに時および/または分が設定されていないと、既定では、最初の実行に指定した時および/または分が、それぞれ使用されます。

## 詳細: _schedule_

一方、_schedule_ は、ジョブの実行回数を_制限_できます。たとえば、frequency が "month" のジョブに、31 日にのみ実行する _schedule_ を設定すると、ジョブは 31 <sup></sup>日がある月にのみ実行されます。

一方で、_schedule_ によりジョブの実行回数を_増やす_こともできます。たとえば、frequency が "month" のジョブに、月 の 1 日と 2 日に実行する _schedule_ を設定すると、ジョブは、月に 1 回ではなく、毎月 1 <sup></sup>日と 2 <sup></sup>日に実行されます。

schedule の要素を複数指定した場合は、評価の順序は大きい要素から小さい要素の順序となります。つまり、週番号、月の日、曜日、時間、および分という順序です。

_schedule_ の要素を次の表に示します。

|**JSON での名前**|**説明**|**有効な値**|
|:---|:---|:---|
|**分**|ジョブを実行する時刻 (分)|<ul><li>整数、または</li><li>整数の配列</li></ul>|
|**hours**|ジョブを実行する時刻 (時)|<ul><li>整数、または</li><li>整数の配列</li></ul>|
|**weekDays**|ジョブを実行する曜日。週単位の頻度だけを指定できます。|<ul><li>"Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday"、または "Sunday"</li><li>、上記のいずれかの値の配列 (最大配列サイズは 7) </li></ul>大文字小文字は区別されない|
|**monthlyOccurrences**|ジョブを実行する月の日にちを指定します。月単位の頻度だけを指定できます。|<ul><li>monthlyOccurence オブジェクトの配列:</li></ul> <pre>{ "day": _day_,<br /> "occurrence": _occurence_<br />}</pre><p> _day_ はジョブを実行する曜日です。たとえば、{Sunday} は、月の毎週日曜日という意味です。必須です。</p><p>_occurrence_ は、月の第何週目に実行するかを表します。たとえば、{Sunday, -1} は月の最終日曜日という意味です。省略可能。</p>|
|**monthDays**|ジョブが実行される月の日にち。月単位の頻度だけを指定できます。|<ul><li>-1 以下で、かつ -31 以上の任意の値。</li><li>1 以上で、かつ 31 以下の任意の値。</li><li>上記の値の配列</li></ul>|

## 例: 繰り返しのスケジュール

次に、schedule オブジェクトとそのサブ要素に注目して、さまざまな繰り返しの例を示します。

次のスケジュールでは、すべて _interval_ を 1 に設定してとを仮定しています。また、_schedule_ の値に合った適切な頻度が指定されていると仮定します。たとえば、スケジュールで frequency に "day" を使用して "monthDays" を変更することはできません。そのような制限は、上で説明しています。

|**例**|**説明**|
|:---|:---|
|<code>{"hours":[5]}</code>|毎日午前 5 時に実行。Azure Scheduler は "hours" の値を、"minutes" の値と 1 つずつ突き合わせ、ジョブを実行するすべての時刻の一覧を作成します。|
|<code>{"minutes":[15],"hours":[5]}</code>|毎日午前 5 時 15 分に実行|
|<code>{"minutes":[15],"hours":[5,17]}</code>|毎日午前 5 時 15 分と 午後 5 時 15 分に実行|
|<code>{"minutes":[15,45],"hours":[5,17]}</code>|毎日午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、および午後 5 時 45 分に実行|
|<code>{"minutes":[0,15,30,45]}</code>|15 分ごとに実行|
|<code>{hours":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23]}</code>|1 時間ごとに実行。このジョブは、1 時間ごとに実行されます。分は、指定されている場合は、_startTime_ によって制御されます。指定されていない場合は、作成時間によって制御されます。たとえば、開始時刻または作成時刻が午後 12 時 25 分である場合 (どちらが適用される場合でも)、ジョブは 0 時 25 分、1 時 25 分、2 時 25 分...23 時 25 分に実行されます。このスケジュールは、_frequency_ に "hour"、_interval_ に 1 を指定して、_schedule_ を指定しないジョブと同等です。両者の違いは、このスケジュールは、_frequency_ と _interval_ を変えることで、他のジョブの作成に使用できる点にあります。たとえば、_frequency_ が "month" の場合には、月 1 回だけ実行されます。_frequency_ が "day" の場合には、スケジュールは毎日実行されます。|
|<code>{minutes:[0]}</code>|毎正時に実行。このジョブも 1 時間ごとに実行されますが、正時に実行されます。(例、午前 12 時、午前 1 時、午前 2 時)。 これは、frequency に "hour"、startTime に 0 分を設定し、さらに頻度が "day" であれば、schedule を設定しなかったジョブと同等です。ただし、頻度が "week" または "month" の場合は、このスケジュールは、それぞれ週 に 1 日だけ、または月に 1 日だけ実行します。|
|<code>{"minutes":[15]}</code>|正時から 15 分経過後に実行。1 時間ごとに実行。午前 0 時 15 分から開始し、午前 1 時 15 分、午前 2 時 15 分...午後 10 時 15 分と続き、最後に午後 11 時 15 分に実行します。|
|<code>{"hours":[17],"weekDays":["saturday"]}</code>|毎週土曜日の午後 5 時に実行|
|<code>{hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|毎週月曜日、水曜日、および金曜日の午後 5 時に実行|
|<code>{"minutes":[15,45],"hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|毎週月曜日、水曜日、および金曜日の午後 5 時 15 分と午後 5 時 45 分に実行|
|<code>{"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|毎週月曜日、水曜日、および金曜日の午前 5 時 と午後 5 時に実行|
|<code>{"minutes":[15,45],"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|毎週月曜日、水曜日、および金曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、および午後 5 時 45 分に実行|
|<code>{"minutes":[0,15,30,45], "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|平日に 15 分ごとに実行|
|<code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|平日の午前 9 時と午後 4 時 45 分の間、15 分ごとに実行|
|<code>{"weekDays":["sunday"]}</code>|日曜日の開始時刻に実行|
|<code>{"weekDays":["tuesday", "thursday"]}</code>|火曜日と木曜日の開始時刻に実行|
|<code>{"minutes":[0],"hours":[6],"monthDays":[28]}</code>|毎月 28 日の午前 6 時に実行 (頻度を月と仮定した場合)|
|<code>{"minutes":[0],"hours":[6],"monthDays":[-1]}</code>|月の最終日の午前 6 時に実行。月の最終日にジョブを実行する場合は、28、29、30、または 31 という日にちではなく -1 を使用します。|
|<code>{"minutes":[0],"hours":[6],"monthDays":[1,-1]}</code>|毎月の最初と最後の日の午前 6 時に実行|
|<code>{monthDays":[1,-1]}</code>|毎月の最初と最後の日の開始時刻に実行|
|<code>{monthDays":[1,14]}</code>|毎月 1 日と 14 日の開始時刻に実行|
|<code>{monthDays":[2]}</code>|月の 2 日の開始時刻に実行|
|<code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|毎月の最初の金曜日の午前 5 時に実行|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|毎月の最初の金曜日の開始時刻に実行|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":-3}]}</code>|毎月、月の最後から 3 番目の金曜日の開始時刻に実行|
|<code>{"minutes":[15],"hours":[5],"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|毎月の最初と最後の金曜日の午前 5 時 15 分に実行|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|毎月の最初と最後の金曜日の開始時刻に実行|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":5}]}</code>|毎月の第 5 金曜日の開始時刻に実行第 5 金曜日にのみ実行するスケジュールとなっているため、月に第 5 金曜日がない場合は、これは実行されません。月の最終金曜日にジョブを実行する場合は、occurrence に対して 5 ではなく -1 を使用することを検討してください。|
|<code>{"minutes":[0,15,30,45],"monthlyOccurrences":[{"day":"friday","occurrence":-1}]}</code>|月の最終金曜日に 15 分ごとに実行|
|<code>{"minutes":[15,45],"hours":[5,17],"monthlyOccurrences":[{"day":"wednesday","occurrence":3}]}</code>|毎月第 3 水曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、および午後 5 時 45 分に実行|

## 関連項目
 
 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)
 
 [Scheduler Concepts, Terminology, and Entity Hierarchy (Scheduler の概念、用語集、エンティティ階層構造)](scheduler-concepts-terms.md)
 
 [管理ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)
 
 [Plans and Billing in Azure Scheduler (Azure Scheduler のプランと課金)](scheduler-plans-billing.md)
 
 [Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)
 
 [Scheduler PowerShell Cmdlets Reference (Scheduler PowerShell コマンドレット リファレンス)](scheduler-powershell-reference.md)
 
 [Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)
 
 [Scheduler の制限、規定値、エラー コード](scheduler-limits-defaults-errors.md)
 
 [Scheduler 送信認証](scheduler-outbound-authentication.md)
 
  

<!---HONumber=July15_HO4-->