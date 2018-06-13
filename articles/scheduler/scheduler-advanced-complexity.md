---
title: Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する
description: Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する方法について説明します。
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692336"
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する

Azure Scheduler ジョブの中核となるのがスケジュールです。 スケジュールは、Scheduler でジョブを実行するタイミングと方法を決定します。 

Scheduler を使って 1 回限りのスケジュールと定期的なスケジュールをジョブに設定することができます。 1 回限りのスケジュールは、指定された時刻に 1 回だけ起動します。 実質的には、1 回だけ実行される定期的なスケジュールです。 定期的なスケジュールは、事前に定義された頻度で起動します。

この柔軟性を活かし、さまざまなビジネス シナリオで Scheduler を使用することができます。その例を次に示します。

* **定期的なデータのクリーンアップ**。 たとえば、毎日、3 か月以上経過したツイートをすべて削除します。
* **アーカイブ**。 たとえば、毎月、バックアップ サービスに請求書の履歴を送信します。
* **外部データの要求**。 たとえば、15 分ごとに、NOAA からスキー場の天気予報を新たに取得します。
* **画像処理**。 たとえば、すべての平日のピーク時以外に、クラウド コンピューティングを利用して、その日にアップロードされた画像を圧縮します。

この記事では、Scheduler を使用して作成できるジョブの例を、手順に沿って説明します。 説明には、各スケジュールを表す JSON データを使用します。 [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx) を使用する場合は、この同じ JSON を使用して [Scheduler ジョブを作成](https://msdn.microsoft.com/library/mt629145.aspx)できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ
この記事に示す例では、Scheduler がサポートするさまざまなシナリオについて説明します。 これらの例では、多くの動作パターンに対応するスケジュールの作成方法を幅広く示します。以下に、いくつかの例を示します。

* 特定の日時に 1 回実行する。
* 特定の回数定期実行する。
* すぐに実行し繰り返す。
* 特定の時刻に開始して、*n* 分、時間、日、週、または月ごとに実行を繰り返す。
* 毎週または毎月の頻度で、特定の曜日または月の特定の日にのみ実行を繰り返す。
* 一定期間に複数回、実行を繰り返す。 たとえば、毎月最後の金曜日と最後の月曜日に実行するか、毎日午前 5 時 15 分と午後 5 時 15 分に実行します。

## <a name="date-and-date-time"></a>日付と日付/時刻
Scheduler ジョブにおける日付の参照は、[ISO 8601 仕様](http://en.wikipedia.org/wiki/ISO_8601)に従い、日付のみを含みます。

Scheduler ジョブにおける日付/時刻の参照は、[ISO 8601 仕様](http://en.wikipedia.org/wiki/ISO_8601)に従い、日付と時刻の両方を含みます。 UTC オフセットを指定していない日付/時刻は UTC と見なされます。  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>JSON と REST API を使用してスケジュールを作成する
[Scheduler REST API](https://msdn.microsoft.com/library/mt629143) を使用して基本的なスケジュールを作成するにはまず、[ご利用のサブスクリプションをリソース プロバイダーに登録](https://msdn.microsoft.com/library/azure/dn790548.aspx)します。 Scheduler のプロバイダー名は **Microsoft.Scheduler** です。 そのうえで[ジョブ コレクションを作成](https://msdn.microsoft.com/library/mt629159.aspx)してください。 最後に、[ジョブを作成](https://msdn.microsoft.com/library/mt629145.aspx)します。 

ジョブを作成するときは、以下に抜粋したような JSON を使用して、スケジュール設定と繰り返しを指定できます。

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>ジョブのスキーマの基礎
次の表に、ジョブの繰り返しとスケジュールを設定する際に使用する主要な要素の概要を示します。

| JSON での名前 | [説明] |
|:--- |:--- |
| **startTime** |日付/時刻の値。 基本的なスケジュールでは、**startTime** は最初の発生日時です。 複雑なスケジュールでは、**startTime** になるとすぐにジョブが開始されます。 |
| **recurrence** |ジョブの繰り返しの規則と、ジョブの実行が繰り返される頻度を指定します。 この recurrence オブジェクトは、**frequency**、**interval**、**endTime**、**count**、**schedule** という要素をサポートします。 **recurrence** を定義する場合は、**frequency** が必要です。 **recurrence** の他の要素は省略可能です。 |
| **frequency** |ジョブが繰り返される頻度の単位を表す文字列です。 サポートされる値は、"minute"、"hour"、"day"、"week"、"month" です。 |
| **interval** |正の整数。 **interval** は、ジョブの実行頻度を決定する **frequency** 値の間隔を示します。 たとえば、**interval** が 3 で **frequency** が "week" の場合、ジョブは 3 週間おきに繰り返されます。<br /><br />Scheduler でサポートされる最大の **interval** は、frequency が月の場合は 18 か月、週の場合は 78 週、日の場合は 548 日です。 時間と分については、1 <= **interval** <= 1000 の範囲をサポートします。 |
| **endTime** |ジョブがそれ以降実行されなくなる日付/時刻を指定する文字列。 **endTime** には、過去の値を設定することができます。 **endTime** と **count** が指定されていない場合は、ジョブが無期限で実行されます。 同じジョブに **endTime** と **count** の両方を含めることはできません。 |
| **count** |ジョブが完了するまでの実行回数を指定する正の (ゼロより大きい) 整数。<br /><br />**count** は、ジョブが完了と見なされる実行回数を表します。 たとえば、**count** に 5 を指定したジョブが月曜日から毎日実行される場合は、このジョブは金曜日に実行された後に完了します。 開始日が過去の場合、最初の実行は作成日時から計算されます。<br /><br />**endTime** または **count** が指定されていない場合は、ジョブが無期限で実行されます。 同じジョブに **endTime** と **count** の両方を含めることはできません。 |
| **schedule** |ジョブに頻度を指定すると、繰り返しのスケジュールに基づいて、そのジョブの繰り返しを変更できます。 **schedule** には、分、時間、曜日、月の日、週番号に基づく調整値を指定します。 |

## <a name="job-schema-defaults-limits-and-examples"></a>ジョブ スキーマの既定値、制限、例
次の各要素については、後でこの記事の中で詳しく取り上げます。

| JSON での名前 | 値の型 | 必須 | 既定値 | 有効な値 | 例 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |文字列 |いいえ  |なし |ISO-8601 の日付/時刻 |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |オブジェクト |いいえ  |なし |recurrence オブジェクト |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |文字列 |[はい] |なし |"minute"、"hour"、"day"、"week"、"month" |`"frequency" : "hour"` |
| **interval** |number |[はい] |なし |1 から 1000 |`"interval":10` |
| **endTime** |文字列 |いいえ  |なし |将来の時刻を表す日付/時刻の値 |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **count** |number |いいえ  |なし |>= 1 |`"count": 5` |
| **schedule** |オブジェクト |いいえ  |なし |schedule オブジェクト |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>詳細情報: startTime
ジョブの実行が **startTime** によってどのように制御されるかを次の表で説明します。

| startTime の値 | 繰り返しなし | スケジュールなしの繰り返し | スケジュールありの繰り返し |
|:--- |:--- |:--- |:--- |
| **開始時刻なし** |すぐに 1 回実行。 |すぐに 1 回実行。 2 回目以降は、最後の実行時刻から計算して実行。 |すぐに 1 回実行。<br /><br />2 回目以降は、繰り返しのスケジュールに基づいて実行。 |
| **開始時刻を過去に設定** |すぐに 1 回実行。 |初回は、開始時刻の後に将来の最初の実行時刻を計算し、その時刻に実行。<br /><br />2 回目以降は、最後の実行時刻から計算して実行。 <br /><br />詳細については、この表の後で紹介する例を参照してください。 |ジョブは、指定した開始時刻と "*同時に*" 開始。 最初は、開始時刻から計算したスケジュールに基づいて実行します。<br /><br />2 回目以降は、繰り返しのスケジュールに基づいて実行。 |
| **開始時刻が将来または現在に設定されている** |指定した開始時刻に 1 回実行。 |指定した開始時刻に 1 回実行。<br /><br />2 回目以降は、最後の実行時刻から計算して実行。|ジョブは、指定した開始時刻と "*同時に*" 開始。 最初は、開始時刻から計算したスケジュールに基づいて実行。<br /><br />2 回目以降は、繰り返しのスケジュールに基づいて実行。 |

ここでは、**startTime** が過去であり、recurrence を設定し、schedule を設定していない場合についての動作の例を説明します。  現在の時刻が 2015 年 4 月 8 日 13 時 00 分、**startTime** が 2015 年 4 月 7 日 14時 00 分で、**recurrence** は 2 日ごと (**frequency** に day と **interval** に 2 を指定) であると仮定します。**startTime** が過去、つまり現在の時刻よりも前であることに注意してください。

この条件では、最初の実行は、2015 年 4 月 9 日 14 時 00 分になります。 Scheduler エンジンは、開始時刻から実行を計算します。 過去のインスタンスはすべて破棄されます。 エンジンは、将来発生する次回のインスタンスを使用します。 この場合は、**startTime** が 2015 年 4 月 7 日午後 2 時 00 分となり、次回のインスタンスは 2 日後、つまり 2015 年 4 月 9 日午後 2 時 00 分となります。

**startTime** が 2015 年 4 月 5 日 14 時 00 分であるか 2015 年 4 月 1 日 14 時 00 分であるかに関係なく、最初の実行時間は同じであることに注意してください\. 最初の実行後、以降の実行はスケジュールを使用して計算されます。 2 回目以降の実行は、2015 年 4 月 11 日午後 2 時 00 分、2015 年 4 月 13 日午後 2 時 00 分、2015 年 4 月 15 日午後 2 時 00 分のように続きます。

最後に、ジョブにスケジュールが設定されている場合に、スケジュールに時および分が設定されていないと、既定では、最初の実行に指定した時および分が、それぞれ使用されます。

## <a name="deep-dive-schedule"></a>詳細情報: schedule
**schedule** を使用すると、ジョブの実行回数を "*制限*" できます。 たとえば、**frequency** が "month" のジョブに、31 日にのみ実行する schedule を設定すると、ジョブが実行されるのは 31 日がある月のみとなります。

**schedule** によりジョブの実行回数を "*増やす*" こともできます。 たとえば、**frequency** が "month" のジョブに、月の 1 日と 2 日に実行する schedule を設定すると、ジョブは、月に 1 回ではなく、毎月 1 日と 2 日に実行されます。

schedule の要素を複数指定した場合は、評価の順序は大きい要素から小さい要素の順序となります。つまり、週番号、月の日、曜日、時間、および分という順序です。

schedule の要素を次の表に詳しく示します。

| JSON での名前 | [説明] | 有効な値 |
|:--- |:--- |:--- |
| **分** |ジョブを実行する時刻 (分)。 |整数の配列。 |
| **hours** |ジョブを実行する時刻 (時)。 |整数の配列。 |
| **weekDays** |ジョブを実行する曜日。 週単位の頻度だけを指定できます。 |次の値の配列 (配列の最大サイズは 7)。<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />大文字/小文字は区別されません。 |
| **monthlyOccurrences** |ジョブを実行する月の日にちを指定します。 月単位の頻度だけを指定できます。 |**monthlyOccurrences** オブジェクトの配列:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** は、ジョブを実行する曜日です。 たとえば、*{Sunday}* は、月の毎週日曜日という意味です。 必須。<br /><br />**occurrence** は、月の第何週目に実行するかを表します。 たとえば、*{Sunday, -1}* は月の最終日曜日という意味です。 省略可能。 |
| **monthDays** |ジョブが実行される月の日にち。 月単位の頻度だけを指定できます。 |次の値の配列。<br />- -1 以下かつ -31 以上の任意の値<br />- 1 以上かつ 31 以下の任意の値|

## <a name="examples-recurrence-schedules"></a>例: 繰り返しのスケジュール
次の例に、さまざまな繰り返しのスケジュールを示します。 この例では、schedule オブジェクトとそのサブ要素に注目します。

スケジュール例では、**interval** が 1\.に設定されていると仮定します。 また、**schedule** の値に合った適切な **frequency** 値が指定されていると仮定します。 たとえば、**frequency** に "day" を使用し、**schedule** に **monthDays** の調整値を指定することはできません。 そのような制限については、この記事の最初の方で説明しています。

| 例 | [説明] |
|:--- |:--- |
| `{"hours":[5]}` |毎日午前 5 時に実行されます。<br /><br />Scheduler は "hours" の値を、"minutes" の値と 1 つずつ突き合わせ、ジョブを実行するすべての時刻の一覧を作成します。 |
| `{"minutes":[15], "hours":[5]}` |毎日午前 5 時 15 分に実行されます。 |
| `{"minutes":[15], "hours":[5,17]}` |毎日午前 5 時 15 分と午後 5 時 15 分に実行されます。 |
| `{"minutes":[15,45], "hours":[5,17]}` |毎日午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |
| `{"minutes":[0,15,30,45]}` |15 分ごとに実行されます。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |1 時間ごとに実行されます。<br /><br />このジョブは、1 時間ごとに実行されます。 分は、**startTime** が指定されていれば、その値によって制御されます。 **startTime** の値が指定されていない場合は、作成時間によって制御されます。 たとえば、開始時刻または作成時刻が午後 12 時 25 分である場合 (どちらが適用される場合でも)、ジョブは 0 時 25 分、1 時 25 分、2 時 25 分...23 時 25 分に実行されます。<br /><br />このスケジュールは、**frequency** に "hour"、**interval** に 1 を指定して、**schedule** 値を指定しないジョブと同等です。 両者の違いは、このスケジュールは、**frequency** と **interval** の値を変えることで、他のジョブの作成に使用できる点にあります。 たとえば、**frequency** が "month" の場合には、月 1 回だけ実行されます。**frequency** が "day" の場合には、スケジュールは毎日実行されます。 |
| `{minutes:[0]}` |毎正時に実行されます。<br /><br />このジョブも 1 時間ごとに実行されますが、正時に実行されます。(例、午前 12 時、午前 1 時、午前 2 時など)。 これは、**frequency** に "hour"、**startTime** 値に 0 分を指定したジョブ、または **schedule** を設定せずに frequency に "day" を指定したジョブに相当します。 ただし、**frequency** が "week" または "month" の場合、スケジュールはそれぞれ週に 1 日または月に 1 日だけ実行されます。 |
| `{"minutes":[15]}` |毎時、正時から 15 分後に実行されます。<br /><br />午前 0 時 15 分から開始され、午前 1 時 15 分、午前 2 時 15 分のように 1 時間ごとに実行され、 最後に午後 11 時 15 分に実行されます。 |
| `{"hours":[17], "weekDays":["saturday"]}` |毎週土曜日の午後 5 時に実行されます。 |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |毎週月曜日、水曜日、金曜日の午後 5 時に実行されます。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |毎週月曜日、水曜日、金曜日の午後 5 時 15 分と午後 5 時 45 分に実行されます。 |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |毎週月曜日、水曜日、金曜日の午後 5 時と午後 5 時に実行されます。 |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |毎週月曜日、水曜日、金曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |平日に 15 分ごとに実行されます。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |平日の午前 9 時から午後 4 時 45 分まで 15 分ごとに実行されます。 |
| `{"weekDays":["sunday"]}` |日曜日の開始時刻に実行されます。 |
| `{"weekDays":["tuesday", "thursday"]}` |火曜日と木曜日の開始時刻に実行されます。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |毎月 28 日の午前 6 時に実行されます (**frequency** が "month" であることを想定)。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |月の最終日の午前 6 時に実行されます。<br /><br />月の最終日にジョブを実行する場合は、28、29、30、または 31 という日にちではなく -1 を使用します。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |毎月最初と最後の日の午前 6 時に実行されます。 |
| `{monthDays":[1,-1]}` |毎月の最初と最後の日の開始時刻に実行されます。 |
| `{monthDays":[1,14]}` |毎月 1 日と 14 日の開始時刻に実行されます。 |
| `{monthDays":[2]}` |月の 2 日の開始時刻に実行されます。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |毎月最初の金曜日の午前 5 時に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |毎月最初の金曜日の開始時刻に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |毎月、月の最後から 3 番目の金曜日の開始時刻に実行されます。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |毎月最初と最後の金曜日の午前 5 時 15 分に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |毎月最初と最後の金曜日の開始時刻に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |毎月第 5 金曜日の開始時刻に実行されます。<br /><br />月に第 5 金曜日がない場合、このジョブは実行されません。 月の最終金曜日にジョブを実行する場合は、occurrence に対して 5 ではなく -1 を使用することを検討してください。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |月の最後の金曜日に 15 分ごとに実行されます。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |毎月第 3 水曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |

## <a name="see-also"></a>関連項目

- [What is Scheduler? (Scheduler とは)](scheduler-intro.md)
- [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
- [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)
- [Azure Scheduler のプランと課金](scheduler-plans-billing.md)
- [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)
- [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)
- [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)
- [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)
- [Azure Scheduler 送信認証](scheduler-outbound-authentication.md)

